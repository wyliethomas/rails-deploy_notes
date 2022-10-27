Preq:
- You are able to ssh into the server


# 1. Install a bunch of *NIX stuff

You will need the following on the server before your Rails app will run.

```curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -```

```curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -```

```echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list```

```sudo add-apt-repository ppa:chris-lea/redis-server```

```sudo apt-get update```

```sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev dirmngr gnupg apt-transport-https ca-certificates redis-server redis-tools nodejs yarn```




# 2. Install Ruby via RVM

https://github.com/rvm/ubuntu_rvm

```sudo apt-add-repository -y ppa:rael-gc/rvm```

```sudo apt-get update```

```sudo apt-get install rvm```


Add your user to rvm group ($USER will automatically insert your username):

```sudo usermod -a -G rvm $USER```

### Restart your terminal window.

You can simply exit then ssh back in.

### Install a Ruby

You can run ```rvm list known``` to show available rubies. But you likely know what version you need.
In this case I know I needed Ruby 3.1.2.

```rvm install 3.1.2```

Hurry and wait. This process will take a few minutes.

... 

```gem install bundler```



# 3. Permissions & Public Key

```ssh-keygen -t rsa```

Copy your public key and add it to your github repo deploy keys

```cat ~/.ssh/id_rsa.pub```

You will need to give your user sudo permissions.

```sudo vim /etc/sudoers```

Find this line:

```
# User privilege specification
root    ALL=(ALL:ALL) ALL
```

And add this line below it. (Assuming your user us ubuntu)

```ubuntu  ALL=(ALL:ALL) ALL```

Update your Rails Secret Key

```cd ~/[YOUR PROJECT]```

```vim config.secrets.yml```



# 4. Clone the project to your server

```cd ~/```

```git clone git@github.com:[YOUR PROJECT]```



# 5. Install Apache

```sudo apt install apache2```

Install the Proxy Mod

```sudo a2enmod proxy```

```sudo a2enmod proxy_http```


Set up your vhost file:

```sudo vim /etc/apache2/sites-available/[YOUR PROJECT NAME].conf```

```
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        ServerName [YOUR DOMAIN].com
        ServerAlias [SUB DOMAIN].[DOMAIN].com
        DocumentRoot /home/ubuntu/[YOUR APP]/public

        <Location /assets>
                ProxyPass !
        </Location>

        <Location /system>
                ProxyPass !
        </Location>

        ProxyPass / http://localhost:3000
        ProxyPassReverse / http://localhost:3000

        #ErrorLog ${APACHE_LOG_DIR}/error.log
        #CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

Enable the conf file

```sudo a2ensite [YOUR PROJECT NAME]```

Disable the default conf file

```sudo a2dissite 000-default.conf```

Restart the server

```sudo service apache2 restart```



# 6. PostgreSQL & database config

```sudo apt-get install postgresql postgresql-contrib libpq-dev```

```sudo su - postgres```

```createuser --pwprompt deploy```

```createdb -O deploy [PROJECT DB NAME]```

```exit```

Almost there. One more weird thing to do. You will need to update a conf file. And you might have to 
hunt for it. Here is where mine is for this example.

```sudo vim /etc/postgresql/14/main/pg_hba.conf```

You need to look for this and change it from peer to md5:

```
# "local" is for Unix domain socket connections only
local   all             all                                     md5
```

Restart the database:

```sudo service postgresql restart```

You can test it real quick to make sure you can authenticate

```pgsql [dbname] --username=[username]```

If you can get in, you're fine to continue. If not, you'll need to trouble shoot your postgres user setup.

### Update your projects database settings.

```cd ~/[YOUR APP]/config```

```vim database.yml```

Paste in your config settings:

```
default: &default
  adapter: postgresql
  encoding: unicode
  username: <%= Figaro.env.postgres_user %>
  password: <%= Figaro.env.postgres_password %>
  pool: 5
  timeout: 5000

development:
  <<: *default
  database: [YOUR DEV DB NAME]

# Warning: The database defined as "test" will be erased and
# re-generated from your development database when you run "rake".
# Do not set this db to the same as development or production.
test:
  <<: *default
  database: [YOUR STAGING DB NAME]

production:
  <<: *default
  database: [YOUR PRODUCTION DB NAME]
```



# 7. Application Setup

You will need to set your secret for production

```cd ~/[YOUR APP]```

```RAILS_ENV=production rake secret```

Hang on to this secret, you will need to put it in your application.yml

Your app will probably have many more vars than this. You should have a application.yml.sample file 
that will have the full set of vars that you will need to set for this environment.

```cd ~/[YOUR APP]/config```

```vim application.yml```

```
production:
  base_url: [YOUR URL]
  postgres_user: [YOUR POSTGRES USER]        
  postgres_password: [YOUR POSTGRES PASSWORD] 
  secret_key_base: [YOUR SECRET] 
test:                          
  base_url:  
  postgres_user:         
  postgres_password:  
development:
  base_url:  
  postgres_user:         
  postgres_password: 
```

Install the gems

```bundle install```

Migrate your DB

```RAILS_ENV=production rake db:migrate```

If you see your migrate run, your rails app is set up! If not, you'll need to carefully read the errors 
and trouble shoot your setup.



# 8. App server











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



# 3. Public Key

```ssh-keygen -t rsa```

Copy your public key and add it to your github repo deploy keys

```cat ~/.ssh/id_rsa.pub```


# 4. Clone the project to your server

```cd ~/```

```git clone git@github.com:[YOUR PROJECT]```






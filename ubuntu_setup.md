Preq:
- You are able to ssh into the server


# Install Ruby via RVM

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

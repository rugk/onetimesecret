# ONE-TIME SECRET - v0.9.1 (2015-04-23)

*Keep sensitive info out of your email & chat logs.*

## What is a One-Time Secret? ##

A one-time secret is a link that can be viewed only one time. A single-use URL.

## Why would I want to use it? ##

When you send people sensitive info like passwords and private links via email or chat, there are copies of that information stored in many places. If you use a one-time link instead, the information persists for a single viewing which means it can't be read by someone else later. This allows you to send sensitive information in a safe way knowing it's seen by one person only. Think of it like a self-destructing message.

<a class="msg" href="https://onetimesecret.com/">Try it out!</a>

## Dependencies

* Any recent Linux (we use Debian, Ubuntu, and CentOS)
* Ruby 1.9.1+
* Redis 2.6+

## Install Dependencies

    # DEBIAN
    $ sudo apt-get update
    $ sudo apt-get install build-essential
    $ sudo apt-get install ntp libyaml-dev libevent-dev zlib1g zlib1g-dev openssl libssl-dev libxml2 libreadline5-dev
    $ mkdir ~/sources

    # CENTOS
    $ sudo yum install gcc gcc-c++ make libtool git ntp
    $ sudo yum install openssl-devel readline-devel libevent-devel libyaml-devel zlib-devel
    $ mkdir ~/sources


## Install Ruby 1.9

    $ cd ~/sources
    $ curl -O ftp://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.3-p362.tar.gz
    $ tar zxf ruby-1.9.3-p362.tar.gz
    $ cd ruby-1.9.3-p362
    $ ./configure && make
    $ sudo make install
    $ sudo gem install bundler


## Install Redis 2.6

    $ cd ~/sources
    $ curl -O http://download.redis.io/releases/redis-2.6.17.tar.gz
    $ tar zxf redis-2.6.17.tar.gz
    $ cd redis-2.6.17
    $ make
    $ sudo make install


## Install One-Time Secret

    $ sudo adduser ots
    $ sudo mkdir /etc/onetime
    $ sudo chown ots /etc/onetime

    $ sudo su - ots
    $ [download onetimesecret]
    $ cd onetimesecret
    $ bundle install --frozen --deployment --without=dev
    $ bin/ots init
    $ sudo mkdir /var/log/onetime /var/run/onetime /var/lib/onetime
    $ sudo chown ots /var/log/onetime /var/run/onetime /var/lib/onetime
    $ cp etc/* /etc/onetime/
    $ [edit settings in /etc/onetime/config]
    $ [edit settings in /etc/onetime/redis.conf]

    $ redis-server /etc/onetime/redis.conf
    $ bundle exec thin -e dev -R config.ru -p 7143 start


## Generating a global secret

We include a global secret in the encryption key so it needs to be long and secure. One approach for generating a secret:

    dd if=/dev/urandom bs=20 count=1 | openssl sha1


## Upgrading to 0.9

Upgrading to 0.9 should be seemless, however b/c of new functionality you will need to add the following to your config file:

    :incoming:
      :enabled: true
      :email: example@onetimesecret.com
      :passphrase: CHANGEME
      :regex: \A[a-zA-Z0-9]{6}\z
    :locales:
      - en
      - es

You run your configuration from `/etc/onetime/config` you will also need to copy the `./etc/locale` directory to `/etc/onetime/locale`:

    $ cd /path/2/onetimesecret
    $ sudo cp -r etc/locale /etc/onetime/

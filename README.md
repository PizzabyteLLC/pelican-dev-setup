# Pelican Development Environment Setup

Pelican development setup script

## Prerequisites

* Ubuntu Linux (preferably latest LTS of 22.04)
* Docker

## Pelican Panel

### Installing PHP and Composer

Run the following in your terminal to install a PPA for updated PHP, as the current Ubuntu repositories do not have updated PHP. This will also install all the required dependencies for PHP to work properly.

```bash
LC_ALL=C.UTF-8 add-apt-repository -y ppa:ondrej/php
sudo apt-get update
sudo apt-get install php8.3 php8.3-{common,cli,gd,mysql,mbstring,bcmath,xml,fpm,curl,zip,intl} tar unzipgit -y
```

Once PHP is installed, install Composer.

```bash
curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
```

### Installing NodeJS 20 (or higher) and `yarn`

Pelican requires NodeJS 20 at a minimum to operate. To install, use the following PPA to get an updated version of NodeJS due to the Ubuntu repositories not having updated NodeJS.

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs
```

Install `yarn`.

```bash
npm i --global yarn
```

### Clone repository

Clone the [`pelican-dev/panel`](https://github.com/pelican-dev/panel) repository.

```bash
git clone https://github.com/pelican-dev/panel.git
```

### Install Sail

Sail is a quick way to spin up a PHP/Laravel/NPM project on the fly in a Docker container, to install in the repository:

```bash
composer install --ignore-platform-reqs
composer require laravel/sail --dev
```

To completely install Sail, run:

```bash
php artisan sail:install
```

> [!NOTE]
>
> This will take a bit of time, this is going to run some Docker installations for your environment

It will prompt what services you want to install, just select MySQL as that is what currently will be the quickest to setup without hassle.

To start Sail, run:

```bash
./vendor/bin/sail up -d
```

> [!CAUTION]
>
> Make sure you do not have nginx/MySQL/MariaDB running or any other services taking up ports `80` or `3306`, disable them with `systemctl` or completely uninstall them. Otherwise the Sail instance will not start.

This starts Sail in a daemon mode, where it will not force output of the Docker containers.

> [!TIP]
>
> It is recommended to have an alias for Sail as its quite cumbersome to use in a terminal.
>
> Add `alias sail='sh $([ -f sail ] && echo sail || echo vendor/bin/sail)'` to your `~/.bashrc` or `~/.zshrc`

#### Copy (or make your own) `.env` file

To start the application, either:

* Copy the `.env.example` file into the current Git repository of the panel you just cloned, or
* Make your own with a custom app key

> [!IMPORTANT]
>
> Without the `APP_KEY` being set, the webserver will always return a `500 Server Error`, setting this fixes it.

### Setup your Pelican environment

To setup Pelican with Sail, run:

```bash
sail artisan migrate
```

This will prompt if you want to run the command while in production. Select "yes" to continue.

To run through the final setups of setup, run:

```bash
sail artisan p:environment:setup
sail artisan p:environment:database
```

To create a user, run:

```bash
sail p:user:make
```

Shutdown Sail for the time being by running:

```bash
sail down
```

To build the NodeJS aspects of the project, run:

```bash
yarn install --frozen-lockfile
yarn build:production
```

Start up Sail again either in a daemon mode or regularly with:

```bash
sail up
```

Visit the page where you have your environment running. This will run on port `80` by default.

> [!CAUTION]
>
> Make sure you do not have nginx running or any other services, disable them with `systemctl` or completely uninstall them. Otherwise the Sail instance will not start.

Log in with your created user account.

## Pelican Wings Daemon

> [!NOTE]
>
> Has not been fully tested, however since Pelican's version of Wings is a fork of Pterodactyl, it is safe to assume it is a carbon copy for installing and setup.

Follow the [Pterodactyl install guide](https://pterodactyl.io/wings/1.0/installing.html) for the wings daemon normally.

First, clone the [pelican-dev/wings](https://github.com/pelican-dev/wings) repository.

```bash
git clone https://github.com/pelican-dev/wings
```

And then run through the [traditional steps](https://pterodactyl.io/wings/1.0/installing.html) to run and setup Wings without a SSL certificate.

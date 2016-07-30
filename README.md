# Capistrano::WPCLI

[![Dependency Status](https://www.versioneye.com/user/projects/561c9bfda193340f2f001728/badge.svg?style=flat)](https://www.versioneye.com/user/projects/561c9bfda193340f2f001728)

**Note: this plugin works only with Capistrano 3.**

Provides command line tools to facilitate Wordpress database and uploads deploy.

## Installation

Add this line to your application's Gemfile:

    gem 'capistrano-wpcli'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install capistrano-wpcli

## Usage

All you need to do is put the following in `Capfile` file:

    require 'capistrano/wpcli'

### How it works (Tasks)

    cap staging wpcli:db:push

Pushes the local WP database to the remote server and replaces the urls.

Optionally backs up the remote database before pushing (if `wpcli_backup_db` is set to true, see Configuration).

- - -

    cap staging wpcli:db:pull

Pulls the remote server WP database to local and replaces the urls.

- - -

    cap staging wpcli:db:backup:remote

Pulls the remote server WP database to localhost, uses `wpcli_local_db_backup_dir` to define the location of the export.

- - -

    cap development wpcli:db:backup:local

Backs up the local WP database to localhost, uses `wpcli_local_db_backup_dir` to define the location of the export.

- - -

    cap staging wpcli:uploads:rsync:push

Push local uploads delta to remote machine using rsync.

- - -

    cap staging wpcli:uploads:rsync:pull

Pull remote uploads delta to local machine using rsync.

### Configuration

This plugin needs some configuration to work properly. You can put all your configs in Capistrano stage files i.e. `config/deploy/production.rb`.

Here's the list of options and the defaults for each option:

- - -

    set :wpcli_remote_url

Url of the WP root installation on the remote server (used by search-replace command).

- - -

    set :wpcli_local_url

Url of the WP root installation on the local server (used by search-replace command).

- - -

    set :local_tmp_dir

Absolute path to local directory temporary directory which is read and writeable.

Defaults to `/tmp`

- - -

    set :wpcli_backup_db

Set to true if you would like to create backups of databases on each push. Defaults to false.

- - -

    set :wpcli_local_db_backup_dir

Absolute or relative path to local directory for storing database backups which is read and writeable.

Defaults to `config/backup`

**IMPORTANT: Make sure to add the folder to .gitignore to prevent db backups from being in version control.**

- - -

    set :wpcli_local_uploads_dir

Absolute or relative path to local WP uploads directory.

Defaults to `web/app/uploads/`.

**IMPORTANT: Add trailing slash!**

- - -

    set :wpcli_remote_uploads_dir

Absolute path to remote wordpress uploads directory.

Defaults to `#{shared_path.to_s}/web/app/uploads/`

**IMPORTANT: Add trailing slash!**

### FAQ

- What if i want to use a custom port for rsync?
  You can by setting your port somewhere inside the :ssh_options precedence.
  See here: http://capistranorb.com/documentation/advanced-features/properties/#precedence

### Vagrant

If you are using another machine as a development server (Vagrant for example), you should define a `dev` role and indicate the path where the project lives on that server. This normally goes on `deploy.rb` file. Here's an example:

    server "example.dev", user: 'vagrant', password: 'vagrant', roles: %w{dev}

    set :dev_path, '/srv/www/example.dev/current'

## Contributing

1. Fork it ( https://github.com/lavmeiker/capistrano-wpcli/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request


# READ AND APPLY THE ISTRUCTIONS BELOW ONLY IF STAGING AND PRODUCTION ARE ON THE SAME SERVER
# These parameters need first to be moved into production.rb and staging.rb and then replace #{fetch(:bedrock_folder)} with the name of production and staging bedrock folder
set :deploy_to, -> { "/srv/www/#{fetch(:bedrock_folder)}" }
set :wpcli_remote_uploads_dir, -> { "/srv/www/#{fetch(:bedrock_folder)}/current/web/app/uploads/" }

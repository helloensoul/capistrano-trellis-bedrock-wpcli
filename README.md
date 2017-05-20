# Capistrano::WPCLI for Trellis and Bedrock

## THIS PROJECT IS NOT MAINTAINED ANYMORE

[![Dependency Status](https://gemnasium.com/badges/github.com/itsensoul/capistrano-trellis-bedrock-wpcli.svg)](https://gemnasium.com/github.com/itsensoul/capistrano-trellis-bedrock-wpcli)

**This project is based on [capistrano-wpcli](https://github.com/lavmeiker/capistrano-wpcli) and works only with Trellis and Bedrock.**

Provides command line tools to facilitate WordPress database and uploads deploy with Trellis and Bedrock.

## Requirements

* Ruby >= 2.0

Required gems:

* `capistrano` (~> 3.6.0)
* `sshkit` (~> 1.11.0)
* `bundler` (~> 1.12.0)
* `rake` (~> 11.2.0)

These can be installed manually with `gem install <gem name>` but it's highly suggested you use [Bundler](http://bundler.io/) to manage them. Bundler is basically the Ruby equivalent to PHP's Composer. Just as Composer manages your PHP packages/dependencies, Bundler manages your Ruby gems/dependencies. Bundler itself is a Gem and can be installed via `gem install bundler` (sudo may be required).

The `Gemfile` in the root of this repo specifies the required Gems (just like `composer.json`). Once you have Bundler installed, run `bundle install` to install the Gems in the `Gemfile`. When using Bundler, you'll need to prefix the `cap` command with `bundle exec` as seen below (this ensures you're not using system Gems which can cause conflicts).

## Installation

1. Download the latest release of [capistrano-bedrock](https://github.com/itsensoul/capistrano-bedrock/releases/latest).
2. Copy the following `capistrano-bedrock` files into the root of your Bedrock project:
  * `Capfile`
  * `Gemfile`
  * `Gemfile.lock`
  * `database.sh`
  * `uploads.sh`
  * `VERSION.md` (to know which version of `capistrano-bedrock` are you using)
3. Copy the content of `capistrano-bedrock .gitignore` file into the `.gitignore` file of your Bedrock project.
4. Copy the following `capistrano-bedrock` files/folders into your `config` directory:
  * `config/deploy/*`
  * `config/deploy.rb`
5. Run `gem install bundler -v "~>1.12.0" && bundle` into the root of your Bedrock project.
6. Edit your `config/deploy.rb` configs to set the roles/servers and connection options.
7. Edit your `config/deploy/*` stage/environment configs to set the specific roles/servers and connection options for each stage/environment.
8. Enjoy `capistrano-trellis-bedrock-wpcli` [Tasks](#tasksanchor).

## Usage

<a name="configurationanchor"></a>
### Configuration

This plugin needs some configuration to work properly.

Here's the list of available options and the defaults for each option to put in your `config/deploy.rb` file:

|Option|Default|Usage|
|---|---|---|
|`set :wpcli_local_url`|`//example.dev`|Url of the WordPress root installation on the local server (used by search-replace command).|
|`set :local_tmp_dir`|`/tmp`|Absolute path to local directory temporary directory which is read and writeable.|
|`set :wpcli_backup_db`|`false`|Set to true if you would like to create remote database backup on each push and local database backup on each pull.|
|`set :wpcli_delete_transients`|`true`|Set to false if you wouldn't like to delete transients on each push and pull. Transients will be removed only on the imported database.|
|`set :wpcli_local_db_backup_dir`|`config/backup`|Absolute or relative path to local directory for storing database backups which is read and writeable. **IMPORTANT: Make sure to add the folder to .gitignore to prevent db backups from being in version control.**|
|`set :wpcli_local_uploads_dir`|`web/app/uploads/`|Absolute or relative path to local WordPress uploads directory. **IMPORTANT: Add trailing slash!**|
|`set :wpcli_args`|`ENV['WPCLI_ARGS']`|You can pass arguments directly to WP-CLI using this var.|
|`set :format_options, log_file:`|`log/capistrano.log`|Capistrano's verbose output is saved to this file to facilitate debugging. Set to `nil` to disable completely. **IMPORTANT: Make sure to add the folder to .gitignore to prevent Capistrano logs from being in version control.**|

<br/>
Here's the list of available options and the defaults for each option to put in your `config/deploy/staging.rb` or `config/deploy/production.rb` file:

|Option|Default|Usage|
|---|---|---|
|`set :wpcli_remote_url`|`//example.com`|Url of the WordPress root installation on the remote server (used by search-replace command).|
|`set :wpcli_remote_uploads_dir`|`#{shared_path.to_s}/uploads/`|Absolute path to remote WordPress uploads directory. If this option is the same for staging and production, you can put this option in your `config/deploy.rb` file **IMPORTANT: Add trailing slash!**|

**Note: if you are using Trellis with staging and production on same server you probably need to modify this standard configuration.**

<a name="tasksanchor"></a>
### Tasks

####1. Manage database

* `bundle exec cap staging/production wpcli:db:push` - Pushes the local WordPress database to the remote server and replaces the urls (Optionally backs up the remote database before pushing, only if `wpcli_backup_db` is set to true, see [Configuration](#configurationanchor))

* `bundle exec cap staging/production wpcli:db:pull` - Pulls the remote server WordPress database to local and replaces the urls (Optionally backs up the local database before pulling, only if `wpcli_backup_db` is set to true, see [Configuration](#configurationanchor))

* `bundle exec cap staging/production wpcli:db:backup:remote` - Backs up remote staging/production database (uses `wpcli_local_db_backup_dir` to define the location of the export)

* `bundle exec cap development wpcli:db:backup:local` - Backs up local vagrant database (uses `wpcli_local_db_backup_dir` to define the location of the export)

####2. Manage updates

* `bundle exec cap staging/production wpcli:uploads:rsync:push` - Pushes the local uploads delta to remote machine using rsync.

* `bundle exec cap staging/production wpcli:uploads:rsync:pull` - Pulls the remote uploads delta to local machine using rsync.

## Contributing

1. Fork it (https://github.com/itsensoul/capistrano-trellis-bedrock-wpcli/fork)
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request

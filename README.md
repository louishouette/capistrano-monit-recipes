# Capistrano::Monit

**Note: this plugin works only with Capistrano 3.** 

Recipes for deploying Monit with Capistrano

Plase check the capistrano
gem version you're using before installing this gem:
`$ bundle show | grep capistrano`

### About

Capistrano Monit plugin allow you to generate a simple config file for Monit, and various config files for monitored processes, such as Nginx, Postgresql and Unicorn (soon Sidekiq too).

Here are the specific things this plugin does for your capistrano deployment
process:

* creates a config file for Monit
* creates process config files for Nginx, Postgresql and Unicorn

### Installation

Put the following in your application's `Gemfile`:

    group :development do
      gem 'capistrano', '~> 3.1'
      gem 'capistrano-monit-recipes', github: 'louishouette/capistrano-monit-recipes'
    end

Then:

    $ bundle install

### Usage

If you're deploying a standard rails app, all you need to do is put
the following in `Capfile` file:

    require 'capistrano-monit-recipes'

Make sure the `deploy_to` path exists and has the right privileges on the
server (i.e. `/home/deploy/apps`).<br/>
Or just install
[capistrano-safe-deploy-to](https://github.com/bruno-/capistrano-safe-deploy-to)
plugin and don't think about it.

To setup the server, run:

    $ bundle exec cap production setup

Easy, right?

Check below to see what happens in the background.


### Configuration

Put all your configs in capistrano stage or deploy files i.e:
`config/deploy/production.rb`<br>
`config/deploy.rb`

Here's the list of options and the defaults for each option:

```ruby
set :monit_configure_nginx, false
set :monit_configure_postgresql, false
set :monit_configure_unicorn, false
set :monit_templates_path, 'config/deploy/templates'
set :monitrc_path, '/etc/monit/monitrc'    

# UNICORN TEMPLATE specific settings
set :monit_unicorn_process_name, -> { "unicorn_#{fetch(:application)}_#{fetch(:stage)}" }
set :monit_unicorn_pid, shared_path.join("tmp/pids/unicorn.pid")
set :monit_unicorn_service_name, -> { "unicorn_#{fetch(:application)}_#{fetch(:stage)}" }
set :monit_unicorn_workers_number, 2
set :monit_unicorn_worker_process_name, -> { "#{fetch(:application)}_#{fetch(:stage)}_unicorn_worker_" }
set :monit_alert_email, nil

# NGINX TEMPLATE specific settings
set :monit_nginx_pid, '/var/run/nginx.pid'

# POSTGRESQL TEMPLATE specific settings
set :monit_postgresql_pid, '/var/run/postgresql/9.3-main.pid'

# MONIT TEMPLATE specific settings
set :monit_gmail_username, nil
set :monit_gmail_password, nil
set :monit_alert_email, nil
set :monit_password, nil
set :monit_port, '2812'
```

### Customizing the templates

If for any reason you want to edit or tweak the templates, you can copy them to
`config/deploy/templates/*.erb` with this command:

    $ bundle exec rails g capistrano:monit:template

After you edit the newly created files in your repo, they will be used as templates on the server.

### More Capistrano automation?

If you'd like to streamline your Capistrano deploys, you might want to check these zero-configuration, plug-n-play plugins:

- [capistrano-unicorn-nginx](https://github.com/bruno-/capistrano-unicorn-nginx)<br/>
no-configuration unicorn and nginx setup with sensible defaults
- [capistrano-rbenv-install](https://github.com/bruno-/capistrano-rbenv-install)<br/>
would you like Capistrano to install rubies for you?
- [capistrano-safe-deploy-to](https://github.com/bruno-/capistrano-safe-deploy-to)<br/>
if you're annoyed that Capistrano does **not** create a deployment path for the
app on the server (default `/var/www/myapp`), this is what you need!

### Contributing and bug reports

Contributions and improvements are very welcome. Just open a pull request and
I'll look it up shortly.

If something is not working for you, or you find a bug please report it.

### Thanks

This is my first gem, and even this README is eavily inspired from [Bruno Sutic](https://github.com/bruno-) work. I use his capistrano gems on a daily basis, they're worth it! So thanks dude!

### License

[MIT](LICENSE.md)
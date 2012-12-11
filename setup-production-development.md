## Linode 主機安裝與 Capistrano


### Update and upgrade system

`sudo apt-get update`
`sudo apt-get upgrade`

### Install mysql

`sudo apt-get install mysql-server`
`sudo apt-get install libmysqlclient-dev`

### Changing the Time Zone

`sudo dpkg-reconfigure tzdata`

### Install Build Tools and Library for Ruby 1.9.3

`sudo apt-get install build-essential zlib1g-dev libssl-dev libreadline5 libyaml-dev`


### Create user: apps

```
sudo adduser apps
sudo passwd apps
sudo vim /etc/sudoers
```

* make apps have sudo power

### Install ruby

```
curl -L https://get.rvm.io | bash -s stable --ruby
source /home/apps/.rvm/scripts/rvm
```

### Install Bundler

`gem install bundler`

### Install libcurl4-openssl-dev for passenger

`sudo aptitude install libcurl4-openssl-dev`

### Install XML parser

`sudo apt-get install libxml2 libxml2-dev libxslt1-dev`

### Nokogiri

`gem install nokogiri`

### Install node.js

<https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager>

```
apt-get install python g++ make
mkdir ~/nodejs && cd $_
wget -N http://nodejs.org/dist/node-latest.tar.gz
tar xzvf node-latest.tar.gz && cd `ls -rd node-v*`
./configure
make install
```

### Install passenger

`gem install passenger`

### Install nginx

`which passenger-install-nginx-module | xargs rvmsudo`

```
# Choose "download, compile, and install Nginx for me"
# Accept defaults for any other questions it asks you
```

### Install imagemagick

`sudo apt-get remove imagemagick`
`sudo apt-get install libmagickcore-dev libmagickwand-dev`


### Install rmagick

`gem install rmagick`

### Install git

`sudo apt-get install git`

### setup a script to control Nginx

```
> cd
> git clone git://github.com/jnstq/rails-nginx-passenger-ubuntu.git
> sudo mv rails-nginx-passenger-ubuntu/nginx/nginx /etc/init.d/nginx
> sudo chown root:root /etc/init.d/nginx
> sudo /etc/init.d/nginx restart
```

### set up nginx.conf

> vi /opt/nginx/conf/nginx.conf

#### insert following codes into the /opt/nginx/conf/nginx.conf

```
server {
      listen 80;
      server_name my_project.cc;
      root /home/apps/my_project/current/public;
      passenger_enabled on;
    }
```

### create folder for the project

`sudo su apps`
`cd ~`
`mkdir ~/my_project`

### set up deploy key for apps 

`ssh-keygen`

`more ~/.ssh/id_rsa.pub`

`pbcopy < ~/.ssh/id_rsa.pub`

#### Add the SSH key to GitHub


* browse https://github.com/rocodev/my_project
* click "Admin"
* click "Deploy keys"
* click "Add deploy key"
* input "Title"
* Paste the key into the "Key" field
* Click "Add key"
* Confirm the action by entering your GitHub password

### Set up authorized keys for developers

`vim /home/apps/.ssh/authorized_keys`


Paste the ssh public keys into the "/home/apps/.ssh/authorized_keys" file

### Set up Capistrano 

<http://ruby-taiwan.org/wiki/deploy_to_production_practice>

`vi /home/apps/my_project/shared/config/database.yml.production`

```
production:
  adapter: mysql2
  encoding: utf8
  reconnect: false
  database: my_project
  pool: 5
  username: root
  password: '1xsedqwdqd'
```

create "/my_project/Capfile"

Paste following codes into the "/my_project/Capfile" file

``` ruby
load 'deploy/assets'
Dir['vendor/gems/*/recipes/*.rb','vendor/plugins/*/recipes/*.rb'].each { |plugin| load(plugin) }
load 'config/deploy' # remove this line to skip loading any of the default tasks
load 'config/deploy/assets'
```

create "/config/deploy.rb"

Paste following codes into the "/config/deploy.rb" file

``` ruby
# -*- encoding : utf-8 -*-
require 'capistrano/ext/multistage'
require 'bundler/capistrano' #Using bundler with Capistrano
require 'cape'

set :stages, %w(staging production)
set :default_stage, "production"


Cape do
  mirror_rake_tasks :dev
end
```

create /config/deploy/production.rb

Paste following codes into the "/config/deploy/production.rb" file

``` ruby
# -*- encoding : utf-8 -*-
require 'rvm-capistrano'
default_environment["PATH"] = "/opt/ruby/bin:/usr/local/bin:/usr/bin:/bin"

set :application, "my_project"
set :repository,  "git@github.com:rocodev/my_project.git"
set :deploy_to, "/home/apps/my_project"

set :branch, "master"
set :scm, :git

set :user, "apps"
set :group, "apps"

set :deploy_to, "/home/apps/#{application}"
set :runner, "apps"
set :deploy_via, :remote_cache
set :git_shallow_clone, 1

set :use_sudo, false
set :rvm_ruby_string, '1.9.3'

role :web, "my_project.cc"                          # Your HTTP server, Apache/etc
role :app, "my_project.cc"                         # This may be the same as your `Web` server
role :db,  "my_project.cc"   , :primary => true # This is where Rails migrations will run

set :deploy_env, "production"
set :rails_env, "production"
set :scm_verbose, true
set :use_sudo, false


namespace :deploy do

  desc "Restart passenger process"
  task :restart, :roles => [:web], :except => { :no_release => true } do
    run "touch #{current_path}/tmp/restart.txt"
  end
end


namespace :my_tasks do
  task :symlink, :roles => [:web] do
    run "mkdir -p #{deploy_to}/shared/log"
    run "mkdir -p #{deploy_to}/shared/pids"
    
    symlink_hash = {
      "#{shared_path}/config/database.yml"   => "#{release_path}/config/database.yml",
    }

    symlink_hash.each do |source, target|
      run "ln -sf #{source} #{target}"
    end
  end

end



namespace :remote_rake do
  desc "Run a task on remote servers, ex: cap staging rake:invoke task=cache:clear"
  task :invoke do
    run "cd #{deploy_to}/current; RAILS_ENV=#{rails_env} bundle exec rake #{ENV['task']}"
  end
end

after "deploy:finalize_update", "my_tasks:symlink"
```

create /config/deploy/assets.rb

Paste following codes into the "/config/deploy/assets.rb" file

``` ruby
# -*- encoding : utf-8 -*-

# from https://github.com/AF83/capistrano-af83/blob/master/lib/capistrano/af83/deploy/assets.rb
set :assets_dependencies, %w(app/assets lib/assets vendor/assets Gemfile.lock config/routes.rb)

namespace :deploy do
  namespace :assets do

    desc <<-DESC
      Run the asset precompilation rake task. You can specify the full path \
      to the rake executable by setting the rake variable. You can also \
      specify additional environment variables to pass to rake via the \
      asset_env variable. The defaults are:

        set :rake,      "rake"
        set :rails_env, "production"
        set :asset_env, "RAILS_GROUPS=assets"
        set :assets_dependencies, fetch(:assets_dependencies) + %w(config/locales/js)
    DESC
    task :precompile, :roles => :web, :except => { :no_release => true } do
      from = source.next_revision(current_revision)
      if capture("cd #{latest_release} && #{source.local.log(from)} #{assets_dependencies.join ' '} | wc -l").to_i > 0
        run %Q{cd #{latest_release} && #{rake} RAILS_ENV=#{rails_env} #{asset_env} assets:precompile}
      else
        logger.info "Skipping asset pre-compilation because there were no asset changes"
      end
    end

  end
end
```

add 'rvm-capistrano' to Gemfile
Paste following codes into the "/Gemfile" file, and bundle install

``` ruby
  gem "rvm-capistrano"
```

initial deployment
`cap deploy:setup`

deploy
`cap deploy`

remote db migrate
`cap deploy:migrate`

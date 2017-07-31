require 'rubygems'
require 'rake'
require 'bundler/setup'
require 'net/http'
require 'json'

# Load DSL and Setup Up Stages
require 'capistrano/setup'
require 'capistrano/docker'

set :namespace,         "bolt"
set :application,       -> {""+rand(36**6).to_s(36)}
set :password,          "bolt30080"
set :ports,             ["80", "443"]
set :volumes,           []
set :links,             []
set :stage,             "production" ### Default stage

set :package, ENV['package']
set :version, ENV['version']
set :php,     ENV['php']
set :title,   ENV['title']
set :theme,   ENV['theme']


set :docker_image,      -> {"rossriley/docker-bolt:"+(fetch(:php)? fetch(:php) : 'latest')}
set :proxy, 'bolt.dockerfly.com'
set :env_vars,          {
    'BOLT_EXT'=>"#{fetch(:package)} #{fetch(:version)}",
    'BOLT_TITLE' => "#{fetch(:title)}",
    'VIRTUAL_HOST' => "#{fetch(:docker_appname)}.#{fetch(:proxy)}",
    'LETSENCRYPT_HOST' => "#{fetch(:docker_appname)}.#{fetch(:proxy)}",
    'LETSENCRYPT_EMAIL' => "ross@oneblackbear.com"
}


set :env_vars, fetch(:env_vars).merge({'BOLT_THEME'=> fetch(:package)}) if fetch(:theme)


task :production do
    set :branch,        "master"
    server 'bolt.rossriley.co.uk', user: 'docker', roles: %w{host}
end


namespace :docker do
    task :run do 
        on roles :host do
            execute build_run_command
            servername = "#{fetch(:docker_appname)}.#{fetch(:proxy)}"
            puts servername          
        end
    end
end
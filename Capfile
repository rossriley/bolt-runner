require 'rubygems'
require 'rake'
require 'bundler/setup'
require 'net/http'
require 'json'

# Load DSL and Setup Up Stages
require 'capistrano/setup'
require 'capistrano/docker'

set :namespace,         "bolt"
set :application,       -> {"runner_"+rand(36**6).to_s(36)}
set :password,          "bolt30080"
set :ports,             ["80"]
set :volumes,           []
set :links,             []
set :stage,             "production" ### Default stage

set :package, ENV['package']
set :version, ENV['version']

set :docker_image,      "rossriley/docker-bolt"
set :env_vars,          {
    'BOLT_EXT'=>"#{fetch(:package)} #{fetch(:version)}"
}
set :proxies, [
    'bolt.dockerfly.com'
]




task :production do
    set :branch,        "master"
    server 'bolt.rossriley.co.uk', user: 'docker', roles: %w{host}
end


namespace :docker do
    task :run do 
        on roles :host do
            execute build_run_command
            invoke 'docker:proxy'            
        end
    end
    
    task :proxy do
        on roles :host do
            config = ""
            fetch(:proxies).each do |proxy|
                proxyport = capture "docker port #{fetch(:docker_appname)}:80"
                config <<  "server {" + "\n"
                config << "  server_name "+proxy+";" + "\n"
                config << "  location / {" + "\n"
                config << "    proxy_pass http://"+proxyport+"/;" + "\n"
                config << "    proxy_set_header Host $http_host;" + "\n"
                config << "  }" + "\n"
                config << "}" + "\n"
            end
            basepath = "dockerappliance/conf/nginx/"
            destination = basepath + fetch(:docker_appname)+".conf"
            io   = StringIO.new(config)
            upload! io,   destination
        end 
    end
end
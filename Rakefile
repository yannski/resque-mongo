load 'tasks/redis.rake'

$LOAD_PATH.unshift 'lib'
require 'resque/tasks'

task :default => :test

desc "Run tests"
task :test do
  # Don't use the rake/testtask because it loads a new
  # Ruby interpreter - we want to run tests with the current
  # `rake` so our library manager still works
  Dir['test/*_test.rb'].each do |f|
    require f
  end
end

desc "Activate kicker - gem install kicker"
task :kick do
  exec "kicker -e rake lib test"
end

task :install => [ 'redis:install', 'dtach:install' ]

desc "Build a gem"
task :gem => [ :test, :gemspec, :build ]

begin
  require 'jeweler'
  require 'resque/version'

  Jeweler::Tasks.new do |gemspec|
    gemspec.name = "resque-mongo"
    gemspec.summary = ""
    gemspec.description = ""
    gemspec.email = "yatiohi@ideopolis.gr"
    gemspec.homepage = "http://github.com/ctrochalakis/resque-mongo"
    gemspec.authors = ["Christos Trochalakis"]
    gemspec.version = Resque::Version

    gemspec.add_dependency "mongo"
    gemspec.add_dependency "vegas", ">=0.1.2"
    gemspec.add_dependency "sinatra", ">=0.9.2"
    gemspec.add_development_dependency "jeweler"
  end
rescue LoadError
  puts "Jeweler not available. Install it with: "
  puts "gem install jeweler"
end

begin
  require 'sdoc_helpers'
rescue LoadError
  puts "sdoc support not enabled. Please gem install sdoc-helpers."
end

desc "Push a new version to Gemcutter"
task :publish => [ :test, :gemspec, :build ] do
  system "git tag v#{Resque::Version}"
  system "git push origin v#{Resque::Version}"
  system "git push origin master"
  system "gem push pkg/resque-mongo-#{Resque::Version}.gem"
  system "git clean -fd"
  exec "rake pages"
end

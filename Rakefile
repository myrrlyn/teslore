$: << 'lib'

require 'akatoshbot'
require 'sprockets'
require 'yaml'
require 'pry'
require 'pathname'
require 'logger'

Log ||= Logger.new STDOUT

task :console do
  TOPLEVEL_BINDING.pry
end

def get_config
  config = YAML.load File.read('config/reddit.yml')
  raise RuntimeError, "must have username, password, and default subreddit" unless config.include?('username') && config.include?('password') && config.include?('subreddit')
  Hash[config.map{|(k, v)| [k.to_sym, v]}]
end

def get_bot config
  bot = AkatoshBot::Client.new username: config[:username], password: config[:password]
  bot.instance_eval File.read('Bindfile') if File.exists?('Bindfile')
  bot
end

def get_subreddit config
  ENV['subreddit'] || config[:subreddit]
end

def get_stylesheet config
  ENV['stylesheet'] || config[:stylesheet] || 'teslore'
end

def get_context id
  context = Sprockets::Environment.new(Pathname(File.dirname(__FILE__))) do |env|
    env.logger = Log
  end
  context.append_path ?.
  context.append_path id
  context
end

def compile id
  ret = get_context(id).find_asset(id).to_s
  ret.gsub! /\/\*\s*\*\//m, ''
  ret.gsub! /^\s*$\r?\n/, ''
  ret
end

namespace :reddit do
  task :compile do
    config = get_config
    Log.info 'Compiling assets'
    mkdir_p 'build'
    stylesheet = get_stylesheet config
    File.open "build/#{stylesheet}.css", 'w' do |f|
      f.write compile(stylesheet)
    end
    Log.info 'Done'
  end

  task :push do
    config = get_config
    Log.info 'Logging into Reddit'
    bot = get_bot config
    Log.info 'Compiling assets'
    compiled = compile get_stylesheet(config)
    subreddit = get_subreddit config
    Log.info "Processing and uploading new stylesheet to /r/#{subreddit}"
    puts bot.put subreddit, compiled
    Log.info 'Done'
  end

  task :refresh do
    config = get_config
    Log.info 'Logging into Reddit'
    bot, subreddit = get_bot(config), get_subreddit(config)
    Log.info "Getting stylesheet for /r/#{subreddit}"
    stylesheet = bot.get subreddit
    Log.info "Processing and uploading new stylesheet to /r/#{subreddit}"
    bot.put subreddit, stylesheet
    Log.info 'Done'
  end
end

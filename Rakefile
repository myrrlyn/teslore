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

def get_bot
  config = YAML.load File.read('config/reddit.yml')
  raise RuntimeError, "must have username, password, and subreddit" unless config.include?('username') && config.include?('password') && config.include?('subreddit')
  username, password, subreddit = config['username'], config['password'], config['subreddit']
  bot = AkatoshBot::Client.new username: username, password: password
  bot.instance_eval File.read('Bindfile') if File.exists?('Bindfile')
  [bot, subreddit]
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

namespace :teslore do
  task :push do
    Log.info 'Logging into Reddit'
    bot, subreddit = get_bot
    Log.info 'Compiling assets'
    compiled = compile 'teslore'
    bot.put subreddit, compiled
    Log.info 'Done'
  end

  task :refresh do
    Log.info 'Logging into Reddit'
    bot, subreddit = get_bot
    Log.info "Getting stylesheet for /r/#{subreddit}"
    stylesheet = bot.get subreddit
    Log.info "Processing and uploading new stylesheet to /r/#{subreddit}"
    bot.put subreddit, stylesheet
    Log.info 'Done'
  end

  task :compile do
    Log.info 'Compiling assets'
    File.open 'build/teslore.css', 'w' do |f|
      f.write compile('teslore')
    end
    Log.info 'Done'
  end
end

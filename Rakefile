$: << 'lib'

require 'akatoshbot'
require 'yaml'
require 'pry'

task :console do
  TOPLEVEL_BINDING.pry
end

def get_bot
  config = YAML.load File.read('config/reddit.yml')
  raise RuntimeError, "must have username, password, and subreddit" unless config.include?('username') && config.include?('password') && config.include?('subreddit')
  username, password, subreddit = config['username'], config['password'], config['subreddit']
  bot = Crassius::Client.new username: username, password: password
  bot.instance_eval File.read('Bindfile') if File.exists?('Bindfile')
  [bot, subreddit]
end

namespace :ab do
  task :push do
    stylesheet = File.read config['stylesheet']
    bot, subreddit = get_bot
    bot.put subreddit, File.read(stylesheet)
  end

  task :refresh do
    bot, subreddit = get_bot
    bot.put subreddit, bot.get(subreddit)
  end
end

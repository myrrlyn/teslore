%w[pry yaml pathname logger akatoshbot sprockets sprockets-sass].each(&method(:require))

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

def get_postprocess config
  (ENV['postprocess'] || config[:postprocess]).split(',')
end

def get_context id
  $build = eval(File.read(File.join(File.dirname(__FILE__), '.version')))[id.to_sym]
  context = Sprockets::Environment.new(Pathname(File.dirname(__FILE__))) do |env|
    env.logger = Log
  end
  context.append_path ?.
  context.append_path id
  context
end

def compile id, config
  ret = get_context(id).find_asset(id).to_s + get_postprocess(config).reduce(''){|s, x| s + File.read(File.join(id, x)) + "\n"}

  # Remove empty comments
  ret.gsub! /\/\*\s*\*\//m, ''

  # Normalize line endings for sending to Reddit
  ret.gsub! /\r\n/, "\n"

  # Replace multiple newlines with one
  ret.gsub! /\n+/, "\n"

  # Verify images
  verify id, ret

  # Return the CSS blob
  ret
end

def verify id, str
  images = Hash[FileList[File.join(id, 'images', '*')].map{|a| [File.basename(a.rpartition(?.).first), true]}]
  return if images.empty?
  str.gsub(/\/\*.+?\*\//m, '').scan(/%%(.+?)%%/).each do |(m)|
    Log.warn "image `#{m}' not found in #{id}/images" unless images.include? m
  end
end

namespace :reddit do
  task :compile do
    config = get_config
    Log.info 'Logging into reddit'
    bot = get_bot config
    Log.info 'Compiling assets'
    mkdir_p 'build'
    stylesheet = get_stylesheet config
    File.open "build/#{stylesheet}.css", 'w' do |f|
      f.write bot.interpret(compile(stylesheet, config))
    end
    Log.info 'Done'
  end

  task :push do
    config = get_config
    Log.info 'Logging into reddit'
    bot = get_bot config
    Log.info 'Compiling assets'
    compiled = compile get_stylesheet(config), config
    subreddit = get_subreddit config
    Log.info "Processing and uploading new stylesheet to /r/#{subreddit}"
    bot.put subreddit, compiled
    Log.info 'Done'
  end

  task :refresh do
    config = get_config
    Log.info 'Logging into reddit'
    bot, subreddit = get_bot(config), get_subreddit(config)
    Log.info "Getting stylesheet for /r/#{subreddit}"
    stylesheet = bot.get subreddit
    Log.info "Processing and uploading new stylesheet to /r/#{subreddit}"
    bot.put subreddit, stylesheet
    Log.info 'Done'
  end
end

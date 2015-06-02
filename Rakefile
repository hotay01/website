$: << File.dirname(__FILE__) + '/lib'
ENV["RACK_ENV"] ||= "development"

require 'sinatra/asset_pipeline/task'
require './apps/dynamic/app'

task default: [:spec, :cucumber, :backup_events]

task :spec do
  sh 'bundle exec rspec'
end

task :cucumber do
  sh 'bundle exec cucumber'
end

task :backup_events do
  require 'open-uri'
  require_relative 'lib/cucumber/website/config'
  extend Cucumber::Website::Config
  config = load_config('development')
  config['site']['calendars'].each do |url|
    file_name = "event-backup/#{Time.now.strftime('%Y%m%d')}-#{url.gsub(/[:\/]/, '-')}"
    file_name += '.ics' unless file_name =~ /\.ics/
    File.open(file_name, 'w') { |io| io.write(open(url).read) }
  end
end

Sinatra::AssetPipeline::Task.define! Cucumber::Website::App

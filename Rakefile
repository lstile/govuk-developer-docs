require_relative './app/requires'

begin
  require 'rspec/core/rake_task'
  RSpec::Core::RakeTask.new(:spec)
rescue LoadError
end

task default: [:spec]

namespace :assets do
  task :precompile do
    sh 'git clone https://github.com/alphagov/govuk-content-schemas.git /tmp/govuk-content-schemas --depth=1 && GOVUK_CONTENT_SCHEMAS_PATH=/tmp/govuk-content-schemas middleman build'
  end
end

def safe_app_name(app_name)
  app_name.to_s.gsub('_', '').gsub('-', '')
end


task :x do
  require "gviz"

  SKIP = %w[static]

  relevant_apps = []

  simple = Gviz.new
  simple.graph do
    tree = YAML.load_file('../govuk-puppet/development-vm/dependencies.yml')
    names = {}
    tree.each do |key, settings|
      app_name = safe_app_name(key)
      names[key] = app_name
      next if app_name.in?(SKIP)

      # Draw the connections between this app and its dependencies
      dependent_apps = settings["dependencies"].to_a.map { |t| safe_app_name(t) } - SKIP
      route app_name => dependent_apps

      relevant_apps << dependent_apps + [app_name]
    end

    relevant_apps.flatten.uniq.each do |safe_name|
      # Create a node for the app
      node safe_name.to_sym, label: names[safe_name]
    end

    nodes shape: 'polygon'

    global(rankdir: "RL", rankseq: 'equally')
  end

  simple.save("deps", :png)
end

desc "Find deployable applications that are not in this repo"
task :verify_deployable_apps do
  common_yaml = HTTP.get_yaml("https://raw.githubusercontent.com/alphagov/govuk-puppet/master/hieradata/common.yaml")
  deployable_applications = common_yaml["deployable_applications"].map { |k, v| v['repository'] || k }
  our_applications = AppDocs.pages.map(&:github_repo_name)

  intentionally_missing =
    %w[
    backdrop
    spotlight
    stagecraft
    performanceplatform-admin
    performanceplatform-big-screen-view

    EFG

    govuk-cdn-logs-monitor
    govuk-content-schemas
    govuk_crawler_worker
    smokey

    errbit
    kibana-gds
    sidekiq-monitoring

    govuk_delivery
  ]

  puts "Deployables is not included in applications.yml:"

  (deployable_applications - (our_applications + intentionally_missing)).uniq.each do |missing_app|
    puts missing_app
  end
end

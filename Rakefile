require 'bundler/setup'
require 'uglifier'
require File.join(File.dirname(__FILE__) + '/lib/trix/environment')

namespace :trix do
  environment = Trix::Environment.new(".")
  environment.paths = %w( assets polyfills src )
  environment.assets = %w( demo.html demo.js trix.js polyfills.js basecamp.png )
  environment.sprockets_environment.js_compressor = Uglifier.new

  desc "Clean Trix distribution"
  task :clean do
    environment.clean
  end

  desc "Build Trix distribution"
  task :dist do
    environment.dist
  end

  desc "Open Trix demo in a browser"
  task :browser => :dist do
    system "open", environment.dist_path_for("index.html")
  end

  desc "Print code statistics"
  task :stats do
    lines = `find src -name '*.coffee' -type f | xargs sed -n '/^ *\\(#.*\\)*\$/!p'`.strip.split("\n")
    classes = lines.grep(/(^|\s)class\s+/)
    methods = lines.grep(/^\s*[^:]+:.*[-=]>\s*$/)
    puts "#{lines.count} lines, #{classes.count} classes, #{methods.count} methods"
  end
end

namespace :test do
  environment = Trix::Environment.new("test")
  environment.paths = %w( assets src vendor ../src ../polyfills )
  environment.assets = %w( test.css test.html test.js polyfills.js )

  desc "Clean Trix tests"
  task :clean do
    environment.clean
  end

  desc "Build Trix tests"
  task :dist do
    environment.dist
  end

  desc "Open Trix tests in a browser"
  task :browser => :dist do
    system "open", environment.dist_path_for("test.html")
  end

  desc "Listen for file changes and run Trix tests in Chrome"
  task :listen do
    require "listen"

    Listen.to("src/", "test/src", "lib") do |modified, added, removed|
      files = modified + added + removed
      puts "\nModified: #{files.join(', ')}"
      puts "Running tests in Chrome..."
      `osascript -l JavaScript -e 'Application("Chrome").windows[0].tabs.whose({ url: { _beginsWith: "http://trix.dev/test" } })[0].reload()'`
    end.start

    sleep
  end

  desc "Run Trix tests on a suite of browser VMs"
  task vm: :dist do
    require_relative "test/vm_test_runner.rb"
    Trix::VMTestRunner.new(environment).run
  end
end

task :default => "test:browser"

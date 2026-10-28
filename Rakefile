# From: https://github.com/ainc/awesomeinc2013/blob/master/Rakefile 

require "rubygems"

require "bundler/setup"
require "jekyll"

ENV["JEKYLL_ENV"] = "production"

desc "Generate blog files"
task :generate do
  Jekyll::Site.new(Jekyll.configuration({
    "source"      => ".",
    "destination" => "_site"
  })).process
end


desc "Disabled: publish through the approved GitHub Actions workflow"
task :publish do
  abort "Publishing is disabled here. Use the approved GitHub Actions workflow on main after release authorization."
end

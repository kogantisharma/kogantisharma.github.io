source "https://rubygems.org"

# Jekyll
gem "jekyll", "~> 4.3"

# Minimal Mistakes theme
gem "minimal-mistakes-jekyll", "~> 4.26"

# Plugins
group :jekyll_plugins do
  gem "jekyll-paginate"
  gem "jekyll-sitemap"
  gem "jekyll-gist"
  gem "jekyll-feed", "~> 0.12"
  gem "jekyll-include-cache"
  gem "jekyll-seo-tag"
end

# Windows / JRuby timezone support
platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem "tzinfo", ">= 1", "< 3"
  gem "tzinfo-data"
end

# Directory watcher (Windows performance)
gem "wdm", "~> 0.1.1", platforms: [:mingw, :x64_mingw, :mswin]

# Webrick (needed for Ruby 3+)
gem "webrick"

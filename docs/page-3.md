Automated Testing
==========
* Saves time
* Make sure changes don't break things

From the terminal:
`$ gem install rspec rack-test`

We're going to also add a `Gemfile`
First, install the gem [bundler](http://bundler.io/)

`$ touch Gemfile #creates an empty file`

Open `Gemfile` and add all the gems so far

```ruby
source 'https://rubygems.org'
gem 'sinatra'
gem 'rspec'
gem 'rack-test'
```

`$ bundle install`

Your first test for this app:
`app_test.rb`

```ruby
require_relative "app"
require "rspec" #Popular testing library
require "rack/test" #Library for testing rack webserver

describe "Hello application" do
  include Rack::Test::Methods
  
  def app
    Sinatra::Application
  end
  
  it "says hello" do
    get "hello"
    last_response.should be_ok
    last_response.body.should == "Hello, Sinatra"
  end
end
```

Run your test

`$ rspec app_test.rb`

Your test should have failed here's why [page-4](page-4.md)
Correct your test `app_test.rb`

Replace `"Hello Sinatra"` with `"Just Do It"`

Run your test, it should pass now.

## Here's our game plan:

GET    /bookmarks    - get a list of all bookmarks
GET    /bookmarks/ID - get the details of bookmark ID
POST   /bookmarks    - create a new bookmark
PUT    /bookmarks/ID - update an existing bookmark
DELETE /bookmarks/ID - delete a bookmark

*CRUD* - major functions that are implemented in relational database applications.

Create the file `bookmark.rb`

```ruby
require "data_mapper"

class bookmark
  include DataMapper::REsource
  
  property :id, Serial
  property :url, String
  property :title, String
end
```

##Let's build our app now.
open up `app.rb` delete the "Hello Sinatra" code and add the following

```ruby
require "sinatra"
require "data_mapper"
require_relative "bookmark"

DataMapper::setup(:default, "sqlite3://#{Dir.pwd}/bookmarks.db")
DataMapper.finalize.auto_upgrade!
```

```ruby
require 'dm-serializer' #converts DataMapper classes to JSON

#CRUD
def get_all_bookmarks
  Bookmark.all(:order => :title)
end

get "/bookmarks" do
  content_type :json
  get_all_bookmarks.to_json
end

post "/bookmarks" do
  input = params.slice "url", "title"
  bookmark = Bookmark.create input
  # Created
  [201, "/bookmarks/#{bookmark['id']}"]
end

class Hash
  def slice(*whitelist) #splat operator https://endofline.wordpress.com/2011/01/21/the-strange-ruby-splat/
    whitelist.inject({}) {|result, key| result.merge(key => self[key])}
  end
end

get "/bookmarks/:id" do
  id= params[:id]
  bookmark = Bookmark.get(id)
  content_type :jsonbookmark.to_json
end

put "/bookmarks/:id" do
  id= params[:id]
  bookmark = )bookmark.get(id)
  input = params.slice "url", "title"
  bookmark.update input
  204 # No Content
end

delete "/bookmarks/:id" do
  id= params[:id]
  bookmark = Bookmark.get(id)
  bookmark.destroy
  200 # OK
end
```

##Write an automated test
Delete the `it` block of your test and add the following

```ruby

describe "Bookmarklet Application" do
  include Rack::Test::Methods
  
  def app
    Sinatra::Application
  end
  
  it "creates a new bookmark" do
    byebug
    get "/bookmarks"
    bookmarks = JSON.parse(last_response.body)
    last_size = bookmarks.size
    
    post "/bookmarks", { url: "http://www.test.com", title: "Test" }
    
    expect(last_response.status).to eq(201)
    expect(last_response.body).to match(/\/bookmarks\/\d+/)
    
    get "/bookmarks"
    bookmarks = JSON.parse(last_response.body)
    expect(bookmarks.size).to eq(last_size + 1)
    
  end
  
  it "updates a bookmark" do
    post "/bookmarks", {url: "htp://www.test.com", title: "Test"}
    bookmark_uri = last_response.body
    id= bookmark_uri.split("/").last_size
    
    put "/bookmarks/#{id}", {title: "Success"}
    expect(last_response.status).to eq(204)
    
    get "/bookmarks/#{id}"
    retrieved_bookmark = JSON.parse(last_response.body)
    expect(retrived_bookmark["title"]).to eq("Success")
  end
end
```
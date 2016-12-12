---
layout: post
title: "Elasticsearch with rails"
date: 2016-02-17 15:30:25 +0800
comments: true
categories: 
---
To implement more complicate or advanced search engine in rails app, `elasticsearch` is a very great open source search and analysis tool with full function. 

<h2>Set up Elasticsearch with rails</h2>

To set up elasticsearch, firstly we need to add this two gems in `Gemfile`.

```ruby
gem 'elasticsearch-model'
gem 'elasticsearch-rails'
```

There are more options in the wild, for instance, `tire` is another choice, also you can find tutorial for `tire` in Rails Cast.

<!-- more -->

<h2>Install Elastic on Mac</h2>

Here we use homwbrew to install elastic on Mac, you can find more ways to get the same purpose, also on Linux and windows.

Before the installation, make sure your java version is 1.7 or above.

Type below commands in terminal to start installation process.

```
brew update
brew install Caskroom/cask/java
...
brew install elasticsearch
...
elasticsearch --config=/usr/local/opt/elasticsearch/config/elasticsearch.yml
```

To test if your installation is good, run this command and you should see it returns a hash-like data.

```
curl -X GET http://localhost:9200/
```

<h2>Add elasticsearch service in CircleCI</h2>

In `circle.yml` file, add elasticsearch service.

```
machine:
  ruby:
    version: 2.1.7
  services:
    - elasticsearch
deployment:

...
```

<h2>Add Elasticsearch on Heroku add-on</h2>

you can either use Heroku's UI to add add-on or just simple use this command.

```
heroku addons:create searchbox
```

<h2>Add initializer in app</h2>

The best way to set up the Elasticsearch client is to create an initialize.

in `config/initializers/elasticsearch.rb`

```
Elasticsearch::Model.client =
  if Rails.env.staging? || Rails.env.production?
    Elasticsearch::Client.new url: ENV['SEARCHBOX_URL']
  elsif Rails.env.development?
    Elasticsearch::Client.new log: true
  else
    Elasticsearch::Client.new
  end
```

<h2>Set up model to connect with Elasticsearch</h2>

add this four lines in your model to establish the connection between your model and elasticsearch server.

```
#This will extend the model with functionality related to Elasticsearch.
include Elasticsearch::Model 

include Elasticsearch::Model::Serializing

#automatically update the index whenever the record changes
include Elasticsearch::Model::Callbacks

#thsi will specify different index name for each environment
index_name ['YOURAPPNAME', Rails.env].join('_')
```

By adding this, now we got `__elasticsearch__` proxy to communicate with elasticsearch server.

For instance, now we can try those commands in the `rails console`

```
Video.__elasticsearch__.import
#this will synchronize the records form our database with elasticsearch server

Video.__sleaticsearch__.search("Inception")
#this will give back records that match the search term, but you will find it hard to read

Video.__sleaticsearch__.search("Inception").records.to_a
#this will also give back results that match the term, but with the form we more familiar with
```

<h2>Overwrite the json format</h2>

By default, if we run `Video.import`, this will send the full information of the objects to elasticsearch server. However, this will cause the speed of searching lower and we only want to send datas to elasticsearch that we want search on, for this purpose, we can specify the columns we want to send to elasticsearch by overwrite the `as_indexed_json` method.

```
def as_indexed_json(options={})
  as_json(only: [:title, :description])
  #as_json is a rails method that will turn Model object to json format so that this String object will be able to send to the wild
end
``` 

Now if we reimport the datas, it will only send title and description of each object to elasticsearch.

<h2>Overwrite search method</h2>

In most of the case, we will want to redesign the search method, for instance, add filter to filter the poblish date of the films or age of users, define the search field and so on. to do so, here is the simple example for how to overwrite the search method in your class.

```
def search(query, options={}) #options is to accept some of the parameter for specific usage.
  search_definition= {
    query: {
      multi_match: {
        query: query,
        fields: {:title^100, :description^50},
        operator: 'or'
      }
    }
  }

  if query.present? && options[:review].present?
    search_definition[query][multi_match][fields] << "reviews.body"
  end

  __elasticsearch__.search(search_definition)

end
```






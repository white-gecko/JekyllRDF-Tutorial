# Jekyll-RDF Tutorial

## Preparation

- Install [Ruby](https://www.ruby-lang.org/) >= 2.1
- Install [Bundler](https://bundler.io/)

## 1. Add Your Data

In the first step you select the dataset you want to publish. Put it into the `_data` directory.

    $ mkdir _data
    $ cp your_dataset.ttl _data/graph.ttl

## 2. Install Jekyll and Jekyll-RDF

Now we are going to install Jekyll-RDF into the current directory using Bundler. Jekyll-RDF will bring Jekyll with it, as a dependency.

    $ cat Gemfile
    source "https://rubygems.org"

    group :jekyll_plugins do
        gem "jekyll-rdf", ">= 3.0.0.a"
    end

    $ bundle install --path .vendor/bundle

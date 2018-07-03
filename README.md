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

## 3. Configure Jekyll and Jekyll-RDF

The configuration for Jekyll and Jekyll-RDF is happening in the `_config.yml` file. You can find more information about possible Jekyll configration parameters in the [Jekyll Documentation](https://jekyllrb.com/docs/configuration/). More information about the Jekyll-RDF configuration in the [Jekyll-RDF README](https://github.com/white-gecko/jekyll-rdf/blob/develop/README.md#configuration).

In lines 1 and 2 we configure the base URL (combined of hostname, protocol, and path) for Jekyll. This URL is also used by Jekyll-RDF to define the base for the rendering of the resource pages.

In lines 4 and 5 we tell Jekyll to activate the Jekyll-RDF plugin, which we have installed in the previous step (*2. Install Jekyll and Jekyll-RDF*).

Starting from line 8 we define the configuration parameters specific to Jekyll-RDF.
In line 9 we specify the path to our RDF graph file using the `path` key.
In line 10 we define which template should be used per default to render a resource using the `default_template` key.

    $ cat -n _config.yml
         1	baseurl: "" # the subpath of your site, e.g. /blog
         2	url: "http://example.org" # the base hostname & protocol for your site, e.g. http://example.com
         3
         4	plugins:
         5	   - jekyll-rdf
         6
         7	# Jekyll RDF settings
         8	jekyll_rdf:
         9	    path: "_data/graph.ttl"
        10	    default_template: "default"

## 4. Create a Jekyll-RDF Template

In the last step we have configured Jekyll RDF to use a default template but we did not create any template so far.
To create a new template we have to create a new file in the `_layouts` directory.
In our example we call this file `default.html` to match the previous configuration.
Below you can find our very simple initial template.

In the lines 1 and 2 we create the minimal YAML [front matter](https://jekyllrb.com/docs/frontmatter/) as it is required for Jekyll.
In line 4 we define some text to print on the resulting pages that consists of the string `Hello` followed the liquid syntax to print the value of a variable `{{ … }}`.
Here we return the variable `page.rdf` which will result in the IRI of the currently rendered RDF resource.

    $ cat -n _layouts/default.html
         1	---
         2	---
         3
         4	Hello {{ page.rdf }}

## 5. Build the Page and Check our Result

Now we are set to create our Jekyll site with one page for each RDF resource.
To build the page we only need to run `bundle exec jekyll serve`. (You can find more build option in [the Jekyll documentation](https://jekyllrb.com/docs/usage/).)
Now Jekyll builds the pages an starts a minimal web server which is available under [`http://localhost:4000`](http://localhost:4000).
If you open this address in you browser you can see pages for each of the RDF resources as shown below.

    Index of /

                   Name                          Last modified              Size
    Parent Directory                   2018/07/03 16:20                   -
    Abraham.html                       2018/07/03 16:34                   33
    Bart.html                          2018/07/03 16:34                   30
    Herb.html                          2018/07/03 16:34                   30
    Homer.html                         2018/07/03 16:34                   31
    Lisa.html                          2018/07/03 16:34                   30
    Maggie.html                        2018/07/03 16:34                   32
    Marge.html                         2018/07/03 16:34                   31
    Moe.html                           2018/07/03 16:34                   29
    Mona.html                          2018/07/03 16:34                   30
    Patty.html                         2018/07/03 16:34                   31
    README.md                          2018/07/03 16:33                   5279
    Selma.html                         2018/07/03 16:34                   31
    TheSimpsons.html                   2018/07/03 16:34                   37
    rdfsites/                          2018/07/03 16:20                   -

    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

    WEBrick/1.3.1 (Ruby/2.3.3/2016-11-21)
    at localhost:4000

If you open one of the pages you can see the evaluated template as we have defined it in the previous step (*4. Create a Jekyll-RDF Template*).

    Hello http://example.org/Bart

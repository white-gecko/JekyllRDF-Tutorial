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

## 6. Specify Templates for Classes and Display RDF Properties

So far we only display the IRI of an RDF Resource but we don't actually use the data encoded in the RDF Graph.
We will now build a template which will only apply for the RDF Class `Family` in our graph.
To tell Jekyll RDF which template to use for this class we create a `class_template_mappings` section in our `_config.yml` as follows:

    …
    jekyll_rdf:
        …
        class_template_mappings:
            "http://www.ifi.uio.no/INF3580/family#Family": "family"

Now we also need to define the layout accordingly. We create a new file called `family.html` in the `_layouts` directory with the content as shown below.
In the lines 1 and 2 we again see the minimal YAML front matter.
In line 4 we define a heading which prints the variable `page.rdf` which will result in the IRI of the currently rendered RDF Resource.
In line 8 we execute the `rdf_property` filter on the page's RDF Model (`page.rdf`). As first parameter we set the IRI of the property which we want to retrieve in angle brackets. The second parameter, which is the language parameter is omitted. The third parameter is set to `true`, which tells the filter to return a list of results rather then a single result.
By using the `assign`-tag we assign the result of the filter to a new variable called `members`.
Notice that we have embraced this line with curly braces and percent signs, because the result is not to be shown on the page. (Learn more about the liquid syntax for objects, tags, and filters in the [Liquid documentation](https://shopify.github.io/liquid/basics/introduction/).)
The lines 9 to 11 are a loop for iterating over the entries in the list `members`, the individual entries are assigned to the variable `member`.
In line 10 we do the output. For the HTML-Link-Tag we specify the hyper link to point to `{{ member.page_url }}` which will bring us to the HTML Page for the respective RDF Resource.
Within the HTML-Link-Tag we again apply the `rdf_property` filter for the `foaf:name` of the respective RDF Resource. This time we directly return the result as output to the page.

    $ cat -n family.html
        1	---
        2	---
        3
        4	<h1>{{ page.rdf }}</h1>
        5
        6	<h2>Family Members</h2>
        7	<ul>
        8	    {% assign members = page.rdf | rdf_property: "<http://www.ifi.uio.no/INF3580/family#hasFamilyMember>", nil, true %}
        9	    {% for member in members %}
        10	    <li><a href="{{ member.page_url }}">{{ member | rdf_property: "<http://xmlns.com/foaf/0.1/name>" }}</a></li>
        11	    {% endfor %}
        12	</ul>

If you open the page `TheSimpsons.html` you can see the evaluated template.

    http://example.org/TheSimpsons

    Family Members

      • Lisa Simpson
      • Maggie Simpson
      • Bart Simpson
      • Homer Simpson
      • Marge Simpson

## 7. Define Prefixes and Execute SPARQL Queries

Now we define another layout to be applied for the individual persons in our knowledge base.
Similar to the previous step (*6. Specify Templates for Classes and Display RDF Properties*), we add a new line to our `_config.yml` to specify the RDF Class IRI followed by the layouts name.

    …
    jekyll_rdf:
        …
        class_template_mappings:
            "http://www.ifi.uio.no/INF3580/family#Family": "family"
            "http://xmlns.com/foaf/0.1/Person": "person"

The next thing to do is to create the according file `person.html` in the `_layouts` directory. This time we are also introducing the `query` filter and the possibility to use prefix definitions.
In the lines 1 to 3 we see the YAML front matter. This time it is not empty, instead we define the location of a file which is holding a prefix definition. You can find this file below.
In line 5 we again apply the `rdf_property` filter. This time we can use the prefixed-form `foaf:name` instead of the full IRI `<http://xmlns.com/foaf/0.1/name>`.
In line 6 we just output the IRI of the curent RDF Resource and in line 10 we print the value of the `foaf:age` property.
From line 15 to 23 we use the `capture`-tag to define a new variabel `siblings_query` and the enclosed lines (line 16 to 22) are assigned as the value of the variable.
The lines 16 to 22 are a standard SPARQL query with a projection (`SELECT`-part) and a selection (`WHERE`-part) with two triple patterns and a `UNION`.
The only thing special is, that the variable `?resourceUri` will be bound during the execution.
In line 24 we apply the `sparql_query` filter on the page's RDF Model (`page.rdf`) and provide the variable `siblings_query` as first argument.
This filter executes the query on the RDF Graph while it binds `?resourceUri` to the input resource, which is `page.rdf` in our case.
As a result of the execution the filter returns the a result-set, which we assign to the variable `siblings` using the `assign`-tag.
The lines 25 to 27 are a loop for iterating over the result-set, the individual result-rows are assigned to the variable `row`.
In line 26 we do the output. The value of the SPARQL variable `?sibling` is now available under `row.sibling`.
For the HTML-Link-Tag we specify the hyper link to point to `{{ row.sibling.page_url }}` which will bring us to the HTML Page for the respective RDF Resource.
Within the HTML-Link-Tag we again apply the `rdf_property` filter for the `foaf:name` of the respective RDF Resource.

    $ cat -n _layouts/person.html
         1	---
         2	rdf_prefix_path: "_data/prefixes.sparql"
         3	---
         4
         5	<h1>{{ page.rdf | rdf_property: "foaf:name" }}</h1>
         6	{{ page.rdf }}
         7
         8	<dl>
         9	    <dt>Age:</dt>
        10	    <dd>{{ page.rdf | rdf_property: "foaf:age" }}</dd>
        11	</dl>
        12
        13	<h2>Siblings</h2>
        14	<ul>
        15	    {% capture siblings_query %}
        16	    SELECT ?sibling WHERE {
        17	        {
        18	            ?resourceUri fam:hasBrother ?sibling
        19	        } UNION {
        20	            ?resourceUri fam:hasSister ?sibling
        21	        }
        22	    }
        23	    {% endcapture %}
        24	    {% assign siblings = page.rdf | sparql_query: siblings_query %}
        25	    {% for row in siblings %}
        26	    <li><a href="{{ row.sibling.page_url }}">{{ row.sibling | rdf_property: "foaf:name" }}</a></li>
        27	    {% endfor %}
        28	</ul>

Prefixes can be defined using the according SPARQL syntax. The prefix mappings can be places in a file which is then included into a layout using the `rdf_prefix_path` key in the YAML [front matter](https://jekyllrb.com/docs/frontmatter/). Once defined, the prefixes can be used in all filters provided by Jekyll-RDF.

    $ cat _data/prefixes.sparql
    PREFIX foaf: <http://xmlns.com/foaf/0.1/>
    PREFIX fam: <http://www.ifi.uio.no/INF3580/family#>


If you open the page `Lisa.html` you can see the evaluated template.

    Lisa Simpson

    http://example.org/Lisa

    Age:
        8

    Siblings

      • Bart Simpson
      • Maggie Simpson

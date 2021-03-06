SphinxsearchBundle0.9
=====================

Forked from maninhat bundle. Embedded 0.9.9 SphinxApi instead of 2.0.1.

Installation:
-------------

1. Download the bundle
2. Configure the bundle

### Step 1: Download the bundle

git clone https://github.com/remilemonnier/SphinxsearchBundle0.9.git

### Step 2: Configure the bundle

``` yaml
sphinxsearch:
    indexes:
        - name: %sphinxsearch_index_categories%
          index:
              - %sphinxsearch_index_categories%
          field_weights:
              title: 10
              description: 5 
        - name: %sphinxsearch_index_items%
          index:
              - %sphinxsearch_index_items%
          field_weights:
              name: 10
              description: 5 
    searchd:
        host:   %sphinxsearch_host%
        port:   %sphinxsearch_port%        
```

At least one index must be defined, and you may define as many as you like.

In the above sample configuration, `Categories` is used as a label for the index named `%sphinxsearch_index_categories%` (as defined in your `sphinxsearch.conf`).  This allows you to avoid having to hard code raw index names inside of your code.



Usage examples:
---------------

The most basic search, using the above configuration as an example, would be:

``` php
$indexesToSearch = array(
  'Items' => array(),
  'Categories' => array(),
);
$sphinxSearch = $this->get('search.sphinxsearch.search');
$searchResults = $sphinxSearch->search('search query', $indexesToSearch);
```

This performs a search for `search query` against the indexes labeled `Items` and `Categories`.  The results of the search would be stored in `$searchResults['Items']` and `$searchResults['Categories']`.

You can also perform more advanced searches, such as:

``` php
$indexesToSearch = array(
  'items' => array(
                    'result_offset' => 0,
                    'result_limit' => 25  //Get only the 25 firts results
				    'field_weights' => array(
				      'name' => 2,
				      'description' => 3,)
    ) 
);
$sphinxSearch = $this->get('search.sphinxsearch.search');
$sphinxSearch->setFilter('isActive', array(1)); // Get only active products
$sphinxSearch->SetSortMode ( SPH_SORT_ATTR_DESC, "date" ); // order by date DESC
$sphinxSearch->SetFilterRange ('price', 0, 10, true); //Items which have price under 10 are exclude

$searchResults = $sphinxSearch->search($text, $indexesToSearch);

```

This would again search `Items` and `Categories` for `search query`, but now `Items` will return up to the first 25 matches and weight the `Name` and `SKU` fields higher than normal, and `Categories` will return up to the first 10.  Note that in order to define a `result_offset` or a `result_limit`, you must explicitly define both values.  Also, this search will use [the Extended query syntax](http://sphinxsearch.com/docs/current.html#extended-syntax), and exclude all results with a `disabled` attribute set to 1.



License:
--------

```
Copyright (c) 2012, Ryan Rogers
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met: 

1. Redistributions of source code must retain the above copyright notice, this
   list of conditions and the following disclaimer. 
2. Redistributions in binary form must reproduce the above copyright notice,
   this list of conditions and the following disclaimer in the documentation
   and/or other materials provided with the distribution. 

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR
ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
(INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND
ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
(INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
```
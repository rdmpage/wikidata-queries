# Wikidata queries

Examples of Wikidata queries relevant to stuff I work on.


## Source(s) of a taxon name in Wikidata

Data sources for a taxonomic name

```
PREFIX prov: <http://www.w3.org/ns/prov#>
PREFIX pr: <http://www.wikidata.org/prop/reference/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX wd: <http://www.wikidata.org/entity/>

SELECT *
WHERE
{
  ?wikidata wdt:P225 "Mammalia" .
  ?wikidata p:P225 ?statement. 
  ?statement prov:wasDerivedFrom ?provenance .
  ?provenance pr:P248 ?reference .
  ?reference wdt:P1476 ?title .
}
```

[Try it](http://tinyurl.com/n68edwv)


## Find author of taxon name

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>

SELECT *
WHERE
{
  ?wikidata wdt:P225 "Zodariidae" .
  ?wikidata p:P225 ?statement .
  ?statement pq:P405 ?authority .
  ?authority rdfs:label ?name .
  
  FILTER (lang(?name) = 'en')
}
```

[Try it](http://tinyurl.com/lur5xtf)

## Find taxa named by author (inverse of previous query)

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>

SELECT *
WHERE
{
  ?statement pq:P405 wd:Q1346792 .
  ?taxon p:P225 ?statement .
  ?taxon wdt:P225 ?taxon_name .  
}
```

[Try it](http://tinyurl.com/yca8qke5)

In this example [wd:Q1346792](https://www.wikidata.org/wiki/Q1346792) is Tamerlan Thorell.

## Find date of publication

Date of publication of name

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX p: <http://www.wikidata.org/prop/>

SELECT *
WHERE
{
  ?wikidata wdt:P225 "Liphistiidae" .
  ?wikidata p:P225 ?statement .
  ?statement pq:P574 ?date .
}
```

[Try it](http://tinyurl.com/kclxpzv)


## Find work based on DOI

```
SELECT *
WHERE
{
  ?work wdt:P356 "10.2476/ASJAA.62.33" .
}
```
[Try it](http://tinyurl.com/k9qs282)

If DOI not found can add it using http://tools.wmflabs.org/sourcemd

## Find work based on DOI (case insensitive) SLOW

Wikidata has DOIs in UPPERCASE, CrossRef recommends lowercase, can use filter to have case 
insensitive query (slow)

```
SELECT *
WHERE
{
  ?work wdt:P356 ?doi .
  FILTER (lcase(str(?doi)) = "10.2476/asjaa.62.33")
}
```

[Try it](http://tinyurl.com/jwgefzd)

## Find Wikidata item for Wikispecies author

Wikispecies authors are on Wikidata, so we can get Wikidata items using the Wikispecies URL
with author name URL encoded (instead of underscores "_" use url encoded spaces %20).

```
SELECT *
WHERE
{
    VALUES ?article {<https://species.wikimedia.org/wiki/Bing%20Zhou>}
	?article schema:about ?author .
    ?author wdt:P31 wd:Q5 .
}
```
[Try it](http://tinyurl.com/meq6ky6)






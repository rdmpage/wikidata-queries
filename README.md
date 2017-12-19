# Wikidata queries

Examples of Wikidata queries relevant to stuff I work on. Includes discussion of how to add data, and federated queries.


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

## Find journal from ISSN

```
SELECT *
WHERE
{
  ?journal wdt:P236 "0074-0276" .
}
```
[Try it](http://tinyurl.com/yb4ufys6)

## Find Wikidata item for Wikispecies author

Wikispecies authors are on Wikidata, so we can get Wikidata items using the Wikispecies URL.
~~with author name URL encoded (instead of underscores "_" use url encoded spaces %20~~).

```
SELECT *
WHERE
{
    VALUES ?article {<https://species.wikimedia.org/wiki/Bing_Zhou>}
	  ?article schema:about ?author .
    ?author wdt:P31 wd:Q5 .
}
```
[Try it]~~(http://tinyurl.com/meq6ky6)~~(http://tinyurl.com/yazywu3y)

## Find an author based on name

```
SELECT ?_human ?name  WHERE { 
  VALUES ?family {"Agassiz"@en }
  VALUES ?given {"Alexander"@en }

  ?_human wdt:P31 wd:Q5.
  ?_human rdfs:label ?name .
                
  ?_human wdt:P734 ?familyName .
  ?familyName rdfs:label ?family . 
                
  ?_human wdt:P735 ?givenName .
  ?givenName rdfs:label ?given . 
  
   FILTER (lang(?name) = 'en')
}
```
[Try it](http://tinyurl.com/y78h344k)

## Find an author based on name and birth and death dates

```
SELECT ?_human ?name  WHERE { 
  
  VALUES ?family {"Agassiz"@en }
  VALUES ?given {"Alexander"@en }
  VALUES ?birth { 1835 }
  VALUES ?death { 1910 }
  
  ?_human wdt:P31 wd:Q5.
  ?_human rdfs:label ?name .
                
  ?_human wdt:P734 ?familyName .
  ?familyName rdfs:label ?family . 
                
  ?_human wdt:P735 ?givenName .
  ?givenName rdfs:label ?given . 
  
  ?_human wdt:P569 ?birth_date .
  ?_human wdt:P570 ?death_date .
  
  FILTER(year(?birth_date) = ?birth) .
  FILTER(year(?death_date) = ?death) .
  FILTER (lang(?name) = 'en')

}
```

[Try it](http://tinyurl.com/ycbyu7v3)


## Generate list and map of museums and their collection codes

Based on the new [Biodiversity Repository ID](https://www.wikidata.org/wiki/Property:P4090) property we could list all museums and herbaria with codes and put them on a map. Only two examples at time of writing:

```
SELECT *  WHERE { 
  ?museum wdt:P4090 ?code .
  ?museum rdfs:label ?name .
  OPTIONAL {
  ?museum wdt:P625 ?coord .
  }
                
  FILTER (lang(?name) = 'en')
}
```

[Try it](http://tinyurl.com/ybwb5oa2)


## Adding data

To add a publication based on its DOI go to http://tools.wmflabs.org/sourcemd and paste in the DOI. If the DOI does not exist in Wikidata, you should redirected to the [QuickStatements](https://tools.wmflabs.org/wikidata-todo/quick_statements.php) tool and see something like this:

```
CREATE
LAST	P356	"10.1016/J.SAJB.2012.07.014"
LAST	P31	Q13442814
LAST	P1476	en:"Species limits in Vachellia (Acacia) karroo (Mimosoideae: Leguminoseae): Evidence from automated ISSR DNA “fingerprinting”"
LAST	Len	"Species limits in Vachellia (Acacia) karroo (Mimosoideae: Leguminoseae): Evidence from automated ISSR DNA “fingerprinting”"
LAST	P304	"36-43"
LAST	P478	"83"
LAST	P577	+2012-11-00T00:00:00Z/10
LAST	P2093	"C.L. Taylor"	P1545	"1"
LAST	P2093	"N.P. Barker"	P1545	"2"
```

These are triples describing the article. “LAST” links every triple to the Wikidata identifier created for the article.

Below you will see the text:

You need to authorize WiDaR to edit Wikidata on you behalf for this tool to work!

https://tools.wmflabs.org/widar/index.php?action=authorize

Authorise WiDaR and then add the statments to Wikidata (click on "Do it"). You should see the results of your editing below:

```
Processing Q32062483 (Q32062483 P356 "10.1016/J.SAJB.2012.07.014")
Processing Q32062483 (Q32062483 P31 Q13442814)
Processing Q32062483 (Q32062483 P1476 en:"Species limits in Vachellia (Acacia) karroo (Mimosoideae: Leguminoseae): Evidence from automated ISSR DNA “fingerprinting”")
Processing Q32062483 (Q32062483 Len "Species limits in Vachellia (Acacia) karroo (Mimosoideae: Leguminoseae): Evidence from automated ISSR DNA “fingerprinting”")
Processing Q32062483 (Q32062483 P304 "36-43")
Processing Q32062483 (Q32062483 P478 "83")
Processing Q32062483 (Q32062483 P577 +2012-11-00T00:00:00Z/10)
Processing Q32062483 (Q32062483 P2093 "C.L. Taylor" P1545 "1")
Processing Q32062483 (Q32062483 P2093 "N.P. Barker" P1545 "2")
All done!.
```

## Add repository code (Institution code) to Wikidata

Go to [QuickStatements](https://tools.wmflabs.org/wikidata-todo/quick_statements.php), make sure you have
authorised WiDar, and enter statements. This example adds "NHMUK" to the entry for the Natural History Museum, London.

```
Q309388	P4090	"NHMUK"
```


### List articles published in a journal

In this example we list all the articles published in *Zootaxa* (ISSN 1175-5326).
```
SELECT * WHERE {
  ?journal wdt:P236 "1175-5326".
  ?work wdt:P1433 ?journal.
  ?work wdt:P1476 ?title.
  ?work wdt:P478 ?volume.
  ?work wdt:P577 ?date .
  OPTIONAL { ?work wdt:P356 ?doi. }
} ORDER BY (year(?date))
```
[Try it](http://tinyurl.com/y9536hy6)

### Federated SPARQL

This query calls two different SPARQL endpoints, [Uniprot](http://sparql.uniprot.org/sparql) and [Wikidata](https://query.wikidata.org). I gather Wikidata limits the possible external SPARQL series it can call, so this query needs to be run on another SPARQL endpoint. This query works on Apache Jena Fuseki Version 3.4.0 (running as a container [stain/jena-fuseki](https://hub.docker.com/r/stain/jena-fuseki/) ).

```
PREFIX up:<http://purl.uniprot.org/core/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX wd: <http://www.wikidata.org/entity/>	
SELECT *
WHERE
{
  # Find taxon in NCBI taxonomy called "Begonia"
  # Convert IRI to string
  SERVICE <http://sparql.uniprot.org/sparql> 
  {
    ?taxon up:scientificName "Begonia" .
    BIND( REPLACE( STR(?taxon),"http://purl.uniprot.org/taxonomy/","" ) AS ?ncbi). 
  }
  
  # Find Wikidata entity for same taxon using NCBI tax_id
  SERVICE <https://query.wikidata.org/sparql> 
  {
    ?wikidata wdt:P685 ?ncbi .
    ?wikidata wdt:P225 ?name .
  }	  
}	
```

Result:

taxon | ncbi | wikidata | name
      --- | --- | --- | --- 
<http://purl.uniprot.org/taxonomy/3681> | "3681" | wd:Q158617 | "Begonia"

For more examples and background see [Integrating Wikidata and other linked data sources – Federated SPARQL queries](http://sulab.org/2017/07/integrating-wikidata-and-other-linked-data-sources-federated-sparql-queries/)

Can also run slightly modified version on the Uniprot SPARQL endpoint: 

```
PREFIX up:<http://purl.uniprot.org/core/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX wd: <http://www.wikidata.org/entity/>	
SELECT *
WHERE
{
  # Find taxon in NCBI taxonomy called "Begonia"
  # Convert IRI to string
  
  ?taxon up:scientificName "Begonia" .
  BIND( REPLACE( STR(?taxon),"http://purl.uniprot.org/taxonomy/","" ) AS ?ncbi). 

  # Find Wikidata entity for same taxon using NCBI tax_id
  SERVICE <https://query.wikidata.org/sparql> 
  {
    ?wikidata wdt:P685 ?ncbi .
    ?wikidata wdt:P225 ?name .
  }	  
}	
```
[Try it](http://tinyurl.com/ybxzk9nm)


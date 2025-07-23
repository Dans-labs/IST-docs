# running skosmos + fuseki via docker compose

* git repo: https://github.com/NatLibFi/Skosmos/
* documentation: https://github.com/NatLibFi/Skosmos/wiki

## installation with docker

https://github.com/NatLibFi/Skosmos/wiki/Install-Skosmos-with-Fuseki-in-Docker

**3 containers setup: Skosmos, Fuseki, Varnish HTTP cache, using docker-compose to bind them together**

follow: https://github.com/NatLibFi/Skosmos/blob/main/dockerfiles/README.md#running-with-docker-compose

Note: DANS Skosmos (https://vocabs.datastations.nl/) runs v2.18.1  (tag: v2.18.1) https://github.com/NatLibFi/Skosmos/tree/v2.18.1/dockerfiles

Docker compose: ./docker-compose.yml
* uncomment unecessary volumes

Docker containers:
* skosmos: http://localhost:9090
* fuseki: http://localhost:9030




## add vocabulary to Skosmos 

In addition to loading a vocabulary to the triple-store(Fuseki), it is necessary to:
* incude a vocabulary-specific configuration in skosmos general config file: `config.ttl` 
    * config fields: https://github.com/NatLibFi/Skosmos/wiki/Configuration#vocabulary-specific-configuration
    * how to: https://github.com/NatLibFi/Skosmos/wiki/InstallTutorial#configure-skosmos


### load RDF/TTL/JSONLD to Fuseki

The curl command in [dockerfiles/README.md](https://github.com/NatLibFi/Skosmos/blob/main/dockerfiles/README.md#running-with-docker-compose) fails with curl >=v8.5.0. Change it to`curl -X POST -H "Content-Type: text/turtle" -T unescothes.ttl "http://localhost:9030/skosmos/data?graph=http%3A%2F%2Fskos.um.es%2Funescothes%2F"`

    No -I or -G.
    The query parameter is URL-encoded and put directly in the URL.
    -T unescothes.ttl uploads the file as the request body.
    -H "Content-Type: text/turtle" sets the content type.

`  
query voc in fuseki:`curl   "http://localhost:9030/skosmos/data?graph=http%3A%2F%2Fskos.um.es%2Funescothes%2F"`


# Index another skos vocabulary

chose one of the [DCAT-AP vocabularies](https://op.europa.eu/en/web/eu-vocabularies/dcat-ap-op)

# Indexing AATC
due to the large size the PHP memory needs to be increased (1Gb will) for Skosmos to display the terms from this vocabulary. 




**load aatc config:**

config added to [dockerfiles/config/config-docker-compose.ttl](dockerfiles/config/config-docker-compose.ttl)
* changed `void:sparqlEndpoint <http://localhost:3030/skosmos/sparql> ;` to `void:sparqlEndpoint <http://fuseki-cache:80/skosmos/sparql> ;` so it would work with docker

**load aatc data:** using only subset, due to large size

`curl -X POST -H "Content-Type: text/turtle" -T /home/andre/Documents/Projects/dans-core-systems/provisioning/files/vocabs/aatc.ttl "http://localhost:9030/skosmos/data?graph=https%3A%2F%2Fvocabularies.dans.knaw.nl%2Faatconcepts%2F"`

https://vocabularies.dans.knaw.nl/aatconcepts/




## query skosmos

`curl -X GET --header 'Accept: application/json' 'http://localhost:9090/rest/v1/vocabularies?lang=en'`

`curl "http://localhost:9090/rest/v1/search?vocab=stw&query=a*"`

```json
{
  "@context": {
    "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
    "onki": "http://schema.onki.fi/onki#",
    "title": {
      "@id": "rdfs:label",
      "@language": "en"
    },
    "vocabularies": "onki:hasVocabulary",
    "id": "onki:vocabularyIdentifier",
    "uri": "@id",
    "@base": "http://localhost:9090/rest/v1/vocabularies"
  },
  "uri": "",
  "vocabularies": [
    {
      "uri": "stw",
      "id": "stw",
      "title": "STW Thesaurus for Economics"
    },
    {
      "uri": "unesco",
      "id": "unesco",
      "title": "UNESCO Thesaurus"
    }
  ]
}
```

`curl -X GET --header 'Accept: application/json' 'http://localhost:9090/rest/v1/AATC/?lang=en'`

```json
{
  "@context": {
    "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
    "skos": "http://www.w3.org/2004/02/skos/core#",
    "onki": "http://schema.onki.fi/onki#",
    "dct": "http://purl.org/dc/terms/",
    "uri": "@id",
    "type": "@type",
    "conceptschemes": "onki:hasConceptScheme",
    "id": "onki:vocabularyIdentifier",
    "defaultLanguage": "onki:defaultLanguage",
    "languages": "onki:language",
    "label": "rdfs:label",
    "prefLabel": "skos:prefLabel",
    "title": "dct:title",
    "@language": "en",
    "@base": "http://localhost:9090/rest/v1/AATC/"
  },
  "uri": "",
  "id": "AATC",
  "marcSource": false,
  "title": "The Art and Architecture Thesaurus Concepts",
  "defaultLanguage": "en",
  "languages": [
    "en",
    "nl"
  ],
  "conceptschemes": [
    {
      "label": "The Art and Architecture Thesaurus Concepts",
      "title": "The Art and Architecture Thesaurus Concepts",
      "uri": "http://vocabularies.dans.knaw.nl/aatconcepts",
      "type": "skos:ConceptScheme"
    }
  ]
}

## query fuseki

General graph 
```
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
SELECT ?cs 
WHERE {
  ?cs a skos:ConceptScheme .
} 
```

`curl http://localhost:9030/skosmos/sparql?query=PREFIX+skos%3A+%3Chttp%3A%2F%2Fwww.w3.org%2F2004%2F02%2Fskos%2Fcore%23%3E%0ASELECT+%3Fcs+%0AWHERE+%7B%0A++%3Fcs+a+skos%3AConceptScheme+.%0A%7D+%0A`



https://vocabularies.dans.knaw.nl/aatconcepts/ graph

```
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
SELECT ?cs 
FROM <https://vocabularies.dans.knaw.nl/aatconcepts/>
WHERE {
  ?cs a skos:ConceptScheme .
} 
```

`curl http://localhost:9030/skosmos/sparql?query=PREFIX%20skos%3A%20%3Chttp%3A%2F%2Fwww.w3.org%2F2004%2F02%2Fskos%2Fcore%23%3E%0ASELECT%20%3Fcs%20%0AFROM%20%3Chttps%3A%2F%2Fvocabularies.dans.knaw.nl%2Faatconcepts%2F%3E%0AWHERE%20%7B%0A%20%20%3Fcs%20a%20skos%3AConceptScheme%20.%0A%7D%20`


http://skos.um.es/unescothes/ graph

```
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
SELECT ?cs 
FROM <http://skos.um.es/unescothes/>
WHERE {
  ?cs a skos:ConceptScheme .
}
LIMIT 1
``` 

`curl http://localhost:9030/skosmos/sparql?query=PREFIX+skos%3A+%3Chttp%3A%2F%2Fwww.w3.org%2F2004%2F02%2Fskos%2Fcore%23%3E%0ASELECT+%3Fcs+%0AFROM+%3Chttp%3A%2F%2Fskos.um.es%2Funescothes%2F%3E%0AWHERE+%7B%0A++%3Fcs+a+skos%3AConceptScheme+.%0A%7D+%0A`


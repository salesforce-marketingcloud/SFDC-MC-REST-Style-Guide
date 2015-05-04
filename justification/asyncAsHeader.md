#Async as header
Options for requesting a request be treaded async are limited. Exploring there where four identified options. The least evil choice for use cases was choosen.

## Within the identifier/service
This creates a duplicate resource pattern which complicates switching and can be confusing consumers of the API when "/async" pattern returns different formats.

## Query string
Is inconsitent with definition of querystring, [see glossary (glossary)]. This confuses the purpose and meaning of querystring values.

## Header
Causes preflight in user agent. 

## Body/envolope
Makes service description very difficult since the request success behavior varies upon body payload 

Problematic for request routing as full request needs to be parsed to route instead of just HTTP headers.


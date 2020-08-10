# Conversie naar Linked Open Data van de beeldbank Waterlands Archief

Deze repo bevat een proef-conversie van de beeldbank van het Waterlands Archief en een beschrijving van hoe deze tot stand is gekomen.

## Export uit MAIS-Flexis
Er zijn twee manieren om data uit te voeren uit MAIS-Flexis:
* een _uitvoer_ gemaakt met een uitvoerstrategie
Deze uitvoer kun je zelf beinvloeden. Er is een standaard uitvoerstrategie voor opname in MDWS (voor publicatie op archieven.nl en/of op de eigen website). Een _uitvoer_-bestand is een text bestand.
* een _export_
Deze mogelijkheid is een rechtstreekse weergave van de data zoals deze in MAIS-Flexis zit. Het is een XML-bestand, maar is niet helemaal _well-formed_ zodat er een paar kleine aanpassingen moeten worden doorgevoerd voordat deze geautomatiseerd kan worden verwerkt.

Voor de conversie van de Beeldbank van het Waterlands Archief heb ik gebruik gemaakt van een XML-export, aangeleverd door het Waterlands Archief. De XML-export heeft als voordeel dat de data compleet is; bij een _uitvoer_ wordt in de standaard _uitvoerstrategie_ een selectie gemaakt en ik weet zonder nadere bestudering van uitvoerstrategie niet welke velden er dan wel en niet zijn opgenomen. Het maken van een _export_ kost bovendien veel minder rekentijd: bij het maken van een _uitvoer_ moeten allerlei combinaties ('joins') in de database worden gelegd.

Dat laatste is ook het voordeel van een _uitvoer_: je kunt data uit verschillende onderdelen aan elkaar koppelen en laten uitvoeren in een bestand. Omdat ik werk met een XML-export heb ik ook een export nodig van de context-toegangen waarnaar wordt verwezen.

### StaVaZa 20200810
Ik heb nog geen XML exports van de context toegangen, dus heb de links voor nu even gelegd met de interne id's van MAIS-Flexis.

## Conversie met MDWS-to-JSONLD
Rick Companje heeft voor mij met [zijn python](https://github.com/hetutrechtsarchief/MF-Export-XML-to-JSON) script de _export_ geconverteerd naar JSONLD. In deze JSONLD verwijst hij naar een lokaal 'context.json'-bestand. (TODO: een nette verwijzing naar een basis-context.json via een URL).

Voor de verwerking van de data met behulp van dit script is een look-up table nodig, die de link bijhoudt tussen de interne identifier en de GUID: we willen de relatie tussen verschillende 'dingen' (bv foto en vervaardiger) namelijk leggen op basis van de GUID. Deze look-up table is opgenomen in een MySQL database. Je kunt het geheel op je eigen machine als localhost installeren, of gebruik maken van mi2rdf.netwerkdigitaalerfgoed.nl. 

### StaVaZa 20200810
Rick heeft voor mij de conversie uitgevoerd op zijn machine. TODO: pythonscript uitrollen op [mi2rdf](https://demo.netwerkdigitaalerfgoed.nl/mi2rdf).

## JSONLD context bestand voor schema.org
Ik heb een contextfile [context.json](context.json) gemaakt die van zo veel mogelijk relevante velden in de JSONLD-file een 'context' aangeeft in [schema.org](https://schema.org/). Zo is van elk object dat volgens de database een foto is, aangegeven dat het rdf:type sdo:ImageObject is. Om de Linked Data te kunnen bestuderen in andere RDF-serializaties, heb ik met behulp van [jsonld-cli](https://github.com/digitalbazaar/jsonld-cli) de JSON-LD omgezet naar NQuads. NQuads is de enige niet-json RDF-serializatie die jsonld-cli ondersteunt.

```
$ jsonld normalize waterlandsarchief-stukje.json > waterlandsarchief-stukje.nq
```

Leesbare turtle heb ik vervolgens met [raptor](http://librdf.org/raptor/rapper.html) gemaakt:

```
$ rapper -i nquads -o turtle waterlandsarchief-stukje.nq > waterlandsarchief-stukje.ttl
```

### StaVaZa 20200810
Ik heb nu een context.json gemaakt voor schema.org. Ik wil graag contexten toevoegen voor RiC-O en Dublin Core.





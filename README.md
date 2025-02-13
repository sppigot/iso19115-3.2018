# ISO 19115-3:2018 schema plugin

This is the ISO19115-3:2018 schema plugin for GeoNetwork 3.6.x or greater version.

The main changes with the previous ISO19115-3 plugin are:
* Update to latest XSD (https://github.com/ISO-TC211/XML/wiki/Schema-Updates)
* Add support for 19115-2 Imagery standards


## Reference documents:

* http://www.iso.org/iso/catalogue_detail.htm?csnumber=53798
* http://www.iso.org/iso/catalogue_detail.htm?csnumber=32579
* https://github.com/ISO-TC211/XML/
 

## Description:

This plugin is composed of:

* indexing
* editing
 * editor associated resources
 * directory support for contact, logo and format.
* viewing
* CSW
* from ISO19115-3 conversion
* from ISO19115-2 conversion
* from/to ISO19139 conversion
* multilingual metadata support
* validation (XSD and Schematron)



## Installing the plugin

Use GeoNetwork 3.6+ version.

### Adding the plugin to the source code

The best approach is to add the plugin as a submodule into GeoNetwork schema module.

```
cd schemas
git submodule add https://github.com/metadata101/iso19115-3.2018.git iso19115-3.2018
```

Choose the branch corresponding to the GeoNetwork version you are using.

Add the new module to the schema/pom.xml:

```
  <module>iso19139</module>
  <module>iso19115-3.2018</module>
</modules>
```

Add the dependency in the web module in web/pom.xml:

```
<dependency>
  <groupId>${project.groupId}</groupId>
  <artifactId>schema-iso19115-3.2018</artifactId>
  <version>${gn.schemas.version}</version>
</dependency>
```

Add the module to the webapp in web/pom.xml:

```
<execution>
  <id>copy-schemas</id>
  <phase>process-resources</phase>
  ...
  <resource>
    <directory>${project.basedir}/../schemas/iso19115-3.2018/src/main/plugin</directory>
    <targetPath>${basedir}/src/main/webapp/WEB-INF/data/config/schema_plugins</targetPath>
  </resource>
```


Build the application.


### Adding the conversion to the import record page

Add `ISO19139-to-ISO19115-3-2018.xsl` and `ISO19115-3-2014-to-ISO19115-3-2018.xsl` to the `/web/src/main/webapp/xsl/conversion/import` folder.

## Metadata rules:

### Metadata identifier

The metadata identifier is stored in the element mdb:MD_Metadata/mdb:metadataIdentifier.
Only the code is set by default but more complete description may be defined (see authority,
codeSpace, version, description).

```
<mdb:metadataIdentifier>
  <mcc:MD_Identifier>
    <mcc:code>
      <gco:CharacterString>{{MetadataUUID}}</gco:CharacterString>
    </mcc:code>
  </mcc:MD_Identifier>
</mdb:metadataIdentifier>
```

### Metadata linkage ("point of truth")

The metadata linkage is updated when saving the record. The link added points
to the catalog the metadata was created. If the metadata is harvested by another
catalog, then this link will provide a way to retrieve the original record in the
source catalog.

```
<mdb:metadataLinkage>
  <cit:CI_OnlineResource>
    <cit:linkage>
      <gco:CharacterString>http://localhost/geonetwork/srv/eng/home?uuid={{MetadataUUID}}</gco:CharacterString>
    </cit:linkage>
    <cit:function>
      <cit:CI_OnLineFunctionCode codeList="http://standards.iso.org/iso/19139/resources/codelist/gmxCodelists.xml#CI_OnLineFunctionCode"
                                 codeListValue="completeMetadata"/>
    </cit:function>
  </cit:CI_OnlineResource>
</mdb:metadataLinkage>
```


### Parent metadata

The parent metadata records is referenced using the following form from the editor:

```
<mdb:parentMetadata uuidref="{{ParentMetadataUUID}}}"/>
```

Nevertheless, the citation code is also indexed.



### Validation

Validation steps are first XSD validation made on the schema, then the schematron validation defined in folder  [iso19115-3.2018/schematron](https://github.com/metadata101/iso19115-3.2018/tree/develop/src/main/plugin/iso19115-3/schematron). 2 famillies of rules are available:
* ISO rules (defined by TC211)
* INSPIRE rules


## CSW requests:

If requesting using output schema http://www.isotc211.org/2005/gmd an ISO19139 record is returned. 
To retrieve the record in ISO19115-3.2018, use http://standards.iso.org/iso/19115/-3/mdb/2.0 output schema.
```
<?xml version="1.0"?>
<csw:GetRecordById xmlns:csw="http://www.opengis.net/cat/csw/2.0.2"
  service="CSW"
  version="2.0.2"
  outputSchema="http://standards.iso.org/iso/19115/-3/mdb/2.0">
    <csw:Id>cecd1ebf-719e-4b1f-b6a7-86c17ed02c62</csw:Id>
    <csw:ElementSetName>brief</csw:ElementSetName>
</csw:GetRecordById>
```
Note: outputSchema = own will also return the record in ISO19115-3.



## More work required

### Formatter


### GML support

* Polygon or line editing and view.

### Imagery

* Better support of mac:childOperation 


## Community

Comments and questions to geonetwork-developers or geonetwork-users mailing lists.


## Contributors

* Simon Pigot (CSIRO)
* François Prunayre (titellus)
* Arnaud De Groof (Spacebel)
* Ted Habermann (hdfgroup)

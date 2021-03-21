# HydroShare Schema.org Technical Implementation

HydroShare has chosen to implement Schema.org metadata by embedding it as a script encoded as JSON-LD (https://json-ld.org/) within the source code for the landing page for each resource in HydroShare. This document describes the specific mapping of HydroShare's resource metadata to Schema.org elements as well as the syntax for how those elements should be encoded using JSON-LD. The implementations of the elements shown in the snippets below follow examples from release v1.2.0 of the Earth Science Information Partners (ESIP) science-on-schema.org (SOSO) guidance documents available at:

https://github.com/ESIPFed/science-on-schema.org/blob/master/guides/Dataset.md

Each of the sections below describe one or more of the Schema.org metadata elements and how they should be implemented for HydroShare resources. Only those elements listed below will be included in HydroShare's Schema.org implementation. Names of the Schema.org elements are linked and prefixed with "so:" to distinguis them in this document.

Each of the example JSON-LD code snippets are from a JSON-LD description of a HydroShare resource and should validate using Google's Structured Data Testing Tool located at:

https://search.google.com/structured-data/testing-tool/u/0/

This tool is being deprecated and is being replaced by the Rich Results Test tool:

https://search.google.com/test/rich-results

We also considered Google's guidance document for describing datasets at:

https://developers.google.com/search/docs/data-types/dataset

The examples below and the full encoding example included in this repository use metadata from an example HydroShare resource that is formally published. For those examples that show encoding for unpublished resources or that show encodings for alternative metadata not used by this resource, the encoding for this resource has been modified to provide an example of how those alternative forms would be expressed and encoded.

## Embedding Schema.org as JSON-LD in HydroShare Resource Landing Pages

The JSON-LD encoding of the Schema.org metadata should be incorporated into the landing page HTML inside the ```<head></head>``` as a ```<script>``` element.

```html
<html>
  <head>
    ...
    <script id="schemaorg" type="application/ld+json">
    {
      "@context": "https://schema.org/",
       "@type": "Dataset",
       "name": "Mobile Sensing Platform Data ...",
       "description": "This dataset includes data collected using a mobile sensing ...",
       ...
    }
    </script>
    ...
  </head>
  ...
</html>
```

## so:name, so:description, and so:keywords elements

[Google's guide to help publishers](https://developers.google.com/search/docs/data-types/dataset) describes the only required Schema.org fields as [so:name](https://schema.org/name) and [so:description](https://schema.org/description). The [so:name](https://schema.org/name) element  is used to encode the title of the resource. The [so:description](https://schema.org/description) element  is used to encode the resource's abstract. The [so:keywords](https://schema.org/keywords)
element is used to encode the resource's descriptive keywords. See Example 1.

## @type and so:additionalType elements

Since we are trying to get HydroShare's resources to show up in Google Dataset search, we will use a type of [so:Dataset](https://schema.org/Dataset) for the "@type" element to specify the type of the node. This isn't strictly true of all resources in HydroShare (e.g., modeling resource types), but for our implementation, we will use this for all resources. See Example 1.

The [so:additionalType](https://schema.org/additionalType) element allows us to specify another type for the resource and allows us to be more specific. Here we will use the HydroShare resource type from the HydroShare vocabulary terms page. See Example 1.

**NOTE**: Using a schema.org @type of [so:CreativeWork](https://schema.org/CreativeWork) instead of [so:Dataset](https://schema.org/Dataset) might be more semantically correct because not all HydroShare resources are "Datasets". All resources qualify as a [so:CreativeWork](https://schema.org/CreativeWork), which is
the superclass of [so:Dataset](https://schema.org/Dataset). If we switched to [so:CreativeWork](https://schema.org/CreativeWork), there are some implications becase [so:Dataset](https://schema.org/Dataset) has some properties that don't apply to [so:CreativeWork](https://schema.org/CreativeWork). For example, the [so:distribution](https://schema.org/distribution) property only applies to objects of type [so:Dataset](https://schema.org/Dataset), and the [so:distribution](https://schema.org/distribution) property is required by DataONE. For now, we will use an @type of [so:Dataset](https://schema.org/Dataset).

### Example 1: so:name, so:description, so:keywords, @type, and so:additionalType elements

```javascript
{
    "@context": "https://schema.org/",
    "@type": "Dataset",
    "additionalType": "http://wwww.hydroshare.org/terms/CompositeResource",
    "name": "Mobile Sensing Platform Data for Dissolved Organic Matter Study in the Northwest Field Canal in Logan, UT",
    "description": "This dataset includes data collected using a mobile sensing platform during baseflow and stormflow conditions in the Northwest Field Canal, located in Logan, UT. Data were collected by floating a payload of sensors in a longitudinal transect down the length of the canal and recording latitude, longitude, and several water quality variables, including fluorescent dissolved organic matter (FDOM), observations from custom fluorometers designed for calculating the fluorescence index (FI), dissolved oxygen, temperature, pH, specific conductance, and turbidity. The methods used in collection and processing of these data are described in detail in the methods document included within this resource.",
    "keywords": ["Mobile Sensing Platform", "dissolved organic matter", "water quality", "stormwater", "urban"]
}
```

## @id, so:url, and so:sameAs elements

These elements will identify the actual resource (the thing being described) and the resource landing page (the web page about the thing that is being described). Formally published resources in HydroShare receive a digital object identifier (DOI), so that will primarily be used to identify the resource. The [so:url](https://schema.org/URL) element is used to identify a web page about the resource, so it will point at the resource landing page. Each resource in HydroShare also has an internal HydroShare identifier, so we can use the [so:sameAs](https://schema.org/sameAs) element to provide both links to the resource landing page.

For published resources, the [so:url](https://schema.org/URL) element will use the URL-encoded DOI to point at the resource's landing page. For unpublished resources, we will use the URL-encoded HydroShare identifier for this purpose. Published resources will have a [so:sameAs](https://schema.org/sameAs) element that uses the URL-encoded HydroShare identifier, whereas unpublished resources will not have a [so:sameAs](https://schema.org/sameAs) element. Example 2 shows how these elements should be implemented for both published and unpublished resources.

The @id element is intended to identify the thing that is being described by the JSON-LD node. To do this, we will use a hash URL that is based on the URL to the landing page (either the URL-encoded DOI for published resources, or the URL-encoded HydroShare identifier for unpublished resources). Thus "@id" for the main node will take on the form: https://doi.org/10.4211/hs.6711ca843bcc4834b51288a3ddc3aa08#resource for published resources and https://www.hydroshare.org/resource/6711ca843bcc4834b51288a3ddc3aa08#resource for unpublished resources.

## so:creativeWorkStatus element

The [so:creativeWorkStatus](https://schema.org/creativeWorkStatus) element indicates the status of a creative work in terms of its stage in a lifecycle. Example terms include Incomplete, Draft, Published, Obsolete. For HydroShare resources, this element will be used to encode the resource's sharing status (e.g., "Published", "Public", "Private", "Obsolete" for obsoleted resources, etc.). See Example 2.

## so:inLanguage element

The [so:inLanguage](https://schema.org/inLanguage) element is used to express the language of the content. This is mapped to HydroShare's language metadata element. See Example 2.

### Example 2: so:url, so:sameAs, @type, so:additionalType, so:name, so:description, so:keywords, so:creativeWorkStatus, and so:inLanguage elements

For formally published resources:

```javascript
{
    "@context": "https://schema.org/",
    "@type": "Dataset",
    "@id": "https://doi.org/10.4211/hs.6711ca843bcc4834b51288a3ddc3aa08#resource",
    "url": "https://doi.org/10.4211/hs.6711ca843bcc4834b51288a3ddc3aa08",
    "sameAs": "https://www.hydroshare.org/resource/6711ca843bcc4834b51288a3ddc3aa08",
    "additionalType": "http://www.hydroshare.org/terms/CompositeResource",
    "name": "Mobile Sensing Platform Data for Dissolved Organic Matter Study in the Northwest Field Canal in Logan, UT",
    "description": "This dataset includes data collected using a mobile sensing platform during baseflow and stormflow conditions in the Northwest Field Canal, located in Logan, UT. Data were collected by floating a payload of sensors in a longitudinal transect down the length of the canal and recording latitude, longitude, and several water quality variables, including fluorescent dissolved organic matter (FDOM), observations from custom fluorometers designed for calculating the fluorescence index (FI), dissolved oxygen, temperature, pH, specific conductance, and turbidity. The methods used in collection and processing of these data are described in detail in the methods document included within this resource.",
    "keywords": ["Mobile Sensing Platform", "dissolved organic matter", "water quality", "stormwater", "urban"],
    "creativeWorkStatus": "Published",
    "inLanguage": "en-US"
}
```

For unpublished resources:

```javascript
{
    "@context": "https://schema.org/",
    "@id": "http://www.hydroshare.org/resource/c1be74eeea614d65a29a185a66a7552f#resource",
    "url": "http://www.hydroshare.org/resource/c1be74eeea614d65a29a185a66a7552f",
    "@type": "Dataset",
    "additionalType": "http://www.hydroshare.org/terms/CompositeResource",
    "name": "Time Series Data for Dissolved Organic Matter Study in the Northwest Field Canal in Logan, UT",
    "description": "This dataset includes time series data collected during baseflow and stormflow conditions in the Northwest Field Canal, located in Logan, UT. Time series data includes fluorescent dissolved organic matter (FDOM), observations from custom fluorometers used to calculate the fluorescence index in situ, turbidity, and rainfall. Methods used for deploying sensors, collecting data, and processing for quality control are described in the methods document contained within this resource.",
    "keywords": ["Urban", "Dissolved Organic Matter", "time series", "Stormwater", "water quality"],
    "creativeWorkStatus": "Public",
    "inLanguage": "en-US"
}
```

## so:identifier element

The [so:identifier](https://schema.org/identifier) element is used to encode the resource's identifier. For formally published resources, this will be the resource's DOI. For unpublished resources, this will be the resource's internal HydroShare identifier. Formally published resources in HydroShare receive a digital object identifier (DOI), so that will primarily be used to identify the resource. The "@id" keyword allows us to give a URI identifier for the JSON-LD node, or thing - i.e., the resource we are describing. Since we aim for published resources to be cataloged by DataONE, we will use the more formal identifier representation suggested by the SOSO guidance. We do not anticipate that unpublished resources will be cataloged by DataONE, so we will just express the HydroShare identifier as a URL. According to SOSO, this is acceptable. See Example 3. 

NOTE: Google's guidance on this is to repeat the [so:identifier](https://schema.org/identifier) element where there are multiple identifiers. Using JSON-LD, this is represented using JSON list syntax. So, for published resources with both a DOI and a HydroShare identifier, both will be encoded in a JSON list. 

See: https://developers.google.com/search/docs/data-types/dataset

### Example 3: so:identifier element

For formally published resources with DOI and HydroShare identifiers:

```javascript
{
   "@context": "https://schema.org/",
   "@type": "Dataset",
   "name": "Mobile Sensing Platform Data for Dissolved Organic Matter Study in the Northwest Field Canal in Logan, UT",
   "description": "This dataset includes data collected using a mobile sensing platform during baseflow and stormflow conditions in the Northwest Field Canal, located in Logan, UT. Data were collected by floating a payload of sensors in a longitudinal transect down the length of the canal and recording latitude, longitude, and several water quality variables, including fluorescent dissolved organic matter (FDOM), observations from custom fluorometers designed for calculating the fluorescence index (FI), dissolved oxygen, temperature, pH, specific conductance, and turbidity. The methods used in collection and processing of these data are described in detail in the methods document included within this resource.",
   "identifier": [
      {
      	"@id": "https://doi.org/10.4211/hs.6711ca843bcc4834b51288a3ddc3aa08",
      	"@type": "PropertyValue",
      	"propertyID": "https://registry.identifiers.org/registry/doi",
      	"value": "doi:10.4211/hs.6711ca843bcc4834b51288a3ddc3aa08",
      	"url": "https://doi.org/10.4211/hs.6711ca843bcc4834b51288a3ddc3aa08"
      },
      "http://www.hydroshare.org/resource/6711ca843bcc4834b51288a3ddc3aa08"
   ]
}
```

For unpublished resources with only HydroShare identifier: 

```javascript
{
    "@context": "https://schema.org/",
    "@type": "Dataset",
    "name": "Time Series Data for Dissolved Organic Matter Study in the Northwest Field Canal in Logan, UT",
    "description": "This dataset includes time series data collected during baseflow and stormflow conditions in the Northwest Field Canal, located in Logan, UT. Time series data includes fluorescent dissolved organic matter (FDOM), observations from custom fluorometers used to calculate the fluorescence index in situ, turbidity, and rainfall. Methods used for deploying sensors, collecting data, and processing for quality control are described in the methods document contained within this resource.",
    "identifier": "http://www.hydroshare.org/resource/c1be74eeea614d65a29a185a66a7552f"
}
```

## so:creator element

The [so:creator](https://schema.org/creator) element  will be used as it is in HydroShare's Dublin Core metadata to encode the creators of the resource. Information in the [so:creator](https://schema.org/creator) element will be drawn directly from the Dublin Core metadata element. Multiple creators will be expressed using a JSON ```@list``` container and will appear according to the order of authorship as specified by the creator order in HydroShare's resource metadata. Each creator will be of type [so:person](https://schema.org/Person) with [so:name](https://schema.org/name) and [so:email](https://schema.org/email) elements. The creator's address will be encoded in an [so:address](https://schema.org/address) element that is of type [so:PostalAddress](https://schema.org/PostalAddress) with a [so:streetAddress](https://schema.org/streetAddress) element. Each creator will have an [so:affiliation](https://schema.org/affiliation) element of type [so:Organization](https://schema.org/Organization) if that affiliation exists in the HydroShare creator metadata. Where URLs for a user's profile or other identifers have been included in the creator metadata, they will be include within a [so:url](https://schema.org/url) element and encoded within a JSON array. See Example 4.

### Example 4: so:creator element with multiple creators, including one that is an organization

```javascript
{
   "@context": "https://schema.org/",
   "@type": "Dataset",
   "name": "Mobile Sensing Platform Data for Dissolved Organic Matter Study in the Northwest Field Canal in Logan, UT",
   "description": "This dataset includes data collected using a mobile sensing platform during baseflow and stormflow conditions in the Northwest Field Canal, located in Logan, UT. Data were collected by floating a payload of sensors in a longitudinal transect down the length of the canal and recording latitude, longitude, and several water quality variables, including fluorescent dissolved organic matter (FDOM), observations from custom fluorometers designed for calculating the fluorescence index (FI), dissolved oxygen, temperature, pH, specific conductance, and turbidity. The methods used in collection and processing of these data are described in detail in the methods document included within this resource.",
   "creator": {
      "@list": [
         {
            "@type": "Person",
            "name": "Jeff Horsburgh",
            "affiliation": {
               "@type": "Organization",
               "name": "Utah State University"
            },
            "email": "jeff.horsburgh@usu.edu",
            "url": "https://www.hydroshare.org/user/6/"
         },
         {
            "@type": "Person",
            "name": "Bryce Mihalevich",
            "address": {
               "@type": "PostalAddress",
               "streetAddress": "8200 Old Main Hill, Logan, UT 84322-8200"
            },
            "affiliation":{
               "@type": "Organization",
               "name": "Utah Water Research Laboratory"
            },
            "email": "bryce.mihalevich@usu.edu"
         },
         {
            "@type": "Organization",
            "name": "Utah Water Research Laboratory",
            "address": {
               "@type": "PostalAddress",
               "streetAddress": "8200 Old Main Hill, Logan, UT 84322-8200"
            },
            "email": "uwrl.receptionist@usu.edu",
            "url": "http://uwrl.usu.edu"
         }
      ]
   }   
}
```

## Coverage elements

### so:spatialCoverage element

The [so:spatialCoverage](https://schema.org/spatialCoverage) element can be mapped from HydroShare's metadata as a box or a point. A box coverage is expressed with a type of [so:place](https://schema.org/Place) and is encoded within a [so:geo](https://schema.org/geo) element that expresses the geographic coordinates of a place and has a type of [so:GeoShape](https://schema.org/GeoShape). A [so:box](https://schema.org/box) is the area enclosed by the rectangle formed by two points. The first point is the lower corner, the second point is the upper corner. A box is expressed as two points separated by a space character. No units or projection information is included. WGS84 is the assumed typical spatial reference system (SRS) for coordinate values, which is consistent with what HydroShare uses, so in general the SRS does not need to be specified.

For a point spatial coverage in HydroShare, the location can be expressed using the point's [so:latitude](https://schema.org/latitude) and [so:longitude](https://schema.org/longitude) coordinates and is encoded similarly to the box coverage. In both cases, the place can include a [so:name](https://schema.org/name) element if the HydroShare user has included a place name in their spatial coverage metadata. 

### Example 5: so:spatialCoverage as a box

```javascript
{
   "@context": "https://schema.org/",
   "@type": "Dataset",
   "name": "Time Series Data for Dissolved Organic Matter Study in the Northwest Field Canal in Logan, UT",
   "description": "This dataset includes time series data collected during baseflow and stormflow conditions in the Northwest Field Canal, located in Logan, UT. Time series data includes fluorescent dissolved organic matter (FDOM), observations from custom fluorometers used to calculate the fluorescence index in situ, turbidity, and rainfall. Methods used for deploying sensors, collecting data, and processing for quality control are described in the methods document contained within this resource.",
   "spatialCoverage": {
      "@type": "Place",
      "geo": {
         "@type": "GeoShape",
         "box": "41.7312, -111.8513 41.7662, -111.8346"
      },
      "name": "The HydroShare user's place name if it is included."
   }
}
```

### Example 6: so:spatialCoverage as a point

```javascript
{
   "@context": "https://schema.org/",
   "@type": "Dataset",
   "name": "Time Series Data for Dissolved Organic Matter Study in the Northwest Field Canal in Logan, UT",
   "description": "This dataset includes time series data collected during baseflow and stormflow conditions in the Northwest Field Canal, located in Logan, UT. Time series data includes fluorescent dissolved organic matter (FDOM), observations from custom fluorometers used to calculate the fluorescence index in situ, turbidity, and rainfall. Methods used for deploying sensors, collecting data, and processing for quality control are described in the methods document contained within this resource.",
   "spatialCoverage": {
      "@type": "Place",
      "geo": {
         "@type": "GeoCoordinates",
         "latitude": 41.7312,
         "longitude": -111.8513
      },
      "name": "The HydroShare user's place name if it is included."
   }
}
```

### so:temporalCoverage element

Temporal coverage in HydroShare is expressed with a Start Date and an End Date. These can be expressed using the [so:temporalCoverage](https://schema.org/temporalCoverage) element to indicate the period that the content applies to, i.e. that it describes, as a textual string indicating a time period in ISO 8601 time interval format (two dates separated by a forward slash).

### Example 7: so:temporalCoverage as a period

```javascript
{
   "@context": "https://schema.org/",
   "@type": "Dataset",
   "name": "Time Series Data for Dissolved Organic Matter Study in the Northwest Field Canal in Logan, UT",
   "description": "This dataset includes time series data collected during baseflow and stormflow conditions in the Northwest Field Canal, located in Logan, UT. Time series data includes fluorescent dissolved organic matter (FDOM), observations from custom fluorometers used to calculate the fluorescence index in situ, turbidity, and rainfall. Methods used for deploying sensors, collecting data, and processing for quality control are described in the methods document contained within this resource.",
   "temporalCoverage": "2015-04-01T00:00:00/2016-11-01T00:00:00"
}
```

## so:provider, so:publisher, so:includedInDataCatalog, so:license, and so:isAccessibleForFree elements

### so:provider and so:publisher elements

HydroShare is the provider of the content, and, for formally published content, HydroShare is also the publisher of the content. Thus, the [so:provider](https://schema.org/provider) element will appear for all resources, whereas the [so:publisher](https://schema.org/publisher) element will appear only for formally published resources. Both [so:provider](https://schema.org/provider) and [so:publisher](https://schema.org/publisher) elements will be encoded similarly with an "@id" element that points at HydroShare's URL. In addition to the "@id" element, the [so:provider](https://schema.org/provider) element will include an "@type" element of [so:Organization](https://schema.org/Organization), a [so:name](https://schema.org/name) element of "HydroShare", and a [su:url](https://schema.org/URL) element that also points at HydroShare's URL. See Example 8.

### so:includedInDataCatalog element

HydroShare is also a data catalog offering data discovery services for its content and so the [so:includedInDataCatalog](https://schema.org/includedInDataCatalog) element expresses that the content is included in HydroShare's data catalog (a collection of datasets). This element will have an "@type" of [so:DataCatalog](https://schema.org/DataCatalog), a [so:name](https://schema.org/name) of "HydroShare", and a [so:url](https://schema.org/URL) of https://www.hydroshare.org/search/ (Example 8).

### so:license element

The [so:license](https://schema.org/license) element can have a type of either [so:url](https://schema.org/URL) or [so:CreativeWork](https://schema.org/CreativeWork) in Schema.org. We've chosen to encode it as a [so:CreativeWork](https://schema.org/CreativeWork) because there are multiple properties of the license that need to be encoded since HydroShare allows users to select not only Creative Commons licenses but also custom, user-defined licenses. Thus, the [so:license](https://schema.org/license) element will include a [so:text](https://schema.org/text) element  that contains the text of the license an a [so:url](https://schema.org/URL) element that provides a link to the license if the user has specified one. See Example 8.

### so:isAccessibleForFree element

The [so:isAccessibleForFree](https://schema.org/isAccessibleForFree) element is a boolean that indicates whether the content can be accessed for free. This will be true for formally published resources and public resources in HydroShare and should be included for those resources (Example 8).

### Example 8: so:provider, so:publisher, so:includedInDataCatalog, so:license, and so:isAccessibleForFree elements

```javascript
{
    "@context": "https://schema.org/",
    "@type": "Dataset",
    "name": "Time Series Data for Dissolved Organic Matter Study in the Northwest Field Canal in Logan, UT",
    "description": "This dataset includes time series data collected during baseflow and stormflow conditions in the Northwest Field Canal, located in Logan, UT. Time series data includes fluorescent dissolved organic matter (FDOM), observations from custom fluorometers used to calculate the fluorescence index in situ, turbidity, and rainfall. Methods used for deploying sensors, collecting data, and processing for quality control are described in the methods document contained within this resource.",
    "provider": {
        "@id": "https://www.hydroshare.org",
        "@type": "Organization",
        "name": "HydroShare",
        "url": "https://www.hydroshare.org"
    },
    "publisher": {
        "@id": "https://www.hydroshare.org"
    },
    "license": {
        "@type": "CreativeWork",
        "text": "This resource is shared under the Creative Commons Attribution CC BY.",
        "url": "http://creativecommons.org/licenses/by/4.0/"
    },
    "isAccessibleForFree": true,
    "includedInDataCatalog": {
        "@type": "DataCatalog",
        "name": "HydroShare",
        "url": "https://www.hydroshare.org/search/"
    }
}
```

## so:citation element

The [so:citation](https://schema.org/citation) element is used in Schema.org to reference some other creative work, such as another publication, web page, scholarly article, etc. It is not used to store the citation of the resource being described as that information is assembled from the information expressed in the other metadata elements. Thus, the [so:citation](https://schema.org/citation) element should be used to encode related resources from HydroShare's metadata. Citations can be expressed as either [so:text](https://schema.org/text) or [so:CreativeWork](https://schema.org/CreativeWork) elements. In either case, there is no way to encode HydroShare's relationship type metadata element. Because of this, the [so:citation](https://schema.org/citation) element will be encoded as an array of text elements, where the text elements are whatever the HydroShare user has specified as the text for the related resource - in the example below, the full text citations of the related resources.

### Example 9: so:citation element

```javascript
{
    "@context": "https://schema.org/",
    "@type": "Dataset",
    "name": "Time Series Data for Dissolved Organic Matter Study in the Northwest Field Canal in Logan, UT",
    "description": "This dataset includes time series data collected during baseflow and stormflow conditions in the Northwest Field Canal, located in Logan, UT. Time series data includes fluorescent dissolved organic matter (FDOM), observations from custom fluorometers used to calculate the fluorescence index in situ, turbidity, and rainfall. Methods used for deploying sensors, collecting data, and processing for quality control are described in the methods document contained within this resource.",
    "citation": [
        "Mihalevich, B. A., Horsburgh, J. S., Melcher, A. A. (2017). High-frequency measurements reveal spatial and temporal patterns of dissolved organic matter in an urban water conveyance, Environmental Monitoring and Assessment, http://dx.doi.org/10.1007/s10661-017-6310-y",
        "Mihalevich, B. A. (2017). Resolving Spatial and Temporal Variability in Dissolved Organic Matter Characteristics within Combined Agricultural and Stormwater Conveyances, Digital Commons at USU, All Graduate Theses and Dissertations, 6264.  https://digitalcommons.usu.edu/etd/6264"
    ]
}
```

## so:funder element

The [so:funder](https://schema.org/funder) element is used to encode the funding agency information from HydroShare's metadata. Funders can be expressed with an "@type" of [so:organization](https://schema.org/Organization) or a [so:person](https://schema.org/Person) using Schema.org. We will use the "@type" of [so:organization](https://schema.org/Organization) because HydroShare allows users to encode the [so:name](https://schema.org/name) of an organization and a [so:url](https://schema.org/URL). HydroShare also enables encoding of an award number and award title in the funding agency metadata. Neither of these can be encoded as part of an [so:organization](https://schema.org/Organization) and so will not appear in HydroShare's Schema.org implementation. See Example 10.

**NOTE**: Schema.org does have a [so:MonetaryGrant](https://schema.org/MonetaryGrant) type, but it is currently proposed for implementation and not approved. Also, it is ambiguous in HydroShare's metadata whether a user is indicating a funding organization or an actual monetary grant as HydroShare does not require a grant number. Furthermore, not all reasearch is funded by a grant and so the term [so:MonetaryGrant](https://schema.org/MonetaryGrant) may be a little problematic. We could also use the [so:funder](https://schema.org/funder) element with an "@type" of [so:Grant](https://schema.org/Grant), but that term is not recognized by Google's validation tool. Additionally, the term has been proposed, but has not been approved as part of Schema.org). 

### Example 10: so:funder element

```javascript
{
    "@context": "https://schema.org/",
    "@type": "Dataset",
    "name": "Time Series Data for Dissolved Organic Matter Study in the Northwest Field Canal in Logan, UT",
    "description": "This dataset includes time series data collected during baseflow and stormflow conditions in the Northwest Field Canal, located in Logan, UT. Time series data includes fluorescent dissolved organic matter (FDOM), observations from custom fluorometers used to calculate the fluorescence index in situ, turbidity, and rainfall. Methods used for deploying sensors, collecting data, and processing for quality control are described in the methods document contained within this resource.",
    "funder": [
	{
            "@type": "Organization",
            "name": "National Science Foundation",
            "url": "https://www.nsf.gov/awardsearch/showAward?AWD_ID=1208732"
        },
        {
            "@type": "Organization",
            "name": "Utah Water Research Laboratory",
            "url": "http://uwrl.usu.edu"
        }
    ]
}
```

## so:dateCreated, so:dateModified, and so:datePublished elements

The [so:dateCreated](https://schema.org/dateCreated), [so:dateModified](https://schema.org/dateModified), and [so:datePublished](https://schema.org/datePublished) elements are mapped directly to their corresponding values from HydroShare's metadata. The [so:datePublished](https://schema.org/datePublished) element will only appear for formally published resources.

### Example 11: so:dateCreated, so:dateModified, and so:datePublished elements

```javascript
{
    "@context": "https://schema.org/",
    "@type": "Dataset",
    "name": "Time Series Data for Dissolved Organic Matter Study in the Northwest Field Canal in Logan, UT",
    "description": "This dataset includes time series data collected during baseflow and stormflow conditions in the Northwest Field Canal, located in Logan, UT. Time series data includes fluorescent dissolved organic matter (FDOM), observations from custom fluorometers used to calculate the fluorescence index in situ, turbidity, and rainfall. Methods used for deploying sensors, collecting data, and processing for quality control are described in the methods document contained within this resource.",
    "dateCreated": "2017-10-18T22:16:43.185313+00:00",
    "dateModified": "2017-10-27T21:11:18.598037+00:00",
    "datePublished": "2017-10-27T21:11:14.736471+00:00"
}
```

## so:subjectOf element

Every HydroShare resource has a Dublin Core metadata file that describes it. The location of this metadata document can be provided using the [so:subjectOf](https://schema.org/subjectOf) element. This element will indicate that the resourcemetadata.xml document for the resource is a "CreativeWork or Event about this Thing" - i.e., the resource being described. Example 12 shows how the [so:subjectOf](https://schema.org/subjectOf) element should be encoded.  

The [so:subjectOf](https://schema.org/subjectOf) element would use an "@type" of "DataDownload" and a [so:name](https://schema.org/name) of "resourcemetadata.xml", which is the name of the metadata file accompanying each HydroShare resource. It will use a [so:description](https://schema.org/description) of "Dublin Core Metadata Document Describing the Dataset". The [so:url](https://schema.org/URL) to the resourcemetadata.xml file for the resource will be specified, and the [so:encodingFormat](https://schema.org/encodingFormat) element will be specified using the mime type for the file "application/rdf+xml". 

### Example 12: so:subjectOf element

```javascript
{
    "@context": "https://schema.org/",
    "@type": "Dataset",
    "name": "Time Series Data for Dissolved Organic Matter Study in the Northwest Field Canal in Logan, UT",
    "description": "This dataset includes time series data collected during baseflow and stormflow conditions in the Northwest Field Canal, located in Logan, UT. Time series data includes fluorescent dissolved organic matter (FDOM), observations from custom fluorometers used to calculate the fluorescence index in situ, turbidity, and rainfall. Methods used for deploying sensors, collecting data, and processing for quality control are described in the methods document contained within this resource.",
    "subjectOf": {
        "@type": "DataDownload",
        "name": "resourcemetadata.xml",
        "description": "Dublin Core Metadata Document Describing the Dataset",
        "url": "https://www.hydroshare.org/hsapi/resource/c1be74eeea614d65a29a185a66a7552f/scimeta/",
        "encodingFormat": "application/rdf+xml"
    }
}
```

## so:distribution element

The [so:distribution](https://schema.org/distribution) element specifies how the content of the resource can be accessed/downloaded. Its value can be of "@type" [so:DataDownload](https://schema.org/DataDownload), which has properties [so:contentSize](https://schema.org/contentSize) a text string indicating the size of the file to download (in this case the zipped BagIt Bag containing the resource), [so:encodingFormat](https://schema.org/encodingFormat) a text string indicating the mime type of the file to be downloaded ("application/zip"), [so:contentUrl](https://schema.org/contentUrl) indicating the URL for accessing the zipped BagIt bag for the resource, [so:description](https://schema.org/description) as a text string describing the zipped Bag, and [dateModified](https://schema.org/dateModified) as an encoding of the date on which the content was last modified. 

According to DataONE's guidance, the [so:distribution](https://schema.org/distribution) element should also include an [so:identifier](https://schema.org/identifier) element and a checksum for the distribution file(s). In Example 13, the [so:identifier](https://schema.org/identifier) element is expressed using the URL-encoded HydroShare internal idenfier for the resource. The checksum is a property of the zipped BagIt Bag file to be downloaded. There is still some discussion about how the checksum value should best be encoded, however, we are choosing to adopt preliminary form of this suggested here: https://github.com/ESIPFed/science-on-schema.org/issues/66

### Example 13: so:distribution element

```javascript
{
   "@context": "https://schema.org/",
   "@type": "Dataset",
   "name": "Mobile Sensing Platform Data for Dissolved Organic Matter Study in the Northwest Field Canal in Logan, UT",
   "description": "This dataset includes data collected using a mobile sensing platform during baseflow and stormflow conditions in the Northwest Field Canal, located in Logan, UT. Data were collected by floating a payload of sensors in a longitudinal transect down the length of the canal and recording latitude, longitude, and several water quality variables, including fluorescent dissolved organic matter (FDOM), observations from custom fluorometers designed for calculating the fluorescence index (FI), dissolved oxygen, temperature, pH, specific conductance, and turbidity. The methods used in collection and processing of these data are described in detail in the methods document included within this resource.",
   "distribution": {
      "@type": "DataDownload",
      "contentSize": "8.2 MB",
      "encodingFormat": "application/zip",
      "contentUrl": "https://www.hydroshare.org/hsapi/resource/6711ca843bcc4834b51288a3ddc3aa08/",
      "description": "Zipped BagIt Bag containing the HydroShare Resource",
      "dateModified": "2019-12-09T21:21:50.284113+00:00",
      "identifier":[
         "https://www.hydroshare.org/resource/6711ca843bcc4834b51288a3ddc3aa08",
         {
            "@type": "PropertyValue",
            "additionalType": [
               "http://www.wikidata.org/entity/Q185235",
               "http://id.loc.gov/vocabulary/preservation/cryptographicHashFunctions/md5"
            ],
            "identifier": "md5:dcc2c7d55a1167e6df887393f1462542",
            "propertyID": "MD5",
            "value": "dcc2c7d55a1167e6df887393f1462542"
         }
      ]
   }
}
```


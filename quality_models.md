
# Quality models in Tools

This document intends to summarise information about quality models in software engineering tools and frameworks, and especially how they are defined in terms of structure, format, and content. The goals behind this information is to define a general-purpose format for the definition of quality models in tools.

JSON files available:

* [ [Download](samples/qm_ossmeter.json) ] The JSON file for the ossmeter default quality model definition.
* [ [Download](samples/qm_alambic.json) ] The JSON file for the [Alambic](https://alambic.io) default [quality model](http://eclipse.castalia.camp/documentation/quality_model.html) for Eclipse.
* [ [Download](samples/qm_polarsys.json) ] The JSON file for the [PolarSys](https://wiki.polarsys.org/EclipseQualityModel) [quality model](http://dashboard.polarsys.org/documentation/quality_model.html) definition.

Pictures (exports) available:

* [ [Download](images/qm_ossmeter.png) ] The PNG export of the ossmeter default quality model.
* [ [Download](images/qm_polarsys_medium.png) ] The PNG export of the polarsys quality model (large view).
* [ [Download](images/qm_polarsys_simple.png) ] The PNG export of the polarsys quality model (simple view).


## Examples of implementation

### Ossmeter

[OSSMeter](https://ossmeter.castalia.camp) uses a JSON file to describe its quality model.

Information held in the QM file includes:

* At the root:
  - `name` is a string for the QM name.
  - `qualityAspects` is an array of hashes (qualityAspects) describing the quality model hierarchy.
* For `qualityAspects`:
  - `name` a name for this qualityAspect. It is a string.
  - `qualityAspects` is an array of hashes defining sub-qualityAspects.
  - `attributes` is an array of hashes describing the attributes attached to this qualityAspects.
* For `attributes`:
  - `name` a name for this attribute. It is a string.
  - `description` a complete description for this attribute. It is a string.
  - `factoids` is an array of strings describing the factoids attached to this attribute.
  - `metrics` is an array of strings describing the metrics attached to this attribute.

A minimal example is reproduced thereafter:

```
{
  "qualityModel": {
    "name": "Default OSSMETER quality model",
    "qualityAspects ": [
      {
        "name": "code",
        "qualityAspects": [
          {
            "name": "Maintainability",
            "qualityAspects": [],
            "attributes": [
              {
                "name": "Analyzability",
                "description": "It refers at what extent the source code SNIP.",
                "factoids": [
                  "percentageCommentedOutCode",
                  "understandabilityFactoid"
                ],
                "metrics": [
                  "org.ossmeter.metricprovider.trans.rascal.LOC.locPerLanguage",
                  "spreadOfUnderstandabilityIssues"
                ]
              },
		"attributes": [
          {
            "name": "Maturity",
            "description": "It refers to the possibility of having SNIP.",
            "factoids": [
              "inefficientStringsFactoid",
              "headerUse"
            ],
            "metrics": [
              "org.ossmeter.metricprovider.style.spreadOfErrorProneness.historic",
              "headerCounts"
            ]
          },
```

### Alambic

[Alambic](https://alambic.io) uses a JSON file to describe its quality model. The file and mechanism are described in [the product's help](http://alambic.io/Documentation/Basics/QualityModel.html).

Information held in the QM file includes:

* At the file level:
  - `name` and `version` of quality model, as strings.
  - `children` is a array of hashes (nodes) describing the quality model hierarchy.
* At the node level:
  - `mnemo` is a unique identifier for the node. Name and description are provided in a separate file and are linked to the quality model through the mnemo id. Field is a string.
  - `active` states if the node is fully functional. It is a boolean, encoded as a string: true/false.
  - `type` defines the type of node (quality `attribute` or `metric`), encoded as a string.
  - `weight` is an optional attribute that provides a factor (weight) to consider when aggreagting values up to the root. Default is 1.

Additional notes:
* The model can have as many levels (sub-children) as required.
* Metrics are leaves and do not have a `children` attribute.
* Branches (i.e. quality attributes) do not need to have the same sub-tree depth.

A sample quality model looks like this:

```
{
  "name": "Alambic quality model for Eclipse",
  "version": "1.0.1",
  "children": [
    {
      "mnemo": "Maintainability",
      "type": "attribute",
      "active": "true",
      "weight": 2,
      "children": [
        {
          "mnemo": "SLOC",
          "type": "metric",
          "active": "true",
        }
      ]
    }
  ]
}
```

## Towards a common format

### Open questions

* There are different concepts here. As far as I understand it, ossmeter uses qualityAspects, then attributes and metrics. That fits well the GQM approach from V. Basili. However in my experience there are not so many differences regarding the technical implementation between qualityAspects and attributes, and I would suggest merging the two concepts -- once again, for the mechanisms/formatting involved, not for the semantics as displayed to the user.
* Should the elements (attributes, metrics..) be defined in the quality model description file -- that includes name, description, references, etc. -- or should they be defined in a dedicated file?

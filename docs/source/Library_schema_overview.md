# Library schema overview

The variety and complexity of events in electrophysiological experiments makes full 
documentation challenging. As more experiments move out of controlled laboratory environments
and into less controlled virtual and real-world settings, the terminology required to adequately
describe events has the potential to grow exponentially.  

In addition, experiments in any given subfield can contribute to pressure to add 
overly-specific terms and jargon to the schema hierarchy—for example, adding musical 
terms to tag events in music-based experiments, video markup terms for experiments 
involving movie viewing, traffic terms for experiments involving
virtual driving, and so forth. 

Clinical fields using neuroimaging also have their own specific
vocabularies for describing data features of clinical interest (e.g., seizure, sleep stage IV).
Including these discipline-specific terms quickly makes the base HED schema unwieldy and less
usable by the broader user community.

Third generation HED instead introduces the concept of the **HED library schema**. 
To use a programming analogy, when programmers write a Python module, the resulting code 
does not become part of the Python language or core library. Instead, the module becomes 
part of a library used in conjunction with core modules of the programming language. 

Similar to the design principles imposed on function names and subclass organization in 
software development, HED library schemas must conform to some basic rules:

``````{admonition} Rules for HED library schema design.
:class: tip

1. Every term must be unique within the library schema and must conform to the rules for
HED schema terms.
2. Schema terms should be readily understood by most users. The terms should not be ambiguous and should be meaningful in themselves without reference to their position in the schema hierarchy.
3. If possible, no schema sub-tree should have more than 7 direct subordinate sub-trees.
4. Terms that are used independently of one another should be in different sub-trees (orthogonality).
5. Every term in the hierarchy satistifies the **is-a** relationship with its parent.
In other words if B has A as a parent in the schema hierarchy, then B is an example of A.
Searching for A will also return B (search generality).

``````

As in Python programming, we anticipate that many HED schema libraries may be defined 
and used, in addition to the base HED schema. Libraries allow individual research or
clinical communities to annotate details of events in experiments designed to answer questions 
of interest to particular to those communities.

Since it would be impossible to avoid naming conflicts across schema libraries
that may be built in parallel by different user communities,
HED supports schema library namespaces.
Users will be able to add library tags qualified with namespace designators.
All HED schemas, including library schemas, 
adhere to [semantic versioning](https://semver.org/). 


## Defining a schema

A HED library schema is defined in the same way as the base HED schema except that it has an
additional attribute name-value pair, `library="xxx"` in the schema header. We will use as an
illustration a library schema for driving. Syntax details for a library schema are similar to
those for the base HED schema.
(See the [HED schema format specification](https://hed-specification.readthedocs.io/en/latest/03_Schema.html)
for more details).

````{admonition} **Example:** Driving library schema (MEDIAWIKI template).

```moin
HED library="driving" version="1.0.0" 
!# start schema 
   [... contents of the HED driving schema ...]
!# end schema
   [... required sections specifying schema attribute definitions ...]
!# end hed
```
````

The required sections specifying the schema attributes  are *unit-class-specification*, 
*unit-modifier-specification*, *value-class-specification*, *schema-attribute-specification*,
and *property-specification*.

````{admonition} **Example:** Driving library schema (XML template).

```xml
<?xml version="1.0" ?>
<HED library="driving" version="1.0.0">
    [... contents of the HED_DRIVE schema ... ]
</HED>
```
````

The schema XML file should be saved as `HED_driving_1.0.0.xml` to facilitate 
specification in tools.

## Schema namespaces

As part of the HED annotation process, users must associate a standard HED schema with their
datasets. Users may also include tags from an arbitrary number of additional library schemas.
For each library schema used to annotate a data recording, the user must associate a local 
name with the appropriate library schema name and version. Each library must be associated 
with a distinct local name within a recording annotations. The local names should be 
strictly alphabetic with no blanks or punctuation. 

The user must pass information about the library schema and their associated local names to 
processing functions. HED uses a standard method of identifying namespace elements by prefixing
HED library schema tags with the associated local names. Tags from different library schemas can
be intermixed with those of the base schema. Since the node names within a library must be
unique, annotators can use short form as well as fully expanded tag paths for library schema 
tags as well as those from the base-schema.

````{admonition} **Example:** Driving library schema example tags.

```
dp:Action/Drive/Change-lanes
dp:Drive/Change-lanes
dp:Change-lanes
```
````

A colon (`:`) is used to separate the qualifying local name from the remainder of the tag. 
Notice that *Action* also appears in the standard HED schema. Identical terms may be used 
in a library schema and the standard HED schema. Use of the same term implies a similar 
purpose. Library schema developers should try not to reuse terms in the standard schema 
unless the intention is to convey a close or identical relationship.


## Attributes and classes

In addition to the specification of tags in the main part of a schema, a HED schema has 
sections that specify unit classes, unit modifiers, value classes, schema attributes, 
and properties. The rules for the handling of these sections for a library schema are 
as follows:

### Required sections

The required sections of a library schema are: the *schema-specification*, 
the *unit-class-specification*, the *unit-modifier-specification*, 
the *value-class-specification* section, the *schema-attribute-specification* section, 
and the *property-specification*. The library schema must include all required 
schema sections even if the content of these sections is empty.

### Relation to base schema

Any schema attribute, unit class, unit modifier, value class, or property used in the
library schema must be specified in the appropriate section of the library schema
regardless of whether these appear in base schema. Validators check the library
schema strictly on the basis of its own specification without reference to another 
schema.

### Schema properties

HED only supports the schema properties listed in Table B.2: *boolProperty*, 
*unitClassProperty*, *unitModifierProperty*, *unitProperty*, and *valueClassProperty*.  
If the library schema uses one of these in the library schema specification, 
then its specification must appear in the *property-specification* section of the library schema.

### Unit classes

The library schema may define unit classes and units as desired or include unit classes or 
units from the base schema. Similarly, library schema may define unit modifiers or 
reuse unit modifiers from the base schema. HED validation and basic analysis tools 
validate these based strictly on the schema specification and do not use any outside 
information for these.

### Value classes

The standard value classes (*dateTimeClass[*]*, *nameClass*, *numericClass[*]*, 
*posixPath[*]*, *textClass[*]*) if used, should have the same meaning as in the 
base schema. The hard-coded behavior associated with the starred ([*]) value 
classes will be the same. Library schema may define additional value classes and 
specify their allowed characters, but no additional hard-coded behavior will be 
available in the standard toolset. This does not preclude special-purpose tools 
from incorporating their own behavior.

### Schema attributes

The standard schema attributes (*allowedCharacter*, *defaultUnits*, *extensionAllowed*,
*recommended*, *relatedTag*, *requireChild*, *required*, *SIUnit*, *SIUnitModifier*,
*SIUnitSymbolModifier*, *suggestedTag*, *tagGroup*, *takesValue*, *topLevelTagGroup*, 
*unique*, *unitClass*, *unitPrefix*, *unitSymbol*, *valueClass*) should have the same
meaning as in the base schema. The hard-coded behavior associated with the schema 
attributes will be the same. Library schema may define additional schema attributes. 
They will be checked for syntax, but no additional hard-coded behavior will be available
in the standard toolset. This does not preclude special-purpose tools from incorporating
their own behavior.

### Syntax checking

Regardless of whether a specification is in the base-schema or not, HED tools can perform basic syntax checking.

````{admonition} Basic syntax checking for library schema.
:class: tip

1. All attributes used in the schema proper must be defined in the schema attribute section of the schema.
2. Undefined attributes cause an error in schema validation.
3. Similar rules apply to unit classes, unit modifiers, value classes, and properties.
4. Actual handling of the semantics by HED tools only occurs for entities appearing in the base schema.
````

## Library schemas in BIDS

The most common use case (for 99.9% of the HED users) is to use one of the standard 
HED schemas available on GitHub in the `hedxml` directory of the `hed-specification` 
repository ([https://github.com/hed-standard/hed-specification/tree/master/hedxml](https://github.com/hed-standard/hed-specification/tree/master/hedxml)).

This section explains the changes that are being proposed in BIDS to accommodate access to 
HED library schemas. This section will be updated as the proposals progress though the 
BIDS review process. All `"fileName"` keys in the following discussion point to the 
names of files located in the `./code` directory of the dataset tree.

The major change to the BIDS specification is to allow the value associated with the
`"HEDVersion"` key in the `dataset_description.json` file to be a dictionary rather 
than a string expressing the HED version. This proposed change will allow users more 
flexibility in specifying the base HED schema and will accommodate an arbitrary number 
of library schemas. The allowed top-level keys in this dictionary are: `"version"`, `
"fileName"`, and `"libraries"`. The `"version"` and `"fileName"` keys pertain to 
the base HED schema, and if both are specified, `"version"` always takes precedence. 

The `"libraries"` key points to a library dictionary. The keys of the library dictionary are 
the nicknames used in the dataset to reference the schema. The values  The `"version"`
key specifies the HED version number of a schema in the standard library and has the 
same effect as directly specifying. The following example specifies a base HED schema 
located in the `./code/myLocalSchema.xml` file of the dataset and two library schemas 
with nicknames `"la"` and `"lb"`. `

````{admonition} **Example:** Proposed specification of library schema in BIDS.

```json
{
    "Name": "A wonderful experiment",
    "BIDSVersion": "1.4.0",
    "HEDVersion": {
        "fileName": "mylocalSchema.xml",
        "libraries": {
            "la": {
                "libraryName": "libraryA",
                "version": "1.0.2"
            },
            "lb": {
                "fileName": "HED_libraryB_0.5.3.xml"
            }
        }
    }
}
```
````

The `"la"` library schema is the `./hedxml/HED_libraryA_1.0.2.xml` file found in the
[`hed-schema-library`](https://github.com/hed-standard/hed-schema-library) repository 
on the [`hed-standard`](https://github.com/hed-standard) working group GitHub site. 
HED tags from this library have the `la:` prefix (e.g., `la:XXX`).  The `"lb"` library
schema can be found in the `./code/HED_libraryB_0.5.3.xml` file in the BIDS dataset. 
Tags from this library are prefixed with `lb:`. NOTE: This specification is preliminary 
and is waiting the resolution of BIDS formats for specifying external files as outline in
[BIDS specification PR #820](https://github.com/bids-standard/bids-specification/pull/820).


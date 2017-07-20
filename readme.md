# Research Data Collections Working Group

![RDA Collections WG](https://www.rd-alliance.org/sites/all/themes/dotte/logo.png)

## 1. Introduction
Several communities have expressed interest in leveraging aggregations of objects with particular focus on building such aggregations through PIDs and possibly providing identifiers for aggregation objects.
There is however no unified cross-community approach to building and managing such collections and no common model for understanding them.

The PID Information Types WG has defined a core model and the central interface for accessing object state information and provided a small number of example types, which were consequently registered in the Type Registry WG prototype.
With these tools available to describe essential object information, collections can be described so to be able to deal with more than a single object at once.

Building collections within diverse domains and then sharing or expanding them across disciplines should enable common tools for end-users and e-infrastructure providers.
Individual disciplinary communities can directly benefit if such tools are made widely available, and cross-community data sharing can benefit from increased unification between collection models and implementations.
PID providers may benefit from marketing additional services on collections.

A common abstract API  for data management of collections will facilitate data-interoperability and reuse by, (1) making solutions for managing collections more sustainable and widely available, thus (2) encouraging better data management practices and (3) allowing data objects in collections to be shared and re-used across projects and domains. It is not the intent of the working group to propose an alternative to existing well established standards for describing and archiving collections but rather to propose an API and implementation for creation, consumption, distribution and citation of collections and their items that could serve as a unifying layer _on top of_ the existing models and which can enable producers and consumers of collections to operate on data items managed in diverse collection models and repositories. Existing solutions focus on describing collections and their semantics with metadata, but do not offer a full set of generic, machine-actionable CRUD operations on them, which is a key innovation of the proposed API.

### 1.1 What is a collection?
Imagine you have a number of objects that belong together.
The type of object is a bit flexible, as long as it is in some digital form; this can include digital documents or scientific articles, individual data files, a zip of several files, digital images, audio or video recordings.
The reason why these objects belong together is also not extremely rigid.
There may for example be a number of files that came out of a scientific model calculation, or a number of recordings from a study session or a very distinctively selected choice of files grouped together for a particular analysis.

In conclusion, a collection is a very flexible mechanism to bind objects together.
While the contents of a collection may therefore undergo changes, its structure is more rigid.
A collection has a distinct identity even throughout such changes and that if offers a set of precisely defined actions used to modify it.

### 1.2 What can we do with a collection?
A similar concept familiar from computer programming are common abstract data types such as lists, arrays and sets.
We know how to add, insert, replace or delete objects from such constructs, and we know that there are mechanisms for this in most higher programming languages.
But programming languages deal with objects in computational processes, while we rather want to talk about our research objects that are much more complex and not bound in computer code.
Nonetheless, we want to do the same things with our bag of objects: Put objects in the collection, take them out again, learn about the number of objects and their total size, look at all objects in the collection in an orderly manner and so on.
We may even have some constraints on the collection, such as whether its objects are ordered or unordered, or whether there are further hierarchies inside it.

## 2. Requirements
A survey done prior to establishment of the Collection WG resulted in a discussion that spun out the following core requirements that should apply to collections across disciplines:

 1. Collections must bear globally registered persistent identifiers (PIDs).
 2. Objects in a collection must bear unique identifiers. These can be PIDs, but also identifiers unique within a specific system’s context as long as they remain valid references throughout changes in object location within the system.
 3. Minimal state information on objects must remain retrievable using the identifier beyond the object’s lifetime.
 4. No assumption should be made on the lifetime of collections. Collections may be deleted at any time or kept over long timespans, depending on use case.
 5. Collections may contain sub-collections, but not recursively. It should be possible to restrict this rule for individual collections.
 6. Objects may belong to more than one collection.
 7. A single collection may contain objects stored at different places.
 8. Collections are finite.
 9. Any object in a collection may be referred to by multiple identifiers within the collection.
 10. Collections must offer well-defined actions (such as create, read, update, delete) that can be executed by software agents with minimal additional context required.
 11. A software agent should be enabled to determine the specific usage (“collection model”) of a collection by querying a specific collection action. There should be no need for a caller to know the model in advance, except in the case of collection creation.
 12. It should be possible to record the role of an object within a specific collection, independent from the role it has in the context of other collections.

There also some additional requirements still subject to discussion on whether or to what extent they should be mandatory.

 1. Objects in collections should have registered data types.
 2. Collections should offer a listener/subscription model for collection change events.
 3. Some elements in a collection may not be named explicitly, but rather given implicitly through a generation rule.

### 2.1 Implementation and Extensibility

In addition to the functional requirements, we need to consider that implementation and extensibility requirements will vary across deployments.  The API for operating against a collection should be standard, but it must be possible for the way in which that API is implemented, and the scope of the operations supported, to be variable.  This variability is on levels: the functionality offered by the service, and the functionality implicit in a collection itself. In both cases, the variations must be explicitly expressed and machine-discoverable.

#### 2.1.1 Service Features

Implementations of the Collection Service may vary in the features they offer.  We have identified the following features which should be possible, but not required, of implementations:

1. the ability to assign PIDs to new collections
2. the ability to enforce access restrictions on collections
3, the ability to support paginated requests
4. the ability to support asynchronous actions
5. the ability to automatically generate new collections from existing collections based upon pre-defined rules
6. the ability to expand recursive collections (and limits of that expansion)
7. the ability to provide support for collection versioning
8. the ability to restrict or expand the supported set-based collection operations
8. the ability to restrict or expand the supported collection model types

#### 2.1.2 Collection Capabilities

Further, the properties of any given collection may impact the actions that are possible for that collection.  We have identified the following collection capabilities which may impact how a producer or consumer operates on and with the collection and its contents:

1. whether or not member items have an implicit ordering
2. if ordered, where new items are inserted in that order
3. whether member items can assume specific roles with respect to the collection (e.g. such as becoming a 'default' item)
4. whether collection membership is static or mutable
5. whether collection metadata is static or mutable
6. whether member items are restricted to a specific data type
7. whether a maximum number of a members items is imposed

## 3. Definition
A first coarse-grained collection definition is as follows<sup title="Adapted from http://smw-rda.esc.rzg.mpg.de/index.php/Collection">[1](http://smw-rda.esc.rzg.mpg.de/index.php/Collection)</sup>:
> A collection is a digital object which bears a unique identifier and consists of a finite number of digital object identifiers and metadata associated with each referenced identifier.

Informally, we refer to the elements referenced in a collection through identifiers as the collection’s content.
The elements are digital objects, and collections are digital objects themselves.
Elements that are other collections are called subcollections of the given collection.
A collection and its subcollections define a graph.
A collection is finite, if the set of identifiers generated by iteratively resolving its subcollections is finite, i.e., if the graph has a finite number of nodes.
Infinite collections may therefore exist in theory, but are too hard to manage in practice and therefore considered to be out of scope here.

A collection’s elements may be arranged in a particular form, including unordered (set) and ordered (list) form.
Ordered form may be useful to describe inherent semantics of a collection, for instance, to arrange subsequent versions of a digital object in order or capture a strict order of slices of a time series.

### 3.1 Fine grained collection definition
We define the following elements within the scope of what we consider collections:

A **collection** is a 2-tuple of an identifier and the collection state.

The **collection state** is a 3-tuple of collection membership, collection capabilities and collection metadata.

The **collection membership** is a finite set of collection item identifiers.

A **collection item identifier** points either to a collection or to some digital object, called a collection leaf, which is not a collection.

The **collection metadata** consists of the collection description and of procedural (or functional or technical) metadata, that are machine interpretable and used for automated processes on collections. Procedural (or functional/technical) metadata consist of collection properties, the Mapping Function and all item metadata.

**Collection properties** contain metadata of the collection structure itself.
 * *Examples:* the collection state like its size or whether it is an ordered list or an unordered set, whether it is mutable or fixed. Also possible relations to other collections, like parent collections, or properties common to all its collection items like mutability or the belonging to one repository.

**Collection Capabilities** fully comprises the set of actions that are supported by it. Actions may or may not affect collection state.  *Remark*: (1) An external agent may provide more actions than are in a collection's capabilities, e.g. more sophisticated composite actions or actions across multiple collections.  (2) An agent submits a capability request to a collection to retrieve the action set.

The **Mapping Function** is a function F mapping from the collection membership to item metadata elements.

 - The function F need not be injective: Multiple items can be related to the same metadata. 
 - *Examples:* the mime types of the items, can be given as list or as the mapping of each item to its mime type. 

The **Collection Description** is all the metadata that are not procedural. Its main purpose is to describe to the domain expert using the collections what its usefulness is.

## 4. Use Cases

- DKRZ
- Perseids Project, Perseus Digital Library
- Nomad
- PECE Project, Rensselear Polytechnic Institute
- ORCID
- CLARIN
- BCO-DMO
- Sead Project
- Coptic SCRIPTORIUM
- Ocean Data Interoperability Platform (ODIP)
- Harvard Astronomy Abstract Service
- Open Philology Project, University of Leipzig
- IGSN

### 4.1 DKRZ use case: Climate data management

Scientific groups and research institutions around the globe develop individual climate models, which are run on their respective HPC systems. However, there is no perfect climate model, and all of them model the physical world in different ways. To assess the quality of climate models, a large exercise is therefore needed: Running the various models with same input and boundary conditions, producing data that can then be analyzed and compared to assess the differences between models or to generate aggregated “ensemble” data products (basic statistics). This exercise is called the Coupled Model Intercomparison Project (CMIP<sup>https://www.wcrp-climate.org/wgcm-cmip</sup>). 

CMIP is in essence a cyclic activity, with each phase running for several years. The previous phase, now finished, was CMIP5; the current phase is called CMIP6. The insights resulting from CMIP data are eventually also used to back the Assessment Reports of the Intergovernmental Panel on Climate Change (IPCC), and therefore, the community workflow of CMIP is also intertwined to some extent with IPCC processes.

#### 4.1.1 ESGF data collection perspectives

Throughout its phases, CMIP data have grown rapidly in volume, exceeding the capabilities of a single institution to handle data collection and distribution. The global Earth System Grid Federation (ESGF<sup>https://esgf.llnl.gov</sup>) has been set up to as a data infrastructure to support CMIP data management, and DKRZ contributes to its development in multiple areas. 

CMIP6 data are at the most basic level netCDF data files, with each individual file containing a single data variable of a single simulation over the simulation's time range and covering the whole globe. Each file also bears an individual PID. These files are then combined into datasets, which consequently represent all data (all variables) from a single simulation.

The already existing solution for aggregating files into datasets (and assigning a PID to these aggregates) can be extended to become conformant with the Collections API to enable standardized read access from third parties. Moreover, this could also stretch to cover an existing pilot implementation that enables end-users to bundle individual collections of datasets and get a referenceable PID for these bundles. 

#### 4.1.2 Climate data processing collection use case

An implementation scenario that is a continuation along a typical user workflow is the use of collections to aggregate data processing outputs. Some users will need to further process CMIP output, for example, to produce specific data subsets or to extract meaningful indices across multiple datasets. The required processing tools exist, for example COWS based on PyWPS<sup>http://cows.badc.rl.ac.uk/</sup>, the Birdhouse processing framework<sup>http://bird-house.github.io/</sup> developed at DKRZ, the Ophidia framework<sup>http://ophidia.cmcc.it/</sup> or the climate4impact.eu<sup>http://www.climate4impact.eu</sup> portal. It is planned that these tools are extended and improved to become common server-side services as part of future projects. 

Processing service output may be quite varied, but in case it does not consist of atomic output, the use of persistently identified collections will enable users to easily refer to the output of a processing call and possibly submit it as a whole to collection-aware data sharing services. Moreover, if processing input data are already bundled with collections, e.g. if it is part of CMIP6, the basic provenance relationship between the collections may be recorded through dedicated metadata.

### 4.2 Perseids Project

The Perseids Project provides a platform for creating, publishing, and sharing research data, in the form of textual transcriptions, annotations and analyses.  The platform itself uses a collection-centric data model, where each dataset produced on the platform is treated as a vertical collection of heterogeneous data objects. In addition, each item in a dataset can be thought of as belonging to one or more other global collections of data objects, grouped by data type, primary topic, community, or other criteria. 

For example, User A, a member of Community B, creates a dataset that include a data object which is treebank (<sup>https://en.wikipedia.org/wiki/Treebank</sup>) of a set of passages from a canonically identified text, Homer's Iliad Book 1, lines 1-10.  Community B has editorial process which enables annotations from members of the community to pass through a peer review process before publication. This data object might belong to:

* the collection of all Ancient Greek treebank data
* the collection of all annotations about Homer's Iliad
* the collection of all annotations about Book 1 Lines 1 through 10 of Homer's Iliad
* the collection of all data created by User A
* the collection of all data approved by the Community B editorial board

As an open platform, we want all data we produce to be easily shared and reused by the larger community, at all stages of the publication lifecycle.  Our requirements call for each data object, as well as the collections themselves, to be able to be persistently identified, versioned, carry fine-grained provenance metadata and be validated against a profile, schema or other verifiable criteria. To facilitiate reuse, we must be able to:

* describe collection items as machine-actionable data types, independent of their identifier schemes, and the properties of the collection to which they belong.
* create reusable templates of collection types with standard descriptive properties and capabilities
* express relationships between collections, items within a collection, and items across collections using one or more standard ontologies
* perform simple CRUD/L operations on collections and items in a collection
* perform more complex discovery operations on collections based upon the properties of individual collection items, such finding all items across all collections that match or don't match or contain a specific item. 


## 5. Models

| Trait: | * | |
|----:|----:|:----|
| | Property (optional): | duplicates allowed [bool] |
| |Operation: | Get iterator : iterator<PID>
| | Operation: | Contains (PID) : bool |
| | Operation: | Assemble clone action : action |
| | Operation: | Search (… criteria …) : list of PIDs |
| | Operation: | Create view (… criteria …) : View identifier |
| | Operation: | Retrieve view (View identifier) : list of PIDs |

|Trait: |Extendable| |
|----:|----:|:----|
| |Property (mandatory): |read-only [bool=False]|
| |Property (optional): |Exclusive membership policy [bool]|
| |Operation: |Add item (PID)|

|Trait: |Ordered| |
|----:|----:|:----|
| |Property (mandatory): |ordered [bool=True]|
| |Operation: |Get item (index) : PID|
| |Operation: |Get slice (start index, end index) : list of PIDs|

|Trait: |Insertions allowed| (extends Extendable+Ordered)|
|----:|----:|:----|
| |Property (mandatory): |middle inserts [bool=True]|
| |Operation: |Insert item before (PID, index)|
| |Operation: |Insert item after (PID, index)|

|Trait: |Shrinkable| |
|----:|----:|:----|
| |Property (optional): |no deletes [bool=True]|
| |Operation: |Remove item (PID) : bool|

|Trait: |Shrinkable+Ordered| |
|----:|----:|:----|
| |Operation: |Remove via index (index)|

|Trait: |Finite| |
|----:|----:|:----|
| |Operation: |Calculate total size : int|

|Trait: |Finite+Hierarchical| |
|----:|----:|:----|
| |Operation: |Calculate maximum depth : int|
| |Operation: |Calculate number of direct children : int|

|Trait: |Rule-based| |
|----:|----:|:----|
| |Property (mandatory): |rule based [bool=True]|


## 6. Permission Management

We expect implementations of the Collections API to have differing requirements and solutions for enforcing access on collections and their member items. The API specification does not presume anything about the mechanism through which access control is enforced, but allows the implentation to declare whether whether or not it enforces access via a Service feature property.  

The OpenAPI specification <sup>(https://swagger.io/specification)</sup> we have used to document the API provides the means through which an implementation can specify a SecurityScheme <sup>(https://swagger.io/specification/#securitySchemeObject></sup> for individual API operations. This supports standard OAuth2 workflows, as well as basic authentication and api keys.  The Collection API also specifies use of the standard HTTP 401 response code for unauthorized requests on any operations wihch might be subject to access controls.

In addition to service and operation level access controls, the API enables the declaration of whether an individual collection itself has access restrictions, and the license and ownership of the collection, via Collection level properties. 

For information on how to implement authentication and authorization solutions, we recommend turning to Single Sign On standards such as OAuth2 <sup>(https://oauth.net/2/)</sup>, Shibboleth <sup>(https://shibboleth.net/)</sup> and SAML <sup>(https://en.wikipedia.org/wiki/SAML_2.0)</sup>. 

## 7. Capabilities
Collection capabilities specify the actions that are possible for a specific collection and thereby also tell a user which actions are impossible in the particular case.
These metadata are essential to work with a collection and therefore must be easily accessible by an implementation.

It should not be required for every implementation to build upon the PID Types API and the DTR.
But in case these are supported, an example for such “allowed actions” would be that the collection only supports items who are of a specific data type X, as expressed by a type ID, or which, in addition, conform to a specific PIT profile (which requires a concrete minimal set of metadata to be included with the item).

## 8. API

[http://rdacollectionswg.github.io/apidocs/#/](http://rdacollectionswg.github.io/apidocs/#/)

The API should enable advanced functionality (e.g. search), to be built by consumers of it, but not all such functionality is in scope for the API itself.

## 9. Implementation

## 10. Conclusion

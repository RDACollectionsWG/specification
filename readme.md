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

## 3. Requirements
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

|Trait: |Hierarchical| |
|----:|----:|:----|
| |Property (optional): |depth limit [int]|
| |Operation: |Assemble powercollection : Virtual collection descriptor|

|Trait: |Finite+Hierarchical| |
|----:|----:|:----|
| |Operation: |Calculate maximum depth : int|
| |Operation: |Calculate number of direct children : int|

|Trait: |Rule-based| |
|----:|----:|:----|
| |Property (mandatory): |rule based [bool=True]|


## 6. Permission Management
### 6.1 Authentication
Authentication can be done by using an existing Single-Sign-On solution (Shibboleth, B2Access etc).
As an alternative, an implementation can create its own user authentication.

### 6.2 Authorization
Authorization has to be done by an API implementation.
An implementation should at a minimum offer the following roles and permissions.
Implementations may choose to add more roles and permissions as required. 
The following roles exist:

1. "owner", indicating the users primarily responsible for a collection.
2. "authenticated" represents every authenticated user
3. "anonymous" represents unauthenticated users

Every collection can have multiple users for each of these roles, including the possibilities to have multiple owners for a collection.
An anonymous user will automatically hold the anonymous role.
There is no administrator role; administrator rights overriding all permissions may be implicitly given by the implementation aside from this authorization concept.

Every collection specifies three permissions for every role: "read", "write" and "modify permissions".
These permissions only refer to collection operations, object operations are independent from them.
For example, write permissions on a collection do not imply that the content of member objects can be changed.
Each collection namespace has a default setting specifying default permissions for every role. A common default may be as follows:

|     | Read | Write | Change permission |
|-----|:----:|:-----:|:-----------------:|
|Owner| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
|Authenticated| :heavy_check_mark: |  |  |
|Anonymous|  |       |                   |

A common variant may be to also provide anonymous users with read access by default.

Additional comments:

  When a new collection is created, the requesting user is set up within its owner role.
  The owner role's permissions are set to the namespace default.

  **Authorization is always set on the level of a single collection, it is not automatically recursive.
  Every collection has individual permissions.**
  An implementation may choose, upon collection creation, to create subcollections with their super collection's permissions, thereby overriding the namespace defaults.
  This should only happen once on creation and not upon permission changes afterwards.
  In addition, an implementation may offer a flag that activates such a mechanism for any individual collection, with the flag also being copied upon collection creation.

  Ownership transfer is possible by adding more users with the owner role and removing others from it.
  An implementation should refuse a permission change request that removes the last user from the owner role.

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

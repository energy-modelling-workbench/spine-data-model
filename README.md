# spine-data-model
Data model for Spine compatible data. _**Work in Progress!**_

This data model description contains two major parts. First, different elements of the data model are introduced one by one. Second, formal properties of each data element are described. This is a description of Spine data model - Spine Toolbox implements the data model and serves as a reference implementation.

## Purpose of the Spine data model

Spine data model serves use cases where the data has graph-like structure, contains complex data types (e.g. multi-dimensional time series data), there is a need for alternative data values (e.g. to build scenarios) and the data should be easily convertible into different formats. This is often the case when modelling something complex.

## Introduction to the data model

Spine data model is an [entity-attribute-value](https://en.wikipedia.org/wiki/Entity–attribute–value_model) with classes and relationships (EAV/CR) plus some further additions (mainly to handle complex data and alternative values for data).

The first data element is the `entity_class`. Entity classes divide the data into categories that can reflect e.g. physical differences (for example *bird* and *country*). Entity classes can also be multi-dimensional (*country__bird*), but more on that later.

Next there is the `entity`, which are instances of `entity_class`. Entities need a unique name within the entity_class. There can be different entity types, that will be explained later. Entities can hold history - old entities can stay in the database, but will not be retrieved unless specifically requested. Entity can have location data, more on that below.

Entity classes have `parameter_definition`s (same as 'Attribute' in EAV). Parameter definitions define the possible parameters for an `entity` of a particular `entity_class`. There can be rules that restrict the format of the parameter, see below. One option is to have an enumerated list of possible values from a `parameter_value_enum`. Parameters can have a default value, which can be used when no value is given (not automatically - only if the application using the data model fetches and uses them), and/or a prefill value, that suggests a value when entering data manually. 

As implied, an `entity` can then have `parameter_value`s based on the `parameter_definition`s. The parameter type restrictions from the `parameter_definition` will naturally limit valid parameter values. Parameter values can have a history - old values for a parameter can remain in the database, but they can be ignored by default and only the latest value is retrieved.

Data is often uncertain and it's therefore important to be able to define `alternative`s for `parameter_value`s and also to be able to have different versions of the same `entity`. An alternative is its own data element, which means that the same alternative can be used for multiple parameters (of multiple entities) and multiple entities. Every parameter value needs to have one alternative. It can make sense to have e.g. *base* alternative to hold the starting point for the analysis. Unlike parameter values, every entity does not have to be in an alternative and they can be in multiple alternatives. In fact, alternative can either **add** or **remove** the existence of the entity.

`alternative`s can be combined to form a `scenario`, which can be used by applications to have a consistent dataset capturing a particular instance from the parameter (and entity) uncertainty landscape. Alternatives and scenarios help to avoid replicating data (without them one would need to have copies of entities to hold the alternative parameter values or even separate complete datasets for every scenario, which means a lot of data replication).

The data model supports metadata. The first form of that is a `commit` message, which can be applied when adding a batch of data to a data container. It is not meant for structural elements like an `entity_class`, but for elements that are data instances, like an `entity` or a `parameter_value`. The second form of metadata is a `metadata_content` element, which needs to follow Spine metadata guidelines. Each `metadata_content` can be linked with multiple entities and/or parameter values, so that metadata does not need to be unnecessarily repeated. This also means that one entity (or parameter value) can have multiple metadata content entries.

![image](https://github.com/energy-modelling-workbench/spine-data-model/assets/40472544/23f0c9e4-4064-4345-985b-55b176947e47)

## Data model definition

### entity_class

|column  |description|data format|
|----|----|----|
|id      |unique index  |integer  |
|rank    |number of dimensions  |integer  |
|name    |unique name      |string  |
|description|description of the entity class|string |
|type|entity class type|enum|
|display icon|character code for font awesome|unicode string |

Entity class type is an enumerated list with following options:
- regular: holds entities with no special meaning
- group: 

### dimension

|column  |description|data format|
|----|----|----|
|id|unique index|integer|
|name|dimension name|string|

### entity_class__dimension

When entity_classes have multiple dimensions, this table defines the order of the dimensions and ensureness uniqueness of [entity_class_id, dimension_id, position].

|column  |description|data format|
|----|----|----|
|id      |unique index  |integer  |





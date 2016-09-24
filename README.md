
========================================================================
 MongoDB Commands
========================================================================

### How to easily run multi-line queries using 'VIM'

_type in Mongo shell_

    EDITOR = 'vim'
    edit query

_inside VIM editor, copy-paste query/function_
_type ':x' to save and exit VIM editor_

    db.sampleCollection.aggregate(query)


========================================================================

### Find ALL records

    db.collectionName.find({}).pretty()


### Filter ALL records based on field value(s)

    db.collectionName.find({}, {
        fieldName1 : value1,
        fieldName2 : value2
    }).pretty()


### Find ONE record

    db.collectionName.findOne()


### Find DISTINCT field values

    db.sampleCollection.distinct("fieldName")


### Find DISTINCT nested field values

    db.sampleCollection.distinct("fieldName.nestedField")


========================================================================

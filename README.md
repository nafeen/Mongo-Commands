
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

### Find ALL records BETWEEN two dates

    begin_date_range = "2016-09-20T00:00:00Z";
    end_date_range = "2016-09-21T00:00:00Z";

    db.collectionName.find(
        { 
            "fieldName": 
            {
        "$gte":begin_date_range,
        "$lt":end_date_range
      }
    }).pretty();


========================================================================

### Find all fields in a collection

_using map-reduce_

    mr = db.runCommand({
        "mapreduce" : "collectionName",
        "map" : function() {
            for (var key in this) { emit(key, null); }
        },
        "reduce" : function(key, stuff) { return null; },
        "out": "fieldName" + "_keys"
    });

    db[mr.result].distinct("fieldName");


========================================================================

### Count distinct of all occurances of a particular field

    db.collectionName.aggregate([
        {
          $match: {
              keywords: { $not: {$size: 0} }
          }
        },
        { 
            $unwind: "$fieldName" 
        },
        {
            $group: {
                _id: {$toLower: '$fieldName'},
                count: { $sum: 1 }
            }
        },
        {
            $match: {
                count: { $gte: 2 }
            }
        },
        { $sort : { count : -1} },
        { $limit : 100 }
    ]);


========================================================================

### Finding aggregate of metrics after grouping a field 

_analogous to SQL GROUP BY_

    db.collectionName.aggregate([{
        $group: {
            "_id" : "$fieldToGroupUnder",
            aggregateField1: { 
                $sum: "$fieldName1"
            },
            aggregateField2: { 
                $sum: "$fieldName2.subFieldName"
            },
        count: { $sum: 1 }
        }
    }]).pretty();


========================================================================

### Join + Filter + Select

    db.collectionName1.aggregate([
        {
            $match : {
                "fieldToFilter" : "filterValue"         // filter
            }
        },
        {
            $lookup : {                                 // Left-outer Join
                from : "collectionName2",               // joining collection
                localField : "fieldFromCollection1",    // joining field
                foreignField : "fieldFromCollection2",  // joining field
                as : "joinFieldLabel"                   // label for result
            }
        },
        {
            $project : {                                // FILTER result set
                "_id" : 0,
                "filterValue" : 1,
                "fieldName1" : 1,
                "fieldName2.subFieldName" : 1
            }
        }
    ])


========================================================================

### Projecting all data and some custom fields with same query

    db.collectionName.aggregate([
      { 
        $project: {
            data : "$$ROOT",            // this prints out all fields
            customField: {
                $concat : [ "$fieldName1", "-", "$fieldName2" ]
            } 
        } 
        }, 
      { 
        $match : { 
            fieldName2 : "fieldValue"
        } 
      }
    ]).pretty();


========================================================================

### Count of documents between TWO dates using ObjectId

    var objIdMin = ObjectId(Math.floor((new Date('2016/1/7'))/1000).toString(16) + "0000000000000000");
    
    var objIdMax = ObjectId(Math.floor((new Date('2016/8/25'))/1000).toString(16) + "0000000000000000");

    db.collectionName.aggregate([
        {
            $match : {
                "_id" : {
                    $gt: objIdMin, 
                    $lt: objIdMax
                }
            }
        },
        {
            $group : {
                _id : null,
                count: { $sum: 1 }
            }
        }
    ])


========================================================================




<properties
	pageTitle="PowerApps: Patch function"
	description="Reference information for the Patch function in PowerApps, including syntax and examples"
	services="powerapps"
	documentationCenter="na"
	authors="gregli-msft"
	manager="dwrede"
	editor=""
	tags=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/21/2015"
   ms.author="gregli"/>

# Patch function in PowerApps #

The Patch function merges multiple records.  You can also use this function with a data source to create or modify a record. 

## Description ##

### Without a data source ###

In its simplest form, Patch takes two arguments – an original record and a change record.  The function compares the columns in each of these records.  If a column appears in only one record, it is passed through to the result unmodified.  If a column appears in both records, the value in the change record overrides the original record’s value in the result.  You can provide multiple change records; values in records later on the argument list override values from earlier change records.

### With a data source ###

When you use this function with a data source, the original record is found within the data source and modified.  For more information see [Working with Data Sources](file-name.md).

If the original record does not contain a [Primary Key](file-name.md), a record is created within the data source.  The [Defaults](function-defaults.md) function returns a record without primary keys that can be useful for this purpose.  Some Data Sources will automatically generate a Primary Key when a record is created, in which case the value of the Primary Key will be available in the return value from Patch.  FIXIT

When used with a data source, the function will return [Blank](file-name.md) if a primary key column is not provided and the column is not generated by the data source.  A data source may run into problems when patching a record, including conflict, validation, and permission issues.  The return value from Patch can be passed to the Errors Function to return detailed error information for the operation, even if the return value is Blank.

## Syntax ##

**Patch**( *OriginalRecord*, *ChangeRecord*, … )

**Patch**( *DataSource*, *OriginalRecord*, *ChangeRecord*, … )

 - DataSource – Optional. The Data Source in which to find Original Record.

- OriginalRecord – Required. The Record to be modified.

- ChangeRecord – Required.  One or more change records to apply to original record. Columns in records later in the parameter list take priority over earlier records.  FIXME

## Examples ##

#### Used without a Data Source ####
| Formula                                 | Description                                                                                                                                           | Result              |
|-----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------|
| Patch( { a: 1, b: 2 }, { a: 3, c: 4 } ) | Modifies the record {a: 1, b: 2} with changes from the record {a: 3, c: 4}.  Columns are merged, with conflicting columns favoring the change record. | { a: 3, b:2, c: 4 } |
|                                         |                                                                                                                                                       |                     |
| Patch( { a: 1, b: 2 }, {a: 3}, {a: 5} ) | Multiple change records are applied, with later change records (read left to right) on the parameter list overriding earlier change records.          | { a: 5, b: 2 }      |

### Used with a Data Source (Primary Key Auto Generated)

#### IceCreams Data Source ####

| Flavor | Quantity | ID (Primary Key, auto generated by data source) |
|--------------|-----------------|------------------------------------------------|
| Chocolate | 100 | 1 |
| Vanilla | 200 | 2 |

| Formula                                 | Description                                                                                                                                           | Result              |
|--------------|-----------------------------------|---------------------|
| Patch(&nbsp;IceCreams, {&nbsp;ID:&nbsp;1&nbsp;}, {&nbsp;Quantity:&nbsp;400&nbsp;} ) | Locates Chocolate’s record in the IceCreams Data Source (by Primary Key) and modifies the Quantity to 400. | {&nbsp;ID: 1, Quantity: 400 } <br><br>Errors( IceCreams, {&nbsp;ID:&nbsp;1,&nbsp;Quantity:&nbsp;400&nbsp;} ) returns Blank |
| Patch( IceCreams, {}, {&nbsp;Flavor:&nbsp;“Strawberry”&nbsp;} ) | Since ID is not supplied, creates a new record in the IceCreams Data Source.  A new primary key of ID=3 is automatically generated by the DataSource.  The Quantity column is left Blank. | { ID: 3, Flavor: “Strawberry” } <br><br> Errors( IceCreams, { ID: 3, Flavor: “Strawberry” } ) returns Blank |

### Used with a Data Source (Primary Key not Auto Generated)

#### Scores Data Source ####

| Name (Primary Key) | Score |
|--------------|-----------------|
| Fred | 100 |
| Jane | 120 |

| Formula                                 | Description                                                                                                                                           | Result              |
|-----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------|
| Patch( Scores, { Name: “Fred” }, { Score: 30 } ) | Locates Fred’s record in the Scores Data Source and modifies the Score to 30.  | { ID: 1, Quantity: 400 } <br><br>Errors( IceCreams, { ID: 1, Quantity: 400 } ) returns Blank |
| Patch( Scores, {}, { Name: “”, Score: 50 } ) | Since ID is not supplied, creates a new record in the IceCreams Data Source.  A new primary key of ID=3 is automatically generated by the DataSource.  The Quantity column is left Blank. | { ID: 3, Flavor: “Strawberry” } <br><br> Errors( IceCreams, { ID: 3, Flavor: “Strawberry” } ) returns Blank |


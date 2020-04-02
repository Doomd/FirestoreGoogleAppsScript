# Firestore for Google Apps Scripts (DOOMD'S FORK)
>***DOOMD'S NOTE: THIS IS A FORK!*** I've created this fork out of necessity as the original author seems to be busy and not able to address some issues. This fork is almost entirely unaltered from the original except for some very minor corrections or bug fixes. I will also endevour to write more comprehensive documentation. The following script/library id (as mentioned in the "Installation" section below), is MINE (ie, this fork's), not the original's. You're welcome to use my version, but I offer NO warranties or support...That said, I do enjoy helping people...so, feel free to message me on [telegram](https://t.me/doomd) if you're stuck...*[FSM](https://www.spaghettimonster.org/) knows I've had my fair share of problems figuring out this library.*
### A Google Apps Script library for accessing Google Cloud Firestore.

This library allows a user (or service account) to authenticate with Firestore and edit their Firestore database within a Google Apps Script.

Read how this project was started [here](http://grahamearley.website/blog/2017/10/18/firestore-in-google-apps-script.html).

## Installation (DOOMD FORK March 25 2020)
In the Google online script editor, select the `Resources` menu item and choose `Libraries...`. In the "Add a library" input box, enter:
```
1eYspHvBlx37akwhXhtBLdc_tnzlIFD_LFlCGMOPPqgFGdSkV9eZXQ6dk
```
and click "Add." Choose the most recent version number.


## Quick start
#### Creating a service account
The easiest way to use this library is to create a Google Service Account for your application and give it read/write access to your datastore. Giving a service account access to your datastore is like giving access to a user's account, but this account is strictly used by your script, not by a person.

If you don't already have a Firestore project you want to use, create one at the [Firebase admin console](https://console.firebase.google.com).

To make a service account,
1. Open the [Google Service Accounts page by clicking here](https://console.developers.google.com/projectselector/iam-admin/serviceaccounts).
2. Select your Firestore project, and then click "Create Service Account."
3. For your service account's role, choose `Datastore > Cloud Datastore Owner`.
4. Check the "Furnish a new private key" box and select JSON as your key type.
5. When you press "Create," your browser will download a `.json` file with your private key (`private_key`), service account email (`client_email`), and project ID (`project_id`). Copy these values into your Google Apps Script — you'll need them to authenticate with Firestore.

#### Create a test document in Firestore from your script
Now, with your service account client email address `email`, private key `key`, and project ID `projectId`, we will authenticate with Firestore to get our `Firestore` object. To do this, get the `Firestore` object from the library:

```javascript
var firestore = FirestoreApp.getFirestore(email, key, projectId);
```

Using this Firestore instance, we will create a Firestore document with a field `name` with value `test!`. Let's encode this as a JSON object:

```javascript
const data = {
  "name": "test!"
}
```

We can choose to create a document in collection called `FirstCollection` without an ID:

```javascript
firestore.createDocument("FirstCollection", data)
```

Alternatively, we can create the document in the `FirstCollection` collection called `FirstDocument`:
```javascript
firestore.createDocument("FirstCollection/FirstDocument", data)
```
### Update a Document with no mask (default update behavior)
To completely overwrite an existing document's contents (clear out all fields and values, and insert any new fields/values you include in the data object), we can use the `updateDocument` function with the (optional) FALSE parameter:
```javascript
firestore.updateDocument("FirstCollection/FirstDocument", data, FALSE)
firestore.updateDocument("FirstCollection/FirstDocument", data) // same result
```
### Update a Document with a Mask
To update the document with new fields or with new data (without overwriting any existing field values unless they are included in the update object) at this location, we can use the `updateDocument` function with the TRUE (Mask) parameter:
```javascript
firestore.updateDocument("FirstCollection/FirstDocument", data, TRUE)
```
**Note:** Although you can call `updateDocument` without using `createDocument` to create the document, any documents in your path will not be created and thus you can only access the document by using the path explicitly.

You can retrieve your data by calling the `getDocument` function:

```javascript
const dataWithMetadata = firestore.getDocument("FirstCollection/FirstDocument")
```

You can also retrieve all documents within a collection by using the `getDocuments` function:

```javascript
const allDocuments = firestore.getDocuments("FirstCollection")
```

If more specific queries need to be performed, you can use the `query` function followed by an `execute` invocation to get that data:

```javascript
const allDocumentsWithTest = firestore.query("FirstCollection").where("name", "==", "Test!").execute()
```


# Method Documenation
To get an instance of the Firestore object for your project from this library, call
```javascript
var firestore = FirestoreApp.getFirestore(email, key, projectId);
```

The following are methods that belong to the Firestore object.

**Library version:** 22

***
<!-- Generated by documentation.js. Update this documentation by updating the source code. -->

### Table of Contents

-   [getFirestore][1]
    -   [Parameters][2]
-   [Firestore][3]
    -   [Parameters][4]
    -   [getDocument][5]
        -   [Parameters][6]
    -   [getDocuments][7]
        -   [Parameters][8]
    -   [getDocumentIds][9]
        -   [Parameters][10]
    -   [createDocument][11]
        -   [Parameters][12]
    -   [updateDocument][13]
        -   [Parameters][14]
    -   [query][15]
        -   [Parameters][16]
    -   [deleteDocument][17]
        -   [Parameters][18]

## getFirestore

Get an object that acts as an authenticated interface with a Firestore project.

### Parameters

-   `email` **[string][20]** the user email address (for authentication)
-   `key` **[string][20]** the user private key (for authentication)
-   `projectId` **[string][20]** the Firestore project ID

Returns **[object][21]** an authenticated interface with a Firestore project

## Firestore

An object that acts as an authenticated interface with a Firestore project.

### Parameters

-   `email` **[string][20]** the user email address (for authentication)
-   `key` **[string][20]** the user private key (for authentication)
-   `projectId` **[string][20]** the Firestore project ID

Returns **[object][21]** an authenticated interface with a Firestore project

## getDocument

Get a document.

#### Parameters

-   `path` **[string][20]** the path to the document

Returns **[object][21]** the document object

## getDocuments

Get a list of all documents in a collection.

#### Parameters

-   `path` **[string][20]** the path to the collection

Returns **[object][21]** an array of the documents in the collection

## getDocumentIds

Get a list of all IDs of the documents in a path

#### Parameters

-   `path` **[string][20]** the path to the collection

Returns **[object][21]** an array of IDs of the documents in the collection

## createDocument

Create a document with the given fields and an auto-generated ID.

#### Parameters

-   `path` **[string][20]** the path where the document will be written
-   `fields` **[object][21]** the document's fields

Returns **[object][21]** the Document object written to Firestore

Example of Creating a Document & Using the return object:
```
function testFSCreate() {
    const data = {
        name: "Doomd",
        date_created: new Date(),
        og_field: "original data"
    }
    let result = firestore.createDocument("testcollection", data);
    let uid = result.name.match(/[^\/]+$/);
    let ts_created = result.createTime;
    let ts_modified = result.updateTime;

    console.log(`${data.name}'s uid is ${uid}, and it was created at ${ts_created}`);
}
```

## updateDocument

Update/patch a document at the given path with new fields.

#### Parameters

-   `path` **[string][20]** the path of the document to update.
                         If document name not provided, a random ID will be generated.
-   `fields` **[object][21]** the document's new fields
-   `mask` **[boolean][22]** if true, the update will use a mask

Returns **[object][21]** the Document object written to Firestore

Example of updating one of the documents we just created without overwriting fields we aren't updating. Notice the 3rd parameter for the mask (set to TRUE):
```
function testFSUpdate() {
    initFirestore();
    let doc = "UzcEL8jDVq4X8su39Qw8"; // Use YOUR uid instead.
    let data = {
        new_field: "cool",
        og_field: "new data"
    }
    firestore.updateDocument("testcollection/" + doc, data, TRUE);
}
```
## query

Run a query against the Firestore Database and
 return an all the documents that match the query.
Must call .execute() to send the request.

#### Parameters

-   `path` **[string][20]** to query

Returns **[object][21]** the JSON response from the GET request

## deleteDocument

Delete the Firestore document at the given path.
Note: this deletes ONLY this document, and not any subcollections.

#### Parameters

-   `path` **[string][20]** the path to the document to delete

Returns **[object][21]** the JSON response from the DELETE request

[1]: #getfirestore

[2]: #parameters

[3]: #firestore

[4]: #parameters-1

[5]: #getdocument

[6]: #parameters-2

[7]: #getdocuments

[8]: #parameters-3

[9]: #getdocumentids

[10]: #parameters-4

[11]: #createdocument

[12]: #parameters-5

[13]: #updatedocument

[14]: #parameters-6

[15]: #query

[16]: #parameters-7

[17]: #deletedocument

[18]: #parameters-8

[20]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String

[21]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object

[22]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean

### Breaking Changes
* v16: **Removed:** `createDocumentWithId(documentId, path, fields)`
  > Utilize `createDocument(path + '/' + documentId, fields)` instead to create a document with a specific ID.

## Contributions
Contributions are welcome — send a pull request! This library is a work in progress. See [here](https://github.com/grahamearley/FirestoreGoogleAppsScript/blob/master/CONTRIBUTING.md) for more information on contributing.

After cloning this repository, you can push it to your own private copy of this Google Apps Script project to test it yourself. See [here](https://github.com/google/clasp) for directions on using `clasp` to develop App Scripts locally.

If you want to view the source code directly on Google Apps Script, where you can make a copy for yourself to edit, click [here](https://script.google.com/d/1VUSl4b1r1eoNcRWotZM3e87ygkxvXltOgyDZhixqncz9lQ3MjfT1iKFw/edit?usp=sharing).

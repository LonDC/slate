---
title: LDC Via API Reference

language_tabs:
  - java
  - javascript

toc_footers:
  - <a href='http://ldcvia.com'>Sign Up for a trial account</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the LDC Via API! You can use our API to access LDC Via API endpoints, this allows you to access and modify documents within databases in your organisation.

We have language bindings in Java and JavaScript (jQuery)! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

# Authentication

> To authorize, always pass the 'apikey' header:

```java
package com.ldcvia.rest;

import java.io.IOException;
import java.io.Serializable;
import java.util.ArrayList;
import java.util.Collections;

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.ResponseHandler;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

import com.ibm.commons.util.io.json.JsonException;
import com.ibm.commons.util.io.json.JsonJavaArray;
import com.ibm.commons.util.io.json.JsonJavaFactory;
import com.ibm.commons.util.io.json.JsonJavaObject;
import com.ibm.commons.util.io.json.JsonParser;
import com.ibm.xsp.extlib.util.ExtLibUtil;

/**
 * Get a list of databases that the current user has access to
 *  
 * @return
 * @throws ClientProtocolException
 * @throws IOException
 * @throws JsonException
 */
public JsonJavaArray getDatabases() throws ClientProtocolException, IOException, JsonException{
	String responseBody = loadURL("/1.0/databases");
	JsonJavaFactory factory = JsonJavaFactory.instanceEx;
	JsonJavaObject json = (JsonJavaObject) JsonParser.fromJson(factory, responseBody);

	return json.getAsArray("databases");
}

/**
 * Helper method to request a URL from the LDC Via service
 * @param url
 * @return
 * @throws ClientProtocolException
 * @throws IOException
 */
private String loadURL(String url) throws ClientProtocolException, IOException {
	CloseableHttpClient httpclient = HttpClients.createDefault();
	HttpGet httpget = new HttpGet(this.baseurl + url);

	// Create a custom response handler
	ResponseHandler<String> responseHandler = new ResponseHandler<String>() {

		public String handleResponse(final HttpResponse response) throws ClientProtocolException, IOException {
			int status = response.getStatusLine().getStatusCode();
			if (status >= 200 && status < 300) {
				HttpEntity entity = response.getEntity();
				return entity != null ? EntityUtils.toString(entity) : null;
			} else {
				throw new ClientProtocolException("Unexpected response status: " + status);
			}
		}

	};
	httpget.addHeader("apikey", "MYSECRETAPIKEY");
	return httpclient.execute(httpget, responseHandler);
}

```

```javascript
$.ajax({
    dataType: 'json',
    type: 'GET',
    headers: { 'apikey': 'MYSECRETAPIKEY' },
    url: '/1.0/databases',
    success: function(data){
      //Do something
    }
  });
```

> Make sure to replace `MYSECRETAPIKEY` with your API key.

Each user in the application is assigned an API Key when they are registered. This key is unique and should be considered a secret (i.e. do not share your key with anyone else). Your API Key is required for all interactions with the API, without the key, or with an incorrect key you will not be able to access the API.

`apikey: INSERTYOURAPIKEYHERE`

<aside class="notice">
You must replace `INSERTYOURAPIKEYHERE` with your personal API key.
</aside>

# Databases

This is probably the simplest API method that we offer, it provides a simple list of databases that a user has access to.

## Get All Databases

```java
/**
 * Get a list of databases that the current user has access to
 *  
 * @return
 * @throws ClientProtocolException
 * @throws IOException
 * @throws JsonException
 */
public JsonJavaArray getDatabases() throws ClientProtocolException, IOException, JsonException{
	String responseBody = loadURL("/1.0/databases");
	JsonJavaFactory factory = JsonJavaFactory.instanceEx;
	JsonJavaObject json = (JsonJavaObject) JsonParser.fromJson(factory, responseBody);

	return json.getAsArray("databases");
}

/**
 * Helper method to request a URL from the LDC Via service
 * @param url
 * @return
 * @throws ClientProtocolException
 * @throws IOException
 */
private String loadURL(String url) throws ClientProtocolException, IOException {
	CloseableHttpClient httpclient = HttpClients.createDefault();
	HttpGet httpget = new HttpGet(this.baseurl + url);

	// Create a custom response handler
	ResponseHandler<String> responseHandler = new ResponseHandler<String>() {

		public String handleResponse(final HttpResponse response) throws ClientProtocolException, IOException {
			int status = response.getStatusLine().getStatusCode();
			if (status >= 200 && status < 300) {
				HttpEntity entity = response.getEntity();
				return entity != null ? EntityUtils.toString(entity) : null;
			} else {
				throw new ClientProtocolException("Unexpected response status: " + status);
			}
		}

	};
	httpget.addHeader("apikey", "MYSECRETAPIKEY");
	return httpclient.execute(httpget, responseHandler);
}
```

```javascript
var root = 'https://example.com';
  var apikey = 'MYSECRETAPIKEY';
  $.ajax({
    dataType: 'json',
    type: 'GET',
    url: root + '/1.0/databases',
    data: data,
    headers: {
      'apikey': apikey
    },
    success: function(res) {
      console.log(res);
    }
  })
```
> The above command returns JSON structured like this:

```json
{
  "databases":[
    "acme-com-discussion",
    "acme-com-discussion2"
  ]
}
```

This endpoint retrieves all databases that you have access to.

### HTTP Request

`GET http://example.com/api/1.0/databases`

## Delete Entire Database

Use this method to delete a database. You must be a super user to perform this operation, otherwise you will receive a 401 error.

```java
//TODO: We need a sample here
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'DELETE',
  headers: { 'apikey': apikey },
  url: '/1.0/databases/' + database,
  success: function(res){
    //do something
  },
  error: function(xhr, status, error){
    if (xhr.status == 401){
      alert("You do not have the rights to delete the database");
    }else{
      alert(status + '\n' + error);
    }
  }
});
```

> If you can delete the database you will see:

```json
{
  "result": "ok"
}
```

### HTTP Request
`DELETE https://example.com/1.0/collections/:database/:collectionname`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service


# Collections

## Get All Collections in a Database
Given a database name, this method will return a list of all collections in the database along with a count of documents in each collection.

The counts of the documents have document level security applied to them, so if there are 100 documents in the collection but the user can see only 10, then the count will be 10.

```java
/**
 * Gets a list of collections for the given database
 * Each element contains two properties: collection and count. Count is relative to the api key used to get the collection list
 * @param dbname
 * @return
 * @throws ClientProtocolException
 * @throws IOException
 * @throws JsonException
 */
@SuppressWarnings("unchecked")
public ArrayList getCollections(String dbname) throws ClientProtocolException, IOException, JsonException{
	String responseBody = loadURL("/1.0/collections/" + dbname);

	JsonJavaFactory factory = JsonJavaFactory.instanceEx;
	ArrayList list = (ArrayList) JsonParser.fromJson(factory, responseBody);
	Collections.sort(list, new CollectionComparator());
	return list;
}

/**
 * Helper method to request a URL from the LDC Via service
 * @param url
 * @return
 * @throws ClientProtocolException
 * @throws IOException
 */
private String loadURL(String url) throws ClientProtocolException, IOException {
	CloseableHttpClient httpclient = HttpClients.createDefault();
	HttpGet httpget = new HttpGet(this.baseurl + url);

	// Create a custom response handler
	ResponseHandler<String> responseHandler = new ResponseHandler<String>() {

		public String handleResponse(final HttpResponse response) throws ClientProtocolException, IOException {
			int status = response.getStatusLine().getStatusCode();
			if (status >= 200 && status < 300) {
				HttpEntity entity = response.getEntity();
				return entity != null ? EntityUtils.toString(entity) : null;
			} else {
				throw new ClientProtocolException("Unexpected response status: " + status);
			}
		}

	};
	httpget.addHeader("apikey", "MYSECRETAPIKEY");
	return httpclient.execute(httpget, responseHandler);
}
```

```javascript
var root = 'https://example.com';
var apikey = 'MYSECRETAPIKEY';
$.ajax({
  dataType: 'json',
  type: 'GET',
  url: root + '/1.0/collections/' + dbname,
  headers: {
    'apikey': apikey
  },
  success: function(res) {
    console.log(res);
  }
})
```
> The above command returns JSON structured like this:

```json
[
  {
    "collection":"Response",
    "count":5
  },
  {
    "collection":"ResponseToResponse",
    "count":7  
  },
  {
    "collection":"MainTopic",
    "count":1
  }
]
```

### HTTP Request

`GET http://example.com/1.0/collections/:database`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service

## Get All Documents In a Collection

Description

```java
/**
 * Get a list of documents from a collection
 * Document level security applies
 * @param dbname
 * @param collection
 * @param position
 * @return
 * @throws ClientProtocolException
 * @throws IOException
 * @throws JsonException
 */
public JsonJavaArray getDocuments(String dbname, String collection, int position) throws ClientProtocolException, IOException, JsonException{
	String responseBody = loadURL("/1.0/collections/" + dbname + "/" + collection + "?start=" + position);

	JsonJavaFactory factory = JsonJavaFactory.instanceEx;
	JsonJavaObject json = (JsonJavaObject) JsonParser.fromJson(factory, responseBody);
	return json.getAsArray("data");
}
```

```javascript
var root = 'https://example.com';
var apikey = 'MYSECRETAPIKEY';
$.ajax({
  dataType: 'json',
  type: 'GET',
  url: root + '/1.0/collections/' + dbname + '/' + collectionname,
  headers: {
    'apikey': apikey
  },
  success: function(res) {
    console.log(res);
  }
})
```

> The above returns JSON structured like this:

```json
{
  "count":1,
  "data":[
    {
      "_id":"53c8cf7983217c4dd0a5580b",
      "Title":"This is the title",
      "Body":"LDC Via frees your Domino data to be used in other applications or to\r\nallow the sunsetting of your Domino infrastructure without losing the data\r\nand security that you have grown used to.\r\n\r\n(See attached file: API.xlsx)",
      "Body__parsed":"LDC Via frees your Domino data to be used in other applications or to\r\nallow the sunsetting of your Domino infrastructure without losing the data\r\nand security that you have grown used to.\r\n\r\n(See attached file: API.xlsx)",
      "readers":[ "CN=Matt White/O=Exhilarate", "CN=Fred Bloggs/O=FCL" ],
      "__unid":"C56DB3F14258431380257D190026303A",
      "__noteid":"10F2",
      "__created":"2014-07-18T06:57:07Z",
      "__modified":"2014-07-18T07:21:31Z",
      "__authors":"CN=Matt White/O=Exhilarate",
      "__form":"MainTopic",
      "_files":[ "API.xlsx" ]
    }
  ]
}
```

### HTTP Request
`GET https://example.com/1.0/collections/:database/:collectionname`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service
:collectionname | The name of the collection to get documents from

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
count | 30 | The number of documents to get data for.
start | 0 | The starting position in the view, use this to page through documents

### Response JSON

Property | Description
-------- | -----------
count | the total count of documents in the collection that the current user can see
data | an array of document objects. the size of this array will not exceed the count URL parameter even if there are more documents in the collection. You will need to page through the data to get it all.
_id | internal unique reference id
rich text fields | when accessed via a collection all formatting is removed and a plain text representation of the content is returned. For full access to the rich text load the relevant document. Rich Text fields always have a second "__parsed" version of the field that can be used for searching purposes, this allows binary data to be stored in the primary rich text field
system fields | fields prefixed with __ are system fields that relate to your original Domino data. If the document has file attachments associated with it, the names of the files can be found in the field _files


## Delete entire collection

To delete a collection, use this method. You must be a super user to perform this operation, or you will receive a 401 error.

```java
//TODO: We need sample code here
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'DELETE',
  headers: { 'apikey': apikey },
  url: '/1.0/collections/' + database + '/' + collection,
  success: function(res){
    getDBCollections(database);
  },
  error: function(xhr, status, error){
    if (xhr.status == 401){
      alert("You do not have the rights to delete the collection");
    }else{
      alert(status + '\n' + error);
    }
  }
})

```

> The above returns JSON structured like this:

```json
{
  "result": "ok"
}
```

### HTTP Request
`DELETE https://example.com/1.0/collections/:database/:collectionname`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service
:collectionname | The name of the collection to delete

## Search in a collection

To perform a search against a collection, you can submit queries to be run against one or more fields.

```java
//TODO: Sample code required
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'POST',
  headers: {
    'apikey': apikey
  },
  data: {
    "filters": [{
      "operator": "contains",
      "field": "FieldToSearch",
      "value": query
    },
    {
      "operator": "equals",
      "field": "From",
      "value": query
    }]
  },
  url: '/1.0/search/' + dbname + "/" + collectionname + "?count=10&start=" + start,
  success: function(res) {
    //Do something
  }
})
```

> The above returns JSON structured like this:

```json
{
  "count":1,
  "data":[
    {
      "_id":"53c8cf7983217c4dd0a5580b",
      "Title":"This is the title",
      "Body":"NSF2MongoDB frees your Domino data to be used in other applications or to\r\nallow the sunsetting of your Domino infrastructure without losing the data\r\nand security that you have grown used to.\r\n\r\n(See attached file: API.xlsx)",
      "readers":[
        "CN=Matt White/O=Exhilarate",
        "CN=Fred Bloggs/O=FCL"
      ],
      "__unid":"C56DB3F14258431380257D190026303A",
      "__noteid":"10F2",
      "__created":"2014-07-18T06:57:07Z",
      "__modified":"2014-07-18T07:21:31Z",
      "__authors":"CN=Matt White/O=Exhilarate",
      "__form":"MainTopic",
      "_files":[
        "API.xlsx"
      ]
    }
  ]
}
```

### HTTP Request
`POST https://example.com/1.0/collections/:database/:collectionname`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
count | 30 | The number of documents to get data for.
start | 0 | The starting position in the view, use this to page through documents

### Response JSON

Property | Description
-------- | -----------
count | the total count of documents in the collection that the current user can see
data | an array of document objects. the size of this array will not exceed the count URL parameter even if there are more documents in the collection. You will need to page through the data to get it all.
_id | internal unique reference id
rich text fields | when accessed via a collection all formatting is removed and a plain text representation of the content is returned. For full access to the rich text load the relevant document. Rich Text fields always have a second "__parsed" version of the field that can be used for searching purposes, this allows binary data to be stored in the primary rich text field
system fields | fields prefixed with __ are system fields that relate to your original Domino data. If the document has file attachments associated with it, the names of the files can be found in the field _files

# Documents

## Get a document

To get the full detail for a document, use this method.

```java
/**
 * Get full detail for an individual document
 * @param dbname
 * @param collection
 * @param unid
 * @return
 * @throws ClientProtocolException
 * @throws IOException
 * @throws JsonException
 */
public JsonJavaObject getDocument(String dbname, String collection, String unid) throws ClientProtocolException, IOException, JsonException {
	String responseBody = loadURL("/1.0/document/" + dbname + "/" + collection + "/" + unid);

	JsonJavaFactory factory = JsonJavaFactory.instanceEx;
	JsonJavaObject json = (JsonJavaObject) JsonParser.fromJson(factory, responseBody);
	return json;
}
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/document/' + dbname + "/" + collectionname + "/" + unid,
  success: function(res) {
    //Do Something
  }
})
```

> The above returns JSON structured like this:

```json
{
  "_id":"53c8cf7783217c4dd0a557f6",
  "Formname":"Response",
  "ThreadSort":" 201218/062011:17:20 771AB2C8B80E229980257A210038850A",
  "ThreadIndent":". . ",
  "From":"CN=Matt White/O=Exhilarate",
  "ParentForm":"Response",
  "ParentSubject":"Response",
  "Categories":"Sales",
  "Body":{
    "type":"multipart",
    "content":[
      {
        "contentType":"multipart/alternative; Boundary=\"0__=0FBBF78ADFB9AAF98f9e8a93df938690918c0FBBF78ADFB9AAF9\"",
"contentDisposition":"inline"
      },
      {
        "contentType":"text/plain; charset=US-ASCII",
        "data":"This looks good to me - we need to make sure that the right Project Manager\r\nis assigned to this project",
        "boundary":"--0__=0FBBF78ADFB9AAF98f9e8a93df938690918c0FBBF78ADFB9AAF9"
      },
      {
        "contentType":"text/html; charset=US-ASCII",
        "contentDisposition":"inline",
        "data":"<html><body><font size=\"1\" face=\"sans-serif\">This looks good to me - we need to make sure that the right Project Manager is assigned to this project </font></body></html>",
        "boundary":"--0__=0FBBF78ADFB9AAF98f9e8a93df938690918c0FBBF78ADFB9AAF9"
      }
    ]
  },
  "Body__parsed":"<html><body><font size=\"1\" face=\"sans-serif\">This looks good to me - we need to make sure that the right Project Manager is assigned to this project </font></body></html>",
  "Subject":"Looks good to me",
  "__href":"http://dev.londc.com:80/demos/Discussion.nsf/api/data/documents/unid/FC7D00934676A6B580257D1900278BAA",
  "__unid":"FC7D00934676A6B580257D1900278BAA",
  "__noteid":"114E",
  "__parentid":"E2445C89357BB15080257D1900278BA9",
  "__created":"2014-07-18T07:11:56Z",
  "__modified":"2014-07-18T07:13:19Z",
  "__authors":  [ "CN=Matt White/O=Exhilarate", "CN=Matt White/O=Exhilarate" ],
  "__form":"Response",
  "_files": [ "api.xls" ]
}
```

### HTTP Request
`GET https://example.com/1.0/collections/:database/:collectionname/:unid`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service.
:collectionname | The name of the collection that contains the document
:unid | The unique ID of the document to get (this maps to the __unid field, not the _id field)

## Insert a new document

To insert a new document, use the PUT method and send JSON data to the service. If a document with the :unid already exists in the collection, an error will be returned. Use the POST method to update existing documents.

If the collection name (or database name) do not currently exist then they will automatically be created. By this process you can effectively create a brand new database by simply inserting a new document. Bear in mind that you will need to set up security for the database once it has been created.

```java
//TODO: Add Sample Code
```

```javascript
//Taken from our Discussion template
disc.saveNewMainTopic = function() {
  var fileInput = $("#fileupload");
  var file = fileInput[0].files[0];
  var reader = new FileReader();
  //Start building the document data
  var data = {};
  data.FormName = "MainTopic";
  data.__form = "MainTopic";
  data.Categories = $("#categories option:selected").val();
  data.From = username;
  data.AbbreviateFrom = username;
  data.Body = {
    "type": "multipart",
    "content": [{
      "contentType": "text/plain; charset=UTF-8",
      "data": CKEDITOR.instances.body.getData()
    }]
  };
  data.Subject = $("#subject").val();
  data.__created = new Date().toISOString();
  data.__modified = new Date().toISOString();

  if (file) {
    //Convert the file attachment to BASE64 string
    reader.onload = function(e) {
      data.Body.content.push({
        "contentType": file.type + "; name=\"" + file.name + "\"",
        "contentDisposition": "attachment; filename=\"" + file.name + "\"",
        "contentTransferEncoding": "base64",
        "data": reader.result.match(/,(.*)$/)[1]
      });
      disc.sendNewMainTopic(data);
    }
    reader.readAsDataURL(file);
  } else {
    disc.sendNewMainTopic(data);
  }
}
disc.sendNewMainTopic = function(data) {
  var unid = new Date().getTime();

  $.ajax({
    dataType: 'json',
    type: 'PUT',
    headers: {
      'apikey': apikey
    },
    data: data,
    url: '/1.0/document/' + dbname + "/" + data.FormName + "/" + unid,
    complete: function(res) {
      //Do something
    }
  });

}
```

> If the document is inserted correctly, the above returns a 200 success code, otherwise you will receive an error

### HTTP Request
`PUT https://example.com/1.0/collections/:database/:collectionname/:unid`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service.
:collectionname | The name of the collection into which the document will be inserted
:unid | The unique identifier of the document to be inserted

## Update an existing document

When you want to update a document use this method. You only need to post the fields that you want to update. Those fields will overwrite matching fields in the document, or add them if they don't already exist. To remove a field set its value to null.

If the document does not exist then an error will be returned, in this case you should use the PUT method instead.


```java
//TODO: Add Sample Code
```

```javascript
//Taken from our Discussion template
disc.saveMainTopic = function(unid) {
  var fileInput = $("#fileupload");
  var file = fileInput[0].files[0];
  var reader = new FileReader();
  //Start building the document data
  var data = {};
  data.FormName = "MainTopic";
  data.__form = "MainTopic";
  data.Categories = $("#categories option:selected").val();
  data.From = username;
  data.AbbreviateFrom = username;
  data.Body = {
    "type": "multipart",
    "content": [{
      "contentType": "text/plain; charset=UTF-8",
      "data": CKEDITOR.instances.body.getData()
    }]
  };
  data.Subject = $("#subject").val();

  if (file) {
    //Convert the file attachment to BASE64 string
    reader.onload = function(e) {
      data.Body.content.push({
        "contentType": file.type + "; name=\"" + file.name + "\"",
        "contentDisposition": "attachment; filename=\"" + file.name + "\"",
        "contentTransferEncoding": "base64",
        "data": reader.result.match(/,(.*)$/)[1]
      });
      disc.sendNewMainTopic(unid, data);
    }
    reader.readAsDataURL(file);
  } else {
    disc.sendMainTopic(unid, data);
  }
}
disc.sendMainTopic = function(unid, data) {

  $.ajax({
    dataType: 'json',
    type: 'POST',
    headers: {
      'apikey': apikey
    },
    data: data,
    url: '/1.0/document/' + dbname + "/" + data.FormName + "/" + unid,
    complete: function(res) {
      //Do something
    }
  });

}
```

> If the document is updated correctly, the above returns a 200 success code, otherwise you will receive an error

### HTTP Request
`POST https://example.com/1.0/collections/:database/:collectionname/:unid`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service.
:collectionname | The name of the collection in which the document will be updated
:unid | The unique identifier of the document to be updated

## Delete a document

To delete a document, use this method. You must have the rights to update the document

```java
//TODO: We need sample code here
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'DELETE',
  headers: { 'apikey': apikey },
  url: '/1.0/collections/' + database + '/' + collection + "/" + unid,
  success: function(res){
    //Do something
  },
  error: function(xhr, status, error){
    if (xhr.status == 401){
      alert("You do not have the rights to delete the document");
    }else{
      alert(status + '\n' + error);
    }
  }
})

```

> The above returns JSON structured like this:

```json
{
  "result": "ok"
}
```

### HTTP Request
`DELETE https://example.com/1.0/collections/:database/:collectionname/:unid`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service
:collectionname | The name of the collection to delete
:unid | The unid of the document to delete

# File Attachments

Given a database name, collection name, document id and attachment name, you can either download the attachment or delete it from the database. To insert new attachments, use the document PUT or POST methods.

## Get a File Attachment

```java
//TODO: We need sample code here
```

```javascript
window.open("https://example.com/1.0/attachment/:database/:collectionname/:unid/:filename");
```

> If the file exists (and you are allowed to access it), then the file will be sent as a binary stream to the requestor. If the file does not exists (or you are not allowed to access it) then an error will be returned.

### HTTP Request
`GET https://example.com/1.0/collections/:database/:collectionname/:unid/:filename`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service
:collectionname | The name of the collection to delete
:unid | The unid of the document to delete
:filename | The name of the file to access (can be retrieved from _files field on any document)

## Delete a file attachment

To delete a file attachment, use this method. You must have the rights to update the document to which the file is associated

```java
//TODO: We need sample code here
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'DELETE',
  headers: { 'apikey': apikey },
  url: '/1.0/collections/' + database + '/' + collection + "/" + unid + "/" + filename,
  success: function(res){
    //Do something
  },
  error: function(xhr, status, error){
    if (xhr.status == 401){
      alert("You do not have the rights to delete the filename");
    }else{
      alert(status + '\n' + error);
    }
  }
})

```

> The above returns JSON structured like this:

```json
{
  "result": "ok"
}
```

### HTTP Request
`DELETE https://example.com/1.0/collections/:database/:collectionname/:unid/:filename`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service
:collectionname | The name of the collection to delete
:unid | The unid of the document to delete
:filename | The name of the file to delete (can be retrieved from _files field on any document)

---
title: LDC Via API Reference

language_tabs:
  - java
  - javascript

toc_footers:
  - <a href='http://ldcvia.com'>Find out more about LDC Via</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the LDC Via API! You can use our API to access LDC Via API endpoints, this allows you to access and modify documents within databases in your organisation.

We have language bindings in Java and JavaScript (jQuery)! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

The video below shows how to get your API key:
<iframe src="//player.vimeo.com/video/113910972" width="500" height="281" frameborder="0" allowfullscreen=""></iframe>

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

## Login

We provide a simple login API to which you can pass a username and password, if they are valid then the matching API key will be returned with which you will be able to perform operations against the rest of the API.

```java
//TODO: Insert sampler here
```

```javascript
var data = {'username': "fred@bloggs.com", 'password': 'SuperSecretPassword'};
var root = 'https://example.com';
  $.ajax({
    dataType: 'json',
    type: 'POST',
    url: root + '/1.0/login',
    data: data,
    success: function(res) {
      console.log(res);
    }
  })
```
> The above command returns JSON structured like this:

```json
{
  "apikey":"d656cafda863458c78219760cb0ef4d1",
  "email":"fred@bloggs.com",
  "roles":{"account":"54be76b40f0f0ce80fe9b21e"},
  "notesnames":[],
  "databases":
    [
      {"database":"dev-londc-com-teamroom-nsf","_id":"54be770c0f0f0ce80fe9b220"},
      {"database":"dev-londc-com-teamroom-nsf","_id":"54be7ce40f0f0ce80fe9b221"},
      {"database":"dev-londc-com-teamroom-nsf","_id":"54c66b660f0f0ce80fe9b24b"},
      {"database":"dev-londc-com-teamroom-nsf","_id":"54d13776b8fcbdac0bee24f5"}
    ]
}
```

This endpoint retrieves all databases that you have access to.

### HTTP Request

`POST http://example.com/api/1.0/login`

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
:collectionname | The name of the collection to search in

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
`GET https://example.com/1.0/document/:database/:collectionname/:unid`

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
`PUT https://example.com/1.0/document/:database/:collectionname/:unid`

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
`POST https://example.com/1.0/document/:database/:collectionname/:unid`

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
  url: '/1.0/document/' + database + '/' + collection + "/" + unid,
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
`DELETE https://example.com/1.0/document/:database/:collectionname/:unid`

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
`GET https://example.com/1.0/attachment/:database/:collectionname/:unid/:filename`

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
`DELETE https://example.com/1.0/attachment/:database/:collectionname/:unid/:filename`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service
:collectionname | The name of the collection to delete
:unid | The unid of the document to delete
:filename | The name of the file to delete (can be retrieved from _files field on any document)

# Meta Data

When data is added to the database, we maintain a description of the fields and data types in each collection.

You have the ability to read and update certain metadata details. An example of when you might want to do this is to update security: to mark a field as being readers or authors.

## Get Meta Data for a collection

To get a list of all fields that are in a collection, use this method.

```java
//TODO: Add sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/metadata/' + dbname + "/" + collectionname,
  success: function(res) {
    //Do Something
  }
})
```

> The above returns JSON structured like this:

```json
{
   "_id":"53c8cc790e76eee06aed49ba",
   "dbname":"dev-londc-com-demos-discussion-nsf",
   "collectionname":"MainTopic",
   "__v":1,
   "lastimported":"2014-07-18T07:40:49.632Z",
   "fields":[
      {
         "fieldname":"From",
         "_id":"53c8cf810e76eee06aed4ad7",
         "fieldtype":"Names"
      },
      {
         "fieldname":"AbbreviateFrom",
         "_id":"53c8cf810e76eee06aed4ad6",
         "fieldtype":"String"
      },
      {
         "fieldname":"Current_Status",
         "_id":"53c8cf810e76eee06aed4ac8",
         "fieldtype":"Number"
      },
      {
         "fieldname":"FilebyDate",
         "_id":"53c8cf810e76eee06aed4ac6",
         "fieldtype":"Date"
      },
      {
         "fieldname":"Editors",
         "_id":"53c8cf810e76eee06aed4abf",
         "fieldtype":"Authors"
      },
      {
         "fieldname":"readers",
         "_id":"53c8cf810e76eee06aed4aa2",
         "fieldtype":"Readers"
      }
   ]
}
```

### HTTP Request
`GET https://example.com/1.0/metadata/:database/:collectionname`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service.
:collectionname | The name of the collection to get meta data for

## Update Meta Data for a Collection

You can update meta data for a collection with this method. The most common use for this will be to enable and disable readers and authors fields, thus controlling document level security.

```java
//TODO: Add a sample
```

```javascript
var data = {
  "fields":[
    {
      "fieldname":"From",
      "fieldtype":"Names"
    }
  ]
};
$.ajax({
  dataType: 'json',
  type: 'POST',
  data: data,
  headers: {
    'apikey': apikey
  },
  url: '/1.0/metadata/' + dbname + "/" + collectionname,
  success: function(res) {
    //Do Something
  }
})
```

> The above returns JSON structured like this:

```json
{"result": "ok"}
```

### HTTP Request
`POST https://example.com/1.0/metadata/:database/:collectionname`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service.
:collectionname | The name of the collection to update meta data for

## Reset Meta Data for a Collection

When data is added to the database, we maintain a description of the fields and data types in each collection. It is possible for this meta data to get out of sync with the data or become corrupt. This method will reset the meta data.

Be aware that if you have set any readers and authors fields in the meta data that these will be reset and thus all document security will be disabled.

Because of this potential, you must be a super user or administrator to run this method.

```java
//TODO: Add a sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/resetmetadata/' + dbname + "/" + collectionname,
  success: function(res) {
    //Do Something
  }
})
```

> The above returns JSON structured like this:

```json
{"result": "ok"}
```

### HTTP Request
`GET https://example.com/1.0/resetmetadata/:database/:collectionname`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service.
:collectionname | The name of the collection that you want to reset meta data for. By default the meta data will be built using a sampling of the first 100 documents in the collection. If all documents have exactly the same fields on them, then this number can be reduced to improve performance. To change the number of documents sampled, add a URL parameter: "&count=n". Likewise, if all documents do not have the same fields on them, then you can increase the sample size using the same technique. Be aware that the larger the sample size, the longer the response will take.

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
count | 100 | The number of documents to sample for field names

# User Details

You can get either your own details of the details of a different user within your organisation. To get you own details omit the :useremail url parameter.

## Get your own user details

```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/userdetails',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns JSON structured like this:

```json
{
  "notesnames":[
    {
      "notesname":"CN=Fred Bloggs/O=FCL"
    },
    {
      "notesname":"Fred Bloggs"
    },
    {
      "notesname":"DocEditors",
      "group": true
    }
  ],
  "apikey":"hwxsftXnd88KFq",
  "username":"Fred Bloggs",
  "email":"fred@fclonline.com",
  "admin":false,
  "superuser":false,
  "databases":[
    {
      "database":"dev-londc-com-demos-discussion-nsf",
      "_id":"53c8cc6e0e76eee06aed497b"
    },
    {
      "database":"dev-londc-com-demos-journal-nsf",
      "_id":"53cecd0819a5ac73b57964e5"
    }
  ]
}
```

### HTTP Request
`GET https://example.com/1.0/userdetails`

## Get other user's details

The get another user's details, use this method. If you are a super user or the same user as the email address, you will be able to get the API Key, otherwise that will not be returned.

```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/userdetails/fred@bloggs.com',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns JSON structured like this:

```json
{
  "notesnames":[
    {
      "notesname":"CN=Fred Bloggs/O=FCL"
    },
    {
      "notesname":"Fred Bloggs"
    },
    {
      "notesname":"DocEditors",
      "group": true
    }
  ],
  "apikey":"hwxsftXnd88KFq",
  "username":"Fred Bloggs",
  "email":"fred@bloggs.com",
  "admin":false,
  "superuser":false,
  "databases":[
    {
      "database":"dev-londc-com-demos-discussion-nsf",
      "_id":"53c8cc6e0e76eee06aed497b"
    },
    {
      "database":"dev-londc-com-demos-journal-nsf",
      "_id":"53cecd0819a5ac73b57964e5"
    }
  ]
}
```

### HTTP Request
`GET https://example.com/1.0/userdetails/:email`

### URL Parameters

Parameter | Description
--------- | -----------
:email | The email address of the person to get

## Add New User

In addition to retrieving user details, you can also create users via the API. A POST to create users involves sending an organisation ID, and a collection of user objects.

Once the API has validated the request, for each user two objects will be created: a User and an Account (the two elements maintain a 1:1 relationship, with the Account controlling a user's ability to log in). In addition, all users created will be linked to the passed-in organisation.

```java
//TODO: Add Sample
```

```javascript
var data = {
 "organisation": "548598c6b283ccf00e5edf43",
 "users": [
 {
 "email": "fbloggs@acme.com",
 "firstname": "Fred",
 "lastname": "Bloggs",
 "notesnames": [
 {
 "notesname": "test01/acme"
 },
 {
 "notesname": "user01/acme"
 }
 ]
 },
 {
 "email": "jh@acme.com",
 "firstname": "Jeremy",
 "lastname": "Hardy"
 }
 ]
};
$.ajax({
  dataType: 'json',
  type: 'POST',
  data: data,
  headers: {
    'apikey': apikey
  },
  url: '/1.0/users',
  success: function(res) {
    //Do Something
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
`POST https://example.com/1.0/users`


## Update Users details

The update another user's details, use this method. You must be a super user to use this method.

```java
//TODO: Add Sample
```

```javascript
var data = {
  "notesnames":
    [
      {"notesname": "CN=Fred Blogs/O=Londc"},
      {"notesname": "Fred Blogs/Londc"},
      {"notesname": "Fred Blogs"},
      {"notesname": "*/Londc"},
    ]
};
$.ajax({
  dataType: 'json',
  type: 'POST',
  data: data,
  headers: {
    'apikey': apikey
  },
  url: '/1.0/userdetails/fred@bloggs.com',
  success: function(res) {
    //Do Something
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
`POST https://example.com/1.0/userdetails/:email`

### URL Parameters

Parameter | Description
--------- | -----------
:email | The email address of the person to update

## Delete User

This method is available for super users to remove another user (you cannot delete yourself!)

```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'DELETE',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/userdetails/fred@bloggs.com',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns JSON structured like this:

```json
{"result": "ok"}
```

### HTTP Request
`DELETE https://example.com/1.0/userdetails/:email`

### URL Parameters

Parameter | Description
--------- | -----------
:email | the email of the person to delete

# Access Control

## Get list of groups in organisation
```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/groups',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns JSON structured like this:

```json
["Group 1", "Group 2"]
```

### HTTP Request
`GET https://example.com/1.0/groups`


## Add multiple users to group
```java
//TODO: Add Sample
```

```javascript
var data = {
"groupname":"TestGroup",
"emails": ["fred@bloggs.com", "joe@blow.com"]
};
$.ajax({
  dataType: 'json',
  type: 'POST',
  data: data,
  headers: {
    'apikey': apikey
  },
  url: '/1.0/groups',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns JSON structured like this:

```json
{"result": "ok"}
```

### HTTP Request
`POST https://example.com/1.0/groups`

## Add single user to group
```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/groups/fred@bloggs.com/Group1',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns 200 Success if the action worked

### HTTP Request
`GET https://example.com/1.0/groups/:email/:groupname`

### URL Parameters

Parameter | Description
--------- | -----------
:email | the email of the user to add to the group
:groupname | the groupname to add the user to

## Remove user from group
```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'DELETE',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/groups/fred@bloggs.com/Group1',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns 200 Success if the action worked

### HTTP Request
`DELETE https://example.com/1.0/groups/:email/:groupname`

### URL Parameters

Parameter | Description
--------- | -----------
:email | the email of the user to remove from the group
:groupname | the groupname to remove the user from

## Get Members of Group
```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/groups/Group1',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns an array of user emails

```json
["fred@bloggs.com", "joe@smith.com"]
```

### HTTP Request
`GET https://example.com/1.0/groups/:groupname`

### URL Parameters

Parameter | Description
--------- | -----------
:groupname | the groupname to get member list of

## Remove Group
```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'DELETE',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/groups/Group1',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns 200 Success if the action worked

### HTTP Request
`DELETE https://example.com/1.0/groups/:groupname`

### URL Parameters

Parameter | Description
--------- | -----------
:groupname | the groupname to delete

## List all users in organisation
```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/users',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns an array of emails

```json
["fred@bloggs.com", "joe@smith.com"]
```

### HTTP Request
`GET https://example.com/1.0/users`

## Add user to database
```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/userdetails/joe@bloggs.com/dev-londc-demos-discussion-nsf',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns 200 Success if the operation completed successfully

### HTTP Request
`GET https://example.com/1.0/userdetails/:email/:database`

### URL Parameters

Parameter | Description
--------- | -----------
:email | the email of the user to give access to the database
:database | the database to give the user access to

## Remove user from database
```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'DELETE',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/userdetails/joe@bloggs.com/dev-londc-demos-discussion-nsf',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns 200 Success if the operation completed successfully

### HTTP Request
`DELETE https://example.com/1.0/userdetails/:email/:database`

### URL Parameters

Parameter | Description
--------- | -----------
:email | the email of the user to remove access to the database
:database | the database to remove the user access to

# Utilities

We provide several extra services that act as utilities to make developing applications easier.

## Get List of Distinct Values

Similar in concept to an @DBColumn in Notes, this will return a unique list of values from a field in a collection

```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/list/dev-londc-com-demos-discussion-nsf/MainTopic/Subject',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns an array of items

```json
["value one", "value two"]
```

### HTTP Request
`GET https://example.com/1.0/list/:database/:collectionname/:fieldname`

### URL Parameters

Parameter | Description
--------- | -----------
:database | the database to get the value list from
:collectionname | the collection to get the value list from
:fieldname | the field to get value list from

## Responses

Given a database, collection name and document id, this method will return to you a list of collection names and document ids that are responses to this document.

```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/responses/dev-londc-com-demos-discussion-nsf/MainTopic/12345678',
  success: function(res) {
    //Do Something
  }
})
```

> The response object is similar to the standard collections response

```json
[
  {
    "_id": "5411514d8c1fd625dc2ab8c9",
    "__unid": "A4D2533D7A91573E80257D1900276F8D",
    "__parentid": "BAB78224CA0B0FEC80257D1900276F91",
    "__form": "Response"
  },
  {
    "_id": "5411514e8c1fd625dc2ab8d3",
    "__unid": "3411614A3E45C7FB80257D1900276F92",
    "__parentid": "BAB78224CA0B0FEC80257D1900276F91",
    "__form": "ResponseToResponse"
  },
  {
    "_id": "5411514d8c1fd625dc2ab8c3",
    "__unid": "8D58E7A1EFD6899C80257D1900276F8E",
    "__parentid": "A4D2533D7A91573E80257D1900276F8D",
    "__form": "ResponseToResponse"
  }
]
```

### HTTP Request
`GET https://example.com/1.0/responses/:database/:collectionname/:unid`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service.
:collectionname | the name of the collection that contains the document
:unid | the document to get a list of responses for

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
:expand | false | If set to true you will be returned an array of documents instead of an array of UNIDs. This can affect performance

### Response Properties
Property | Description
-------- | -----------
_id | the internal reference for the document
__unid | the unid of the response document
__parentid | the parentid of the document, this may not be the same as the :unid parameter as this method returns response to responses
__form | the collection of the response document

## Export to PDF
Given a database, collection name and document id, this method will return PDF with all fields on the document.

```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/pdf/dev-londc-com-demos-discussion-nsf/MainTopic/12345678',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns a binary stream of the PDF document

### HTTP Request
`GET https://example.com/1.0/pdf/:database/:collectionname/:unid`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service.
:collectionname | the name of the collection to get the document from
:unid | the unid of the document to export to PDF

## Export to Excel
Given a database and collection name, this method will return an Excel spreadsheet list of documents in the collection. The response will contain a list of documents from the collection, you can control how many documents and where in the collection to get them from using URL parameters.

The count of the documents have document level security applied to them, so if there are 100 documents in the collection but the user can see only 10, then the count will be 10.

```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/excel/dev-londc-com-demos-discussion-nsf/MainTopic',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns a binary stream of the PDF document

### HTTP Request
`GET https://example.com/1.0/excel/:database/:collectionname`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service.
:collectionname | the name of the collection to export

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
count | 500 | The number of documents to get data for.
start | 0 | The starting position in the view


## DocLinks
Given a database, collection, document and field name, this service will parse the field (it must be a rich text field) and return details of any doclinks that are contained within.

```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/doclink/dev-londc-com-demos-discussion-nsf/MainTopic/12345678012345456',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns a JSON object with the parsed doc links (or an empty array if no doc links were found). There are three possible formats, for database links, view links and document links. If the document link is linking to a document in the same database the object will contain the notes:// url and also the apiurl to get the related document. If the document is in a different database then the object will just contain the notes url.

```json
[
  {
    "notesurl": "Notes://Mixed2/80257AC50035BEE2/9B03730C11B24C94852565E20060BED0/BC9F435105F5F9A880257C3100401F50",
    "type": "doclink",
    "documentid": "BC9F435105F5F9A880257C3100401F50",
    "apiurl": "/1.0/document/dev-londc-com-teamstudio-widgets-teamroom-nsf/Response/BC9F435105F5F9A880257C3100401F50"
  },
  {
    "notesurl": "Notes://Mixed2/80257AC50035BEE2/9B03730C11B24C94852565E20060BED0",
    "type": "viewlink"
  },
  {
    "notesurl": "Notes://Mixed2/80257AC50035BEE2",
    "type": "dblink"
  }
]
```

### HTTP Request
`GET https://example.com/1.0/doclink/:database/:collectionname/:unid/:fieldname`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service.
:collectionname | the name of the collection to export
:unid | The unid of the document
:fieldname | The fieldname to parse for doclinks


# Audit Trail
Every single operation against our API is recorded in our logs. We provide a set of APIs to access those logs for further analysis.

## Get All Logs
When you want to get a count of activity for your entire environment, use the /activity service. If you want to get detailed logs of activity, then you will need to provide either a database, a database and collection or a database, collection and document id. In all cases, if you issue a GET request then you will be returned all records for that scope.

```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/activity',
  success: function(res) {
    //Do Something
  }
})
```

> The response object is an object with of counts of logs per database

```json
{
"testdb":1196,
"192-168-0-13-unpsampler-nsf":39,
"dev-londc-com-demos-journal-nsf":43
}
```

### HTTP Request
`GET https://example.com/1.0/activity`

## Search Logs
If you want to further filter the logs to a specific date range or only contain certain messages, for example, then all of the above methods can also be used with POST requests. The responses will be the same format as above. To restrict the logs you can add all of some of the following criteria in a JSON post.

When you want to get a count of activity for your entire environment, use the /activity service. If you want to get detailed logs of activity, then you will need to provide either a database, a database and collection or a database, collection and document id. In all cases, if you issue a GET request then you will be returned all records for that scope.

```java
//TODO: Add Sample
```

```javascript
var data = {
  "startdate": {"year": 2014, "month": 6, "day": 1},
  "enddate": {"year": 2014, "month": 7, "day": 1},
  "filters": [
    {"operator": "equals", "field": "level", "value": "info"}
  ]
}
$.ajax({
  dataType: 'json',
  type: 'POST',
  data: data,
  headers: {
    'apikey': apikey
  },
  url: '/1.0/activity',
  success: function(res) {
    //Do Something
  }
})
```

> The response object is an object with of counts of logs per database

```json
{
"testdb":200,
"192-168-0-13-unpsampler-nsf":10,
"dev-londc-com-demos-journal-nsf":0
}
```

### HTTP Request
`POST https://example.com/1.0/activity`

## Get Single Database Logs

To filter logs at a database level, add the database name to the URL structure

```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/activity/dev-londc-com-demos-discussion-nsf?start=0&count=2',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns JSON structured like this:

```json
{
"count":3901,
"docs":[
{
"_id":"53f22101edf3aa08da052184",
"source":"api.v1.js",
"db":"dev-londc-com-demos-discussion-nsf",
"collection":"MainTopic",
"timestamp":"2014-08-18T15:51:29.205Z",
"level":"info",
"message":"Get Collection",
"user":"support@londc.com"
},
{
"_id":"53f22101edf3aa08da052189",
"source":"api.v1.js",
"db":"dev-londc-com-demos-discussion-nsf",
"collection":"MainTopic",
"timestamp":"2014-08-18T15:51:29.307Z",
"level":"info",
"message":"Get Collection",
"user":"support@londc.com"
}
]}
```

### HTTP Request
`GET https://example.com/1.0/activity/:database`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service.

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
count | 30 | The number of documents to get data for.
start | 0 | the position in the results to start at


## Search Single Database Logs

To further filter logs at a database level, add the database name to the URL structure and then POST whatever filters are required

```java
//TODO: Add Sample
```

```javascript
var data = {
  "startdate": {"year": 2014, "month": 6, "day": 1},
  "enddate": {"year": 2014, "month": 7, "day": 1},
  "filters": [
    {"operator": "equals", "field": "level", "value": "info"}
  ]
}
$.ajax({
  dataType: 'json',
  type: 'POST',
  data: data,
  headers: {
    'apikey': apikey
  },
  url: '/1.0/activity',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns JSON structured like this:

```json
{
"count":205,
"docs":[
{
"_id":"53f22101edf3aa08da052184",
"source":"api.v1.js",
"db":"dev-londc-com-demos-discussion-nsf",
"collection":"MainTopic",
"timestamp":"2014-06-18T15:51:29.205Z",
"level":"info",
"message":"Get Collection",
"user":"support@londc.com"
},
{
"_id":"53f22101edf3aa08da052189",
"source":"api.v1.js",
"db":"dev-londc-com-demos-discussion-nsf",
"collection":"MainTopic",
"timestamp":"2014-06-18T15:51:29.307Z",
"level":"info",
"message":"Get Collection",
"user":"support@londc.com"
}
]}
```

### HTTP Request
`GET https://example.com/1.0/activity/:database`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service.

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
count | 30 | The number of documents to get data for.
start | 0 | the position in the results to start at

## Get Single Collection logs

To filter logs at a collection level, add the database name and collection name to the URL structure

```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/activity/dev-londc-com-demos-discussion-nsf/MainTopic?start=0&count=2',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns JSON structured like this:

```json
{
"count":3901,
"docs":[
{
"_id":"53f22101edf3aa08da052184",
"source":"api.v1.js",
"db":"dev-londc-com-demos-discussion-nsf",
"collection":"MainTopic",
"timestamp":"2014-08-18T15:51:29.205Z",
"level":"info",
"message":"Get Collection",
"user":"support@londc.com"
},
{
"_id":"53f22101edf3aa08da052189",
"source":"api.v1.js",
"db":"dev-londc-com-demos-discussion-nsf",
"collection":"MainTopic",
"timestamp":"2014-08-18T15:51:29.307Z",
"level":"info",
"message":"Get Collection",
"user":"support@londc.com"
}
]}
```

### HTTP Request
`GET https://example.com/1.0/activity/:database/:collectionname`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service.
:collectionname | The name of the collection to get data for

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
count | 30 | The number of documents to get data for.
start | 0 | the position in the results to start at

## Search Single Collection Logs

To further filter logs at a collection level, add the database name and collection name to the URL structure and then POST whatever filters are required

```java
//TODO: Add Sample
```

```javascript
var data = {
  "startdate": {"year": 2014, "month": 6, "day": 1},
  "enddate": {"year": 2014, "month": 7, "day": 1},
  "filters": [
    {"operator": "equals", "field": "level", "value": "info"}
  ]
}
$.ajax({
  dataType: 'json',
  type: 'POST',
  data: data,
  headers: {
    'apikey': apikey
  },
  url: '/1.0/activity/dev-londc-com-demos-discussion-nsf/MainTopic?start=0&count=2',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns JSON structured like this:

```json
{
"count":205,
"docs":[
{
"_id":"53f22101edf3aa08da052184",
"source":"api.v1.js",
"db":"dev-londc-com-demos-discussion-nsf",
"collection":"MainTopic",
"timestamp":"2014-06-18T15:51:29.205Z",
"level":"info",
"message":"Get Collection",
"user":"support@londc.com"
},
{
"_id":"53f22101edf3aa08da052189",
"source":"api.v1.js",
"db":"dev-londc-com-demos-discussion-nsf",
"collection":"MainTopic",
"timestamp":"2014-06-18T15:51:29.307Z",
"level":"info",
"message":"Get Collection",
"user":"support@londc.com"
}
]}
```

### HTTP Request
`GET https://example.com/1.0/activity/:database/:collectionname`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service.
:collectionname | The name of the collection to look for logs of

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
count | 30 | The number of documents to get data for.
start | 0 | the position in the results to start at

## Get Single Document Logs
To filter logs at a document level, add the database name, collection name and document unid to the URL structure

```java
//TODO: Add Sample
```

```javascript
$.ajax({
  dataType: 'json',
  type: 'GET',
  headers: {
    'apikey': apikey
  },
  url: '/1.0/activity/dev-londc-com-demos-discussion-nsf/MainTopic/CBF71C6B10257F5C80257D1900261D7A?start=0&count=2',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns JSON structured like this:

```json
{
  "count": 308,
  "data": [
    {
      "_id": "54e59614a969c4da7049c85d",
      "collection": "MainTopic",
      "document": "CBF71C6B10257F5C80257D1900261D7A",
      "timestamp": "2015-02-19T07:51:48.826Z",
      "level": "info",
      "message": "Get Response Heirarchy",
      "user": "fred@ldcvia.com"
    },
    {
      "_id": "54e59614a969c4da7049c85c",
      "collection": "MainTopic",
      "document": "CBF71C6B10257F5C80257D1900261D7A",
      "timestamp": "2015-02-19T07:51:48.807Z",
      "level": "info",
      "message": "Get Document",
      "user": "fred@ldcvia.com"
    }
  ]
}
```

### HTTP Request
`GET https://example.com/1.0/activity/:database/:collectionname/:unid`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service.
:collectionname | The name of the collection to get the document from
:unid | The unid of the document to get logs for

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
count | 30 | The number of documents to get data for.
start | 0 | the position in the results to start at

## Search Single Document Logs

To further filter logs at a document level, add the database name, collection name and document unid to the URL structure and then POST whatever filters are required

```java
//TODO: Add Sample
```

```javascript
var data = {
  "startdate": {"year": 2015, "month": 2, "day": 1},
  "enddate": {"year": 2014, "month": 3, "day": 1},
  "filters": [
    {"operator": "equals", "field": "level", "value": "info"}
  ]
}
$.ajax({
  dataType: 'json',
  type: 'POST',
  data: data,
  headers: {
    'apikey': apikey
  },
  url: '/1.0/activity/dev-londc-com-demos-discussion-nsf/MainTopic/CBF71C6B10257F5C80257D1900261D7A?start=0&count=2',
  success: function(res) {
    //Do Something
  }
})
```

> The above returns JSON structured like this:

```json
{
  "count": 308,
  "data": [
    {
      "_id": "54e59614a969c4da7049c85d",
      "collection": "MainTopic",
      "document": "CBF71C6B10257F5C80257D1900261D7A",
      "timestamp": "2015-02-19T07:51:48.826Z",
      "level": "info",
      "message": "Get Response Heirarchy",
      "user": "fred@ldcvia.com"
    },
    {
      "_id": "54e59614a969c4da7049c85c",
      "collection": "MainTopic",
      "document": "CBF71C6B10257F5C80257D1900261D7A",
      "timestamp": "2015-02-19T07:51:48.807Z",
      "level": "info",
      "message": "Get Document",
      "user": "fred@ldcvia.com"
    }
  ]
}
```

### HTTP Request
`GET https://example.com/1.0/activity/:database/:collectionname/:unid`

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the databases service.
:collectionname | The name of the collection containing the document to look for logs of
:unid | The unid of the document to look for logs of

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
count | 30 | The number of documents to get data for.
start | 0 | the position in the results to start at

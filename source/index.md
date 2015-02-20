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
`DELETE https://example.com/1.0/collections/:database/:collectionname

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the API 1.0 databases method.


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
:database | This is the unique name of the database which can be accessed using the API 1.0 databases method.

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
`GET https://example.com/1.0/collections/:database/:collectionname

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the API 1.0 databases method.
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
`DELETE https://example.com/1.0/collections/:database/:collectionname

### URL Parameters

Parameter | Description
--------- | -----------
:database | This is the unique name of the database which can be accessed using the API 1.0 databases method.
:collectionname | The name of the collection to delete

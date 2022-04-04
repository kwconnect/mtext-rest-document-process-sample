# mtext-rest-document-process-sample

A postman collection demonstrating the usage of some M/TEXT and M/OMS rest api methods to create, change and process documents.

## Preparation

To test this postman collection install M/TEXT version 6.12 or 6.13 on your computer. If you have access to a M/TEXT system
activate the sample projects from M/Workbench on the server.
Copy the xml files to your postman working directory. 

Import the collection to your postman workspace. Open the collection and go to variables tab and change the marked variables 
with your values. 
Please omit the trailing slash of baseHost parameter. The password is clear text. 
If you are running this collection on a M/TEXT 6.12 version adjust the expected version.

![grafik](https://user-images.githubusercontent.com/30256627/161446982-5ee43afa-a32e-4b9f-837e-a9eafc0f03f7.png)

The marked requests will send some xml data in a multipart/form-data body. For this purpose, the XML files mentioned above are used. Please make sure that the files are available.

![image](https://user-images.githubusercontent.com/8683821/161506801-2e96a54d-21ba-470c-b144-5e2d397878ef.png)

## Requests in detail

### Check version of M/TEXT and M/OMS server (GET)

The requests "Returns M/TEXT version information" and "Returns M/OMS version information" query the server version.

### List documents (GET)

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
* filter - filter expression to query documents

The request queries the M/TEXT database for sample documents. It shows a simple search for documents starting with a given name.

```
Name like "WillkommenDocument*"
```

The filter expression can become arbitrarily complex. Every searchable metadata and datamodel node can be used to query documents. The documents are searched accross the whole document storage of M/TEXT.
There are other methods to search documents in a known folder or querying the postbox of the current user.

### Delete document (DELETE)

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
Path parameters:
* docname - full qualified name of document to delete

The request deletes a given document in M/TEXT database.

When running the collection all documents found in request before are deleted.

### Returns user information (GET)

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
* login-id - user to get information about
* obtain-role-users - setting that parameter to true to obtain role users

Read user information from the current user. 

* guid
* role membership

The guid can be used to assign the document to the creator. This is done by setting a default metadata called assignee. The document is 
queryable in the postbox of the user (see request "List users postbox"). 

The choosen user should be member of a M/TEXT role. This role is taken to assign the document to a group of users which own that role. The document is queryable in the group postbox showing documents which are assigned to roles the current user owns.

### Query template (GET)

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
* filter -  filter expression to restrict the list of templates
* project-name - optional name of project to restrict the list of templates

M/TEXT uses a template to create a document from. The request shows how to search a template by its name. 
The filter expression can contain a complex query:

```
Name like "Willkommen*" AND DocumentType "TONIC"
```

Templates can have metadata, which can also be used to search for templates.

```
METADATA.SPARTE = "Kfz-Versicherung" AND DocumentType "TONIC"
```

Assuming some fictional metadata a query could look like this:

```
METADATA.TEMPLATE.CATEGORY = 'ACATEGORY' AND 
((METADATA.TEMPLATE.VALIDFROM <= '2022-04-01' AND METADATA.VALIDTO > '2022-04-01') OR 
METADATA.TEMPLATE.VALIDALWAYS = 'TRUE')
```
### Returns template metadata (GET)

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
* include-empty-metadata-values - set this parameter to false to restrict the result to non empty metadata values
Path parameters:
* template-name - full qualified name of template

Templates have metadata to describe and configure general parameters. The metadata can be used to query templates.

### Returns list of datasources (GET)

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
* include-empty-metadata-values - set this parameter to false to restrict the result to non empty metadata values of datasources
Path parameters:
* template-name - full qualified name of template

Templates get their data for document creation in datasources. A datasource can be a xml, json or csv stream. The name of datasource is imported because it must be given to put the data into the right datasource. Use always the same name for your datasources is property style. 

At kwsoft consulting we suggest to name it **DATA**. 

Datasources can also have metadata to describe certain requirements to the content of the datasource. This can be used to implement some template driven 
data mining to assemble to proper input data to the template. In the current sample there are no metadata.

The collection requires a template with a single datasource.

### Creates a M/TEXT document using document data binding (POST, multipart-form-data)

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
* document-name - name of document or full qualified name of document with path. 
If name ends with asterisk, M/TEXT tries to create a unique name by increasing the number. Only usefull for testing.
* mtext.documentMetaDataParameter.Metadata.Assignee - assign a value to Metadata.Assignee 
* mtext.documentMetaDataParameter.Metadata.GroupAssignee - assign a value to Metadata.GroupAssignee 
Path parameters:
* template-name - full qualified name of template
Form data:
* xml:{{datasource}} - the name of the formdata is made of the type of input data and the name of the datasource. Possible input prefix are xml, 
json and csv. For csv there are more possible parameters.

There are more possible parameters which are not used in this request. The parameters starting with mtext.documentMetaDataParameter. are 
optional parameters to set some metadata values.

The datasources are given as form-data entries in the body section.

The request creates a document and stores it in the home folder of the current user. The document can be used in subsequent calls of the api.

### List users postbox (GET)

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
* sortBy - result column to sort by (for complete list of valid values look into documentation, often used: MODIFIED_ON, NAME)
* sortOrder - asc or desc 

Because the document metadata METADATA.ASSIGNEE is set at creation time the document is visible in the postbox of the current user.

It is not possible to use this method to show a different users postbox. But the postbox is only a document search with following  
filter query:

```
METADATA.ASSIGNEE = "<GUID of user>"
```

The guid of the user can be queried with "Returns user information". With this information a filter query to list documents of other users can be 
created.

**The authenticated user must have access rights to the documents returned.**

### Document infos: read document information (GET)

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
Path parameters:
* doc-name - full qualified document name

Retrieve some information about current document

* Folder (part of full qualified name)
* Document name
* Date created
* Date last modfied
* Date last printed to M/OMS
* Date locked
* User created
* User last modified
* User last printed
* User locked
* Type of document (TONIC or CLASSIC)
* If document is versioning

### Document infos: read the metadata of this document

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
Path parameters:
* doc-name - full qualified document name

Retrieve the metadata of the document. 

This request returns the metadata and the searchable datamodel nodes of the current document.

### Document infos: read data provider names

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
Path parameters:
* doc-name - full qualified document name

Retrieve a list of data provider names. 

At creation time the data is put into datasources. Every datasource can be mapped to one or more datamodels. This datamodels are stored as data providers 
in the document. The values from data given at creation time is accessible through this dataproviders. 

Data providers can contain nodes which are calculated from others. Look for topic "virtual datanodes" in M/TEXT documentation.

The metadata retrieved in the request before is also stored in a data provider.

Running the collection this request triggers the following request for every data provider which is found in the document.

### Document infos: read data provider data

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
* data-provider-name - name of data provider
Path parameters:
* doc-name - full qualified document name

Retrieve the whole data stored in the given data provider.

This method can be used to get some information about the data the document is based on.

Running the collection this request is triggered by the preceding request for every data provider which is found in the document.

### Print document to M/OMS (GET)

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
* destination - optional parameter, default is OMS, destination to send document to M/OMS
Path parameters:
* doc-name - full qualified document name

Printing the document to M/OMS formats the document and produces the document according to its output settings. The document is inserted into 
M/OMS database and InputId which primary key for M/OMS input document is returned.

### Update document metadata (POST)

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
* is-delta - should always be set to true, to signal that only some metadata values are set
Path parameters:
* doc-name - full qualified document name
Body (application/json):
* array of key value pairs, if a metadata has multiple values the property is **values** and its value is an array of values.

```
[
    {
        "key": "Metadata.State",
        "value": "Printed"
    },
    {
        "key": "Metadata.Assignee",
        "value": ""
    }
]
```

Set some metadata to reflect changes to the document.

The demonstration case updates the Metadata.State and the Assignee. The Metadata.State is a predefined metadata to reflect user defined 
document states. The valid values of state can be customized in project configuration in M/Workbench.

### Export as PDF (GET)

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
* mime-type - mime type of result document
* download - setting this parameter to true, sets the response header content-dispositon
* mtext.exportDocumentConfiguration.destinationName - optional parameter to specify a destination from M/TEXT configuration (mtext.conf.xml)
If this parameter is not set the mime-type parameter is used to derive the destination name by prepending "export-" to the mime type.
Path parameters:
* doc-name - full qualified document name

Format the document and render the output to pdf format. The formating is done at request time and the result is returned to caller.

Postman shows a preview of the created pdf. 

Other formats are also possible. It depends on the configured destinations in the M/TEXT configuration.

### Read M/OMS splitdocuments (POST)

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
Body (application/json):
* object with property **select** containing some query parameters

This request uses M/OMS rest api to query split documents created for output channels in M/OMS.

When printed to M/OMS the output processing happens in M/OMS according the processing rules defined. The processing can happen instantly 
or at regular time slots or manually according the customer needs and requirments.

Every split document represents a piece of document processing for a certain output channel. The state in column kwStatus is representing the current state of processing. The values have to be customized in M/OMS configuration (moms.conf.xml).

### Update document data (POST, multipart-form-data)

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
* template-name - full qualified template name (can be taken from METADATA.INITIALDATABINDING)
Path parameters:
* doc-name - full qualified document name
Form data:
* xml:{{datasource}} - the name of the formdata is made of the type of input data and the name of the datasource. Possible input prefix are xml, 
json and csv. For csv there are more possible parameters.

Update the data providers in the document to reflect some external data changes.

### List users group postbox (GET)

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
* sortBy - result column to sort by (for complete list of valid values look into documentation, often used: MODIFIED_ON, NAME)
* sortOrder - asc or desc 

Because the document metadata METADATA.GROUPASSIGNEE is set at creation time the document is still visible in the group postbox of the current user. 

The metadata METADATA.GROUPASSIGNEE can have multiple values to assign a document to muliple roles. Every user owning one of this roles can 
list the document in its group postbox.

### Query printout information (GET)

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
Path parameters:
* doc-name - full qualified document name

Retrieve information about the document print operations. 

## Run the collection

The collection can be run at once. The requests are ordered to show a document processing flow.

* Delete the document (normally at the end, for demonstration purpose at beginning to clean up the space)
* List template
* Query template information
* Create the document
* Query document information
* List document 
* Update document, document metadata
* Print document
* Export document
* Query output processing

Every request can run standalone. Please check the order. Otherwise default values or values from last run will be taken and produce unexpected results.






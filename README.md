# mtext-rest-document-process-sample

A postman collection demonstrating the usage of some M/TEXT and M/OMS rest api methods to create, change and process documents.

## Preparation

To test this postman collection install M/TEXT version 6.12 or 6.13 on your computer. If you have access to a M/TEXT system
activate the sample projects from M/Workbench in the server.
Copy the xml files to your postman working directory. 

Import the collection to your postman workspace. Open the collection and go to variables tab and change the marked variables 
with your values. 
The password is clear text. 
If you are running this collection on a M/TEXT 6.12 version adjust the expected version.

![grafik](https://user-images.githubusercontent.com/30256627/161436798-aced769d-480c-42c1-a1cd-2e2d69eacfc9.png)

The marked request are sending some xml file content in a mimepart. Check that the files are found.

![grafik](https://user-images.githubusercontent.com/30256627/161436830-2387ffa8-0a65-4437-b437-18ed9ce36a9e.png)

## Requests in detail

The collection demonstrates 

* how to query documents
* how to delete documents
* how to query available templates
* how to create a document with a choosen template
* how to query information about this document
* 

### Check version of M/TEXT and M/OMS server

The requests "Returns M/TEXT version information" and "Returns M/OMS version information" query the server version.

### List documents

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
* filter - filter expression to query documents

The request queries the M/TEXT database for sample documents. It shows a simple search of documents starting with a given name.
The filter is using a simple query by name.

```
Name like "WillkommenDocument*"
```

The query can be more complex. Every searchable metadata and datamodel node can be used to query documents. The documents are search accross the 
whole document storage of M/TEXT.
There are other methods to search documents in a known folder or quering the postbox of the current user.

The test script calls the request "Delete document" to delete all existing sample documents.

### Delete document

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
Path parameters:
* docname - full qualified name of document to delete

The request deletes a given document in M/TEXT database.

When running the collection all documents found in request before are deleted.

### Returns user information

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
* login-id - user to get information about
* obtain-role-users - setting that parameter to true to obtain role users

Read user information from the current user. 

* guid
* role membership

The guid can be used to assign the document at creation to the creator. This is done by setting a default metadata called assignee. The document is 
querable in the postbox of the user (see request "List users postbox"). 

The choosen user should be member of a M/TEXT role. This role is taken to assign the document to a group of users which own that role. The document is querable in the group postbox showing documents which are assigned to roles the current user owns.

### Query template

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
* filter -  filter expression to restrict the list of templates
* project-name - optional name of project to restrict the list of templates

M/TEXT uses template to create a document from. The request show who to search a template by its name. 
The filter expression can contain a complex query:

```
Name like "Willkommen*" AND DocumentType "TONIC"
```

Template can have metadata, which can also be used to search templates.

```
METADATA.SPARTE = "Kfz-Versicherung" AND DocumentType "TONIC"
```

Assuming some fictional metadata a query can be something like this:

```
METADATA.TEMPLATE.CATEGORY = 'ACATEGORY' AND 
((METADATA.TEMPLATE.VALIDFROM <= '2022-04-01' AND METADATA.VALIDTO > '2022-04-01') OR 
METADATA.TEMPLATE.VALIDALWAYS = 'TRUE')
```
### Returns template metadata

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
* include-empty-metadata-values - set this parameter to false to restrict the result to non empty metadata values
Path parameters:
* template-name - full qualified name of template

Template have metadata to describe and configure general parameters. The metadata can be used to query templates.

### Returns list of datasources

Parameters:
* username - M/TEXT username to use for authentifcation
* passwordplain - password in clear text
* include-empty-metadata-values - set this parameter to false to restrict the result to non empty metadata values of datasources
Path parameters:
* template-name - full qualified name of template

Templates get there data for document creation in datasources. A datasource can be a xml, json or csv stream. The name of datasource is imported because it must be given to put the data into the right datasource. Use always the same name for your datasources is property style. 

At kwsoft consulting we suggest to name it **DATA**. 

Datasources can also have metadata to describe certain requirements to the content of the datasource. This can be used to implement some template driven 
data mining to assemble to proper input data to the template. In the current sample there are no metadata.

The collection requires a template with a single datasource.

### Creates a M/TEXT document using document data binding




## Run the collection

Running every request by itself isn't possible because the requests take parameters which are set by preceding requests.






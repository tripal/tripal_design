# Tripal v4 - Publications Design Document 
This design documents includes multiple classes that are needed for querying, retrieving and importing publications into Tripal using a database-backend agnostic approach.

## Search Query Form Classes 

### ManagePubSearchQueriesForm 

Provides the form interface for managing a publication query. 

### AddPubSearchQueryForm 

Provides the form interface for adding a single query for a given publication library. 

### DeletePubSearchQueryForm 

Deletes a single search query. 

 

## TripalPubLibrary Plugin 

### TripalPubLibrary (Annotation Class) 

This is the annotation class for the plugin 

#### Member Variables 

- `id`: the plugin id 
- `label`: the label for the plugin 
- `description`: the description for the plugin 

 
### TripalPubLibraryInterface 

Interface class for the TripalPubLibrary class. This interface provides the functions that all TripalPubLibrary classes must implement. 

#### Member Variables 

#### Functions 

- `form()`:  provides the form elements for library that is used by AddPubSearchQueryForm 
- `formSubmit()`: 
- `formValidate()`: 
- `parse()`:  
- `retrieve()`: runs a query and retrieves results. This function is used by the `AddPubSearchQueryForm` to retrieve results using a query definition array. 
- `run()`: runs the `retrieve()` and `parse()` functions for a given query. Requires that the query is already saved and pulls the query definition from the database. 

### TripalPubLibraryBase 

The base class for all `TripalPubLibrary` implementation. It provides common functionality that all `TripalPubLibrary` implementations will need. 

### TripalPubLibraryPubmed 

An instance of a `TripalPubLibrary` plugin that talks directly to PubMed. It should retrieve and parse the results of a query into a common publication object. 

### TripalPubLibraryAgricola 

An instance of a `TripalPubLibrary` plugin that talks directly to the USDA Agricola database. It should retrieve and parse the results of a query into a common publication object. 

### TripalPubLibraryBibTex 

An instance of a `TripalPubLibrary` plugin that can import from a file.   

## PubBuddyService 

A base class that manages a `ChadoBuddyRecord` instance to store the publication. It is used in place of the old associative array for storing publications.   

#### Member Variables 

- record: an instance of `ChadoBuddyRecord` 

#### Functions 

- `setAttribute($attribute, $value)`
- `checkAuthors()`
- `getAttribute($attribute)`
- `getCitation()`: retrieves the citation for a publication. 

### ChadoPubBuddyService 

Implements a PubBuddyService and is responsible for saving the publication to Chado 

### Neo4jPubBuddyService 

Theoretical example for storing in another database. 

## TripalImporter Plugins 

### PubFileImporter  

Implementation of the TripalImporter plugin for loading publication data into Chado using a file. 

#### Functions: 

- `form()`: Lets the user upload a file. Lets the user select the file `TripalPubLibrary` plugin that can upload files 
- `run()`: Instantiate the proper instance of the TripalPubLibrary plugin (e.g. `TripalPubLibraryBibTex`) call the library->run() and pass the query id iterate through the array returned by the call above and store the records in Chado. 

 
## PubSearchQueryImporter  

Implementation of the `TripalImporter` plugin for loading publication data into Chado using a search query. It will allow the user to select from a set of already created search queries. 

#### Form Elements
- Select list for the database (eg. PubMed)
- Autocomplete text field (the name of the search query name)

#### Functions: 

- `form()`: Let's the user select a search query – no file upload needed. 
- `run()`:   Query the `tripal_pub_library_query` table, for the query information to get the TripalPubLibrary  plugin id.  Instantiate the proper instance of the TripalPubLibrary plugin (e.g. `TripalPubLibraryPubmed`). call the library->run() and pass the query id iterate through the array returned by the call above and store the records in Chado.
  - Tables (pub, pub_dbxref, dbxref, pubauthor, pubprop) - Look at GFF importer which does procedural functions to check for publications first and then insert publications not found in the CHADO schema.

 

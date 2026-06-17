# ABAP Country API -- Back-End Challenge

## Overview

This project implements an ABAP version of the REST Countries API using an SAP ABAP Environment.

What does this code do:
- consumes the REST Countries API,
- parses the JSON response in ABAP,
- calculates population density,
- and exposes the result through an OData V4 service using a RAP custom entity.



## Architecture

The solution is split into the following components:

- ZCL_COUNTRY_TYPES  
  Defines the country data model used internally in ABAP.

- ZCL_COUNTRY_API_2  
  Handles the HTTP call to the REST Countries API and parses the JSON response into the ABAP structure defined previously.

- ZCL_COUNTRY_QUERY  
  Implements IF_RAP_QUERY_PROVIDER and exposes the parsed data to RAP/OData.

- ZCE_COUNTRY  
  RAP custom entity that defines the OData-facing structure.

- ZUI_COUNTRY_SRV  
  Service definition exposing the custom entity.

- ZUI_COUNTRY_O4  
  OData V4 service binding used to publish the service. 



## Data Model

The exposed country entity contains the following fields:

- Official country name
- Capital
- Currency
- Population
- Area
- Population density

Population density is calculated as:

Population density (inhabitants/km2) = population / area



## Brief Explanation of the Chosen Solution and Design Decisions

The goal here was to keep the solution simple, well-structured and functional, as requested.


	1 - ZCL_COUNTRY_API_2 calls the REST Countries API and retrieves the raw JSON response.
	2 - The JSON response is parsed in ZCL_COUNTRY_API_2 into an ABAP internal table using the data
		structure defined in ZCL_Country_TYPES.
	3 - The parsed data is filtered to keep only the fields defined in ZCL_COUNTRY_TYPES.
	4 - The Density field is calculated in ZCL_COUNTRY_API_2.
	5 - ZCL_COUNTRY_QUERY implements IF_RAP_QUERY_PROVIDER and provides the country data to the 
		RAP custom entity ZCE_COUNTRY.
	6 - The service definition ZUI_COUNTRY_SRV exposes the custome entity ZCE_COUNTRY as an 
		OData entity.
	7 - The service binding ZUI_COUNTRY_O4 publishes the service as an OData V4 endpoint 
		for external consumption.



Design Decision 1: The URL is hardcoded.

	Since the goal here was to keep the solution simple and functional, I kept the URL hardcoded.
	This way, the URL is there, the code gets the JSON raw response, and then it exposes the data 
	via OData v4 later on, as requested. 
	The code does not ask the user for the URL or any other indications. That would add an extra 
	layer of complexity that I concluded it is probably beyond the scope of this challenge.

Design Decision 2: There is no mid step where data is stored in a physical database.

	Although this was my first approach idea, after some reading, I decided not to go down this route.
	Once again, the goal here was to keep the solution simple and functional, and the true goal was to get the data and expose it via OData V4.
	Storing the data would add an extra layer of complexity.

Design Decision 3: Since I did not go the physical database route, I went with creating a RAP custom entity.

	This way, the request is fulfilled, and the focus in on the API integration and OData exposure.



## SETUP Instructions


### PREREQUISITES

To run this project you need:
	- connection to a SAP ABAP ENVIRONMENT
	- Eclipse with ABAP Development Tools (ADT)
	


### PROJECT SETUP

1 - Create your package


2 - Import the following ABAP objects from the repository into your package:
		
    - ZCL_COUNTRY_TYPES
		- ZCL_COUNTRY_API_2
		- ZCL_COUNTRY_QUERY
		- ZCE_COUNTRY (custom entity)
		- ZUI_COUNTRY_SRV (service definition)


3 - Activate all objects in the order shown above.


4 - ATTENTION! The Service Binding it not on the repository files. You will have to create it on your own, after importing and activating the previous files.
  	
    To create it, do as follows:
  		1. Right click on ZUI_COUNTRY_SRV (the service definition for this project)
  		2. Choose "New Service Binding".
  		3. On the "Binding Type" dropdown menu, choose "OData V4 - Web API"
  		4. Use the following name: ZUI_COUNTRY_O4
  		5. Click finish.
		

5 - Configure the REST COUNTRIES API URL ---- USE THE KEY PROVIDED IN THE EMAIL

  	Since the URL is hardcoded, you need to go to ZCL_COUNTRY_API_2 (line 65 - 74). Add/change the URL as needed.
  	Ex: 'https://api.restcountries.com/countries/v5?limit=2'
  	Ex: 'https://api.restcountries.com/countries/v5/names.common/Portugal'
  	
  	Add the key -- lv_api_key = 'xx_add_you_key_here_exx'. -- on line 72


6 - Go to ZUI_COUNTRY_O4 (service binding) and publish the Local Service Endpoint.
	    
    Wait a moment until it finishes. It may take a bit.


7 - On the ZUI_COUNTRY_O4 window, click on Test. It will open a window in your browser.

	  This window shows a lot of information, and you can even use the GET /COUNTRIES dropdown to generate responses.
	  HOWEVER, since the URL is hardcoded, this will only search in what you defined in the URL. 
	  So, if for example you used (...)limit=20, you will only be able to search the information in those 20 results.


8 - Click on the link at the top : (...)/sap/opu/odata4/sap/zui_country_o4/srvd_a2x/sap/zui_country_srv/0001/ and add Countries at the end.

	  It should look like: (...)/sap/opu/odata4/sap/zui_country_o4/srvd_a2x/sap/zui_country_srv/0001/Countries


9 - Loading the usrl, the result will look like: (depending on the URL you used)

        	{
         	 "@odata.context": "$metadata#Countries",
          	"@odata.metadataEtag": "W/\"20260615115920\"",
         	 "value": [
        	   {
         	     "official_country_name": "Portuguese Republic",
          	    "capital": "Lisbon",
          	   "currency": "EUR",
           	   "population": 10749635,
            	  "area": 92090,
           	   "population_density": 116.73
          	  }
         	 ]
        	}




Thank you for reading!










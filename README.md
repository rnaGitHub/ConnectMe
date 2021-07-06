# ConnectMe

Flow diagram:
![Flow Diagram](FlowDiagram.png)

## Table of Contents

### Requirements for ConnectMe & extending DEASSA framework



### SPII - how this is being handled
1. how it is determined if it is SPII data vs non-regulated data

2. how we are handling it


### EventHub to RAW zone - Using DEASSA to ingest data


#### What's required:
   A new widget called 'Message_Type' was added to the Runway Self Service form to process data with multiple message types. 

   for e.g. Message_Type for ConnectMe -> MetaData[Empty], Others[FlightTeams, StaticTeams, OneToOne, DoD] 
	
   For applications without Message_Types, the value "none" needs to be entered.

Notebooks modified for ConnectMe:
- ##### events_from_eh_to_raw
- ##### EventHubIngestor
- ##### MetadataTool
- ##### SchemaUtil

The changes include:
- Data validations added to check if the widget value is left empty. If so, value "none" is used. This is to avoid errors during job executions.
     
	for e.g. val ADLS_CONTAINER: String = Option(dbutils.widgets.get("ADLS_Container")).filter(_.nonEmpty).getOrElse("none")
	
- A new widget called 'Message_Type' was added to the events_from_eh_to_raw notebook to capture data with multiple message types. 
	  
	for e.g. Message_Type for ConnectMe -> MetaData[Empty], Others[FlightTeams, StaticTeams, OneToOne, DoD] 

	Multiple data streams are written to seperate tables based on the Message_Types.
	
- Added code to create new json file (if it does not already exist) containing Map of MessageTypes in Delta Lake container.	

	File Location: /cntme/metadata/messageType
	
	File Name: dataFeedMessageTypeMap.json
			
	File Contents:
				{
    				"cntme": {
        				"eventType": {
            				"MetaData": ["Empty"],
            				"Others": ["FlightTeams", "StaticTeams", "OneToOne", "DoD"]
        				}
    				}
				}

- Added code in SchemaUtil notebook to use appropriate schema file during the stream read.	
		for e.g.        
		"Others_FlightTeams" -> "/schema_structtype/schema_structtype_flightteams.json",         
		"Others_StaticTeams" -> "/schema_structtype/schema_structtype_staticteams.json",
		"Others_OneToOne" -> "/schema_structtype/schema_structtype_onetoone.json",
		"Others_DoD" -> "/schema_structtype/schema_structtype_dod.json",
		"MetaData_Empty" -> "/schema_structtype/schema_structtype_metadata.json"

### RAW to STRUCT zone 
Notebooks modified for ConnectMe:
- ##### 	raw_to_struct_cntme
- ##### 	RawToStructProcessor

Incoming data streams are read and based on the Message_Types in the data, seperate write streams are created and the data stored in the delta lake tables in STRUCT.
The appropriate schema is applied to each data stream before the write. 

#### DEASSA Self Service Form changes
- Modified React/TypeScript code Runway\runway\plugins\access-control\src\components\SelfService\SelfServiceReviewPage.tsx to add a field "Message_Type" to the DEASSA self serice form.
	for e.g. Message_Type: `${formInput.messageType}`,
              

# ConnectMe

## Table of Contents

### Requirements for ConnectMe & extending DEASSA framework



### SPII - how this is being handled
1. how it is determined if it is SPII data vs non-regulated data

2. how we are handling it


### Using DEASSA to ingest data for ConnectMe into RAW zone


#### What's required:


#### Notebook Changes:
Following notebooks were modified:
- ##### events_from_eh_to_raw
- ##### EventHubIngestor
- ##### MetadataTool
- ##### SchemaUtil

- Added data validations to check if the widget value is left empty. Is so, a "none" is used. This is to avoid errors during job executions.
     
	for e.g. val ADLS_CONTAINER: String = Option(dbutils.widgets.get("ADLS_Container")).filter(_.nonEmpty).getOrElse("none")
	
- Added widget called 'Message_Type' that will capture the different message types in the data. 
	  
	for e.g. for e.g. Message_Type = MetaData[Empty], Others[FlightTeams, StaticTeams, OneToOne, DoD] 
	For applications without Message_Types, enter "none".

- Added code to create a new json file (if not exists) containing Map of MessageTypes in delta lake container.	
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

#### DEASSA Self Service Form changes
- Modified React/TypeScript code Runway\runway\plugins\access-control\src\components\SelfService\SelfServiceReviewPage.tsx to add a field "Message_Type" to the DEASSA self serice form.
	for e.g. Message_Type: `${formInput.messageType}`,
              


        




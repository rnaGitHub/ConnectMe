# Using DEASSA to ingest data for ConnectMe into RAW zone

## What's required:



## Notebook Changes:

1. events_from_eh_to_raw
	- Added data validations to notebook. If a widget is left empty, replace it with "none". This is to avoid errors during job executions.
     
	  for e.g. val ADLS_CONTAINER: String = Option(dbutils.widgets.get("ADLS_Container")).filter(_.nonEmpty).getOrElse("none")
	
    - Added widget called 'Message_Type' that will capture the different message types in the data. 
	  for e.g. for e.g. Message_Type = MetaData[Empty], Others[FlightTeams, StaticTeams, OneToOne, DoD] 
			For applications without Message_Types, enter "none".
		3.	Added code to create a new json file (if not exists) containing Map of MessageTypes in delta lake container.
			location: /cntme/metadata/messageType
			filename: dataFeedMessageTypeMap.json
			{
    			"cntme": {
        			"eventType": {
            			"MetaData": ["Empty"],
            			"Others": ["FlightTeams", "StaticTeams", "OneToOne", "DoD"]
        			}
    			}
			}
		4. 	

        

	EventHubIngestor
	raw_to_struct_cntme
	RawToStructProcessor
	/development/common/general/MetadataTool
	SchemaUtil

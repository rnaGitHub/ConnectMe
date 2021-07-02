# ConnectMe

Using DEASSA to ingest data for ConnectMe into RAW zone

What's required:

Notebook Changes:

    events_from_eh_to_raw
        Added validations when reading the widgets. If a widget is left empty, replace it with "none". This is to avoid errors during job executions.
            for e.g. val ADLS_CONTAINER: String = Option(dbutils.widgets.get("ADLS_Container")).filter(_.nonEmpty).getOrElse("none")

        

	EventHubIngestor
	raw_to_struct_cntme
	RawToStructProcessor
	/development/common/general/MetadataTool
	SchemaUtil

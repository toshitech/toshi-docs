# Journey states
Please see below for the mapping of journey states that TOSHI can provide as part delivery or return tracking.

By default, TOSHI will send the `State` column, but at request can send the `State (short)` column (e.g. if there's a limit on event state length).

|Journey type|State         |State (short)     |Description       |
|------------|--------------|------------------|------------------|
|delivery    |scheduled     |SHED              |The delivery has been scheduled|
|delivery    |en_route_to_client|EN_RO             |This delivery is next on the route|
|delivery    |arrived_at_client|ARRIV             |The TOSHI Assistant has arrived at the customer's address|
|delivery    |completed     |COMPL             |The delivery has been successfuly completed|
|delivery    |cancelled     |CANCE             |The delivery has been cancelled|
|delivery    |unable_to_deliver|UNABL             |We were unable to deliver|
|return      |scheduled     |RETSCHED          |The return has been scheduled|
|return      |arrived_at_client|RETARRIV          |The TOSHI Assistant has arrived at the customer's address|
|return      |collected_items|RETCOLLE          |The collection has been successfuly completed|
|return      |completed     |RETCOMPL          |The items have been returned to the TOSHI warehouse (ecommerce) or to the store (store to door)|
|return      |cancelled     |RETCANCE          |The return has been cancelled|

Generated from https://docs.google.com/spreadsheets/d/1DyR6YbPVOkvhrmsusvrrJ33RuG0iVIc4dtYJ1aJizPI/edit#gid=0

# Receiving journey states

TOSHI can provide live tracking of journey states as either an event tracking webhook, or a return CSV file.

## Webhooks
Please contact engineering@toshi.co for details.

## Return CSV file
Please contact engineering@toshi.co with SFTP details (host, username, password, directory) for your staging and production environments, and TOSHI will configure an hourly CSV return file that will include each state transition for each journey once. Once a state transition has been sent, it will not be resent in any subsequent CSV files.

### Example
The below is an example of the standard tracking CSV file that TOSHI generates. Please note that this can be customised (e.g. you might want to set the `Order number` column to use your own Order number, rather than the TOSHI generated number). Please contact TOSHI for any queries regarding this file.

|Order Number|Tracking Number|Event Code        |Event Date        |Event Time|Most Recent|Scan Location|
|------------|---------------|------------------|------------------|----------|-----------|-------------|
|ABC001      |TOS-ASE-000001 |en_route_to_client|20220501          |08:21     |FALSE      |LDN          |
|ABC001      |TOS-ASE-000001 |arrived_at_client |20220501          |15:30     |TRUE       |LDN          |

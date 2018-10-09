# Shared State

**EXTENSION\_NAME**: `com.adobe.module.identity`

The Identity extension shared state is created in the following situations:

* When the extension first loads and is initialized
* When handling a sync request, regardless if the actual sync occurs. Occurs when setting the Push Identifier, Advertising Identifier, or customer's custom identifiers
* When the global privacy status changes to `optedout`, a shared state is set after the identifiers are cleared

| Key | Type | Description |
| :--- | :--- | :--- |
| mid | String | MID is a unique ID for that visitor for the given Experience Cloud organization ID |
| advertisingidentifier | String | iOS: the IDFA from retrieved Apple APIs<br />Android: The Advertising Identifier that is returned from Google Play Services |
| pushidentifier | String | The SHA1 hashed Push Identifier |
| blob | String | The blob value returned by the the Experience Cloud ID Service |
| locationhint | String | The Experience Cloud ID Service region ID. A region ID \(or location hint\), is a numeric identifier for the geographic location of a particular ID service data center |
| visitoridslist | List<Map<String, Object>> | The list of all the customer's custom identifiers |
| lastsync | Long | The timestamp of the last  sync with The Experience Cloud ID Service (in seconds), by default it is 0 |


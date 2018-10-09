# Shared State

**MODULE\_NAME**: `com.adobe.module.identity`

The Identity extension shared state is created in the following situations:

* When the module first loads and is initialized
* When handling a sync request, regardless if the actual sync occurs. Occurs when setting the push ID, advertising ID, or customer's custom identifiers.
* When a successful sync request occurs, the shared state is updated after receiving a response from the Visitor ID service.

| Key | Type | Description |
| :--- | :--- | :--- |
| mid | String | MID is a unique ID for that visitor for the given Experience Cloud organization ID |
| advertisingidentifier | String | iOS: the IDFA from retrieved Apple APIs<br />Android: The Advertising Identifier that is returned from Google Play Services |
| pushidentifier | String | The SHA1 hashed Push Identifier |
| blob | String | The blob value returned by the the Experience Cloud ID Service |
| locationhint | String | The Experience Cloud ID Service region ID. A region ID \(or location hint\), is a numeric identifier for the geographic location of a particular ID service data center |
| visitoridslist | List<Map<String, Object>> | The list of all the customer's custom identifiers |
| lastsync | Long | The timestamp of the last  sync with The Experience Cloud ID Service (in seconds), by default it is 0 |


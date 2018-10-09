

# Events Dispatched by the Identity Service

## Identity Response Identity

This event is a response from the Identity extension to the public API callbacks and it will be generated in response to an Identity Request Identity event when:

- retrieving the Experience Cloud ID (MID) 
- retrieving the custom visitor identifiers
- syncronizing a visitor identifier or a list of identifiers with the Adobe Experience Cloud ID Service is complete
- the result for the append to URL request is ready
- privacy status changes to OPT_OUT and all the identifiers are cleared

### Event Details

| **Event Type**               | **Event Source**                       | **Paired** | **Paired Event**                                             |
| :--------------------------- | :------------------------------------- | :--------- | :----------------------------------------------------------- |
| com.adobe.eventType.identity | com.adobe.eventSource.responseIdentity | Yes        | [Identity Request Identity Event](./events-handled-by-the-identity-service.md#identity-request-identity) |

### Data Payload Definition

Here are the key-value pairs in this event:

| **Key**               | **Value Type**            | **Optional** | **Description**                                              |
| :-------------------- | :------------------------ | :----------- | :----------------------------------------------------------- |
| mid                   | String                    | Yes          | The value of the Experience Cloud Identifier (MID)           |
| advertisingidentifier | String                    | Yes          | The value of the Advertising identifier if it was set before |
| pushidentifier        | String                    | Yes          | The value of the Push Identifier if one was set before       |
| blob                  | String                    | Yes          | The blob value retrieved from the Experience Cloud ID Service |
| locationhint          | String                    | Yes          | The location hint value retrieved from the Experience Cloud ID Service |
| visitoridslist        | List<Map<String, Object>> | Yes          | A list of visitor identifiers that were synced before using `syncIdentifier` or `syncIdentifiers` public APIs. Each visitor id will have the following keys: `id_origin`, `id_type`, `id`, `authentication_state` |
| lastsync              | Long                      | Yes          | Timestamp in seconds of the last sync call sent to the Identity service, by default it is 0 |
| updatedurl            | String                    | Yes          | The update URL when `appendVisitorInfoForURL` (Android) / `appendToUrl` (iOS) public API is called |

### Event Data Example

**When all identifiers, blob and location hint are populated**

```json
{
    "mid": "midExample",
    "advertisingidentifier": "advertisingIdExample",
    "pushidentifier": "pushIdExample",
    "blob": "blobExample",
    "locationhint": "locationHintExample",
    "visitoridslist": [{
        "id_type": "type1",
        "id_origin": "origin1",
        "id": "id1",
        "authentication_state": 1
    },
    {
        "id_type": "type2",
        "id_origin": "origin2",
        "id": "id2",
        "authentication_state": 2
    }]
}
```

### Event Data Example

**The result for the appendToUrl request**

```json
{
    "updatedurl": "https://example.com/path?query=parameter1&other=parameter2&adobe_mc=MCMID%3Dmidexample%7CMCAID%3Daidexample&adobe_aa_vid=visitoridexample"
}
```



## Configuration Content Request 

The Identity Extension dispatches a new Configuration Content Request event when a privacy change is detected. This event will be handled by the Core extension and the new privacy status will be broadcasted to other extensions.

For more details about this event see [SDK Core - Configuration Content Request](../sdk-core/events-handled-by-sdk-core.md#configuration-request-content)



## Analytics Content Request

The Identity Extension dispatches a new Analytics Content Request event when a push status preference change is detected. This event will be handled by the Analytics extension and a new internal hit will be sent to the Adobe Analytics Server.

For more details about this event see [Analytics Content Request](../adobe-analytics/events-handled-by-adobe-analytics.md#analytics-content-request)




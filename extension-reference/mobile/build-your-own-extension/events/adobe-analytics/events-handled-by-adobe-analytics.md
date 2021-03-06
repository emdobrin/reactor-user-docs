# Events Handled by Adobe Analytics

### Analytics Content Request

This event is a request to do processing on the analytics hits queue and can be used in one of the following situations:

* Send a new Analytics hit to the server. 
* Send all the Analytics hits, regardless of the batching settings. 
* Clear all the Analytics hits that are queued, but are not yet sent. 
* Get current number of Analytics hits in the queue.

The Analytics extension receives this event with the following keys, and the extension processes the keys accordingly:

* `action`/`state` - gets the analytics request object and processes the hit.
* `clearhitsqueue` - Clears the analytics hit database.
* `forcekick` - force kicks all the hits in database.
* `getqueuesize` - Event comes with pairID. Analytics module return the count of the current queued hits in database to the associated one time listener.

**Tip**: The keys are optional, but there should be at least one key specified at a time \(action/state/contextdata\) for the hit to be processed.

This event is generated by:

* Calling the Analytics request `track` public API.
* The SDK when tracking internal actions.     For more information, see [Internal Events for Adobe Analytics]().
* Calling the analyticsSendQueuedHits API 
* Calling the analyticsClearQueue public API 
* Calling the analyticsGetQueueSize public API

#### Event Details

| **Event Type** | **Event Source** | **Paired** |
| :--- | :--- | :--- |
| com.adobe.eventType.analytics | com.adobe.eventSource.requestContent | No |

#### Data Payload Definition

The payload definition is composed of the following:

| **Key** | **Value Type** | **Optional** | **Description** |
| :--- | :--- | :--- | :--- |
| action | String | Yes | The action name value. |
| state | String | Yes | The state name value. |
| contextdata | Map&lt;String, String&gt; | Yes | The additional context data parameters. |
| trackinternal | Boot | Yes | Bool indicating whether this is an internal Analytics hit that is generated by the SDK. |
| forcekick | Bool | Yes | Bool indicating whether this is a request for sending all the Analytics hits. |
| clearhitsqueue | Bool | Yes | Bool indicating whether this is a clear hits queue event. |
| getqueuesize | Bool | Yes | Bool indicating whether this is a request for getting current number of queued hits. |

#### Event Data Example

**Track Request**

Here is an example of event data for Track request

```javascript
{
    "action": "clicked add button",
    "state":"cart page",
    "contextdata":{
        "key1" : "value1",
        "key2" : "value2"
    },
    "trackinternal": false
}
```

#### Event Data Example Example

**Send all Analytics Hits Request**

```javascript
{
    "forcekick": true
}
```

#### Event Data Example

**Clear All the Analytics Hits Request**

```javascript
{
    "clearhitsqueue": true
}
```

#### Event Data Example

**Get Queue Size Request**

```javascript
{
    "getqueuesize": true
}
```

### Analytics Request Identity

This event is responsible for fetching the Analytics tracking identifier \(AID\) from the Analytics extension.

This event will be generated the `analyticsGetTrackingIdentifier` public API call.

On receiving the analytics identity request event, Analytics module completes one of the following tasks:

* Checks the persistence for the saved aid.
* If `nil`, creates a sync network request to the analytic server to retrieve aid.

Dispatches an Analytics Identity Response Event with the aid value

#### Data Payload Definition

No key is required for the Analytics request event.

#### Event Details

| **Event Type** | **Event Source** | **Paired** |
| :--- | :--- | :--- |
| com.adobe.eventType.analytics | com.adobe.eventSource.requestIdentity | Yes |

### Configuration Response Content

The data property in this event is used by each extension to modify its current settings. Each extension is responsible for reading out the part of the data property for which it is concerned.

This event will be generated in the following scenarios:

* Initial config requested by the customer.
* Configuration modified \(remote update or local developer action\).
* In response to a configuration request event with the `config.getData` data key.

For more information about the data payload definition for this event, see the _Configuration Keys_ topics in each extension section.

#### Data Payload Definition

The Analytics extension will read the following keys from the configuration event:

| **Key** | **Value Type** | **Optional** | **Description** |
| :--- | :--- | :--- | :--- |
| analytics.server | String | No | The Analytics server |
| analytics.rsids | String | No | The Analytics report suites, separated by commas |
| analytics.batchLimit | Int | Yes | The hit batching settings, by default it is 0. |
| analytics.aamForwardingEnabled | Bool | Yes | Indicates if Audience Manager forwarding is enabled. By default this option is disabled. |
| analytics.offlineEnabled | Bool | Yes | Indicates if Analytics offline batching is enabled. By default this option is disabled. |
| lifecycle.backdateSessionInfo | Bool | Yes | Indicates if Analytics should backdate the lifecycle SessionInfo hit. This flag is used when `analytics.offlineEnabled` is also enabled. By default this option is disabled. |
| analytics.referrerTimeout | Int | Yes | Indicates for how long will the SDK wait for refferer information to come \(the value is in seconds\). By default this value is 0. |
| experienceCloud.org | String | No | The Experience cloud org ID |
| global.ssl | Bool | Yes | Bool indicating if the network call should  use http/https. By default this value will be set on `true` \(https enabled\). |
| global.privacy | String | Yes | This contains the mobile privacy status settings |

#### Event Data Example

```javascript
{ 
"experienceCloud.org": "3CE342C75100435B0A490D4C@AdobeOrg",  
"analytics.rsids": "mobile1pravinsample",  
"analytics.server": "obumobile1.sc.omtrdc.net",  
"analytics.aamForwardingEnabled": false,  
"analytics.offlineEnabled": true,  
"analytics.batchLimit": 0,  
"global.ssl": false,  
"global.privacy": "optedin",  
"global.timezone": "PDT",  
"global.timezoneOffset": -420,  
"lifecycle.sessionTimeout": 300,  
"lifecycle.backdateSessionInfo": false
}
```

### Acquistion Referrer Information Response

This event will be the response from the Acquisition extension for the referrer information request event, and this event will be generated by the Acquisition extension when the request event for referrer information is received by the extension and the information is retrieved by the extension.

Analytics listens to the referrer information and create a analytics hit along with data from the install lifecycle event.

#### Event Details

| **Event Type** | **Event Source** | **Paired** |
| :--- | :--- | :--- |
| com.adobe.eventType.acquisition | com.adobe.eventSource.responseContent | No |

#### Data Payload Definition

Here are the key/value pairs that will be present in this event:

| **Key** | **Value Type** | **Optional** | **Description** |
| :--- | :--- | :--- | :--- |
| contextdata | Map | No | The data value will contain the KV pairs that define the referrer information collected by the Acquisition extension. |

The key-value pairs that are standard and defined in the SDK are:

* campaign 
* source 
* medium 
* term 
* content 
* trackingCode 
* contextData 
* adobeData

The data might contain other custom key-value pairs not defined as standard. Also, all the KV pairs are optional.

#### Sample Event Payload

```javascript
{
    "contextdata": {
        "campaign": "testCampaign",
        "source": "adv",
        "contextData":{
                "contextDatakey":"contextDataValue"
        },
        "adobeData":{
                "adobeDataKey":"adobeDataValue"
        },
        "mycustomkey":"mycustomValue"
    }
}
```

## Lifecycle Request Content

This event represents a request to the Lifecycle module to start/stop collecting data.

This event will be generated when `collectLifecycleData()` and `pauseCollectingLifecycleData()` are used.

The Analytics module listens only to the Lifecycle Start Event, and the Analytics database queue should then be held for up to `DEFAULT_LIFECYCLE_RESPONSE_WAIT_TIMEOUT` \(ms\) to wait for the corresponding response content event.

#### Event Details

| **Event Type** | **Event Source** | **Paired** |
| :--- | :--- | :--- |
| com.adobe.eventType.lifecycle | com.adobe.eventSource.requestContent | No |

#### Data Payload Definition

| **Key** | **Value Type** | **Optional** | **Description** |
| :--- | :--- | :--- | :--- |
| type | String | No | Has one of these values: Start\|Stop\|Pause |
| contextdata | Map | YES | Map containing the user supplied context data. |

```javascript
{
    "type":"start",
    "contextData":{
        "contextDataKey":"contextDataValue"
    }
}
```

## Lifecycle Response Content

This event is a response from the Lifecycle module to notify lifecycle context data in which a client/module might be interested.

This event will be generated by the Lifecycle module, and the public API is called after a lifecycle start, stop, or pause.

The Analytics module waits for the lifecycle data and appends it to a exiting hit. Otherwise, a new hit is sent for lifecycle data, and the lifecycle timer is cancelled to mark that the lifecycle data has been received and processed.

If the backdate session option is enabled, use the timestamp of the last session as the timestamp of the current analytics request, and insert the data to the Analytics queue before any other request is received in the current session.

#### Event Details

| **Event Type** | **Event Source** | **Paired** |
| :--- | :--- | :--- |
| com.adobe.eventType.lifecycle | com.adobe.eventSource.responseContent | No |

#### Data Payload Definition

| **Key** | **Value Type** | **Optional** | **Description** |
| :--- | :--- | :--- | :--- |
| lifecyclecontextdata | Map | NO | . |
| starttimestampmillis | Long | NO |  |
| previoussessionpausetimestampmillis | Long | NO |  |
| maxsessionlength | Long | NO |  |

```javascript
{
    "lifecyclecontextdata":{
        "installdate":"22/05/2014",
        "hourofday":6,
        "dayofweek":2,
        "osversion":"iOS 12.3",
        "appID":"contextDataValue",
        "resolution":"600x1345",
        "devicename":"iPhoneX",
        "launchevent":"LaunchEvent",
        "prevsessionlength":345,
        "locale":"en-US",
        "runmode":"Application",
        "launches":42
    },
    "starttimestampmillis" :44,
    "maxsessionlength":22,
}
```

#### Install Event

The SDK tracks the application install details when lifecycle is enabled. These details include the install date and the daily engaged/monthly engaged user badges.

```javascript
{
    "lifecyclecontextdata": {
        "installevent" : "InstallEvent",
        "dailyenguserevent" : "DailyEngUserEvent",
        "monthlyenguserevent":"MonthlyEngUserEvent",
        "installdate":"22/05/2014"
        ...
    },
}
```

#### Launch Event

When lifecycle is enabled, the SDK tracks every new launch of the application.

A launch is considered to be new when the following conditions are met:

* The application is reopened from the background after the configured session timeout.
* The application is opened after a force close.

Launch data includes information about the application's number of launches, days since first run, days since last use, user daily/monthly engaged badges, and other lifecycle metrics.

We also track the application version upgrades.

```javascript
{
    "lifecyclecontextdata": {
        "launchevent" : "LaunchEvent",
        "dailyenguserevent" : "DailyEngUserEvent",
        "monthlyenguserevent":"MonthlyEngUserEvent",
        "installdate":"22/05/2014",
        "launches":42,
        "dayssincelastuse" : 4,
        "dayssincefirstuse" : 28
        ...
    },
}
```

#### Crash Event

If your application is terminated without having first been backgrounded, the SDK reports a crash the next time your app is launched.

If the `backdateSessionInfo` flag is enabled in your configuration, this information is sent as an individual hit. Otherwise it is sent as part of the launch event.

```javascript
{
    "lifecyclecontextdata": {
        "crashevent" : "CrashEvent",
        "launchevent" : "LaunchEvent",
        "dailyenguserevent" : "DailyEngUserEvent",
        "monthlyenguserevent":"MonthlyEngUserEvent",
        "installdate":"22/05/2014",
        "launches":42,
        "dayssincelastuse" : 4,
        "dayssincefirstuse" : 28
        ...
    },
}
```

#### SessionInfo

This hit contains information, such as the session lenght, about the previous launch session.

If the `backdateSessionInfo` flag is enabled in your configuration, this information is sent as an individual hit. Otherwise it will be sent as part of the launch event.


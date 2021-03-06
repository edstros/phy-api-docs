For access to the `/api/v2/events` endpoint you must be [Authenticated](https://github.com/bkon-connect/phy-api-docs/wiki/Authentication)

#### GET `/api/v2/events`

The index endpoint that returns `current` and `future` events.  Example payload:

```
{
  "current": [
    {
      "_id": "570e88900b37ab737ca13f4d",
      "name": "Black Friday",
      "start": "2016-11-29T03:00:00.000Z",
      "stop": "2016-11-29T10:00:00.000Z",
      'notes": "Black Friday second floor",
      "timezone": "UTC",
      "destination": "580fb6a05e197e010019eeb1",
      "metaMessageActive": false,
      "disabled": false,
      "beacons": [
      "7FBDBb"
      ],
      "proximity": 2,
      "redirectUrl": "http://www.theblackfriday.com/dollar-general-blackfriday.shtml"
    }
  ],
  "future": [
    {
      "_id": "570e87cc0b37ab737ca13efe",
      "name": "Christmas Specials",
      "start": "2016-12-01T10:00:00.000Z",
      "stop": "2016-12-25T18:00:00.000Z",
      "notes": "Christmas event, second floor",
      "timezone": "UTC",
      "destination": "580fc3e75e197e010019ef5b",
      "metaMessageActive": false,
      "disabled": false,
      "beacons": [
      "7FBDBb"
      ],
      "proximity": 2,
      "redirectUrl": "http://www.dollargeneral.com/category/index.jsp?categoryId=15452376"
    }
  ]
}
```

#### GET `/api/v2/events/:id`

Show endpoint for an individual event based on event ID.

Example payload:

```
{
  "_id": "570e87cc0b37ab737ca13efe",
  "name": "Christmas Specials",
  "start": "2016-12-01T10:00:00.000Z",
  "stop": "2016-12-25T18:00:00.000Z",
  "redirectUrl": "http://www.dollargeneral.com/category/index.jsp?categoryId=15452376"
  "proximity": 2,
  "beacons": <array of populated beacon objects>,
  "timezone": "UTC",
    "destination": {
        "_id": "580fb6a05e197e010019eeb1",
        "updatedAt": "2016-10-27T16:25:34.373Z",
        "createdAt": "2016-10-25T19:46:40.992Z",
        "createdBy": 749,
        "account": 001,
        "notes": "example",
        "url": "http://www.dollargeneral.com/category/example",
        "metaMessage": "580fb6a05e197e010019eeb2",
        "__v": 0,
        "proximity": 2,
        "micrositeActive": false,
        "tapActionActive": false,
        "metaMessageActive": true,
        "name": "micro-event"
    },
}
```

#### POST `/api/v2/events`

Create a new `Event` resource with / without a `Meta Message`

Arguments:
<table>
  <tr>
    <th>Parameter</th>
    <th>Example</th>
    <th>Notes</th>
  </tr>
  <tr>
    <td>"name"</td>
    <td>"Name of event"</td>
    <td>`String` - Optional but Recomended</td>
  </tr>
  <tr>
    <td>"redirectUrl"</td>
    <td>"https://www.google.com"</td>
    <td>`String` - defaults to 'https://phy.net/setup'. Required.</td>
  </tr>
  <tr>
    <td>"metaMessage"</td>
    <td>`{"title": "Landing on Google", "description": "a custom description of google"}`</td>
    <td>`Object` - Optional.  See CoverCard™ (also known as MetaMessage) API docs for more options</td>
  </tr>
  <tr>
    <td>"metaMessageActive"</td>
    <td>true</td>
    <td>`Boolean` - defaults to `false`.  Optional.</td>
  </tr>
  <tr>
    <td>"proximity"</td>
    <td>2</td>
    <td>`Int` - defaults to `2`.  Can be `-1`, `0`, `1`, `2`</td>
  </tr>
  <tr>
    <td>"start"</td>
    <td>["2016-05-15", "09:00"]</td>
    <td>`Array` of `String` items - takes a format of ['yyyy-mm-dd', 'hh:mm'].  Required.</td>
  </tr>
  <tr>
    <td>"stop"</td>
    <td>["2016-05-16", "09:00"]</td>
    <td>`Array` of `String` items - takes a format of ['yyyy-mm-dd', 'hh:mm'].  Required.  (even though our schema says otherwise)</td>
  </tr>
  <tr>
    <td>"timezone"</td>
    <td>"America/Los_Angeles"</td>
    <td>`String` - Defaults to UTC. Required.  For options see https://en.wikipedia.org/wiki/List_of_tz_database_time_zones and use the `TZ` column</td>
  </tr>
  <tr>
    <td>"beacons"</td>
    <td>["123test", "456test"]</td>
    <td>`Array` of `String` items - requires an array of Beacon IDs.  Required.</td>
  </tr>
  <tr>
    <td>"destination"</td>
    <td>"43951ajdsflka3241"</td>
    <td>`String` (technically a Mongo ObjectId).  Id of a destination to bind to this event.  Optional.</td>
  </tr>
</table>

Example Payload without `Meta Message`:
```
{
  "_id":"57643d9274c2962083dd3263",
  "name":"My awesome api event without MM",
  "start":"2016-09-01T12:00:00.000Z",
  "stop":"2016-09-02T12:00:00.000Z",
  "redirectUrl":"https://google.com",
  "proximity":2,
  "timezone":"America/Los_Angeles",
  "beacons":["4c92QZ"]
}
```

Example Payload with `Meta Message`:
```
{
  "_id":"57644fd37fcce5d8838b30b9",
  "name":"My awesome api event",
  "start":"2016-09-01T12:00:00.000Z",
  "stop":"2016-09-02T12:00:00.000Z",
  "redirectUrl":"https://google.com",
  "proximity":2,
  "beacons":["4c92QZ"],
  "timezone":"America/Los_Angeles",
  "metaMessage":{
    "_id":"57644fd37fcce5d8838b30ba",
    "title":"My awesome api event mm title",
    "description":"see title"
  },
  "metaMessageActive":true
}
```

#### PUT/PATCH `/api/v2/events/:id`

The update endpoint for individual `Event` resources.  Requires an ID.

Additionally, in order to update an event, ALL update requests must be sent with:

* a list of beacon IDs in array form
* a start and stop time in an array date pair form
* a timezone region (to retain accuracy)

If you want to update a `Meta Message` you must pass through both `metaMessageActive` AND `metaMessage` properties.

(see below for format)

Arguments

<table>
  <tr>
    <th>Parameter</th>
    <th>Example</th>
    <th>Notes</th>
  </tr>
  <tr>
    <td>"name"</td>
    <td>"Name of event"</td>
    <td>`String` - Optional but Highly Recommended. </td>
  </tr>
  <tr>
    <td>"redirectUrl"</td>
    <td>"https://www.google.com"</td>
    <td>`String` - defaults to 'https://phy.net/setup'. Required.</td>
  </tr>
  <tr>
    <td>"metaMessage"</td>
    <td>{"title": "Landing on Google", "description": "a custom description of google"}</td>
    <td>`Object` - Optional.  See CoverCard™ (also known as MetaMessage) API docs for more options</td>
  </tr>
  <tr>
    <td>"metaMessageActive"</td>
    <td>true</td>
    <td>`Boolean` - defaults to `false`.  Optional.</td>
  </tr>
  <tr>
    <td>"proximity"</td>
    <td>2</td>
    <td>`Int` - defaults to `2`.  Can be `-1`, `0`, `1`, `2`. Optional after set once.</td>
  </tr>
  <tr>
    <td>"start"</td>
    <td>["2016-05-15", "09:00"]</td>
    <td>`Array` of `String` items - takes a format of ['yyyy-mm-dd', 'hh:mm'].  Required.</td>
  </tr>
  <tr>
    <td>"stop"</td>
    <td>["2016-05-16", "09:00"]</td>
    <td>`Array` of `String` items - takes a format of ['yyyy-mm-dd', 'hh:mm'].  Required.</td>
  </tr>
  <tr>
    <td>"timezone"</td>
    <td>"America/Los_Angeles"</td>
    <td>`String` - Defaults to UTC. Required.  For options see https://en.wikipedia.org/wiki/List_of_tz_database_time_zones and use the `TZ` column</td>
  </tr>
  <tr>
    <td>"beacons"</td>
    <td>["123test", "456test"]</td>
    <td>`Array` of `String` items - takes an array of Beacon IDs.  Required.</td>
  </tr>
  <tr>
    <td>"destination"</td>
    <td>"43951ajdsflka3241"</td>
    <td>`String` (technically a Mongo ObjectId).  ID of a destination to bind to this event.  Optional.</td>
  </tr>
</table>

Example payload of updating an `Event` without a `Meta Message`:
```
{
  "_id":"576450837fcce5d8838b30c2",
  "name":"My awesome api event without MM updated without MM",
  "start":"2016-09-01T19:00:00.000Z",
  "stop":"2016-09-02T19:00:00.000Z",
  "redirectUrl":"https://google.com",
  "proximity":2,
  "beacons":["3sjpaF"],
  "timezone":"America/Los_Angeles"
}
```
Example payload of updating an `Event` with a `Meta message`:
```
{
  "_id":"576450837fcce5d8838b30c2",
  "name":"My awesome api event without MM updated with MM",
  "start":"2016-09-01T19:00:00.000Z",
  "stop":"2016-09-02T19:00:00.000Z",
  "redirectUrl":"https://google.com",
  "proximity":2,
  "beacons":["3sjpaF"],
  "timezone":"America/Los_Angeles",
  "metaMessage":{
    "_id":"576453167fcce5d8838b30c9",
    "title":"My awesome api event mm title that previously had none",
    "description":"see title"
  },
  "metaMessageActive":true
}
```

#### DELETE `/api/v2/events/:id`

The deletion endpoint for `Event` resources.

There is no response other than a `204` status code.


#### ERRORS

Upon error, the client will receive a `JSON` response in the format of:

```
{
  error: <boolean>,
  message: <intl error message>,
  statusCode: <int>
}
```

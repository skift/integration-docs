# Skift Forum API

## Overview
This API provides access to Forum information like speakers, sponsors, and schedule. This information is maintained by the Events team.

The Skift Forum API is a read-only JSON REST API with token-based authentication. The only accepted method is `GET`.

The base URL of the API is `https://forum.skift.com/wp-json/forum/v1`. All requests must include the parameter `eventId` with a valid eTouches event ID.



## Authentication
The API is authenticated using a bearer token. The token is static and will be provided by the Skift team.



## Usage

### Endpoints
There are four resources to query:
- `/speakers`
- `/schedule`
- `/sponsors`
- `/partners`

### Querying
A `GET` request to a resource will retrieve the entire list of entries. Individual items can be retrieved by ID, i.e. `GET` `speakers/id/1`.

Each table is searchable by providing parameters in this format:
`${RESOURCE}/${KEY}/$VALUE`. For example, `GET` `sponsors/premium/0?eventId=281682` will retrieve all "premium" sponsors for the event with ID 281682. The `${KEY}` must be a valid model attribute, or else the API will return a `404` error.

### Responses
Responses will be a JSON object with the properties `count` (integer) and `items` (array of objects).

### Errors
- `401` Unauthorized
    - The Authentication header has not been provided or is incorrect
- `400` No event ID set
    - All queries must include the `GET` param `eventId`
- `404` Invalid event
    - The event ID does not match the list of current events. Check in with Skift team to make sure you have the correct IDs.
- `404` Invalid column
    - A search cannot be performed because the query has an invalid model key
- `500` server error
    - I broke something (sorry)

## Models

- Speaker
```
    id: integer,
    name: string,
    company: string,
    title: string,
    slug: string,
    image: string,
    showOnHP: boolean,
    branded: boolean,
    bio: string,
    sort: integer (deprecated)
```

- Schedule
```
    id: integer,
    name: string,
    location: string,
    eventDate: MySql formatted date string,
    hideSessionTime: boolean,
    eventEndDate: MySql formatted date string,
    speakers: Array [speaker]
    otherSpeaker: string,
    moderator: string,
    sponsor: integer,
    speaker_tbd: boolean,
    preEvent: boolean,
    description: string
```

- Sponsor
```
id: integer,
name: string,
image: string,
link: string,
premium: boolean,
description: string,
sort: integer
```

- Partner
```
    id: integer,
    name: string,
    image: string,
    link: string,
    premium: boolean,
    description: string,
    sort: integer
```

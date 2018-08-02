# Skift Forum API

## Overview
This API provides access to Forum information like speakers, sponsors, and schedule. This information is maintained by the Events team.

The Skift Forum API is a read-only JSON REST API with token-based authentication. The only accepted method is `GET`.

The base URL of the API is `https://forum.skift.com/wp-json/forum/v1`. All requests except for `GET` `/events` must include the parameter `eventId` with a valid eTouches event ID.



## Authentication
The API is authenticated using a bearer token. The token is static and will be provided by the Skift team.


## Usage

### Endpoints
There are four resources to query:
- `/speakers`
- `/schedule`
- `/sponsors`
- `/events`

### Querying

#### Querying Events
The `/events` endpoint returns an array of objects with active events. No other parameters are required/permitted. This list is completely static -- event properties do not change once they are set. The `eventId` specified in the API response is the same identifier that should be used to retrieve all other forum information.

#### All other queries
A `GET` request to a resource will retrieve the entire list of entries. Individual items can be retrieved by ID, i.e. `GET` `speakers/id/1`.

Each table is searchable by providing parameters in this format:
`${RESOURCE}/${KEY}/$VALUE`. For example, `GET` `sponsors/premium/0?eventId=281682` will retrieve all "premium" sponsors for the event with ID 281682. The `${KEY}` must be a valid model attribute, or else the API will return a `404` error.

To retrieve "Partners," which were previously maintained in their own table, query `sponsors/isPartner/1`.

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
- Event
```
    eventName: string,
    eventId: integer,
    date: string,
    location: string,
    venue: string,
    icon: string
```

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
    sort: integer,
    isPartner: boolean
```

## Examples
- Retrieve basic information about all events
```http
GET /wp-json/forum/v1/events HTTP/1.1
Host: forum.skift.com
Authorization: Bearer #################
```

Response:
```json
{
    "count": 1,
    "items": [
        {
            "eventName": "Skift Global Forum New York",
            "eventId": 281682,
            "date": "September 27-28, 2018",
            "location": "New York City",
            "venue": "Jazz at Lincoln Center’s Frederick P. Rose Hall",
            "icon": null
        }
    ]
}
```

- Retrieve event speakers
```http
GET /wp-json/forum/v1/speakers/?eventId=281682 HTTP/1.1
Host: forum.skift.com
Authorization: Bearer #################
```

Response:
```json
{
    "count": 30,
    "items": [
        {
            "id": 1,
            "company": "Hyatt Hotels Corporation",
            "title": "President and CEO",
            "slug": "Mark-Hoplamazian",
            "image": "https://forum.skift.com/newyork/wp-content/uploads/sites/6/2018/01/Mark-Hoplamazian.jpg",
            "showOnHP": true,
            "branded": false,
            "bio": "<p>Mark S. Hoplamazian was appointed to the board of directors in November 2006 and named president and chief executive officer of&nbsp;Hyatt Hotels Corporation in December 2006. Prior to being appointed to his present position, Hoplamazian served as president of The Pritzker Organization (TPO), the principal financial and investment adviser for Pritzker family business interests. During his 17-year tenure with TPO, he&nbsp;served as adviser to various Pritzker family-owned companies, including Hyatt Hotels Corporation and its predecessors.</p>",
            "sort": 9999,
            "name": "Mark Hoplamazian"
        },
        {
            "id": 2,
            "company": "Ian Schrager Company",
            "title": "Founder",
            "slug": "Ian-Schrager",
            "image": "https://forum.skift.com/newyork/wp-content/uploads/sites/6/2018/01/500x500Ian-Schrager.jpg",
            "showOnHP": true,
            "branded": false,
            "bio": "<p>For over 5 decades, since the 70s, Ian Schrager has achieved international recognition for concepts that have revolutionized the entertainment, hospitality, food and beverage, retail and residential industries. Few people, if any, have had the impact on popular culture that Ian Schrager has.</p>",
            "sort": 9999,
            "name": "Ian Schrager"
        }
    ]
}
```


- Retrieve the whole schedule for an event

```http
GET /wp-json/forum/v1/schedule/?eventId=281682 HTTP/1.1
Host: forum.skift.com
Authorization: Bearer #################
```

Response:
```json
{
    "count": 36,
    "items": [
        {
            "id": 1,
            "name": "Welcome",
            "location": "",
            "eventDate": "2018-09-27 08:45:00",
            "hideSessionTime": false,
            "eventEndDate": null,
            "speakers": [
                {
                    "id": 29,
                    "company": "Skift",
                    "title": "Founder & CEO",
                    "slug": "Rafat-Ali",
                    "image": "https://forum.skift.com/newyork/wp-content/uploads/sites/6/2018/06/rafat.jpg",
                    "showOnHP": false,
                    "branded": false,
                    "bio": "",
                    "sort": 9999,
                    "name": "Rafat Ali"
                }
            ],
            "otherSpeaker": "",
            "moderator": "",
            "sponsor": 0,
            "speaker_tbd": false,
            "preEvent": false,
            "description": ""
        }
    ]
}
```

- Retrieve event speakers
```http
GET /wp-json/forum/v1/speakers/?eventId=281682 HTTP/1.1
Host: forum.skift.com
Authorization: Bearer #################
```

Response:
```json
{
    "count": 30,
    "items": [
        {
            "id": 1,
            "company": "Hyatt Hotels Corporation",
            "title": "President and CEO",
            "slug": "Mark-Hoplamazian",
            "image": "https://forum.skift.com/newyork/wp-content/uploads/sites/6/2018/01/Mark-Hoplamazian.jpg",
            "showOnHP": true,
            "branded": false,
            "bio": "<p>Mark S. Hoplamazian was appointed to the board of directors in November 2006 and named president and chief executive officer of&nbsp;Hyatt Hotels Corporation in December 2006. Prior to being appointed to his present position, Hoplamazian served as president of The Pritzker Organization (TPO), the principal financial and investment adviser for Pritzker family business interests. During his 17-year tenure with TPO, he&nbsp;served as adviser to various Pritzker family-owned companies, including Hyatt Hotels Corporation and its predecessors.</p>",
            "sort": 9999,
            "name": "Mark Hoplamazian"
        },
        {
            "id": 2,
            "company": "Ian Schrager Company",
            "title": "Founder",
            "slug": "Ian-Schrager",
            "image": "https://forum.skift.com/newyork/wp-content/uploads/sites/6/2018/01/500x500Ian-Schrager.jpg",
            "showOnHP": true,
            "branded": false,
            "bio": "<p>For over 5 decades, since the 70s, Ian Schrager has achieved international recognition for concepts that have revolutionized the entertainment, hospitality, food and beverage, retail and residential industries. Few people, if any, have had the impact on popular culture that Ian Schrager has.</p>",
            "sort": 9999,
            "name": "Ian Schrager"
        }
    ]
}
```

- Retrieve brand talk speakers
```http
GET /forum/wp-json/forum/v1/speakers/branded/1?eventId=281682 HTTP/1.1
Host: forum.skift.com
Authorization: Bearer #################
```

Response:
```json
{
    "count": 2,
    "items": [
        {
            "id": 20,
            "company": "Travelsify",
            "title": "Co-Founder and CEO",
            "slug": "Bruno-Chauvat",
            "image": "https://forum.skift.com/newyork/wp-content/uploads/sites/6/2018/04/Bruno-Chauvat-copy.jpg",
            "showOnHP": false,
            "branded": true,
            "bio": "<p>Bruno Chauvat is the co-founder and CEO of Travelsify, the world\\'s first travel product DNA platform, powering personalized voice search for online travel agencies, metasearch, and hotel groups, thanks to its unprecedented database of hotel DNA and restaurant DNA.</p>",
            "sort": 9999,
            "name": "Bruno Chauvat"
        },
        {
            "id": 21,
            "company": "VisitDallas",
            "title": "President and CEO",
            "slug": "phillip-jones",
            "image": "https://forum.skift.com/newyork/wp-content/uploads/sites/6/2018/04/Phillip-Jones-Headshot_Visit-Dallas.jpg",
            "showOnHP": false,
            "branded": true,
            "bio": "<p>Phillip Jones joined VisitDallas as president and CEO in 2003. Under Jones’ direction, the Dallas-Fort Worth area is now the No. 1 visitor destination in the state, and Dallas is a top 10 convention city nationwide</p>",
            "sort": 9999,
            "name": "Phillip  Jones"
        }
    ]
}
```

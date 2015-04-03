## Overview

All requests and responses from the Versal API should be made over https to
`stack.versal.com/api2`.

**Version**: 0.6.6

### Authentication

All requests to the Versal API must be authenticated by setting the SID header
to the unique session ID provided by Versal. If you are an org admin and don't
have one of these `SID`s, please contact Versal and we will provide you one.

```bash
curl https://stack.versal.com/api2/courses/123 \
  -H 'SID: 28438e94-480d-11e3-95fd-ce3f5508acd9'
```

### Pagination

All responses for collections of resources return a subset of the collection and
an `X-Pagination` header describing the total volume of resources available. The
body of the header is a JSON object containing pagination information:

`X-Pagination: {"count":3737,"page":1,"nextPage":2,"perPage":20,"pageCount":187}`

## User Creation

New users may be created within an organization by e-mail address.

### Sample Request

```bash
curl -H 'SID: 28438e94-480d-11e3-95fd-ce3f5508acd9' \
  -X POST \
  -d'{"roles":["learner"],"user":{"email":"rj@versal.com","firstname":"RJ","lastname":"Zaworski","fullname":"RJ Zaworski"}}'\
  https://stack.versal.com/api2/orgs/:orgId/users
```

### Sample Response

```javascript
{
  "user": {
    "email": "rj@versal.com",
    "firstname": "RJ",
    "lastname": "Zaworski",
    "fullname": "RJ Zaworski",
    "subscriptions": [],
    "orgs": []
    "id": "1234"
  },
  "roles": [
    "learner"
  ]
}
```

<table width="250.0%" cellspacing="0" cellpadding="0" class="t1">
<tbody>
<tr>
  <th>Field Name</th>
  <th>Type</th>
  <th>Description</th>
</tr>
<tr>
  <td><tt>id</tt></td>
  <td><tt>String</tt></td>
  <td>The new user’s ID</td>
</tr>
<tr>
  <td><tt>email</tt></td>
  <td><tt>String</tt></td>
  <td>The new user’s e-mail address</td>
</tr>
<tr>
  <td><tt>firstname</tt></td>
  <td><tt>String</tt></td>
  <td>(Optional) The new user’s first name</td>
</tr>
<tr>
  <td><tt>lastname</tt></td>
  <td><tt>String</tt></td>
  <td>(Optional) The new user’s last name</td>
</tr>
<tr>
  <td><tt>fullname</tt></td>
  <td><tt>String</tt></td>
  <td>(Optional) The new user’s full name, or concatenated first and last name if full name is not provided</td>
</tr>
</tbody>
</table>

## User Authentication

User authentication creates the SID needed to launch the course player.

### Sample Request

```bash
curl -H 'SID: 28438e94-480d-11e3-95fd-ce3f5508acd9' \
  -X POST \
  -d '{ "email": "rj@versal.com", "userId": "1234" }' \
  https://stack.versal.com/api2/sessions
```

Either the `email` or `userId` field is required, but not both. Make sure the
emails you supply on user creation are unique.

### Sample Response

```javascript
{
  "sessionId": "abcdeffe-1234-4321-5678-123456789",
  "user": {
    "id": "1234",
    "email": "rj@versal.com",
    "firstname": "RJ",
    "lastname": "Zaworski",
    "fullname": "RJ Zaworski"
  }
}
```

<table width="250.0%" cellspacing="0" cellpadding="0" class="t1">
<tbody>
<tr>
  <th>Field Name</th>
  <th>Type</th>
  <th>Description</th>
</tr>
<tr>
  <td><tt>sessionId</tt></td>
  <td><tt>String</tt></td>
  <td>A valid session ID (SID) for the user</td>
</tr>
<tr>
  <td><tt>user</tt></td>
  <td><tt>Object</tt></td>
  <td>A complete representation of the signed-in user, including:
* id — String the user’s ID
* email — String the user’s e-mail</td>
</tr>
</tbody>
</table>

## User Lookup

SIDs created during authentication may subsequently be used to retrieve the user

### Sample Request

```
curl -H 'SID: abcdeffe-1234-4321-5678-123456789' \
  -X GET \
  https://stack.versal.com/api2/user
```

### Sample Response
```javascript
{
  "id": "1234",
  "email": "rj@versal.com",
  "firstname": "RJ",
  "lastname": "Zaworski",
  "fullname": "RJ Zaworski"
}
```

<table width="250.0%" cellspacing="0" cellpadding="0" class="t1">
<tbody>
<tr>
  <th>Field Name</th>
  <th>Type</th>
  <th>Description</th>
</tr>
<tr>
  <td><tt>id</tt></td>
  <td><tt>String</tt></td>
  <td>The user’s Versal id</td>
</tr>
<tr>
  <td><tt>email</tt></td>
  <td><tt>String</tt></td>
  <td>The user’s e-mail</td>
</tr>
<tr>
  <td><tt>firstname</tt></td>
  <td><tt>String</tt></td>
  <td>The user’s first name</td>
</tr>
<tr>
  <td><tt>lastname</tt></td>
  <td><tt>String</tt></td>
  <td>The user’s last name</td>
</tr>
<tr>
  <td><tt>fullname</tt></td>
  <td><tt>String</tt></td>
  <td>The user’s full name</td>
</tr>
</tbody>
</table>

## User update

SIDs created during authentication may subsequently be used to update the user's
attributes. Note that the `PUT` verb in this instances behaves as a `PATCH`:
attributes omitted in an update request will simply be ignored by the server.

### Sample Request

```
curl -H 'SID: abcdeffe-1234-4321-5678-123456789' \
  -X PUT \
  -d '{"lastname":"Newman"}' \
  https://stack.versal.com/api2/users/1234
```

### Sample Response
```javascript
{
  "id": "1234",
  "email": "rj@versal.com",
  "firstname": "RJ",
  "lastname": "Newman",
  "fullname": "RJ Newman"
}
```

<table width="250.0%" cellspacing="0" cellpadding="0" class="t1">
<tbody>
<tr>
  <th>Field Name</th>
  <th>Type</th>
  <th>Description</th>
</tr>
<tr>
  <td><tt>id</tt></td>
  <td><tt>String</tt></td>
  <td>The user’s Versal id</td>
</tr>
<tr>
  <td><tt>email</tt></td>
  <td><tt>String</tt></td>
  <td>The user’s e-mail</td>
</tr>
<tr>
  <td><tt>firstname</tt></td>
  <td><tt>String</tt></td>
  <td>The user’s first name</td>
</tr>
<tr>
  <td><tt>lastname</tt></td>
  <td><tt>String</tt></td>
  <td>The user’s last name</td>
</tr>
<tr>
  <td><tt>fullname</tt></td>
  <td><tt>String</tt></td>
  <td>The user’s full name</td>
</tr>
</tbody>
</table>

## List of Courses

Courses are available from a globally-available list with optional
per-organization filtering.

### Sample Request

```bash
curl -H 'SID: 28438e94-480d-11e3-95fd-ce3f5508acd9' \
  https://stack.versal.com/api2/courses?orgId=myorganization
```

### Sample Response

```javascript
[
  {
    "id": "1234",
    "title": "My Versal Course",
    "longDesc": "",
    "shortDesc": "",
    "createdAt": "2013-09-08T16:33:34.000Z",
    "org": {
      "orgName": "myorganization"
    }
  }
]

```
## Single Course

Deep course requests (`?depth=full`) retrieve the complete content of a course.
These requests expose the lesson and gadget IDs required by subsequent
statistics requests.

### Sample Request

```bash
curl -H 'SID: 28438e94-480d-11e3-95fd-ce3f5508acd9' \
  https://stack.versal.com/api2/courses/1234?depth=full
```

### Sample Response

```javascript
{
  "id": "1234",
  "title": "My Versal Course",
  "longDesc": "",
  "shortDesc": "",
  "createdAt": "2013-09-08T16:33:34.000Z",
  "lessons": [{
    "id": "123",
    "title": "The First Lesson",
    "gadgets": [{
      "type": "versal/text",
      "id": "123"
    }]
  }],
  "authors": [{ 
    "id": "123",
    "fn": "RJ Zaworski"
  }]
}
```

<table width="250.0%" cellspacing="0" cellpadding="0" class="t1">
<tbody>
<tr>
  <th>Field Name</th>
  <th>Type</th>
  <th>Description</th>
</tr>
<tr>
  <td><tt>id</tt></td>
  <td><tt>String</tt></td>
  <td>The id of the course</td>
</tr>
<tr>
  <td><tt>title</tt></td>
  <td><tt>String</tt></td>
  <td>The title of the course</td>
</tr>
<tr>
  <td><tt>shortDesc</tt></td>
  <td><tt>String</tt></td>
  <td>A short description of the course</td>
</tr>
<tr>
  <td><tt>longDesc</tt></td>
  <td><tt>String</tt></td>
  <td>A longer description of the course</td>
</tr>
<tr>
  <td><tt>createdAt</tt></td>
  <td><tt>Date (ISO-8601 String)</tt></td>
  <td>Date the course was created</td>
</tr>
<tr>
  <td><tt>lessons</tt></td>
  <td><tt>Array[Object]</tt></td>
  <td>An array of objects representing the lessons in the course. Each lesson includes:
* `id` — `String` the lesson ID
* `title` — `String` the lesson title
* `gadgets` — `Array[Object]` a list of gadgets in the lesson describing the gadget as `{ type, id }</tt></td>
</tr>
<tr>
  <td><tt>authors</tt></td>
  <td><tt>Array[Object]</tt></td>
  <td>An array of objects representing all authors of the course. Each author includes:
* `id` — String` the author’s user ID
* `fn` — String` the author’s full name</td>
</tr>
</tbody>
</table>

## Learner Progress

Learner progress requests retrieve a summary of the progress of all users
enrolled in a specific course.

### Sample Request

```bash
curl -H 'SID: 28438e94-480d-11e3-95fd-ce3f5508acd9' \
  https://stack.versal.com/api2/courses/1234/userdata
```

### Sample Response

```javascript
[{
  "email": "rj@versal.com",
  "firstname": "RJ",
  "lastname": "Zaworski",
  "fullname": "RJ Zaworski",
  "title": "Intro to Versal",
  "lastActivity": "2013-10-26T21:46:39.000Z",
  "currentLesson": 3,
  "lastLesson": 2,
  "lastSurveyCompleted": "How is Versal?",
  "courseCompleted": true
}]
```

<table width="250.0%" cellspacing="0" cellpadding="0" class="t1">
<tbody>
<tr>
  <th>Field Name</th>
  <th>Type</th>
  <th>Description</th>
</tr>
<tr>
  <td><tt>email</tt></td>
  <td><tt>String</tt></td>
  <td>User’s e-mail</td>
</tr>
<tr>
  <td><tt>firstname</tt></td>
  <td><tt>String</tt></td>
  <td>User’s first name (if available)</td>
</tr>
<tr>
  <td><tt>lastname</tt></td>
  <td><tt>String</tt></td>
  <td>User’s last name (if available)</td>
</tr>
<tr>
  <td><tt>fullname</tt></td>
  <td><tt>String</tt></td>
  <td>User’s full name (if available)</td>
</tr>
<tr>
  <td><tt>title</tt></td>
  <td><tt>String</tt></td>
  <td>Course title</td>
</tr>
<tr>
  <td><tt>lastActivity</tt></td>
  <td><tt>Date (ISO-8601 String)</tt></td>
  <td>Date of user’s last activity in course</td>
</tr>
<tr>
  <td><tt>currentLesson</tt></td>
  <td><tt>Number</tt></td>
  <td>Index of the most recent lesson accessed by the user</td>
</tr>
<tr>
  <td><tt>lastLesson</tt></td>
  <td><tt>Number</tt></td>
  <td>Index of the previous lesson accessed by the user</td>
</tr>
<tr>
  <td><tt>lastSurveyCompleted</tt></td>
  <td><tt>String</tt></td>
  <td>Title of the last survey completed</td>
</tr>
<tr>
  <td><tt>courseCompleted</tt></td>
  <td><tt>Boolean</tt></td>
  <td>Whether the course has been completed</td>
</tr>
</tbody>
</table>

## Gadget Config

Gadget configurations include all user-defined metadata (e.g. the title and
questions of a survey) attached to a particular gadget.

### Sample Request

```bash
curl -H 'SID: 28438e94-480d-11e3-95fd-ce3f5508acd9' \
  https://stack.versal.com/api2/courses/123/lessons/123/gadgets/321/config
```

### Sample Response (will vary between gadgets)

```javascript
{
  "title": "A title"
}
```

***Note****: the contents of the response **Object** are defined at the gadget
level and will vary from one gadget to the next*

## Gadget User Data

Gadget user data requests return the interactions of all users with a particular
gadget (e.g. user responses to a survey)

### Sample Request

```bash
curl -H 'SID: 28438e94-480d-11e3-95fd-ce3f5508acd9' \
  https://stack.versal.com/api2/courses/123/lessons/123/gadgets/321/userdata
```

### Sample Response

```javascript
[{
  "user": {
    "id": "123",
    "fn": "RJ Zaworski",
    "email": "rj@versal.com"
  },
  "state": {
    "answers": [
      "IHNFC"
    ]
  }
}]
```

<table width="250.0%" cellspacing="0" cellpadding="0" class="t1">
<tbody>
<tr>
  <th>Field Name</th>
  <th>Type</th>
  <th>Description</th>
</tr>
<tr>
  <td><tt>user</tt></td>
  <td><tt>Object</tt></td>
  <td>An object containing the e-mail of the user who completed the survey
* `id` — `String` the user’s user ID
* `fn` — `String` the user’s full name
* `email` — `String` representation of the user’s e-mail address (if available)</td>
</tr>
<tr>
  <td><tt>state</tt></td>
  <td><tt>Object</tt></td>
  <td>An object containing the user’s interactions</td>
</tr>
</tbody>
</table>

***Note:*** *the schema of the _state_ field in the response is defined at the
gadget level and will vary from one gadget to the next.*

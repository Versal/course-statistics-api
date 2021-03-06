Versal Partner API
==================
The Versal Partner API enables organizations to manage users, courses, and performance metrics from applications built on top of the Versal Platform.

To get started, try out our interactive [API demo](https://versal.com/learn/beb7mv/lessons/1).

# Concepts
## API Keys
API keys grant broad access to organization data on Versal. Among other operations, they can be used to:
- create and modify organization users
- create sessions for organization users
- retrieve organizational courses
- retrieve learner metrics

If your existing organization has not received an API key, please [contact Versal support](https://support.versal.com) and we will issue one. If you have not yet registered with Versal, [sign up for a free trial](https://versal.com/trial/business) to receive your API key.
## User Sessions
User sessions grant a single user restricted access to organization resources. They can be used to retrieve, launch, and consume content using the Versal Course Player.
## User Org Roles
Users are assigned a role within your organization.

  | Role        | Description           | 
  | ------------- |---------------| 
  | admin|  Admins have full control over all of the resources within the organization. They can add and remove users from the organization, manage user roles, edit and manage courses, and view course analytics.
  | instructor|  Instructors can view any course shared with the organization, create and manage their own courses, invite learners to courses, and view course analytics.
  | learner|  Learners can enroll in any course shared with the organization.
  | member|  Members are associated with the customer organization but have no permissions on any courses or individual orgs. These have to be explicitly granted.
## User Course Roles
Users can be assigned a role on a course.

_Please note that org roles take precedence over course roles_

  | Role        | Description           | 
  | ------------- |---------------| 
  | author/contributor|  Authors or contributors can edit the course.
  | learner|  Learners can learn the course.
  | publisher|  Publishers can publish newer revisions of the course - all learners only see the latest revision.
## Versal Course Player
The Versal Course Player is a browser-based runtime for Versal course content. An overview of its usage and API is available [here](https://support.versal.com/hc/en-us/articles/203271866-Embedding-organizational-courses).
# Topics
## Requests
All requests and responses reference the Versal Partner API available at: https://stack.versal.com/api2. Requests must be made over HTTPS; requests over standard HTTP will be redirected to the corresponding HTTPS endpoint.
## Authentication
All requests to the Versal API must include an SID header with a valid API key or user session ID. If your organization has not received an API key, please please [contact Versal support](https://support.versal.com) and we will issue one.

``` curl -H 'SID: YOUR_API_KEY' https://stack.versal.com/api2/courses/123  ```
## Pagination
All responses for collections of resources return a subset of the collection and an X-Pagination header describing the total volume of resources available. The body of the header is a JSON object containing pagination information:

``` X-Pagination: {"count":3737,"page":1,"nextPage":2,"perPage":20,"pageCount":187} ```

Subsequent pages from the collection endpoint may be retrieved by setting the page query parameter:
``` curl https://stack.versal.com/api2/courses/123/userdata?page=2 -H 'SID: YOUR_API_KEY' ```

## 
- [Session Creation](#sessions)
- [Users and Organization Members](#users)
- [Courses](#courses)
- [Course Learners](#course-learners)
- [Course Learner Progress](#course-learner-progress)
- [Gadget Configuration](#gadget-configurations)
- [Gadget Learner Data](#gadget-learner-data)

**Version:** 1.0

## Additional Resources

**Terms of service:**  
https://versal.com/terms

[Contact Versal Support](https://support.versal.com/)

This document is also available in [Swagger 2.0](versal-partner-api-swagger.yaml) format. 

# Endpoints

## Sessions

### /sessions
---
##### ***POST***
**Summary:** Add a new user API session

**Description:** User sessions include the SID needed to launch the Versal Course Player. User sessions have a 30-day lifetime. The session lifetime is refreshed with every use.

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| SID | header | API key | Yes | string (uuid) |
| body | body | Indicates the session owner user | Yes | [Session_Input_Model](#session_input_model) |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 201 | Session created | [Session_View_Model](#session_view_model) |
| 400 | Bad request | [Error_View_Model](#error_view_model) |
| 401 | Invalid credentials | [Error_View_Model](#error_view_model) |
| 403 | Insufficient permissions | [Error_View_Model](#error_view_model) |
| 404 | User not found | [Error_View_Model](#error_view_model) |

## Users

### /orgs/{orgId}/delete_users
---
##### ***POST***
**Summary:** Batch remove a list of users identified by user ID from your organization

**Description:** See [DELETE /orgs/{orgId}/users/{userId}](#delete)

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| SID | header | API key | Yes | string (uuid) |
| orgId | path |  | Yes | integer |
| body | body |  | Yes | [Batch_Delete_Members_Input_Model](#batch_delete_members_input_model)

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | OK |  |
| 400 | Bad request | [Error_View_Model](#error_view_model) |
| 401 | Invalid credentials | [Error_View_Model](#error_view_model) |
| 403 | Insufficient permissions | [Error_View_Model](#error_view_model) |
| 404 | Org or User not found | [Error_View_Model](#error_view_model) |


### /orgs/{orgId}/users
---
##### ***POST***
**Summary:** Add a new user to your organization with a role

**Description:** Add a new user to your organization with a role

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| SID | header | API key | Yes | string (uuid) |
| orgId | path |  | Yes | integer |
| body | body |  | Yes | [Org_User_Input_Model](#org_user_input_model) |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 201 | User created | [Org_User_View_Model](#org_user_view_model) |
| 400 | Bad request | [Error_View_Model](#error_view_model) |
| 401 | Invalid credentials | [Error_View_Model](#error_view_model) |
| 403 | Permission denied | [Error_View_Model](#error_view_model) |

### /user
---
##### ***GET***
**Summary:** Retrieve the user associated with this SID

**Description:** Retrieve the user associated with this SID

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| SID | header | The session ID for the user of interest | Yes | string (uuid) |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | OK | [User_View_Model](#user_view_model) |
| 401 | Invalid credentials | [Error_View_Model](#error_view_model) |

### /users/{userId}
---
##### ***PUT***
**Summary:** Update a user record

**Description:** Both API keys and user session IDs may be used to modify existing users. Note that the PUT verb in this instances behaves as a PATCH: attributes omitted in an update request will simply be ignored by the server.

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| SID | header | API integration key or session belonging to the user of interest | Yes | string (uuid) |
| userId | path | Numeric user ID | Yes | integer |
| body | body |  | Yes | [User_Patch_Model](#user_patch_model) |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 201 | OK | [User_View_Model](#user_view_model) |
| 400 | Bad request | [Error_View_Model](#error_view_model) |
| 401 | Invalid credentials | [Error_View_Model](#error_view_model) |
| 403 | Insufficient permissions | [Error_View_Model](#error_view_model) |
| 404 | User not found | [Error_View_Model](#error_view_model) |

### /orgs/{orgId}/users/{userId}
---
##### ***DELETE***
**Summary:** Remove a user from your organization

**Description:** Removes the user from organization member lists. Revokes the user's permission to interact with resources in your organization. Removes the user's enrollment records for courses in your organization. Untracks the user from course learner reporting.

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| SID | header | API integration key | Yes | string (uuid) |
| orgId | path | Your numeric organization ID | Yes | integer |
| userId | path | The numeric user ID | Yes | integer |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | OK |  |
| 400 | Bad request | [Error_View_Model](#error_view_model) |
| 401 | Invalid credentials | [Error_View_Model](#error_view_model) |
| 403 | Insufficient permissions | [Error_View_Model](#error_view_model) |
| 404 | Org or User not found | [Error_View_Model](#error_view_model) |


##### ***PUT***
**Summary:** Update a user's role in your organization

**Description:** Change or remove a user's role within your organization

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| SID | header | API integration key | Yes | string (uuid) |
| orgId | path | Your numeric organization ID | Yes | integer |
| userId | path | The numeric user ID | Yes | integer |
| body | body | Describes user's updated role | Yes | [Role_Update_Input_Model](#role_update_input_model) |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 201 | OK | object |
| 400 | Bad request | [Error_View_Model](#error_view_model) |
| 401 | Invalid credentials | [Error_View_Model](#error_view_model) |
| 403 | Insufficient permissions | [Error_View_Model](#error_view_model) |
| 404 | Org or User not found | [Error_View_Model](#error_view_model) |

### /orgs/{orgId}/members
---
##### ***GET***
**Summary:** List users in your organization

**Description:** The organization member listing displays users in your organization and their assigned role. Users that are part of the organization but have not been assigned a role are shown with an empty roles:[] record. 
Returns a paginated result set.

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| SID | header | API integration key | Yes | string (uuid) |
| orgId | path | Your numeric organization ID | Yes | integer |
| page | query | View page of a paginated result set | No | integer |
| perPage | query | Set page size of a paginated result set | No | integer |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | OK | [ [Org_Member_View_Model](#org_member_view_model) ] |
| 400 | Bad request | [Error_View_Model](#error_view_model) |
| 401 | Invalid credentials | [Error_View_Model](#error_view_model) |
| 403 | Insufficient permissions | [Error_View_Model](#error_view_model) |
| 404 | Org not found | [Error_View_Model](#error_view_model) |

## Courses

### /courses
---
##### ***GET***
**Summary:** List available courses

**Description:** Lists courses available to the calling session. 
Published course revisions are returned when the calling session does not have edit rights for the course. In-edit (sandbox) course revisions are returned when the calling session has edit rights for the course.
Returns a paginated result set.

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| SID | header | API integration key or user session ID | Yes | string (uuid) |
| title | query | Filter for courses with title containing this string (case insensitive) | No | string |
| author | query | Filter for courses where the given user ID has permission to edit and/or publish the course | No | integer |
| catalog | query | Filter for courses that are published ("labs") or unpublished ("sandbox") | No | string |
| page | query | View page of a paginated result set | No | integer |
| perPage | query | Set page size of a paginated result set | No | integer |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | OK | [ [Base_Course_View_Model](#base_course_view_model) ] |
| 400 | Bad request | [Error_View_Model](#error_view_model) |
| 401 | Invalid credentials | [Error_View_Model](#error_view_model) |

### /courses/{courseId}
---
##### ***GET***
**Summary:** Retrieve content and metadata of a course

**Description:** Deep course requests (?depth=full) retrieve the complete content of a course. These requests expose the lesson and gadget IDs required by subsequent requests.
The published course revision is returned when the calling session does not have edit rights for the course. The in-edit (sandbox) course revision is returned when the calling session has edit rights for the course. To request the published course revision with a privileged session, use "GET /catalogs/staged/courses/{courseId}"

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| SID | header | API integration key or user session ID | Yes | string (uuid) |
| courseId | path |  | Yes | string |
| depth | query | Depth "full" returns all course lessons and gadgets. Depth "lessons" returns course lessons only and omits the lesson.gadget[] arrays. | No | string |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | OK | [Extended_Course_View_Model](#extended_course_view_model) |
| 401 | Invalid credentials | [Error_View_Model](#error_view_model) |
| 403 | Permission denied | [Error_View_Model](#error_view_model) |
| 404 | Not found | [Error_View_Model](#error_view_model) |

### /catalogs/staged/courses/{courseId}
---
##### ***GET***
**Summary:** Request the published revision of the given Course. See "GET /courses/{courseId}"

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| courseId | path |  | Yes | string |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | OK | [Extended_Course_View_Model](#extended_course_view_model) |
| 401 | Invalid credentials | [Error_View_Model](#error_view_model) |
| 403 | Permission denied | [Error_View_Model](#error_view_model) |
| 404 | Not found | [Error_View_Model](#error_view_model) |

## Course Learners

### /courses/{courseId}/users/{userId}
---
##### ***PATCH***
**Summary:** Mark a learner as tracked for a course

**Description:** Tracking a user within a course will include that learner's progress in subsequent course progress reports.

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| SID | header | API integration key | Yes | string (uuid) |
| courseId | path | Course where learner's progress will be tracked | Yes | string |
| userId | path | Learner's user ID | Yes | string |
| body | body | Toggles learner tracking for the user | Yes | [User_Tracking_Input_Model](#user_tracking_input_model) |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | OK | object |
| 400 | Bad request | [Error_View_Model](#error_view_model) |
| 401 | Invalid credentials | [Error_View_Model](#error_view_model) |
| 403 | Permission denied | [Error_View_Model](#error_view_model) |
| 404 | Not found | [Error_View_Model](#error_view_model) |

## Course Learner Progress

### /courses/{courseId}/userdata
---
##### ***GET***
**Summary:** Retrieve learner progress summaries for a course

**Description:** Learner progress requests retrieve a summary of the progress of all users enrolled in a specific course.
Returns a paginated result set.

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| SID | header | API integration key | Yes | string (uuid) |
| courseId | path |  | Yes | string |
| page | query | View page of a paginated result set | No | integer |
| perPage | query | Set page size of a paginated result set | No | integer |
| userIds | query | Comma-separated list of user IDs. Results will be filtered to users in this list. | No | string (CSV) |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | OK | [Learner_Progress_View_Model](#learner_progress_view_model) |
| 400 | Bad request | [Error_View_Model](#error_view_model) |
| 401 | Invalid credentials | [Error_View_Model](#error_view_model) |
| 403 | Permission denied | [Error_View_Model](#error_view_model) |
| 404 | Not found | [Error_View_Model](#error_view_model) |

## Gadget Configurations

### /courses/{courseId}/gadgets/{gadgetId}/config
---
##### ***GET***
**Summary:** Retrieve the configuration for a single gadget

**Description:** Gadget configurations include all user-defined metadata (e.g. the title and questions of a survey) attached to a particular gadget. Schema varies by gadget type.
When the calling session belongs to any client with edit permissions on the parent course, the in-edit (sandbox) version of the gadget configuration is returned. When the calling session belongs to a client without edit permissions on the course, the published version of the gadget configuration is returned. 
When the calling session belongs to a user with edit permissions on the parent course, the response will include the gadget's private fields (e.g., the answer key for a quiz gadget). For all other calling session types, gadget private fields will be redacted from the response.

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| courseId | path | ID for the course containing the gadget | Yes | string |
| gadgetId | path | Gadget ID | Yes | string |
| SID | header | API integration key or user session | Yes | string (uuid) |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | OK | object |
| 401 | Invalid credentials | [Error_View_Model](#error_view_model) |
| 403 | Permission denied | [Error_View_Model](#error_view_model) |
| 404 | Not found | [Error_View_Model](#error_view_model) |

### /catalogs/staged/courses/{courseId}/gadgets/{gadgetId}/config
---
##### ***GET***
**Summary:** Retrieve the published version of the configuration for a single gadget

**Description:** See "GET /courses/{courseId}/gadgets/{gadgetId}/config"

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| courseId | path | ID for the parent course | Yes | string |
| gadgetId | path | Gadget ID | Yes | string |
| SID | header | API integration key or user session | Yes | string (uuid) |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | OK | object |
| 401 | Invalid credentials | [Error_View_Model](#error_view_model) |
| 403 | Permission denied | [Error_View_Model](#error_view_model) |
| 404 | Not found | [Error_View_Model](#error_view_model) |

## Gadget Learner Data

### /courses/{courseId}/lessons/{lessonId}/gadgets/{gadgetId}/userdata
---
##### ***GET***
**Summary:** Retrieve user data for a single gadget

**Description:** Gadget user data requests return the interactions of all users with a particular gadget (e.g. user responses to a quiz).
Returns a paginated result set.

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| courseId | path | ID for the parent course | Yes | string |
| lessonId | path | ID for the parent lesson | Yes | string |
| gadgetId | path | Gadget ID | Yes | string |
| page | query | View page of a paginated result set | No | integer |
| perPage | query | Set page size of a paginated result set | No | integer |
| SID | header | API integration key or user session | Yes | string (uuid) |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | OK | [ [Gadget_User_Data_View_Model](#gadget_user_data_view_model) ] |
| 401 | Invalid credentials | [Error_View_Model](#error_view_model) |
| 403 | Permission denied | [Error_View_Model](#error_view_model) |
| 404 | Not found | [Error_View_Model](#error_view_model) |

### Models
---

### Base_Course_View_Model  

| Name | Type | Description | 
| ---- | ---- | ----------- |
| catalogs | [ string ] | Contains 'labs' if the course has been published |
| courseId | string | Duplicate of id. |
| coverImage | object | Course cover image metadata. Omitted if the course does not have a cover image. |
| createdAt | date | Course creation time |
| id | string | Unique, API-generated course identifier |
| public | boolean | Indicates whether the course is publicly available |
| longDesc | string | A paragraph statement describing the course. Omitted if not defined. |
| shortDesc | string | A summary statement of the course. Omitted if not defined. |
| tags | [ string ] | A list of user-defined tags indicating course metadata |
| title | string | Course title. Omitted if not defined. |
| users | [ [User_View_Model](#user_view_model) ] | Lists users with rights to edit and/or publish the course |

### Batch_Delete_Members_Input_Model

| Name | Type | Description |
| ---- | ---- | ----------- |
| users | [string] | List of user IDs |

### Error_View_Model  

| Name | Type | Description |
| ---- | ---- | ----------- |
| error | integer | HTTP error code |
| message | string | Describes the error condition |

### Extended_Course_View_Model  

| Name | Type | Description | 
| ---- | ---- | ----------- | 
| catalogs | [ string ] | Contains 'labs' if the course has been published |
| courseId | string | Duplicate of id. |
| coverImage | object | Course cover image metadata. Omitted if the course does not have a cover image. |
| currentPosition | [Learner_Course_Position_View_Model](#Learner_Course_Position_View_Model) | Information about the session user's most recent course activity. Omitted if the user has not visited the course or if the calling session is an API key. |
| createdAt | date | Course creation time |
| id | string | Unique, API-generated course identifier |
| public | boolean | Indicates whether the course is publicly available |
| longDesc | string | A paragraph statement describing the course. Omitted if not defined. |
| shortDesc | string | A summary statement of the course. Omitted if not defined. |
| status    | string | `"complete"` if the session user has completed the course. Omitted if the user has not completed the course or if the calling session is an API key.|
| tags | [ string ] | A list of user-defined tags indicating course metadata |
| title | string | Course title. Omitted if not defined. |
| users | [ [User_View_Model](#user_view_model) ] | Lists users with rights to edit and/or publish the course |
| lastPublished | date | Date of most recent course publication |
| lessons | [ [Lesson_View_Model](#lesson_view_model) ] |  |

### Gadget_View_model  

| Name | Type | Description | 
| ---- | ---- | ----------- | 
| id | string | Gadget identifier. Unique within a course. |
| type | string | Gadget type |
| config | object | Schema varies by gadget type |

### Gadget_User_Data_View_Model  

| Name | Type | Description | 
| ---- | ---- | ----------- | 
| user | [User_View_Model](#user_view_model) |  |
| state | object | Schema varies by gadget type |

### Learner_Course_Position_View_Model
| Name | Type | Description |
| ---- | ---- | ----------- |
| currentLessonId | string | Learner's last visited lesson ID |
| currentLessonIndex | integer | Learner's last visited lesson index |
| updatedAt | date | Learner's last course visit date |

### Learner_Progress_View_Model  

| Name | Type | Description | 
| ---- | ---- | ----------- | 
| completeDate | date | Date learner completed the course. Omitted if learner has not completed the course. |
| lastActivity | date | Date learner last visited the course |
| email | string (email) | Learner's email address. Omitted if not available. |
| courseCompleted | boolean | Indicates whether learner has completed the course |
| firstname | string | Learner's given name. Omitted if not available. |
| currentLesson | integer | Index of last visited lesson |
| lastname | string | Learner's family name. Omitted if not available. |
| id | string | Learner's user ID |
| fullname | string | Learner's full name. Omitted if not available. |
| title | string | Course title |
| startDate | date | Date learner started the course |
| currentLessonId | string | ID of last visited lesson |

### Lesson_View_Model  

| Name | Type | Description | 
| ---- | ---- | ----------- | 
| id | string | Lesson identifier. Unique within a course. |
| title | string | Lesson title. |
| gadgets | [ [Gadget_View_model](#gadget_view_model) ] |  |

### Member_View_Model  

| Name | Type | Description |
| ---- | ---- | ----------- |
| id | string | User API ID |
| firstname | string | Given name |
| lastname | string | Family name |
| fullname | string | Full name |
| email | string (email) |  |

### Org_Member_View_Model  

| Name | Type | Description |
| ---- | ---- | ----------- |
| user | [Member_View_Model](#member_view_model) |  |
| roles | [ string ] | Accepted values: "admin", "instructor", "learner" | No |

### Org_User_Input_Model  

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| roles | [ string ] | Accepted values: "admin", "instructor", "learner", "member" | Yes |
| user | [User_Input_Model](#user_input_model) |  | Yes |

### Org_User_View_Model  

| Name | Type | Description | 
| ---- | ---- | ----------- |
| roles | [ string ] | Values: "admin", "instructor", "learner" | No |
| user | [User_View_Model](#user_view_model) |  |

### Role_Update_Input_Model  

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| roles | [ string ] | Accepted values: "admin", "instructor", "learner" | Yes |

### Session_Input_Model  

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| userId | string (int64) | Required if email is not given | (Yes) |
| email | string (email) | Required if userId is not given | (Yes) |

### Session_View_Model  

| Name | Type | Description |
| ---- | ---- | ----------- |
| sessionId | string |  |
| user | [User_View_Model](#user_view_model) |  |

### User_Input_Model  

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| email | string (email) | Unique email address | Yes |
| firstname | string | Given name | No |
| lastname | string | Family name | No |
| fullname | string | Full name | No |
| longDesc | string | A paragraph about the user | No |
| shortDesc | string | A brief statement about the user | No |

### User_Patch_Model  

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| firstname | string | Given name | No |
| lastname | string | Family name | No |
| fullname | string | Full name | No |
| longDesc | string | A paragraph about the user | No |
| shortDesc | string | A brief statement about the user | No |

### User_Tracking_Input_Model

| Name | Type | Description |
| ---- | ---- | ----------- |
| tracked | boolean | Whether the user is tracked for the course |
| roles | [ string ] | Accepted values: "learner", "author", "contributor", "publisher" |

### User_View_Model  

| Name | Type | Description |
| ---- | ---- | ----------- |
| id | string (int64) | Unique API-generated identifier |
| username | string | A unique, human-readable user identifier |
| email | string (email) | Unique email address |
| firstname | string | Given name |
| lastname | string | Family name |
| fullname | string | Full name |
| fn | string | Alternate full name |
| displayname | string | A calculated display name based on available non-null name fields |
| longDesc | string | A paragraph about the user |
| shortDesc | string | A brief statement about the user |
| image | object | Profile image asset metadata |

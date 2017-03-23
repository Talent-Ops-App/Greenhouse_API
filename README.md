# Greenhouse_API

## List of relevant server sample requests and response to the greenhouse api server

## Application API
### The application object
Applications associate candidates with jobs. There are 2 kinds of applications: candidates and prospects. Candidate applications always have exactly 1 job. Prospect applications can have 0 or more jobs.

### Noteworthy attributes

|**Attribute**| **Description**|
|-------------|------------------|
|id	| Application ID
|prospect	| If true, this is a prospect application which means that the associated person is a prospect and has not yet applied for |this job.
|status	| One of: active, rejected, hired.
|jobs	| An array containing the job that the candidate applied for.
|candidate_id |	The ID of the candidate who is applying for this job.
|current_stage |	The current stage that this application is in.
|credited_to.id |	The ID of the user who will receive credit for this application.
|answers	| The answers provided to the questions in the job post for this application. Array contains the text value of the question and answer. Answers are always plaintext strings. Booleans will return Yes or No.
|custom_fields	| Contains a hash of the custom fields configured for this resource. The properties in this hash reflect the active custom fields as of the time this method is called.
|keyed_custom_fields |	This contains the same information as custom_fields but formatted in a different way that includes more information. This will tell you the type of custom field data to expect, the text name of custom field, and the value. The key of this hash is the custom field’s immutable field key, which will not change even if the name of the custom field is changed in Greenhouse.

#### Sample Response
```json
{
  "id": 985314,
  "candidate_id": 978031,
  "prospect": false,
  "applied_at": "2016-03-26T20:11:39.000Z",
  "rejected_at": "2016-08-17T21:08:29.686Z",
  "last_activity_at": "2016-08-27T16:13:15.000Z",
  "source": {
    "id": 1871,
    "public_name": "Happy Hour"
  },
  "credited_to": {
    "id": 4080,
    "name": "Kate Austen",
    "employee_id": null
  },
  "rejection_reason": {
    "id": 8,
    "name": "Lacking skill(s)/qualification(s)",
    "type": {
      "id": 1,
      "name": "We rejected them"
    }
  },
  "rejection_details": null,
  "jobs": [
    {
      "id": 123,
      "name": "Accounting Manager"
    }
  ],
  "status": "rejected",
  "current_stage": {
    "id": 62828,
    "name": "Recruiter Phone Screen"
  },
  "answers": [
    {
      "question": "Why do you want to work for us?",
      "answer": "I heard you're awesome!"
    },
    {
      "question": "How did you hear about this job?",
      "answer": "From a former colleague."
    }
  ],
  "custom_fields": {
    "bio": "This is a bio",
    "birthday": "1992-01-27"
  },
  "keyed_custom_fields": {
    "date_of_birth": {
      "name": "Birthday",
      "type": "date",
      "value": "1992-01-27"
    },
    "bio": {
      "name": "Bio",
      "type": "long_text",
      "value": "This is a bio"
    }
  }
}
```

### GET: List Applications

#### List all of an organization’s applications.

#### HTTP Request

***GET `https://harvest.greenhouse.io/v1/applications`***

|Querystring |parameters|
|------------|----------|
|Parameter | Description
|per_page | Return up to this number of objects per response. Must be an integer between 1 and 500. Defaults to 100.
|page | A cursor for use in pagination. Returns the n-th chunk of per_page objects.
|created_before	| Return only applications that were created before this timestamp. Timestamp must be in in ISO-8601 format.
|created_after | Return only applications that were created after this timestamp. Timestamp must be in in ISO-8601 format.
|last_activity_after |	Return only applications where ‘updated_at’ is after this timestamp. Timestamp must be in in ISO-8601 format.
|job_id | If supplied, only return applications that involve this job. Will return both candidates and prospects.

#### Sample Response
```json
[
  {
    "id": 4341,
    "candidate_id": 23123,
    "prospect": true,
    "applied_at": "2014-03-26T18:03:45.000Z",
    "rejected_at": null,
    "last_activity_at": "2014-03-27T16:12:02.000Z",
    "source": {
      "id": 13,
      "public_name": "Linkedin (Prospecting)"
    },
    "credited_to": {
      "id": 4080,
      "name": "Kate Austen"
    },
    "rejection_reason": null,
    "rejection_details": null,
    "jobs": [
      {
        "id": 203037,
        "name": "Full Stack Engineer - New York "
      },
      {
        "id": 201428,
        "name": "Full Stack Engineer - Austin"
      }
    ],
    "status": "active",
    "current_stage": null,
    "answers": []
  }
]
```


### GET: Retrieve Application

#### Retrieve an application by its id.

#### HTTP Request

***GET `https://harvest.greenhouse.io/v1/applications/{id}`***

URL parameters

|Parameter | Description|
|----------|------------|
|id	| ID of the application to retrieve

#### Sample Response
```json
{
  "id": 23239258,
  "candidate_id": 14834098,
  "prospect": false,
  "applied_at": "2015-12-28T20:53:12.801Z",
  "rejected_at": null,
  "last_activity_at": "2016-12-30T07:10:03.082Z",
  "source": {
    "id": 16,
    "public_name": "Referral"
  },
  "credited_to": {
    "id": 107050,
    "name": "Ivan Interviewer",
    "employee_id": null
  },
  "rejection_reason": null,
  "rejection_details": null,
  "jobs": [
    {
      "id": 87181,
      "name": "Product Manager"
    }
  ],
  "status": "hired",
  "current_stage": null,
  "answers": [
    {
      "question": "How many years experience do you have?",
      "answer": "5-7"
    },
    {
      "question": "Website",
      "answer": "mywebsite.com"
    }
  ]
}
```


### DELETE: Delete Application

Delete an application by id. Note that only candidate applications can be deleted, you cannot delete a prospect application.

#### HTTP Request

***DELETE `https://harvest.greenhouse.io/v1/applications/{id}`***.

The above returns a JSON response, structured like this:
```json
{
  "message": "Application 29622362 has been deleted."
}
```
#### Headers
|Header	| Description|
|-------|------------|
|On-Behalf-Of | ID of the user issuing this request. Required for auditing purposes.


### PATCH: Update Application

Update this application. The response is populated with the application’s information which will reflect its new state. You can update applications whose status is active, rejected, or hired.

#### HTTP Request

***PATCH `https://harvest.greenhouse.io/v1/applications/{id}`***

#### Headers

|Header | Description|
|-|-|
|On-Behalf-Of| ID of the user issuing this request. Required for auditing purposes.|

#### JSON Body Parameters

|Parameter | Required | Type | Description|
|-|-|-|-|
|source_id |	No |	integer |	The ID of the application’s source
|referrer |	No	|object	|An object representing the referrer
|referrer[type]	|No	|string	|A string representing the type of referrer: 'id’, 'email’, or 'outside’
|referrer[value]	|No	|string	|The id of the user who made the referral (not the referrer id)
|custom_fields[]	|No	|custom_field	|Array of hashes containing new custom field values. Passing an empty array does nothing.


The above command takes a JSON request, structured like this:
```json
{
  "source_id": 16,
  "referrer": {
    "type": "id",
    "value": 123
  }
}
```
The above returns a JSON response, structured like this:
```json
{
  "id": 47669412,
  "candidate_id": 38445573,
  "prospect": false,
  "applied_at": "2017-01-25T22:46:08.601Z",
  "rejected_at": null,
  "last_activity_at": "2017-01-25T22:46:11.284Z",
  "source": {
    "id": 33,
    "public_name": "Glassdoor"
  },
   "credited_to": {
    "id": 4080,
    "name": "Kate Austen",
    "employee_id": null
  },
  "rejection_reason": null,
  "rejection_details": null,
  "jobs": [
    {
      "id": 211706,
      "name": "Community Manager - New York"
    }
  ],
  "status": "active",
  "current_stage": {
    "id": 1551138,
    "name": "Application Review"
   },
  "answers": [
    {
      "question": "How many years experience do you have?",
      "answer": "2-4"
    },
    {
      "question": "Can do you the travel required for this job?",
      "answer": "Yes"
    }
  ],
  "custom_fields": {
    "current_title": "Community Manager",
    "requires_visa_sponsorship?": false
  },
  "keyed_custom_fields": {
    "current_title": {
      "name": "Current Title",
      "type": "short_text",
      "value": "Community Manager"
    },
    "requires_visa_sponsorship_": {
      "name": "Requires visa sponsorship?",
      "type": "boolean",
      "value": false
    }
  }
}
```



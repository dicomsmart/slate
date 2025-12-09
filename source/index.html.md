---
title: dicomfix API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - python

toc_footers:
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the dicomfix API
---

# Introduction

This document describes the interface between an external application and dicomfix, a RIS developed by [DicomSmart](https://www.dicomsmart.com). 

**This documentation is only valid for dicomfix version 4.0.4+**

# Authentication

> Using authentication headers

```shell
# Pass the correct headers with each request
curl "api_endpoint_here"  \ 
--header "X-DICOMFIX-TOKEN: xxxx" \
--header "X-DICOMFIX-SECRET: vvvv"
```

```python
import requests

url = "api_endpoint_here"

headers = {
    "x-dicomfix-token": "xxxx",
    "x-dicomfix-secret": "vvvv"
}

response = requests.get(url, headers=headers)
```

All calls to the endpoints have to be authenticated. 

The external application will be provided with one or more API token-secret credential pairs. 

The managers of the external application shall be responsible for the safekeeping of each credential pair. 

Authentication information must be sent via these request headers: 

* X-DICOMFIX-TOKEN 
* X-DICOMFIX-SECRET

Credential pairs can be optionally limited to only being able to operate on resources from a particular Department.


# Worklist Items

## Creating

```shell
curl --request POST \
  --url https://site/dfix/api/worklist \
  --header 'content-type: application/json' \
  --header 'x-dicomfix-secret: 28eWECu2FaPexAd8GUqgckBkt7R5sSzC' \
  --header 'x-dicomfix-token: local' \
  --data '{
  "date": "20251209",
  "time": "161100",
  "prediag": "G44.2 Tension-type headache",
  "custom1": "VIP Patient",
  "priority": "ROUTINE",
  "patientType": "",
  "placerOrderNumber": "202512093456-1",
  "price": "3850.00",
  "code": "37",
  "externalPortalAccessCode": "ABC123Aaa",
  "assignedRadiologist": "",
  "department": "1",
  "comments": "Additional instructions",
  "laterality": "R_LAT",
  "controlNumber": "202512093456",
  "patient": {
    "patientId": "1001",
    "otherId": "",
    "givenName": "John",
    "familyName": "Doe",
    "birthdate": "19900131",
    "email": "sample@dicomfix.com",
    "phone": "5553451486",
    "address": "123 NW St. Suite ABC",
    "sex": "M"
  }
}'
```

```python
import requests

url = "https://site/dfix/api/worklist"

payload = {
    "date": "20251209",
    "time": "161100",
    "prediag": "G44.2 Tension-type headache",
    "custom1": "VIP Patient",
    "priority": "ROUTINE",
    "patientType": "",
    "placerOrderNumber": "202512093456-1",
    "price": "3850.00",
    "code": "37",
    "externalPortalAccessCode": "ABC123Aaa",
    "assignedRadiologist": "",
    "department": "1",
    "comments": "Additional instructions",
    "laterality": "R_LAT",
    "controlNumber": "202512093456",
    "patient": {
        "patientId": "1001",
        "otherId": "",
        "givenName": "John",
        "familyName": "Doe",
        "birthdate": "19900131",
        "email": "sample@dicomfix.com",
        "phone": "5553451486",
        "address": "123 NW St. Suite ABC",
        "sex": "M"
    }
}
headers = {
    "x-dicomfix-secret": "28eWECu2FaPexAd8GUqgckBkt7R5sSzC",
    "x-dicomfix-token": "local",
    "content-type": "application/json"
}

response = requests.post(url, json=payload, headers=headers)

print(response.json())
```


> **200** The endpoint returns a JSON representation of the created Worklist Entry, if successful.

```json
{
 "specificCharacterSet": "ISO_IR 100",
 "patientName": "Doe^John^^^",
 "patientID": "1001",
 "patientBirthDate": "19900131",
 "otherPatientIDs": "",
 "patientSex": "F",
 "modality": "US",
 "accessionNumber": "220675",
 "requestedProcedurePriority": "ROUTINE",
 "studyInstanceUID": "1.2.826.0.1.3680043.9.67.1655216852563.56.75",
 "requestedProcedureDescription": "ABDOMEN",
 "requestedProcedureID": "220675",
 "scheduledStationAETitle": "hospital",
 "scheduledProcedureStepStatus": "SCHEDULED",
 "scheduledProcedureStepStartTime": "0957",
 "scheduledProcedureStepStartDate": "20220614",
 "scheduledProcedureStepID": "220675",
 "scheduledProcedureStepDescription": "ABDOMEN",
 "comments": "Special Instructions",
 "issuerOfPatientID": "DICOMFIX"
}
```

> Sample error message:

```json
{"message": "Invalid Requested Procedure code"}
```

Use this endpoint to create new Worklist Items directly.

### HTTP Request

`POST http://site/dfix/api/worklist`

### Payload 

Field | Information | Required
----- | ----------- | --------
date | Date of the request, format yyyyMMdd | No, if no date is provided, the current date is used
time | Time of the request, format HHmmss  | No, if no time is provided, the current time is used
prediag | Reason for Visit | No
custom1 | Custom field, to be used freely | No
priority | Possible values STAT,HIGH,ROUTINE,MEDIUM,LOW | No, ROUTINE is the default value
patientType | Administrative ID of the Patient Type, if the administrative ID that is provided does not match an existing Type of Patient, the value is ignored | No
externalPortalAccessCode | Access code provided to the patient used to locate what’s going to be the resulting study through the portal | No, must be unique if provided
assignedRadiologist | Administrative ID of an existing Radiologist | No, but, if present, must be from a valid user that belongs to the Department
department | Administrative ID of Department | Yes, if the token used is restricted to a Department this ID has to match it
code | Administrative ID of the requested procedure. It has to match an existing and active procedure | Yes
laterality | Laterality information, R_LAT or L_LAT | No
placerOrderNumber | This number must uniquely identify this particular requested study. | Yes
comments | The Comments attribute is intended to transmit non-structured information, which can be displayed to the operator of the Modality. | No
price | Format ####.## | No
patientId | Patient ID. | No
otherId | Alternative Patient ID. | No
givenName | Patient Name. | Yes
familyName | Patient Last Name. | Yes
birthdate | Patient Birthdate, format yyyyMMdd. | Yes
email | Patient email address. Make sure it is a valid email address. | No
phone | Patient phone number. | No
address | Patient Address. | No
sex | Possible Values F, M or O. | No, defaults to F
controlNumber | A number used to group similar worklist items, like requested studies belonging to the same patient's visit. | No

### Errors

Code | Meaning
---- | -------
401 Unauthorized | Invalid token or secret. This could also be returned if the used token is limited to one Department and the worklist entry being created belongs to a different one.
400 Bad Request | Unable to process the request, probably due to some validation error. The message will contain more information.
422 Unprocessable Entity | This will be returned if an existing worklist entry or study already has that placer order number.


## Discontinuing



```shell
curl "https://site/dfix/api/worklist/123?discontinueReason=Will%20reschedule" \
-X DELETE \
--header "X-DICOMFIX-TOKEN: xxxx" \
--header "X-DICOMFIX-SECRET: vvvv"
```

> **200** Success message:

```json
{"message" : "Worklist Item discontinued"}
```

Use this endpoint to discontinue a particular worklist item.

### HTTP Request

`DELETE https://site/dfix/api/worklist/<Placer Order Number>?discontinueReason=<Reason>`

### URL Parameters

Parameter | Description
--------- | -----------
Placer Order Number | The number that uniquely identifies this worklist item
discontinueReason | Short explanation about the discontinuation reasons

### Errors

Code | Meaning
---- | -------
401 Unauthorized | Invalid token or secret. This could also be returned if the used token is limited to one Department and the worklist entry being discontinued belongs to a different one.
404 Resource Not Found | No worklist entry matches the placerOrderNumber.
422 Unprocessable Entity | The Worklist item can’t be discontinued. It is already discontinued or has been processed.


# Imaging Service Requests

## Creating

```shell
curl --request POST \
  --url https://site/dfix/api/imagingsr \
  --header 'content-type: application/json' \
  --header 'x-dicomfix-secret: vvvv' \
  --header 'x-dicomfix-token: xxxx' \
  --data '{
  "controlNumber": "202512093456",
  "date": "20251209",
  "time": "161100",
  "prediag": "G44.2 Tension-type headache",
  "custom1": "VIP Patient",
  "priority": "ROUTINE",
  "ptype": "",
  "ref": "",
  "department": "IMG",
  "patient": {
    "patientId": "15395070",
    "otherId": "",
    "givenName": "Jane",
    "familyName": "Doe",
    "birthdate": "19900131",
    "email": "sample@dicomfix.com",
    "phone": "(555)345-1486",
    "address": "123 NW St. Suite ABC",
    "sex": "F"
  },
  "requestedStudies": [
    {
      "placerOrderNumber": "202512093456-1",
      "price": "385.00",
      "assignedRadiologist": "",
      "externalPortalAccessCode": "",
      "comments": "no previous studies",
      "code": "311714"
    },
    {
      "placerOrderNumber": "202512093456-2",
      "price": "300.00",
      "assignedRadiologist": "",
      "externalPortalAccessCode": "",
      "comments": "",
      "code": "MAM"
    }
  ]
}'
```

```python
import requests

url = "https://site/dfix/api/imagingsr"

payload = {
    "controlNumber": "202512093456",
    "date": "20250618",
    "time": "161100",
    "prediag": "G44.2 Tension-type headache",
    "custom1": "VIP Patient",
    "priority": "ROUTINE",
    "ptype": "",
    "ref": "",
    "department": "IMG",
    "patient": {
        "patientId": "15395070",
        "otherId": "",
        "givenName": "Jane",
        "familyName": "Doe",
        "birthdate": "19900131",
        "email": "sample@dicomfix.com",
        "phone": "(555)345-1486",
        "address": "123 NW St. Suite ABC",
        "sex": "F"
    },
    "requestedStudies": [
        {
            "placerOrderNumber": "202512093456-1",
            "price": "385.00",
            "assignedRadiologist": "",
            "externalPortalAccessCode": "",
            "comments": "no previous studies",
            "code": "311714"
        },
        {
            "placerOrderNumber": "202512093456-2",
            "price": "300.00",
            "assignedRadiologist": "",
            "externalPortalAccessCode": "",
            "comments": "",
            "code": "MAM"
        }
    ]
}
headers = {
    "x-dicomfix-secret": "vvvv",
    "x-dicomfix-token": "xxxx",
    "content-type": "application/json"
}

response = requests.post(url, json=payload, headers=headers)

print(response.json())
```

> **200** A simple success message.

```json
{"message": "Imaging Service Request Item Created"}
```

This endpoint will create Imaging Service Requests. A single request can contain one or more requested procedures.

The creation will work if at least one of the *code* values for the received requested studies matches a valid and active procedure administrative ID. Requested Studies with invalid *code* values will be ignored. If none of the requested studies have valid *code* values the creation will fail.

<aside class="notice">The creation process won't check if the Referring Physician or the Assigned Radiologist, if passed, will have access to the Study. Make sure the existing users are not excluded from the Imaging Service Request Department.</aside>

### HTTP Request

`POST http://site/dfix/api/imagingsr`

### Payload 

#### Imaging Service Rquest
Field | Information | Required
----- | ----------- | --------
controlNumber | Identification number of the imaging service request. This field becomes the Control Number of each requested study. This value must unequivocally identify a single imaging service request and can not be repeated. | Yes
date | Date of the request, format yyyyMMdd. | No, if no date is provided, the current date is used.
time | Time of the request, format HHmmss. | No, if no time is provided, the current time is used.
prediag | Reason for Visit. | No
custom1 | Custom field, to be used freely | No
priority | Possible values STAT,HIGH,ROUTINE,MEDIUM,LOW. | No, ROUTINE is the default value
ptype | Administrative ID of the Patient Type, if the administrative ID that is provided does not match an existing Type of Patient, the value is ignored. | No
ref | Administrative ID of an existing Referring Physician. | No, but, if present, must be from a valid user from the Department.
department | Administrative ID of Department. | Yes. Must be a valid department ID

#### Patient
Field | Information | Required
----- | ----------- | --------
patientId | Patient ID. | Yes
otherId | Alternative Patient ID. | No
givenName | Patient Name. | Yes
familyName | Patient Last Name. | Yes
birthdate | Patient Birthdate, format yyyyMMdd. | Yes
email | Patient email address. Make sure it is a valid email address. | No
phone | Patient phone number. | No
address | Patient Address. | No
sex | Possible Values F, M or O. | No, defaults to O


#### Requested Study
Field | Information | Required
----- | ----------- | --------
code | Administrative ID of the requested procedure. At least of the provided codes must be from valid and active procedures. | Yes
placerOrderNumber | This value must uniquely identify this particular requested study, not just inside the imaging service request but globally. | Yes
price | Format ####.## | No
assignedRadiologist | Administrative ID of an existing Radiologist. | No, but, if present, must be from a valid user that belongs to the Department.
externalPortalAccessCode | Access code provided to the patient used to locate what’s going to be the resulting study through the portal. | No, must be unique if provided
comments | The Comments attribute is intended to transmit non-structured information, which can be displayed to the operator of the Modality. | No

### Errors

Code | Meaning
---- | -------
401 Unauthorized | Invalid token or secret. This could also be returned if the used token is limited to one Department and the Imaging Service Request being created belongs to a different one.
400 Bad Request | Unable to process the request, probably due to some validation error. The message will contain more information.
422 Unprocessable Entity | This will be returned if at least one of the requested studies is using a repeated Order Placer Number or if another Imaging Service Request already exists with the given control number.

## Deleting

```shell
curl "https://site/dfix/api/imagingsr/202512093456" \
-X DELETE \
--header "X-DICOMFIX-TOKEN: xxxx" \
--header "X-DICOMFIX-SECRET: vvvv"
```


> **200** A Simple success message

```json
{"message": "Imaging Service Request Deleted"}
```

Use the control number to delete any unprocessed Imaging Service Request. You can't delete Imaging Service Requests after they were admitted but you can try to [discontinue](#discontinuing) their resulting Worklist Items individually.

<aside class="notice">Unlike the worklist item, the Imaging Service Request is fully deleted. It can be re-created with the same Control Number later.</aside>

### HTTP Request

`DELETE http://site/dfix/api/imagingsr/<Control Number>`


### URL Parameters

Parameter | Description
--------- | -----------
Control Number | The number that uniquely identifies this imaging service request.

### Errors

Code | Meaning
---- | -------
401 Unauthorized | Invalid token or secret. This could also be returned if the used token is limited to one Department and the Imaging Service Request being deleted belongs to a different one.
400 Bad Request | Unable to process the request, probably due to some validation error. The message will contain more information.
404 Resource Not Found | No Imaging Service Request matches the Control Number.
422 Unprocessable Entity | The Imaging Service Request has already been processed and can’t be deleted.

# Studies

## Listing

```shell
curl "https://site/dfix/api/studies?patientID=555.342.12&page=1" \
--header "X-DICOMFIX-TOKEN: xxxx" \
--header "X-DICOMFIX-SECRET: vvvv"
```

```python
import requests

url = "https://site/dfix/api/studies"

headers = {
    "x-dicomfix-token": "xxxx",
    "x-dicomfix-secret": "vvvv"
}

response = requests.get(url, headers=headers)

print(response.json())
```

> **200** Sample response, it will include both the data and metadata about the results, like total pages and the page number:

```json
{
    "totalRowCount": 2,
    "pageSize": 20,
    "pageNumber": 1,
    "totalPages": 1,
    "data": [
        {
            "patient": {
                "patientId": "555.342.12",
                "otherId": "9800450",
                "givenName": "John",
                "familyName": "Doe",
                "birthdate": "19961114",
                "email": "",
                "phone": "",
                "address": "",
                "sex": "M"
            },
            "id": 56859,
            "description": "CHEST PA",
            "status": "APPROVED",
            "studyDate": "20230606 15:40",
            "modalitiesInStudy": "CR",
            "department": "Site A",
            "links": {
                "report": "/dfix/api/reports/56859",
                "viewer": "/dfix/pview?id=56859&check=8947ab071.…9385abf2332a17afe&token=ccoEuhW"
            }
        },
        {
            "patient": {
                "patientId": "555.342.12",
                "otherId": "9800450",
                "givenName": "John",
                "familyName": "Done",
                "birthdate": "19961114",
                "email": "",
                "phone": "",
                "address": "",
                "sex": "M"
            },
            "id": 56850,
            "description": "CT ABDOMEN",
            "status": "READ",
            "studyDate": "20230518 13:23",
            "modalitiesInStudy": "CT",
            "department": "Site B",
            "links": {
               "viewer": "/dfix/pview?id=56850&check=e2199ed3b9…3ba2c149aa44430&token=ccoEuhW"
            }
        }
    ]
}
```

Use this enpoint to list all existing studies. You can filter the results by status and Patient ID.

Each study will contain a collection of "links":

* A “report” URL to fetch the Report. This will only be added to APPROVED studies. Consult the [Reports](#reports) sections for additional options to retrieve the report.
* A "viewer" URL for the study results page. A "token" parameter will be added to the URL if an endpoint is provided to dicomfix to generate valid security tokens. Please refer to the External Token section.


### HTTP Request

`GET https://site/dfix/api/studies?status=<Status>&patientID=<Patient ID>&page=1`

<aside class="notice">The results are paginated with 20 items per page. If the token is restricted to a Department studies belonging to other Departments will not be returned even if they belong to the same patient.</aside>

### URL Parameters

Parameter | Description | Requiered
--------- | ----------- | ---------
status | Filter by status: PERFORMED, READ, TYPED, APPROVED, DISCONTINUED, ARCHIVED (case insensitive) | No
patientID | Filter by Patient ID | No
pageNumber | Request this particular page | No, defaults to 1 if not provided.


# Reports

## Getting 

```shell
curl "https://site/dfix/api/studies?patientID=555.342.12&page=1" \
--header "X-DICOMFIX-TOKEN: xxxx" \
--header "X-DICOMFIX-SECRET: vvvv"
```

> **200** Responses will be either PDF, Base64 or Plain Text

Use this to get the report of a particular study. You can choose the return type to be a PDF (the default option), a Base64 representation of the PDF o the report's contents as plain text.

This endpoint will only return reports from **APPROVED** studies and will always return the latest version of the report.


<aside class="notice">The plain text option will not work for reports that were created by uploading an external PDF.</aside>

### HTTP Request

`GET dfix/api/reports/<study id>`

### URL Parameters

Parameter | Description | Required
--------- | ----------- | ---------
study id | The database id of the study, consult the [Study](#studies) section to list studies and get their ids. | Yes
base64 | If true, the PDF report will be returned as a base64 String. | No, defaults to false.
includeImages | If true, the report will include the first 30 images from the Study.  | No, defaults to false.
columns | Columns for the layout of the included images, possible values: 1,2,3,4,5  Any value over 5 defaults to 5. | Only if includeImages if true.
plainText | If true, a plain text representation of the report is returned and all other parameters are ignored. | No, defaults to false.

### Errors

Code | Meaning
---- | -------
401 Unauthorized | Invalid token or secret. This could also be returned if the used token is limited to one Department and the report is from a study that belongs to another Department.
400 Bad Request | Unable to process the request, probably due to some validation error. The message will contain more information.



---
title: Search for free slots
keywords: appointments, use case, search, free, slots, schedule
tags: [appointments,use_case]
sidebar: appointments_sidebar
permalink: appointments_use_case_search_for_free_slots.html
summary: "Use case for searching for free slots within a date range."
---

## Use Case ##

This specification describes a single use case. For complete details and background please see the [Appointment Management Capability Bundle](appointments.html).

Refer to [Consumer sessions illustrated](appointments_consumer_sessions.html) for how this API Use Case could be used in the context of a typical consumer appointment management session.

## Security ##

- GP Connect utilises TLS Mutual Authentication for system level authorization.
- GP Connect utilises a JSON Web Tokens (JWT) to transmit clinical audit & provenance details. 


## Search Parameters ##

Provider systems SHALL support the following search parameters:

| Name | Type | Description | Paths |
|---|---|---|---|
| `status` | `token` | The free/busy status of the appointment | `Slot.status` |
| `start` | `date` | Slot start date/time. | `Slot.start` |
| `end` | `date` | Slot end date/time. | `Slot.end` |
| `searchFilter` | `token` | A generic token to allow consumers to pass additional search criteria to the provider. | N/A |

{% include note.html content="The supported search parameters should be included in the [FHIR Capability Statement](foundations_use_case_get_the_fhir_capability_statement.html)." %}


## _include Parameters ##

Provider systems SHALL support the following include parameters:

| Name | Description | Paths |
|---|---|---|
| `_include=Slot:schedule` | Include Schedule Resources referenced within the returned Slot Resources | `Slot.schedule` |
| `_include:recurse= Schedule:actor:Practitioner` | Include Practitioner Resources referenced within the returned Schedule Resources | `Schedule.actor:Practitioner` |
| `_include:recurse= Schedule:actor:Location` | Include Location Resources referenced within the returned Schedule Resources | `Schedule.actor:Location` |


The following parameters SHALL be included in the request:

- The `start` parameter SHALL only be included once in the request.
- The `start` parameter SHALL be supplied with the `ge` search prefix. For example 'start=ge2017-09-22' which indicates that the consumer would like slots where the slot start date is on or after "2017-09-22".
- The `end` parameter SHALL only be included once in the request.
- The `end` parameter SHALL be supplied with the `le` search prefix. For example 'end=le2017-09-26' which indicates that the consumer would like slots where the slot end date is on or before "2017-09-26".
  
  ![Diagram - Date range parameters](images/appointments/SearchForFreeSlots.png)

- `status=free` specifies that only free slots will be returned. Note: the slot status value of `free` SHALL be specified, other slot status values are not permitted.

- `_include=Slot:schedule` specifies that associated Schedule resources are returned.


The following parameters MAY be included to minimise the number of API calls required to prepare an appointment booking:

- _include:recurse=Schedule:actor:Practitioner
- _include:recurse=Schedule:actor:Location


### Enhanced slot filtering ###

{% include important.html content="
It is recognized that Provider Systems must offer GP practices more functionality to enable them to better manage their available appointment slots in the light of increasing access requirements from other organisations. <br/><br/>
 
For the Appointment Management rc.3 specification, Provider Systems SHALL provide a mechanism by which GP practices can indicate which slots are available for booking via the GP Connect API specifically, thereby ensuring that their whole appointment book is not made available to external organisations.  
 " %}
 
The GP Connect programme is currently consulting with GP Principal System Providers to define a standard API interface to enable more granular slot filtering. A common example of this would be where a practice reserves a small number of slots specifically for use by Urgent Care appointment. 

In view of this, an additional place-holder parameter `searchFilter` has been included in the Appointment Management rc.3 specification. It is envisaged that this parameter will in future be used to specify these additional filtering parameters through the use of agreed valueSets.
 
The following provides some examples of search filters valueSets in consideration to meet requirements for future fine-grained slot filtering.  

| ValueSet System URI | Description |
| --- | --- |
| (TBD) booking-organisation | Booking organisation identifier (ODS code) |
| (TBD) consumer-type | Consuming Organisation type making the request, for example '111 call centre'. |
| (TBD) disposition | Urgent Care Disposition Code required for the patients care. |
| (TBD) service-id | Urgent Care Service-Id required for the available slot. |

Where searchFilters are sent by consumers which are not explicitly supported in this specification (i.e. the valueSet used is not listed here) providers SHALL ignore any such searchFilter parameters and SHALL NOT return an error.

## Search for free slots on the wire ##

On the wire a `Search for free slots` request would look something like one of the following:

```http
GET /Slot?start=ge2017-10-20T00:00:00&end=le2017-10-31T23:59:59&status=free&_include=Slot:schedule&_include:recurse=Schedule:actor:Practitioner&_include:recurse=Schedule:actor:Location
```

```http
GET /Slot?start=ge2017-10-20T00:00:00&end=le2017-10-31T23:59:59&status=free&_include=Slot:schedule
```

```http
GET /Slot?start=ge2017-10-20T00:00:00&end=le2017-10-31T23:59:59&status=free&_include=Slot:schedule&searchFilter=[typeSystemTBC]|UC&searchFilter=[dispositonSystemTBC]|DX001
```


## Prerequisites ##

### Consumer ###

The Consumer system:

- SHALL have previously resolved the organisation's FHIR endpoint Base URL through the [Spine Directory Service](https://nhsconnect.github.io/gpconnect/integration_spine_directory_service.html)
- SHALL request a maximum date range covering a two week period.

{% include tip.html content="Multiple separate API calls can be made if a larger date range is required however consideration should be given to the load this placed on the Provider system." %}

## API Usage ##

### Request Operation ###

#### FHIR Relative Request ####

```http
GET /Slot?[start={search_prefix}start_date]
          [&end=[{search_prefix}end_date]
          [&status=free]
          [&_include=Slot:schedule]
          {&_include:recurse=Schedule:actor:Practitioner}
          {&_include:recurse=Schedule:actor:Location}
```

#### FHIR Absolute Request ####

```http
GET https://[proxy_server]/https://[provider_server]/[fhir_base]
          /Slot?[start={search_prefix}start_date]
          [&end=[{search_prefix}end_date]
          [&status=free]
          [&_include=Slot:schedule]
          {&_include:recurse=Schedule:actor:Practitioner}
          {&_include:recurse=Schedule:actor:Location}
```

#### Request Headers ####

Consumers SHALL include the following additional HTTP request headers:

| Header               | Value |
|----------------------|-------|
| `Ssp-TraceID`        | Consumer's TraceID (i.e. GUID/UUID) |
| `Ssp-From`           | Consumer's ASID |
| `Ssp-To`             | Provider's ASID |
| `Ssp-InteractionID`  | `urn:nhs:names:services:gpconnect:fhir:rest:search:slot` |

#### Payload Request Body ####

N/A


#### Error Handling ####

The Provider system SHALL return an error if:

- the time period defined by `start` and `end` parameters is greater than a two week period.
- the `status` parameter is absent or is present with a value other than `free`.
- the `_include=Slot:schedule` is absent.

SHALL return an [GPConnect-OperationOutcome-1](https://fhir.nhs.uk/STU3/StructureDefinition/GPConnect-OperationOutcome-1) ![STU3](images/stu3.png) resource that provides additional detail when one or more parameters are corrupt or a specific business rule/constraint is breached.

Refer to [Development - FHIR API Guidance - Error Handling](development_fhir_error_handling_guidance.html) for details of error codes.

### Request Response ###

#### Response Headers ####

Provider systems are not expected to add any specific headers beyond that described in the HTTP and FHIR&reg; standards.

#### Payload Response Body ####

Provider systems:

- SHALL return a `200` **OK** HTTP status code on successful retrieval of a "free" slot details.
- SHALL include the free `Slot` details for the organisation which have a `status` of "free" and fall fully within the requested date range. I.e. free slots which start before the `start` parameter and free slots which end after `end` search parameter SHALL NOT be returned. 
- SHALL include the `Schedule` and `Slot` details associated with the returned slots as defined by the search parameter which have been specified. `Practitioner` is required in the searchset `Bundle` only if available.
 
  The response `Bundle` SHALL only contain `Schedule`, `Organization`, `Practitioner` and `Location` Resources related to the returned free `Slot` Resources. If no free slots are returned for the requested time period then no Resources should be returned within the response `Bundle`.

- SHALL include `Practitioner` and `Location` resources associated with Schedule resources in the response Bundle ONLY where requested to do so by the consumer using the `_include:recurse=Schedule:actor:Practitioner` and/or `_include:recurse=Schedule:actor:Location` parameters.

- SHALL manage slot `start` and `end` times to indicate which slots can be considered `adjacent` and therefore be booked against a single appointment as part of a `multi slot appointment booking`. Providers are responsible for the implementation of business rules that forbid the booking of non-adjacent slots according to their own practices.

  To allow consumers to implement multi slot appointment booking, the consumer needs to be able to identify which slots can be considered adjacent. A provider SHALL indicate which slots are adjacent or not adjacent using the following rules:

  * To indicate two slots (Slot A & Slot B) are adjacent, the two slots SHALL reference the same schedule resource and the ```start``` time of ```Slot B``` SHALL equals ```end``` time of ```Slot A```.
  * If the slots do not conform to the rule above, either the slots do not link to the same schedule or the start time of one slot is not the same as the end time of the previous slot then these slots SHALL not be considered adjacent.
  
- SHALL returned resources conforming to the GP Connect profiled versions of the base FHIR resources listed on the [Appointment Management Resources](datalibraryappointment.html) page.

```json
{
	"resourceType": "Bundle",
	"type": "searchset",
	"entry": [{
		"fullUrl": "Organisation/23",
		"resource": {
			"fullUrl": "http://gpconnect.aprovider.nhs.net/GP001/STU3/1/Organization/23",
			"resource": {
				"resourceType": "Organization",
				"id": "23",
				"meta": {
					"versionId": "636064088098730113",
					"lastUpdated": "2016-08-10T13:52:54.516+01:00",
					"profile": ["https://fhir.nhs.uk/STU3/StructureDefinition/CareConnect-GPC-Organization-1"]
				},
				"identifier": [{
					"system": "https://fhir.nhs.uk/Id/ods-organization-code",
					"value": "O001"
				}],
				"name": "Honley GP Practice"
			}
		}
	},
	{
		"fullUrl": "Location/17",
		"resource": {
			"resourceType": "Location",
			"id": "17",
			"meta": {
				"versionId": "636064088100870233",
				"lastUpdated": "2016-08-10T14:27:49.778+01:00",
				"profile": ["https://fhir.nhs.uk/STU3/StructureDefinition/CareConnect-GPC-Location-1"]
			},
			"identifier": [{
				"system": "https://fhir.nhs.uk/Id/ods-site-code",
				"value": "L001"
			}],
			"name": "Honley Highstreet"
		}
	},
	{
		"fullUrl": "Schedule/14",
		"resource": {
			"resourceType": "Schedule",
			"id": "14",
			"meta": {
				"versionId": "1469444400000",
				"lastUpdated": "2017-11-08T12:00:00.000+01:00",
				"profile": ["https://fhir.nhs.uk/STU3/StructureDefinition/GPConnect-Schedule-1"]
			},
			"actor": [{
				"reference": "Location/17"
			},
			{
				"reference": "Practitioner/2"
			}],
			"comment": "Schedule 1 for general appointments"
		}
	},
	{
		"fullUrl": "Practitioner/2",
		"resource": {
			"resourceType": "Practitioner",
			"id": "2",
			"meta": {
				"versionId": "636064088099800115",
				"lastUpdated": "2016-08-10T13:47:09.966+01:00",
				"profile": ["https://fhir.nhs.uk/STU3/StructureDefinition/CareConnect-GPC-Practitioner-1"]
			},
			"identifier": [{
				"system": "https://fhir.nhs.uk/Id/sds-user-id",
				"value": "S001"
			}],
			"name": {
				"family": ["Black"],
				"given": ["Sarah"],
				"prefix": ["Mrs"]
			},
			"gender": "female"
		}
	},
	{
		"fullUrl": "Slot/1584",
		"resource": {
			"resourceType": "Slot",
			"id": "1584",
			"meta": {
				"versionId": "1471219260000",
				"lastUpdated": "2017-11-09T01:01:00.000+01:00",
				"profile": ["https://fhir.nhs.uk/STU3/StructureDefinition/GPConnect-Slot-1"]
			},
			"schedule": {
				"reference": "Schedule/14"
			},
			"status": "free",
			"start": "2016-08-15T11:30:00.000+01:00",
			"end": "2016-08-15T11:59:59.000+01:00"
		}
	},
	{
		"fullUrl": "Slot/1644",
		"resource": {
			"resourceType": "Slot",
			"id": "1644",
			"meta": {
				"versionId": "1471219260112",
				"lastUpdated": "2017-11-09T02:13:05.000+01:00",
				"profile": ["https://fhir.nhs.uk/STU3/StructureDefinition/GPConnect-Slot-1"]
			},
			"schedule": {
				"reference": "Schedule/14"
			},
			"status": "free",
			"start": "2016-08-15T12:00:00.000+01:00",
			"end": "2016-08-15T12:29:59.000+01:00"
		}
	}]	
}
```

## Examples ##

### C# ###

{% include tip.html content="C# code snippets utilise Ewout Kramer's [fhir-net-api](https://github.com/ewoutkramer/fhir-net-api) library which is the official .NET API for HL7&reg; FHIR&reg;." %}

```csharp
var client = new FhirClient("http://gpconnect.aprovider.nhs.net/GP001/STU3/1/");
client.PreferredFormat = ResourceFormat.Json;

[ to add ]

FhirSerializer.SerializeResourceToJson(resource).Dump();
```

### Java ###

{% include tip.html content="Java code snippets utilise James Agnew's [hapi-fhir](https://github.com/jamesagnew/hapi-fhir/
) library." %}

```java
FhirContext ctx = FhirContext.forStu3();
IGenericClient client = ctx.newRestfulGenericClient("http://gpconnect.aprovider.nhs.net/GP001/STU3/1");

[ to add ]

System.out.println(ctx.newJsonParser().setPrettyPrint(true).encodeResourceToString(responseBundle));
```

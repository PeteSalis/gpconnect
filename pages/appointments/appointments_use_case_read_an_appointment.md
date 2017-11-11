---
title: Read an appointment
keywords: appointments, use case, read
tags: [appointments,use_case]
sidebar: appointments_sidebar
permalink: appointments_use_case_read_an_appointment.html
summary: "Use case for reading an appointment resource."
---


## API Use Case ##

This specification describes a single use cases. For complete details and background please see the [Appointment Management Capability Bundle](appointments.html).

## Security ##

- GP Connect utilises TLS Mutual Authentication for system level authorization.
- GP Connect utilises a JSON Web Tokens (JWT) to transmit clinical audit & provenance details. 

## Consumer ##

The Consumer system:

- SHALL have previously resolved the organisation's FHIR endpoint Base URL through the [Spine Directory Service](https://nhsconnect.github.io/gpconnect/integration_spine_directory_service.html)

## API Usage ##

### Request Operation ###

#### FHIR Relative Request ####

```http
GET /Appointment/[id]
```

#### FHIR Absolute Request ####

```http
GET https://[proxy_server]/https://[provider_server]/[fhir_base]/Appointment/[id]
```

#### Request Headers ####

Consumers SHALL include the following additional HTTP request headers:

| Header               | Value |
|----------------------|-------|
| `Ssp-TraceID`        | Consumer's TraceID (i.e. GUID/UUID) |
| `Ssp-From`           | Consumer's ASID |
| `Ssp-To`             | Provider's ASID |
| `Ssp-InteractionID`  | `urn:nhs:names:services:gpconnect:fhir:rest:read:appointment`|

#### Payload Request Body ####

N/A

#### Error Handling ####

Provider systems SHALL return an [GPConnect-OperationOutcome-1](https://fhir.nhs.uk/STU3/StructureDefinition/GPConnect-OperationOutcome-1) ![STU3](images/stu3.png) resource that provides additional detail when one or more data fields are corrupt or a specific business rule/constraint is breached.

For example:

- Where a Logical identifier of the resource is not valid/can't be found on the server, a 404 HTTP Status code would be returned with the relevent OperationOutcome resource.
- Where insufficient data about an appointment is present in the provider system to populate an appointment resource which validates to the `GPConnect-Appointment-1` profile, an 500 HTTP Status code should be returned, together with the appropriate OperationOutcome resource providing diagnostic detail.   

Refer to [Development - FHIR API Guidance - Error Handling](development_fhir_error_handling_guidance.html) for details of error codes.

### Request Response ###

#### Response Headers ####

Provider systems are not expected to add any specific headers beyond that described in the HTTP and FHIR&reg; standards.

#### Payload Response Body ####

Provider systems:

- SHALL return a `200` **OK** HTTP status code on successful execution of the operation.
- SHALL return `Appointment` resources that conform to the [GPConnect-Appointment-1](https://fhir.nhs.uk/STU3/StructureDefinition/GPConnect-Appointment-1) ![STU3](images/stu3.png) resource profile.
- SHALL include the URI of the `GPConnect-Appointment-1` profile StructureDefinition in the `Appointment.meta.profile` element of the returned appointment resource.
- SHALL include the `versionId` of the current version of the appointment resource.
- SHALL include all relevant business `identifier` details (if any) for the appointment resource.

```json
{
	"resourceType": "Appointment",
	"id": "148",
	"meta": {
		"versionId": "1503310820000",
		"lastUpdated": "2017-11-08T10:20:20.000+00:00",
		"profile": ["https://fhir.nhs.uk/STU3/StructureDefinition/GPConnect-Appointment-1"]
	},
	"contained": [{
		"resourceType": "Organization",
		"id": "1",
		"meta": {
			"profile": ["https://fhir.nhs.uk/STU3/StructureDefinition/CareConnect-GPC-Organization-1"]
		},
		"name": "Test Organization Name",
		"telecom": [{
			"system": "phone",
			"value": "0300 303 5678"
		}]
	}],
	"extension": [{
		"url": "https://fhir.nhs.uk/STU3/StructureDefinition/Extension-GPConnect-BookingOrganisation-1",
		"valueReference": {
			"reference": "#1"
		}
	}],
	"status": "booked",
	"reason": {
		"coding": [{
			"system": "http://snomed.info/sct",
			"code": "00001",
			"display": "Default Appointment Type"
		}],
		"text": "Default Appointment Type"
	},
	"description": "GP Connect Appointment description 148",
	"start": "2017-08-21T10:20:00.000+00:00",
	"end": "2017-08-21T10:50:00.000+00:00",
	"slot": [{
		"reference": "Slot/544"
	},
	{
		"reference": "Slot/545"
	},
	{
		"reference": "Slot/546"
	}],
	"created": "2017-10-09T13:48:41+01:00",
	"comment": "Test Appointment Comment 148",
	"participant": [{
		"actor": {
			"reference": "Patient/2"
		},
		"status": "accepted"
	},
	{
		"actor": {
			"reference": "Location/1"
		},
		"status": "accepted"
	},
	{
		"actor": {
			"reference": "Practitioner/2"
		},
		"status": "accepted"
	}]
}
```

## Examples ##

### C# ###

{% include tip.html content="C# code snippets utilise Ewout Kramer's [fhir-net-api](https://github.com/ewoutkramer/fhir-net-api) library which is the official .NET API for HL7&reg; FHIR&reg;." %}

```csharp
var client = new FhirClient("http://gpconnect.aprovider.nhs.net/GP001/STU3/1/");
client.PreferredFormat = ResourceFormat.Json;
var resource = client.Read<Appointment>("Appointment/148");
FhirSerializer.SerializeResourceToJson(resource).Dump();
```

### Java ###

{% include tip.html content="Java code snippets utilise James Agnew's [hapi-fhir](https://github.com/jamesagnew/hapi-fhir/
) library." %}

```java
FhirContext ctx = FhirContext.forStu3();
IGenericClient client = ctx.newRestfulGenericClient("http://gpconnect.aprovider.nhs.net/GP001/STU3/1");
Appointment appointment = client.read().resource(Appointment.class).withId("148").execute();
System.out.println(fhirContext.newJsonParser().setPrettyPrint(true).encodeResourceToString(appointment));
```
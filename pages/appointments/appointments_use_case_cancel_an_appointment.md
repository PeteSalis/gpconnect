---
title: Cancel an appointment for a patient at an organisation
keywords: appointments, use case, cancel, free, slots, schedule
tags: [appointments,use_case]
sidebar: appointments_sidebar
permalink: appointments_use_case_cancel_an_appointment.html
summary: "Use case for cancelling an appointment for a patient with a given organisation."
---

## Use Case ##

The typical flow to cancel an appointment is:

 1.	Search by `NHS Number` for, or otherwise obtain, a `Patient` resource.
 2. Search for `Appointment` resources for the `Patient` resource.
 3. Choose an `Appointment` resource and cancel it by amending the `status` to `cancelled`.

## Security ##

- GP Connect utilises TLS Mutual Authentication for system level authorization.
- GP Connect utilises a JSON Web Tokens (JWT) to transmit clinical audit & provenance details. 

## Prerequisites ##

### Consumer ###

The Consumer system:

- SHALL have previously resolved the organisation's FHIR endpoint Base URL through the [Spine Directory Service](https://nhsconnect.github.io/gpconnect/integration_spine_directory_service.html)
- SHALL have previously traced the patient's NHS Number using the [Personal Demographics Service]( https://nhsconnect.github.io/gpconnect/integration_personal_demographic_service.html) or an equivalent service.
- SHALL have previously found the appointment id using [Retrieve a patient's appointments](https://nhsconnect.github.io/gpconnect/appointments_use_case_retrieve_a_patients_appointments.html).

## API Usage ##

### Request Operation ###

#### FHIR Relative Request ####

```http
PUT /Appointment/[id]
```

#### FHIR Absolute Request ####

```http
PUT https://[proxy_server]/https://[provider_server]/[fhir_base]/Appointment/[id]
```

#### Request Headers ####

Consumers SHALL include the following additional HTTP request headers:

| Header               | Value |
|----------------------|-------|
| `Ssp-TraceID`        | Consumer's TraceID (i.e. GUID/UUID) |
| `Ssp-From`           | Consumer's ASID |
| `Ssp-To`             | Provider's ASID |
| `Ssp-InteractionID`  | `urn:nhs:names:services:gpconnect:fhir:rest:cancel:appointment` |

#### Payload Request Body ####

The request payload is a profiled version of the standard FHIR [Appointment](https://www.hl7.org/fhir/STU3/appointment.html) ![STU3](images/stu3.png) resource, see [FHIR Resources](/datalibraryappointment.html) page for more detail.

Consumer systems:
- SHALL send an `Appointment` resource that conform to the [GPConnect-Appointment-1](https://fhir.nhs.uk/STU3/StructureDefinition/GPConnect-Appointment-1) ![STU3](images/stu3.png) profile.
- SHALL include the URI of the `GPConnect-Appointment-1` profile StructureDefinition in the `Appointment.meta.profile` element of the appointment resource.

Only the following data-elements can be modified when performing an appointment cancellation.
- the appointment `status` MUST be updated to "cancelled".
- the appointment `cancellation-reason` extension SHALL be included with the cancellation reason details.

{% include important.html content="If any content other than the appointment cancellation reason or appointment status is updated the server SHALL reject the amendment and return an error." %}

On the wire a JSON serialised request would look something like the following:

```json
{
	"resourceType": "Appointment",
	"id": "9",
	"meta": {
		"versionId": "636068818095315079",
		"lastUpdated": "2016-08-15T19:16:49.971+01:00",
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
	},
	{
		"url": "https://fhir.nhs.uk/STU3/StructureDefinition/Extension-GPConnect-AppointmentCancellationReason-1",
		"valueString": "Free text cancellation reason."
	}],
	"status": "cancelled",
	"description": "Free text description updated.",
	"start": "2016-05-30T10:00:00+01:00",
	"end": "2016-05-30T10:25:00+01:00",
	"slot": [{
		"reference": "Slot/1",
		"display": "Slot 1"
	}],
	"created": "2017-10-09T13:48:41+01:00",
	"comment": "Free text comment.",
	"participant": [{
		"actor": {
			"reference": "Patient/1",
			"display": "Mr. Mike Smith"
		},
		"status": "accepted"
	},
	{
		"actor": {
			"reference": "Location/32",
			"display": "Leeds GP Clinic"
		},
		"status": "accepted"
	}]
}
```

#### Error Handling ####

The Provider system:

- SHALL return an [GPConnect-OperationOutcome-1](https://fhir.nhs.uk/STU3/StructureDefinition/GPConnect-OperationOutcome-1) ![STU3](images/stu3.png) resource that provides additional detail when one or more data fields are corrupt or a specific business rule/constraint is breached.
- SHALL return an error if any appointment details other than the appointment `status` and `cancellation-reason` fields are attempted to be updated.

Refer to [Development - FHIR API Guidance - Error Handling](development_fhir_error_handling_guidance.html) for details of error codes.

### Request Response ###

#### Response Headers ####

Provider systems are not expected to add any specific headers beyond that described in the HTTP and FHIR&reg; standards.

#### Payload Response Body ####

Provider systems:

- SHALL return a `200` **OK** HTTP status code on successful execution of the operation.
- SHALL return an `Appointment` resource that conform to the [GPConnect-Appointment-1](https://fhir.nhs.uk/STU3/StructureDefinition/GPConnect-Appointment-1) ![STU3](images/stu3.png) profile.
- SHALL include the URI of the `GPConnect-Appointment-1` profile StructureDefinition in the `Appointment.meta.profile` element of the returned appointment resource.
- SHALL include the `versionId` of the current version of each appointment resource.
- SHALL have updated the appointment `status` to "cancelled".
- SHALL have updated the appointment `cancellation-reason` inline with any details supplied in the request.

```json
{
	"resourceType": "Appointment",
	"id": "9",
	"meta": {
		"versionId": "636068818095315079",
		"lastUpdated": "2016-08-15T19:16:49.971+01:00",
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
	},
	{
		"url": "https://fhir.nhs.uk/STU3/StructureDefinition/Extension-GPConnect-AppointmentCancellationReason-1",
		"valueString": "Free text cancellation reason."
	}],
	"status": "cancelled",
	"description": "Free text description updated.",
	"start": "2016-05-30T10:00:00+01:00",
	"end": "2016-05-30T10:25:00+01:00",
	"slot": [{
		"reference": "Slot/1",
		"display": "Slot 1"
	}],
	"created": "2017-10-09T13:48:41+01:00",
	"comment": "Free text comment.",
	"participant": [{
		"actor": {
			"reference": "Patient/1",
			"display": "Mr. Mike Smith"
		},
		"status": "accepted"
	},
	{
		"actor": {
			"reference": "Location/32",
			"display": "Leeds GP Clinic"
		},
		"status": "accepted"
	}]
}
```

{% include important.html content="A status response `200` **OK** implies that the state of any resources affected by the appointment cancellation (i.e. the associated `Slot`) subsequently reflects the cancellation (for example, `Appointment.status`, `Slot.status` are updated inline with any internal integrity constraints)." %}

## Examples ##

### C# ###

{% include tip.html content="C# code snippets utilise Ewout Kramer's [fhir-net-api](https://github.com/ewoutkramer/fhir-net-api) library which is the official .NET API for HL7&reg; FHIR&reg;." %}

```csharp
var client = new FhirClient("http://gpconnect.aprovider.nhs.net/GP001/STU3/1");
client.PreferredFormat = ResourceFormat.Json;
var appointment = client.Read<Appointment>("Appointment/9");
// Cancel The Appointment
appointment.Status = Appointment.AppointmentStatus.Cancelled;
appointment.Extension.Add(new Extension("https://fhir.nhs.uk/STU3/StructureDefinition/Extension-GPConnect-AppointmentCancellationReason-1",new FhirString("Free text cancellation reason.")));
var cancelledAppointment = client.Update<Appointment>(appointment);
FhirSerializer.SerializeResourceToJson(cancelledAppointment).Dump();
```

### Java ###

{% include tip.html content="Java code snippets utilise James Agnew's [hapi-fhir](https://github.com/jamesagnew/hapi-fhir/
) library." %}

```java
// Read appointment to be updated
FhirContext ctx = FhirContext.forStu3();
IGenericClient client = ctx.newRestfulGenericClient("http://gpconnect.aprovider.nhs.net/GP001/STU3/1");
Appointment appointment = client.read().resource(Appointment.class).withId("9").execute();

// Amend appointment status and cancellation reason
appointment.setStatus(AppointmentStatusEnum.CANCELLED);
ExtensionDt extension = new ExtensionDt(false);
extension.setUrl("https://fhir.nhs.uk/STU3/StructureDefinition/Extension-GPConnect-AppointmentCancellationReason-1");
extension.setValue(new StringDt("Free text cancellation reason."));
appointment.addUndeclaredExtension(extension);

// Cancel appointment
MethodOutcome response = client.update()
	.resource(appointment)
	.prefer(PreferReturnEnum.REPRESENTATION)
	.preferResponseType(Appointment.class)
	.execute();

System.out.println(fhirContext.newJsonParser().setPrettyPrint(true).encodeResourceToString(response.getResource()));
```

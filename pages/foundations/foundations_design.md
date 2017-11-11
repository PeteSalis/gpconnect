---
title: Foundations Design
keywords: foundations design
tags: [design,foundations]
sidebar: foundations_sidebar
permalink: foundations_design.html
summary: "Overview of the design decisions made in relation to the Foundations capability."
---

## Business Identifiers ##

The following business identifier types are to be supported by GP Connect systems:

| Identifier | Resource | System |
| ---------- | -------- | ------ |
| NHS Number | Patient | `https://fhir.nhs.uk/Id/nhs-number` |
| ODS Code | Organisation | `https://fhir.nhs.uk/Id/ods-organization-code` |
| ODS Site Code | Location | `https://fhir.nhs.uk/Id/ods-site-code` |
| SDS User ID | Practitioner | `https://fhir.nhs.uk/Id/sds-user-id` |

{% include important.html content="Support for additional identifier types inline with the existing GPSoC requirements is also under consideration." %}


## Definition of Organisation and Location entities ##

The GP practice organisation is a legal entities which is represented by the FHIR `Organization` resource. This entity will have an assigned [ODS code](https://digital.nhs.uk/organisation-data-service). 

The practice organisation will have one or more operating locations (a.k.a. sites or branches) which together perform the patient healthcare for which the organisation is legally responsible. Each operating location is represented by a FHIR `Location` resource. 

Where ODS Site codes are defined in SPINE configuration, these map to GP practice operating locations, and hence to Location FHIR resources.

The above definition of the Organization and Location are particularly relevant to two elements within the `Patient` resource and the `location participant` within the `Appointment` resource.

### Organization and Location in the Patient resource ###

The [Patient](https://fhir.nhs.uk/STU3/StructureDefinition/CareConnect-GPC-Patient-1) resource contains the `managingOrganization` element which should reference an organization resource representing the legal entity which is responsible for the patients record.

The [Patient](https://fhir.nhs.uk/STU3/StructureDefinition/CareConnect-GPC-Patient-1) resource also contains the `preferredBranchSurgery` element within the [Registration Details](https://fhir.nhs.uk/STU3/StructureDefinition/Extension-CareConnect-GPC-RegistrationDetails-1) extension. This preferred branch surgery should contain a reference to a location resource representing the patients preferred operating location which may be a branch surgery or the organizations main surgery.

### Location in the Appointment resource ###

When [booking an appointment](appointments_use_case_book_an_appointment.html) it must contain one participant elements which references a Location resource. This location resource should represents the physical location where the appointment is to take place. This Location resource may be a representation of a branch surgery, the main surgery, a room within a surgery or another location.

When booking an appointment using the GP Connect API, the [search for free slots](appointments_use_case_search_for_free_slots.html) interaction needs to be performed to get back free slots in which the appointment can be booked. The slots are linked to a schedule which is linked to a location. If the patients preferred branch surgery is known it could be used to filter the free slots based on the location within the schedule to which they are linked.


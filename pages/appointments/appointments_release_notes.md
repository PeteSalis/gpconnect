---
title: Appointments Release Notes
keywords: appointments, release notes
tags: []
sidebar: appointments_sidebar
permalink: appointments_release_notes.html
summary: "Release notes for the various versions of the Appointment Management capability."
---

#### 1.0.0-rc.3

{% include important.html content="Release 1.0.0-rc.3 contains a change of FHIR version from DSTU2 to STU3 as the base fhir version for the GP Connect API Capbility." %}

  - [Retrieve a patient's appointments](appointments_use_case_retrieve_a_patients_appointments.html) - Uplifted to STU3 profiles.
  - [Retrieve a patient's appointments](appointments_use_case_retrieve_a_patients_appointments.html) - Updated the wording and examples relating to the `start` search parameter(s) to clear up the expectations and remove the miss leading wording around start and end of the date range.
  - [Search for free slots](appointments_use_case_search_for_free_slots.html) - Uplifted to STU3 FHIR profiles for all resources. Significant changes to note are `freeBusyType` has changed to `status`, the practitioner reference has moved in the schedule resource from an extension to `Schedule.actor`.
  - [Read an appointment](appointments_use_case_read_an_appointment.html) - Uplifted to STU3 profiles.
  - [Book an appointment](appointments_use_case_book_an_appointment.html) - Uplifted to STU3 profiles. A significant change to note is that the create extension has become the standard created element within the appointment resource.
  - [Amend an appointment](appointments_use_case_amend_an_appointment.html) - Uplifted to STU3 profiles.
  - [Cancel an appointment](appointments_use_case_cancel_an_appointment.html) - Uplifted to STU3 profiles.
  - [Book an appointment](appointments_use_case_book_an_appointment.html), [Amend an appointment](appointments_use_case_amend_an_appointment.html) - Added guidanace for providers that do not support both appointment description and appointment comment to append the recieved comment string to the mandatory description element.

#### 1.0.0-rc.2 (Released: 13/10/2017)
  ***Specification Updates***
  - [Appointment Management](appointments.html), [Search for free slots](appointments_use_case_search_for_free_slots.html), [Operation Guidance](development_fhir_operation_guidance.html) - Changed to the InteractionId for "Search for free slots" to reflect move to restful endpoint instead of operation.
  - [Search for free slots](appointments_use_case_search_for_free_slots.html#search-parameters) - Updated formatting of search for free slots page and added clarification around the use of start and end parameters.
  - [Book an appointment](appointments_use_case_book_an_appointment.html#payload-request-body) - Added guidance around use of bookingOrganization and creation dateTime extension within appointment resource.
  - [Search for free slots](appointments_use_case_search_for_free_slots.html#search-parameters) - Added `disposition` and `service-id` search parameters for urgent care use cases.
  - [Appointments - Consumer Sessions Illustrated](appointments_consumer_sessions.html), [Spine Integration Illustrated](integration_illustrated.html) - Added pages to highlight the sequence of interactions required to perform common business processes using the GP Connect API.
  
  ***Profile Updates***
  - [gpconnect-appointment-1](https://fhir.nhs.uk/StructureDefinition/gpconnect-appointment-1) - Added `bookingOrganization` extension and `created` extension within appointment resource profile.

#### 1.0.0-rc.1 (Released: 22/09/2017)
  - [Book an appointment](appointments_use_case_book_an_appointment.html#payload-request-body), [Search for free slots](appointments_use_case_search_for_free_slots.html#payload-response-body) - Additional information has been added on how providers should indicate that slots are adjacent and therefore can be used for multi slot appointment booking by consumers.
  - [Design Decisions](appointments_design.html) - API will not provide appointment re-scheduling through a single API interaction.
  - [Book an appointment](appointments_use_case_book_an_appointment.html#payload-request-body) - where participants are specified in request body, these must include a actor resource reference.
  - [Amend Appointment](appointments_use_case_amend_an_appointment.html#error-handling) - Uplifted error handling guidanace around modification of appointment resource elements.
  - [Amend Appointment](appointments_use_case_amend_an_appointment.html#payload-request-body) - Changed specification to clarify which fields may be amended.
  - [Book an appointment](appointments_use_case_book_an_appointment.html), [Common API Guidance](development_fhir_api_guidance.html#managing-return-content) - Moved and uplifted guidance on resource state from book appointment to common api guidance.
  - [Search for free slots](appointments_use_case_search_for_free_slots.html) - Provider will return only details of free slots which have a date/time span fully within the time period specified
  - [Book an appointment](appointments_use_case_book_an_appointment.html#error-handling) - Added guidance around providers right to implement business rules to prevent misuse of the appointment booking api.
  - [Amend an appoinment](appointments_use_case_amend_an_appointment.html) - Clarity on use of PUT verb and the effect on the resultant state of the `Appointment.reason` element.
  - [Search for free slots Use Case](appointments_use_case_search_for_free_slots.html), [Appointment Management Resources](datalibraryappointment.html#search-for-free-slots) - Changed "Search for free slots" from a fhir operation to restful call.

#### 1.0.0-beta.2 (Released: 08/09/2017)
  - Removed practitioner participant as a mandatory element within the appointment resource (Page - Book an appointment).
  - Added location participant as a mandatory element within the appointment resource (Page - Book an appointment).
  - Updated datalibrary to be capability specific, moving it to reside under the "Development" menu within the capability. This is to allow the Profiled FHIR Resources to be referenced per capability and updated independantly. For Appointment Management we have updated specification to reference FHIR profiles stored on FHIR reference server rather than those on the old DMS capability pack.
  - Updated book appointment with guidance on use of the comment and description elements within the resource.
  - Updated search prefixes listed on "Retrieve a patient's appointments" page, within Appointment Management API Use Case section, to align with common API guidance, removed "ne" search prefix and added the "eq" search prefix. Also updated the link to the Common API Guidance page to directly link to the search section.
  - Corrected examples on API Use Cases pages to include FHIR Resource Profile in the returned FHIR resources.
  - Added clarification on the expected response when the [Search for free slots](appointments_use_case_search_for_free_slots.html) API use case does not return any free slots for the requested time period.
  - Updated API Use Case pages to make clear the expectations around the FHIR Resource Metadata Profile element, along with updates to examples to show new profile definitions.
  - Added clarification around GP Connect "FHIR Resource Profile" and "Metadata Profile" requirements on the request/consumer side of the [Book an Appointment](appointments_use_case_book_an_appointment.htm), [Amend  an Appointment](appointments_use_case_amend_an_appointment.html) and [Cancel an appointment](appointments_use_case_cancel_an_appointment.html) API use cases. 
  - Added clarification on the expected response content for the [Retrieve a Patient's Appointments](appointments_use_case_retrieve_a_patients_appointments.html) API use case.
  - Additional detail on error conditions for [Read an appointment](appointments_use_case_read_an_appointment.html)
  - [Cancel an appointment](appointments_use_case_cancel_an_appointment.html) API use case associated with cancel an appointment interaction
  - [Book an appointment](development_fhir_api_guidance.html#create-resource) emphasised requirement for provider to return Location header describing details of the newly created resource.
  - [Retrieve a patient's appointments](appointments_use_case_retrieve_a_patients_appointments.html#payload-response-body) includes guidance on what data items should be returned where date range parameters are ommitted in the request querystring.
  - [Search for free slots](appointments_use_case_search_for_free_slots.html) - updated identifier system within examples to match identifiers within new fhir profiles
  - Added [design decision](appointments_design.html) to clarify responsibilities for consumer, SSP and provider in an implementation which provides a federated appointment management capability. 
  - [Amend Appointment](appointments_use_case_amend_an_appointment.html), [Book Appointment](appointments_use_case_book_an_appointment.html), [Cancel Appointment](appointments_use_case_cancel_an_appointment.html), [Read Appointment](appointments_use_case_read_an_appointment.html), [Retrieve a patiens appointments](appointments_use_case_retrieve_a_patients_appointments.html), [Search for free slots](appointments_use_case_search_for_free_slots.html) - Added C# and Java code examples for all the appointment management API Use Cases.
  - [Book and appointment](appointments_use_case_book_an_appointment.html#consumer) - Added clarification around the prerequisites, indicating that the consumer should have performed a "Find a patient" request to obtain the patient logical identifier on the fhir server.
  - [Amend Appointment](appointments_use_case_amend_an_appointment.html), [Book Appointment](appointments_use_case_book_an_appointment.html), [Cancel Appointment](appointments_use_case_cancel_an_appointment.html), [Read Appointment](appointments_use_case_read_an_appointment.html), [Retrieve a patiens appointments](appointments_use_case_retrieve_a_patients_appointments.html), [Search for free slots](appointments_use_case_search_for_free_slots.html) - Uplifted examples to use correct urls and elements.
  
#### 1.0.0-beta.1
  - Initial release of the Appointment Management capability pack on Github in early August 2016.
  
#### 1.0.0-alpha.1
  - Initial release for feedback/comments as part of the late May 2016 release.
  
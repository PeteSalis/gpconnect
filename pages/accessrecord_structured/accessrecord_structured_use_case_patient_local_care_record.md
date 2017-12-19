---
title: Review GP practice information
keywords: access record structured, use case, retrieve
tags: [accessrecord_structured,use_case]
sidebar: accessrecord_rest_sidebar
permalink: accessrecord_structured_use_case_patient_local_care_record.html
summary: "Use case for retrieval of a patient's care history from local care record on GP system"
---

## Use case ##
A professional user of the local care record (‘care professional’) reviews a patient’s GP practice information for details of medications.

## Description ##
A patient is admitted to hospital. The clinician responsible for the care of the patient in the hospital needs to review their care history and medication records from the GP Practice clinical system to assist in determining the appropriate care provision and support. These could be current or past medications, repeat or acute medications, and can be in inpatient, outpatient and community settings. The clinician has access to the local care record (LCR), which shares clinical information relevant to the direct care of their patient from a variety of local hospital, community and GP organisations.

## Use case justification ##
Currently, when a patient’s record is viewed in the LCR, their medication records from the GP practice clinical system are presented as a static HTML view from a third-party supplier. This view is rigid and not in keeping with the needs of professionals or the design of the system’s user interface (UI). Nor is it the preferred strategy for sharing a patient information within the LCR landscape. 

Electronically consuming the FHIR-compliant profiles for a patient’s medication detail from their GP practice clinical system will allow the LCR to:
-	share this data in a manner which is congruent and consistent with all other care partners
-	present the data in a manner which is appropriate for the care setting
-	share more relevant data, pertinent to the role of the professional involved and improve care delivery
-	present the data in a uniform and streamlined interface
-	provide a higher quality user experience
-	provide a higher fidelity audit record

## Primary actors ##
- care professionals
-	clinicians
-	LCR subsystem

## Secondary actors ##
- Patient

## Triggers ##
At the point of direct care, during a consultation or care delivery setting, the professional/clinician is discussing the medical history with their patient.

## Preconditions ##
Patient has been admitted onto the hospital’s Patient Administration System (PAS) and has gone through identification including retrieving their NHS number:
 -	the clinician has been advised by the local hospital’s system that their patient has information available within the LCR
 -	the clinician is logged into, and has a valid session with, the LCR
 -	the clinician has relevant access and permission to view the patient’s details in the LCR
 -	the patient has been registered within the GP practice clinical system and has data available for sharing with other clinical systems. **Note:** if no such data is available, it will not be possible to initiate the basic flow.
 -	the GP practice clinical system exists within the Spine Security Proxy (SSP)


## Postconditions ##

**On success:**
- the patient’s medication records are taken from the GP practice clinical system and presented in the LCR

**Guaranteed:**
-	 access and data returned is recorded for auditing purposes

## Basic flow with alternative and exception flows ##

 

| Step number | Step |
| ---- | -------------- | 
| Step 1 | Clinician requests the medication records for the patient using the LCR interface. |
| Step 2 | The LCR identifies the patient’s GP practice endpoint using PDS/SDS lookup. |
| Step 3 | The LCR requests the patient’s medication records from their registered GP practice. |
| Step 4 | Spine Security Proxy (SSP) checks organisation to organisation sharing agreement exists between requesting organisation (LCR) and the patient’s registered GP practice, and that the interaction (for example, Get Medication Details) is part of the sharing agreement. |
| Step 5 | GP practice clinical system checks patient permissions and consent to share. | 
| Step 6 | 	The LCR receives the medication records via the GP Connect service and presents the results to the clinician within the LCR UI. 

The following information is returned and presented in the LCR UI, as a minimum, for all medication records:

-	type of medication (for example, inpatient/outpatient/community
-	status of the medication (for example, active/cancelled/stopped)
-	priority of the medication (for example, routine/urgent/asap) 
-	details of medication to be taken (for example, Co-codamol 8mg/500mg)
-	request date/time
-	who wrote the prescription
-	details of supporting information/justification and reasons for writing the prescription
-	how the medication should be taken
-	how long the medication should be taken for
-	any additional supplementary notes/annotations | 	
| Step 7 | The clinician then reviews the information presented and determines the best course of action in response to the information. This may involve requesting additional data sets from GP Connect to supplement the data already presented within this list. | 
| Exceptions |  | 
| Step 4a | SSP sharing agreement between ‘to’ organisation and ‘from’ organisation doesn’t exist. | 
| Step 4b | Exception is reported in LCR user interface; user is directed to contact local service desk for resolution. | 
| Step 5a | The patient has not consented to the sharing of medical data from the GP practice. | 
| Step 5b | Exception is reported in the LCR user interface. | 
| Step 6a | A logic or integration exception occurs in the retrieval of data. | 
| Step 6b | Exception is reported in the LCR user interface and is logged within the LCR’s exception tracing database; user is directed to contact local service desk for resolution. | 



---
title: Reconcile patient’s medications
keywords: access record structured, use case, reconcile
tags: [accessrecord_structured,use_case]
sidebar: accessrecord_rest_sidebar
permalink: accessrecord_structured_use_case_reconciliation_acute_care.html
summary: "Use case for reconciling a patient’s medications on acute care admission"
---

## Use case ##

Medicines reconciliation acute care admissions

## Brief description ##
Upon admission to or attendance at hospital, the attending clinician is responsible for consolidating a list of the patient’s medications. Information on medications listed within the GP system (Summary Care Record (SCR)) is gathered, in paper format and then discussed with the patient/patient proxy for validation. Amendments, where required, are annotated on the paper copies. The clinician then manually transcribes all appropriate medications into the hospital Patient Administration System (PAS)/Electronic Patient Record (EPR) system and the patient’s paper drug chart before completing the admission/consultation process.

## Use case justification ##

**Clinical and administration:**
-	access to accurate information at the point of care reducing the opportunity for errors to occur
-	reduction in transcription between systems and paper to IT, leading to a reduction in prescribing errors
-	reduction in clinical time wasted (away from the patient) collecting and collating information
-	reduction in clinical time wasted (away from the patient) manually updating IT systems
-	reducing the paper flow through departments by utilising the systems workflow to manage tasks using staff time efficiently
**Patient focused:**
-	security of patient information is maintained and improved through the reduction of paper-based ‘patient identifiable documents’ in use within departments
-	increased patient/clinician time due to reduction in time spent collecting and transcribing information away from the patient
-	increased patient safety due to the reduction in manual transcription errors
-	better patient experience as they are not being asked for information which should already be available to the clinician
**Cost reduction:**
-	reduction in printing supplies
## Primary actors ##
-	clinician
-	PAS/EPR
-	GP Connect
-	GP clinical system

## Secondary actors ##

-	patient

## Triggers ##

Patient is admitted to, or attends, hospital for treatment.

## Preconditions ##

-	the patient’s details have been verified and entered on the hospital’s PAS / EPR upon admission/attendance
-	hospital staff have the correct/appropriate system access rights
-	the patient’s GP has agreed to share patient information via GP Connect
-	the patient allows this shared information to be viewed/used by hospital staff
-	electronic interactions between hospital system(s)/GP Connect/GP clinical system have been correctly configured
-	an Electronic Prescribing and Medicines Administration (EPMA) system is in use and integrates with the PAS/EPR

## Postconditions ##

**Guaranteed:**
-	A full history of medication used by the patient on admission/attendance:
o	medication prescribed by the patient’s GP
o	medication prescribed in other care settings
o	medication obtained elsewhere \- for example, ‘over the counter’
o	patients receive the correct medications when admitted

## Basic flow with alternative and exception flows ##


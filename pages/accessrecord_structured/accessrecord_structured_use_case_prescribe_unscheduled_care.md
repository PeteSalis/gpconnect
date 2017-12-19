---
title: Prescribe medication in unscheduled care
keywords: access record structured, use case, retrieve
tags: [accessrecord_structured,use_case]
sidebar: accessrecord_rest_sidebar
permalink: accessrecord_structured_use_case_prescribe_unscheduled_care.html
summary: "Use case for prescribing medication for patient in unscheduled care using active checking in an electronic prescribing environment"
---

## Use case ##
Patient to be prescribed medication in unscheduled care using active checking in an electronic prescribing environment.

## Description ##
A patient presents at unscheduled care (urgent care, minor injuries, walk-in centre, mental health crisis team) and a clinician needs to prescribe medication for the patient. They have access to electronic prescribing and the Medicine Administration System (EPMA), which includes decision support (checks for allergies, adverse reactions and medical conditions). 
The flow of information will be from the GP practice clinical system into the EPMA.
Information must be SNOMED coded for allergies, adverse reactions and conditions. Allergies and adverse reactions must include SNOMED coding for the allergen as a minimum. Conditions must be SNOMED coded.
These are required for active checking support in EPMA systems to cross-check against the contraindications from the drug file.
This is in an unscheduled care setting and, as such, data sharing agreements should not be required. This should only respect patient’s consent to share.

## Use case justification ##
In an unscheduled care setting, the patient may not be able to communicate in detail the details of allergies, adverse reactions or conditions that are relevant to prescribing. They may do so in a lay or generic terminology and this will not be clearly medically confirmed for the purposes of active checking of medication on prescribing.

As the patient’s GP is likely to have this information recorded, including the provenance and details of that information (for example, the type and severity of an allergic reaction and, if this is medically confirmed, reported by a clinician or just reported by the patient), automatically importing this information on registration at the unscheduled care location will ensure that this:
1)	Happens
2)	Is recorded accurately
3)	Does not require repeated querying of the patient for known data.

Discharge summaries supplied from unscheduled care settings are required to provide this information in a structured form (https://isd.hscic.gov.uk/trud3/user/guest/group/41/pack/34/subpack/240/releases)

## Primary actors ##
Clinician (doctor, nurse prescriber), EPMA

## Secondary actors ##

-	Patient

## Triggers ##

Clinician prescribes medication to the patient.

## Preconditions ##

-	patient has been registered in attendance at the unscheduled care location and has gone through identification including retrieving their NHS number
-	the clinician is a qualified prescriber with access to create allergies, adverse reactions, conditions and prescribe medication within the EPMA system

## Postconditions ##

**On success**
-	allergies, adverse reactions and conditions are imported and recorded against the patient record in the EPMA

**Guaranteed**
-	the list of allergies, adverse reactions and conditions are recorded on the EPMA. Access is recorded for auditing purposes.

## Basic flow with alternative and exception flows ##


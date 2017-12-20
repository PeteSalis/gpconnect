---
title: View urgent care plan on GP system 
keywords: access record structured, use case, retrieve
tags: [accessrecord_structured,use_case]
sidebar: accessrecord_rest_sidebar
permalink: accessrecord_structured_use_case_urgent_care_plan.html
summary: "Use case for viewing a patient’s urgent care plan on GP system for details of current medication"
---

## Use Case ##

View GP current medication

## Brief description ##

A user views a patient’s urgent care plan and, along with the existing care plan data, can see current medications pulled in real time from the GP system relevant for that patient.
For the purposes of this use case, we will assume the user is an urgent care user, such as a paramedic working for the ambulance service. However, the urgent care plan is used in emergency, primary, and secondary services, and clinicians from all these areas also access the urgent care plan for patients.
The following list comprises the types of urgent care users who will use the information:
-	doctor in A&E department
-	nurse in A&E department
-	clinical adviser in 111 service
-	out-of-hours (OOH) GP
-	paramedic from ambulance service
Smartcard access is not required to access the urgent care plan. Many users log in with a username and password. A smartcard is one of the options they have available to them. Some practices have implemented single sign-on, which links their GP practice to the urgent care plan and provides a way for them to access the application from their GP system.

## Use case justification ##

This use case is focused on patient safety, where a wide range of clinicians who currently do not have access to patient current medication could have this information available to them.
Primary actors: 
-	urgent care user (ambulance service paramedic, OOH GP, 111 and A&E staff)
-	non-urgent care user (acute hospital clinician, community nurse, allied health professional, social worker)
-	GPs

## Triggers ##

Urgent care user accesses urgent care plan and navigates to medication page of that patient’s plan.

## Preconditions ##

-	patient is registered to a GP covered by the urgent care plan
-	current medications have been populated in GP system and this is available to GP connect
-	care plan has been created for relevant patient
-	urgent care actor is a registered user and user’s organisation has signed an Information Sharing Agreement (ISA) and acceptable use policies

## Postconditions ##

**On success**
-	when accessing the medications page on the care plan, all current medications available in the relevant GP system are presented to the user instantly, in real time

**Guaranteed**
-	The request to pull data and the outcome – successful/unsuccessful with other metadata is logged and auditable.
- It is clear to the user where the call has been completed and there are no current medications captured (‘none’ or similar message is presented).
- It is clear to the user where the GP connect service is not contactable (‘GP system not available’ or similar message is presented).


## Basic flow with alternative and exception flows ##

| Step number       | Step description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Step   1       |    User logs in from N3   network (or on non-N3 via authentication partner).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|    Step   2       |    User accesses a patient’s urgent   care plan.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|    Step   3       |    Care plan system confirms   the patient’s GP practice using Spine Directory Service (SDS)/Personal   Demographics Service (PDS) lookup.                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|    Step   4       |    Urgent care user clicks on   the medications tab within system.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|    Step   5       |    Care   plan system requests the patients ‘Current Medication List’ from their   registered GP Practice.       The   term ‘current medication’ refers to medication prescribed to the patient (as   either a single prescription or an instance of a repeat prescription) within   the last three months.                                                                                                                                                                                                                                                                                                        |
|    Step   6       |    Spine   Security Proxy (SSP) checks that an organisation to organisation sharing   agreement exists between requesting organisation (doctors) and the patient’s   registered GP practice, and that the interaction (for example, Get   Medications) is part of the sharing agreement.                                                                                                                                                                                                                                                                                                                           |
|    Step   7       |    GP   practice clinical system checks patient permissions and consent to share.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|    Step   8       |    System   receives the ‘Current Medication List’ and presents the results to the clinician.       The   following information is returned with the following information:   -            The date that the medication was prescribed    -            the medication code   -            the medication name   -            the medication dosage   -            the medication prescribed quantity   -            The number of allowed repeats   -            The number of issued repeats<br>   Format to   be confirmed but could be presented in a similar way to the GP system   medication page layout.    |
|    Step   9       |    Care   plan system will import the medication information into a temporary storage. This   will include mapping the data received into the care plan system’s own data   structures/coding for medications.                                                                                                                                                                                                                                                                                                                                                                                                     |
|    Step   10      |    User   is then able to view current medications and act accordingly.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|    Exceptions     |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|    Step   2a      |    GP practice is not found on   SDS.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|    Step   3a      |    No   medications returned                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|    Step   4a      |    Patient   not returned                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
|    Step   5a      |    Patient   consent not given                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|    Step   6a      |    GP   Connect connectivity down                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|    Step   7a      |    Current   medication is updated by GP and user re-checks.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |

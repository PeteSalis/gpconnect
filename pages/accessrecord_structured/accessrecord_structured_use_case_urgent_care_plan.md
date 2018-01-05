---
title: View urgent care plan on GP system 
keywords: access record structured, use case, retrieve
tags: [accessrecord_structured,use_case]
sidebar: accessrecord_structured_sidebar
permalink: accessrecord_structured_use_case_urgent_care_plan.html
summary: "Use case for viewing a patient’s urgent care plan on GP system for details of current medication"
---

## Use case ##

**View GP current medication.**

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

This use case is focused on patient safety<sup>1</sup>, where a wide range of clinicians who currently do not have access to patient current medication could have this information available to them.

## Primary actors ## 
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
-	the request to pull data and the outcome – successful/unsuccessful with other metadata is logged and auditable
- it is clear to the user where the call has been completed and there are no current medications captured (‘none’ or similar message is presented)
- it is clear to the user where the GP connect service is not contactable (‘GP system not available’ or similar message is presented)


## Basic flow with alternative and exception flows ##

<table>
	<tr>
		<th>Step number</th>
		<th>Step description</th>
	</tr>
	<tr>
		<td>Step 1</td>
		<td>User logs in from N3 network (or on non-N3 via authentication partner).</td>
	</tr>
	<tr>
		<td>Step 2</td>
		<td>User accesses a patient’s urgent care plan.</td>
	</tr>
	<tr>
		<td>Step 3</td>
		<td>Care plan system confirms the patient’s GP practice using Spine Directory Service (SDS)/Personal Demographics Service (PDS) lookup.</td>
	</tr>
	<tr>
		<td>Step 4</td>
		<td>Urgent care user clicks on the medications tab within system.</td>
	</tr>
	<tr>
		<td>Step 5</td>
		<td>Care plan system requests the patients ‘Current Medication List’ from their registered GP Practice. The term ‘current medication’ refers to medication prescribed to the patient (as either a single prescription or an instance of a repeat prescription) within the last three months.</td>
	</tr>
	<tr>
		<td>Step 6</td>
		<td>Spine Security Proxy (SSP) checks that an organisation to organisation sharing agreement exists between requesting organisation (doctors) and the patient’s registered GP practice, and that the interaction (for example, Get Medications) is part of the sharing agreement.</td>
	</tr>
	<tr>
		<td>Step 7</td>
		<td>GP practice clinical system checks patient permissions and consent to share.</td>
	</tr>
	<tr>
		<td>Step 8</td>
		<td>System receives the ‘Current Medication List’ and presents the results to the clinician.<br/>
			<br/>
			The following information is returned with the following information:
				<ul>
					<li>the date that the medication was prescribed</li>
					<li>the medication code</li>
					<li>the medication name</li>
					<li>the medication dosage</li>
					<li>the medication prescribed quantity</li>
					<li>the number of allowed repeats</li>
					<li>the number of issued repeats</li>
				</ul>
			<br/>
			Format to   be confirmed but could be presented in a similar way to the GP system   medication page layout.
		</td>
	</tr>
	<tr>
		<td>Step 9</td>
		<td>Care plan system will import the medication information into a temporary storage. This will include mapping the data received into the care plan system’s own data structures/coding for medications.</td>
	</tr>
	<tr>
		<td>Step 10</td>
		<td>User is then able to view current medications and act accordingly.</td>
	</tr>
	<tr>
		<td>Exceptions</td>
		<td/>
	</tr>
	<tr>
		<td>Step 2a</td>
		<td>GP practice is not found on SDS.</td>
	</tr>
	<tr>
		<td>Step 3a</td>
		<td>No medications returned.</td>
	</tr>
	<tr>
		<td>Step 4a</td>
		<td>Patient not returned.</td>
	</tr>
	<tr>
		<td>Step 5a</td>
		<td>Patient consent not given.</td>
	</tr>
	<tr>
		<td>Step 6a</td>
		<td>GP Connect connectivity down.</td>
	</tr>
	<tr>
		<td>Step 7a</td>
		<td>Current medication is updated by GP and user re-checks.</td>
	</tr>
</table>

<sup>1</sup> [https://www.england.nhs.uk/wp-content/uploads/2014/03/psa-sup-info-med-error.pdf](https://www.england.nhs.uk/wp-content/uploads/2014/03/psa-sup-info-med-error.pdf)

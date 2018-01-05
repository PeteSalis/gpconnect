---
title: Reconcile patient’s medications
keywords: access record structured, use case, reconcile
tags: [accessrecord_structured,use_case]
sidebar: accessrecord_structured_sidebar
permalink: accessrecord_structured_use_case_reconciliation_acute_care.html
summary: "Use case for reconciling a patient’s medications on acute care admission"
---

## Use case ##

**Medicines reconciliation acute care admissions**

On admission to or attendance at hospital, the attending clinician is responsible for consolidating a list of the patient’s medications. Information on medications listed within the GP system (Summary Care Record (SCR)) is gathered, in paper format and then discussed with the patient/patient proxy for validation. Amendments, where required, are annotated on the paper copies. The clinician then manually transcribes all appropriate medications into the hospital Patient Administration System (PAS)/Electronic Patient Record (EPR) system and the patient’s paper drug chart before completing the admission/consultation process.

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

<table>
	<tr>
		<th>Step number</th><th>Step description</th>
	</tr><tr>
		<td>Step 1</td><td>Patient attends/admitted to hospital.</td>
	</tr><tr>
		<td>Step 2</td><td>Clinician identifies need to establish the patient’s medication history.</td>
	</tr><tr>
		<td>Step 3</td><td>Clinician accesses the hospital PAS/EPR system to retrieve GP medication history. PAS requests GP medication list from GP Connect.</td>
	</tr><tr>
		<td>Step 4</td><td>GP Connect requests GP medication list from the GP clinical system.</td>
	</tr><tr>
		<td>Step 5</td>
		<td>GP  clinical system provides the defined medication list to GP Connect.<br/>
			<br>
			This medication information will be the same as  that provided in the SCR / SCR AI (Additional Information) and will be  subject to the same exclusion criteria:
			<ul>
				<li>Acute Medications (with a prescribed date in  the last 12 months)
					<ul>
						<li>Prescribed date</li>
						<li>Medication Item</li>
						<li>Dosage Instructions (including day of  administration where applicable)</li>
						<li>Quantity</li>
						<li>Source (the organisation that issued the prescription)</li>
					</ul>
				</li>
				<li>Current Repeat Medications (with an end date  in the future)
					<ul>
						<li>Last issued date</li>
						<li>Medication Item</li>
						<li>Dosage Instructions (including day of administration where applicable)</li>
						<li>Quantity</li>
						<li>Reason for medication</li>
						<li>Source (the organisation that issued the  prescription)</li>
					</ul>
				</li>
				<li>Discontinued Repeat Medications (with an end date in the past and the last scripts prescribed date in the last six months)
					<ul>
						<li>Last Issued</li>
						<li>Medication Item</li>
						<li>Dosage Instructions (including days of administration where applicable)</li>
						<li>Quantity</li>
						<li>Reason for Medication</li>
						<li>Date Discontinued</li>
						<li>Source (the organisation that issued the  prescription)</li>
					</ul>
				</li>
				<li>For all three categories above:
					<ul>
						<li>Include mixtures</li>
						<li>Includes medications recorded on the GP  record that were prescribed by another organisation (example an hospital)</li>
						<li>Do not include medications where the script  was cancelled before being prescribed (example entered in error)</li>
					</ul>
				</li>
				<li>GP Connect API Search Criteria:
					<ul>
						<li>Search MedicationStatement  by:
							<ul>
								<li>Patient (using NHS Number)</li>
								<li>Status (active and completed)</li>
								<li>LastIssueDate (see  above for timeframes)</li>
							</ul>
						</li>
						<li>Include MedicationOrder  by:
							<ul>
								<li>All  Medication Orders linked to the MedicationStatement</li>
							</ul>
						</li>
					</ul>
				</li>
			</ul>
		</td>
	</tr><tr>
		<td>Step 6</td><td>GP Connect presents the GP  medication list to the hospital PAS/EPR.</td>
	</tr><tr>
		<td>Step 7</td><td>PAS/EPR  saves a copy of the GP medications list directly to PAS/EPR.</td>
	</tr><tr>
		<td>Step 8</td><td>PAS/EPR  presents an integrated view of GP medications to the clinician for  manipulation into the EPMA.</td>
	</tr><tr>
		<td>Step 9</td><td>Clinician  reviews and verifies GP medication list with Patient/Patient Proxy.</td>
	</tr><tr>
		<td>Step 10</td><td>Clinician marks medication  for ‘Inclusion As-Is’ into the PAS / EPR.</td>
	</tr><tr>
		<td>Step 11</td><td>Medication is integrated  into the PAS/EPR as ‘Medication on Arrival’.</td>
	</tr><tr>
		<td>Step 9a</td><td>This scenario details the  situation where medications are identified in addition to those recorded  within the GP clinical system and are not present within the PAS/EPR integrated  view. This scenario includes items which may be ‘over the counter’   medications.<br>9A1.       Clinician selects ‘Add New Medication’.<br>9A2.       Clinician manually enters new medication details.</td>
	</tr><tr>
		<td>Step 9b</td><td>This scenario details the  situation where discrepancies are identified with one or more items of the  patient’s medications recorded within the GP clinical system and are  presented within the PAS/EPR integrated view, but the medication is still in  use.<br>9B1.       Clinician marks medication for ‘Inclusion with Amendment’   into the PAS / EPR.<br>9B2.       Clinician amends medication details.</td>
	</tr><tr>
		<td>Step 9c</td><td>This scenario details the  situation where discrepancies are identified with one or more items of the  patient’s medications recorded within the GP clinical system and are  presented within the PAS/EPR integrated view, but the medication is no longer  in use.<br>9C1.       Clinician marks medication as ‘Stopped’.<br>9C2.       Clinician adds the reason for the medication being stopped.</td>
	</tr>
</table>
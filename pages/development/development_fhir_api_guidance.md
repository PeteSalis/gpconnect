---
title: FHIR API guidance
keywords: fhir development
tags: [fhir,development]
sidebar: overview_sidebar
permalink: development_fhir_api_guidance.html
summary: "Implementation Guidance for developers - focusing on FHIR specifics"
---

## Purpose ##

This document is intended for use by software developers looking to build a conformant GP Connect API interface utilising the FHIR&reg; standard, with a particular focus on FHIR specifics

### Notational conventions ###

The keywords "**MUST**", "**MUST NOT**", "**REQUIRED**", "**SHALL**", "**SHALL NOT**", "**SHOULD**", "**SHOULD NOT**", "**RECOMMENDED**", "**MAY**", and "**OPTIONAL**" in this document are to be interpreted as described in [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt).

## FHIR implementation guidance ##

The purpose of the implementation guide is to describe adaptations of the base FHIR specification specific for GP Connect First of Type (FoT) implementations. It therefore focuses mainly on any additional constraints and specialisations from the base specification that apply to GP Connect. The complete specification therefore comprises the base FHIR specification plus the constraints and specialisations described herein. Where there is a conflict between the base specification and this document, this document shall take precedence.

### FHIR overview ###

As outlined on the official [HL7&reg; FHIR](http://hl7.org/fhir/) website:

> FHIR (Fast Health Interoperability Resources) is designed to enable information exchange to support the provision of healthcare in a wide variety of settings. The specification builds on and adapts modern, widely used RESTful practices to enable the provision of integrated healthcare across a wide range of teams and organizations.

### [FHIR timelines](http://hl7.org/fhir/timelines.html) ###

{% include note.html content="This document is written to accompany the currently released version of FHIR which is **Draft Standard For Trial Use (DSTU) 2**." %}

FHIR is currently a draft standard and as such is expected to evolve and develop over time. As such the NHS Digital expects FHIR clients and servers (developed as part of GP Connect) to be maintained and uplifted to newer versions of the FHIR&reg; standard as they become available and are staged into the GP Connect programme.

A pre-release version of FHIR (expected to be the basis of STU3) has been published as the [May 2016](http://hl7.org/fhir/2016May/) release. The FHIR community is expecting the finalised STU3 release to be made available in late 2016/early 2017.

When a new release of the FHIR standard has been published for use NHS Digital will carry out an impact assessment of all FHIR resources used by the GP Connect API project to determine the impact of transitioning to the new FHIR release. Following this assessment, the FHIR Resource library will be updated to include resources using this new release. NHS Digital will work with Consumers and Providers throughout this transition phase and are expected to maintain both versions of the FHIR release until it has been agreed that the old version can be deprecated.

<p/>

{% include note.html content="[STU3](http://hl7.org/fhir/2016May/) includes the introduction of a new draft `Task` resource which may impact the future implementation direction of the GP Connect Task profile." %}

### FHIR implementations ###

The Health Level Seven (HL7&reg;) International standards body maintains a list of open source FHIR implementations on their [Wiki](http://wiki.hl7.org/index.php?title=Open_Source_FHIR_implementations). Currently five FHIR server implementations and a number of client libraries are available as open source software. These are written in a variety of popular programming languages (e.g. Java, C#.NET, JavaScript and Python).

{% include tip.html content="NHS Digital strongly recommends software vendors utilise (and contributing back to) an existing open source FHIR library to both help accelerate development and to grow/mature the open source FHIR ecosystem." %}

### FHIR in scope ###

To help API implementers deal with the FHIR learning curve NHS Digital has worked to constrain the scope of the FHIR&reg; standard that is expected to be implemented in the first tranche of development work as follows: 

1. [Resource](https://www.hl7.org/fhir/DSTU2/resource.html)
	1. [Resource Identity](https://www.hl7.org/fhir/DSTU2/resource.html#id)
	1. [Business Identifiers](https://www.hl7.org/fhir/DSTU2/resource.html#identifiers)
2. [Domain Resource](https://www.hl7.org/fhir/DSTU2/domainresource.html)
	1. Common API
		1. [Patient](https://www.hl7.org/fhir/DSTU2/patient.html) profiled as gpconnect-patient-1.
		2. [Practitioner](https://www.hl7.org/fhir/DSTU2/practitioner.html) profiled as gpconnect-practitioner-1.
		3. [Organization](https://www.hl7.org/fhir/DSTU2/organization.html) profiled as gpconnect-organization-1.
		4. [Location](https://www.hl7.org/fhir/DSTU2/location.html) profiled as gpconnect-location-1
	2. Care Record API
		1. Please refer to the [Resource Type](#ResourceType) section of this document. 
	3. Bookings API
		1. [Schedule](https://www.hl7.org/fhir/DSTU2/schedule.html) profiled as gpconnect-schedule-1
		2. [Slot](https://www.hl7.org/fhir/DSTU2/schedule.html) profiled as gpconnect-slot-1
		3. [Appointment](https://www.hl7.org/fhir/DSTU2/appointment.html) profiled as gpconnect-appointment-1
	3. Task API
		1. [Order](https://www.hl7.org/fhir/DSTU2/order.html) profiled as gpconnect-order-1
3. [Resource Metadata](https://www.hl7.org/fhir/DSTU2/resource.html#Meta)
	1. [profile](https://www.hl7.org/fhir/DSTU2/resource.html#metadata)
	2. [version Id](https://www.hl7.org/fhir/DSTU2/resource.html#metadata)
4. [Bundle](https://www.hl7.org/fhir/DSTU2/bundle.html)
5. [Data Types](https://www.hl7.org/fhir/DSTU2/datatypes.html)
	1. [Primitive Types](https://www.hl7.org/fhir/DSTU2/datatypes.html#primitive)
		1. All primitive types SHALL be supported.
	2. [Complex Types](https://www.hl7.org/fhir/DSTU2/datatypes.html#complex) 
		1. The following complex types SHALL be supported.
			1. [Identifier](https://www.hl7.org/fhir/DSTU2/datatypes.html#identifier)
			2. [Coding](https://www.hl7.org/fhir/DSTU2/datatypes.html#codeableconcept)
			2. [Codeable Concept](https://www.hl7.org/fhir/DSTU2/datatypes.html#coding)
			3. [Period](https://www.hl7.org/fhir/DSTU2/datatypes.html#period)
		4. It is expected that further complex types will be introduced as required to model structured data.
6. Interactions
	1.  Instance
		1.  [READ](https://www.hl7.org/fhir/DSTU2/http.html#read)
		2.  [UPDATE](https://www.hl7.org/fhir/DSTU2/http.html#update)
		3.  [DELETE](https://www.hl7.org/fhir/DSTU2/http.html#delete)
	2.  Type Level
		1.  [CREATE](https://www.hl7.org/fhir/DSTU2/http.html#create)
		2.  [SEARCH](https://www.hl7.org/fhir/DSTU2/http.html#search)
7.  [Search](https://www.hl7.org/fhir/DSTU2/http.html#search)
	1.  [Search Parameter Types](https://www.hl7.org/fhir/DSTU2/search.html#ptypes)
		1.  [Date](https://www.hl7.org/fhir/DSTU2/search.html#date)
		2.  [Token](https://www.hl7.org/fhir/DSTU2/search.html#token)
		3.  [Reference](https://www.hl7.org/fhir/DSTU2/search.html#reference)
	2. [Search Prefixes](https://www.hl7.org/fhir/DSTU2/search.html#prefix)
		1. lt (less than)
		2. le (less or equal to)
		3. gt (great than)
		4. ge (greater or equal to)
		5. eq (equal to)
	3.  Parameters for all resources
		1.  [_query](https://www.hl7.org/fhir/DSTU2/search.html#query)
		2.  [_list](https://www.hl7.org/fhir/DSTU2/search.html#list)
	4.  Search result parameters
		1.  [_include](https://www.hl7.org/fhir/DSTU2/search.html#include) can be used internally inside a named `_query` operation.
		2.  [_sort](https://www.hl7.org/fhir/DSTU2/search.html#sort) can be used internally inside a named `_query` operation.
	
3.  [Operations](https://www.hl7.org/fhir/DSTU2/operations.html)
	1.  [Implementation Defined Operations](https://www.hl7.org/fhir/DSTU2/operations.html#extensibility)

{% include important.html content="It is fully expected that over time new areas of the FHIR&reg; standard will be brought into scope as new capabilities are requested and vendors understanding of FHIR&reg; standard matures. This is in-line with the agreed iterative/Agile engagement process that has been agreed between the NHS Digital and the Principal GP system vendors." %}

### FHIR out of scope ###

GP Connect provider systems are not expected to implement the following aspects of the FHIR&reg; standard under the scope of the first tranche of development work:

1. Operations
	1. Validate a resource
	2. Meta operations
	3. Generate a document
	4. Process message
	5. Find a functional list
	6. ConceptMap operations
	7. Closure Table Maintenance
	8. Fetch records
	9. Questionnaire operations
	10. Terminology operations
2. Interactions
	1. Instance
		1. HISTORY
		2. [VREAD](https://www.hl7.org/fhir/DSTU2/http.html#vread)
	2. Type Level
		1. HISTORY
	3. Whole System
		1. BATCH TRANSACTION
		2. HISTORY
		3. SEARCH
	4. Conditional Interactions
		1. CREATE
		2. UPDATE
		3. DELETE
3. Search
	1. Parameter Types
		1. Number
		2. String
		3. Quantity
		4. Composite
		5. URL
	2. Parameters for all resources
		1. _language
		2. _lastUpdate
		3. _tag
		4. _profile
		5. _security
		6. _text
		7. _content
		8. _filter
	3. Search result parameters
		1. _revinclude
		2. _count		
		3. _summary
		4. _elements
		5. _contained 
		6. _containedType
	4. Chained parameters
	5. Paging / Page Count
4. Resource Metadata
	1. lastUpdated
	2. security
	2. tag

{% include warning.html content="Vendors are free to implement additional FHIR functionality above that mandated under the GP Connect delivery if they so desire. However, the Spine Security Proxy (SSP) will only forward accredited system interactions." %}

### Use of Must-Support flag ###

Some resource profiles used in GP Connect make use of the [Must-Support](https://www.hl7.org/fhir/DSTU2/conformance-rules.html#mustSupport) flag. 

Where a Must-Support flag is present on a resource element, a `consumer` system SHALL populate the field in the request body if data is available to do so, irrespective of the fact that field cardinality may be `0..1` or `0..*`. 

Similarly, `provider` systems SHALL populate an element in responses where data is available to do so, irrespective of optional cardinality. When a `provider` system receives data from a consumer for a field marked with the Must-Support flag, the provider system SHALL store this data field in such a way that the data element is preserved and the element can be populated in future responses to consumer requests for the resource in question.  

For example, see the [Register a patient request body](foundations_use_case_register_a_patient.html#payload-request-body)

### FHIR System conformance ###

Servers SHALL provide a read-only [FHIR Conformance resource](https://www.hl7.org/fhir/DSTU2/conformance.html) that identifies all of the profiles and operations that the server supports for each resource type.

A servers conformance statement SHALL be available using the following [conformance interactions](http://hl7.org/fhir/http.html#conformance):

```
GET [base]/metadata {?_format=[mime-type]}
```

Refer to [Foundations - Get The FHIR Conformance Profile](foundations_use_case_get_the_fhir_capability_statement.html) for an example GP Connect FHIR conformance profile.

{% include roadmap.html content="NHS Digital is evaluating the benefits of providing a centrally hosted FHIR server to act as a definition repository for *Content* and *Operation Control* [Infrastructure Resources](https://www.hl7.org/fhir/DSTU2/infrastructure.html). However, this is out of scope for the initial GP Connect deployments." %}

### FHIR Resource conformance ###

To help a consumer find the correct set of reports for a use-case, a provider of resources SHALL, for any profile declared in [Conformance.profile](https://www.hl7.org/fhir/DSTU2/profiling.html#2.13.0.3.2) mark resources with profile assertions documenting the profile(s) they conform to. A provider of resources SHOULD also ensure that any resource instance that would reasonably be expected to conform to the declared profiles SHOULD be published in this form.

### GP Connect FHIR API conformance ###

GP Connect comprises a number of RESTful API bundles. Each API bundle is intended to support sets of related use cases, for example the Appointment API bundle supports viewing, booking and cancelling GP appointments in a number of scenarios.

Individual API bundles may be provided independently of each other. GP Connect conformance may be claimed in relation to one or more API bundles. A provider claiming to provide an API bundle must be fully conformant (i.e. implement all of the resource profiles and interactions for the API bundle as specified in this document and all of the general requirements described herein).

### Incomplete data - expected behaviour ###

Where resource instance data available in clinical systems is either insufficient or corrupt and thus a resource cannot be constructed by a provider system which meets the associated FHIR profile, the following guidance describes expected behaviour:

**Scenario: GET resource by logical ID** 
 
An HTTP 500 error should be returned with an OperationOutcome resource providing diagnostic details. This may include details of the resource in the location element. 

**Scenario: FHIR response bundles**

When an response bundle contains multiple resources, one or more of which cannot be populated as available data does not enable the resource in question to be populated to validate the associated profile, the provider will behave as follows: The request as a whole will error with an 500 HTTP Status returned, together with the appropriate OperationOutcome resource providing diagnostic detail of the the error.

## FHIR Resources ##

### Resource Data Types ###

The FHIR specification defines a set of [data types](https://www.hl7.org/fhir/DSTU2/datatypes.html) that are used for the resource elements.

The user locale (i.e. user's language, region and any special variant preferences that the user may want to see in their user interface) of a systems SHALL NOT effect the FHIR on the wire representation of any data types (especially date-time and number formats).

Certain aspects of [Primitive Data Type](https://www.hl7.org/fhir/DSTU2/datatypes.html#primitive) respresentation warrant further consideration and SHALL be taken into consideration when designing and constructing FHIR resources.

For example:

- Leading 0 digits are not allowed.
- Strings SHALL NOT exceed 1MB in size.
- URIs are case sensitive.
- UUID values (urn:uuid:53fefa32-fcbb-4ff8-8a92-55ee120877b7) use all lowercase.
- Dates have no time zone.
- Dates can be partial dates (e.g. just year or year + month).
- Precision of the decimal value has signficance.
- Primitive types other than string SHALL NOT have leading or trailing whitespace.
- [Use of null](https://www.hl7.org/fhir/DSTU2/json.html#null) and empty / zero length values in [XML and JSON representations](https://www.hl7.org/fhir/DSTU2/datatypes.html#1.19.0.1.1)

### Resource Narrative ###

The FHIR [resource narrative](https://www.hl7.org/fhir/DSTU2/narrative.html) is not currently expected to be populated. 

### Resource References ###

The FHIR resource model includes [resource references](http://hl7.org/fhir/references.html) from one resource to another.

A reference can be either:

- a relative or absolute URL to a resource managed by the same resource server (a local reference), or 
- an absolute URL to a resource managed by another resource server (a remote reference)

A provider’s ability to process a request relating to a resource may depend on its ability to use one or more resource references that the resource contains (i.e. its ability to ‘follow the links’ to other resources).

{% include important.html content="GP Connect clients and servers SHALL utilise local relative references only and as such the resources will be expected to reside on the same server." %}

Resource references SHALL include a short human-readable `display` field for identification of the resource that is being referenced which can be used for display purposes without needing to pull the entire referenced resource. The short human-readable `display` field SHALL be formatted inline with Common User Interface (CUI) guidance where such guidance exists (e.g. patient name).

| Resource | Display Format |
| -------- | -------------- |
| `Patient` | patient.name |
| `Practitioner` | practitioner.name |
| `Organization` | organization.name |

{% include todo.html content="Further display field guidance to be added in [Stage 2.](designprinciples_maturity_model.html)" %}

### Resource Metadata ###

Servers SHALL provide the `profile` [metadata](https://www.hl7.org/fhir/DSTU2/resource.html#Meta) for each resource, asserting that the content conforms to one of the GP Connect resource profiles.  

Servers SHALL provide the `version Id` metadata for each item. This SHALL change each time the content of the resource changes.

Consumer creating or amending a resource SHALL provide the `profile` metadata details within the sent resource. The `profile` metadata should be checked for by the Provider to ensure predictable process and for forward compatibility when a server can handle multiple profiles for the same type of resource.

Clients SHALL utilise the `version Id` when performing updates to allow [management of resource contention](https://www.hl7.org/fhir/DSTU2/http.html#concurrency) and to protect against [Lost Updates](http://www.w3.org/1999/04/Editing/).

### Resource Transactions ###

When performing an update or create [resource transactions](https://www.hl7.org/fhir/DSTU2/http.html#transactional-integrity), servers:

-	SHALL **validate the content against valid profiles** and business rules before creating/updating the resource.
-	MAY apply business rules that alter the content.
-	MAY merge updated content with existing content.

Servers SHALL validate the existence of any referenced resources when creating or updating a resource. For example, a `Slot` reference (e.g. `Slot/D497DB00-99AA-11E5-A837-0800200C9A66`) used when creating a new `Appointment` would be checked for existence on the server and an error returned (and the create interaction aborted) if the slot does not exist.

Refer to the GitHub hosted [GP Connect FHIR Repository](https://github.com/nhsconnect/gpconnect-fhir) for the published FHIR profiles.

Refer to the [HL7&reg; FHIR&reg; Validator](https://www.hl7.org/fhir/DSTU2/validation.html#jar) page for the most upto date details on how FHIR resources can be validated. 

Servers SHALL provide a read interaction for every resource it accepts update interactions on.

Consumers SHALL follow the pattern described in the [Transactional Integrity](https://www.hl7.org/fhir/DSTU2/http.html#transactional-integrity) section of the base FHIR specification, built on top of version-aware updates, for updating resources.

## FHIR Resource Interactions ##

### Requests ###

Servers SHALL be able to consume and process the following requests for GP Connect FoT.

| Interaction | Path | Request Verb | Request Content-Type | Body | Prefer | Conditional |
| ----------- | ---- | ------------ | -------------------- | ---- | ------ | ----------- |
| `read`   | `/[type]/[id]` | `GET` | N/A | N/A | N/A | `ETag` |
| `update` | `/[type]/[id]` | `PUT` | R | Resource | O | `If-Match`|
| `delete` | `/[type]/[id]` | `DELETE` | N/A | N/A | N/A | N/A |
| `create` | `/[type]` | `POST` | R | Resource | O | N/A |
| `search` | `/[type]?` | `GET` | N/A | N/A | N/A | N/A |
| `(operation)` | `/[type]/$[name]` `/[type]/[id]/$[name]` | `POST` <br/> `GET` | R <br/> `application/x-www-form-urlencoded` | Parameters <br/> form data | N/A | N/A |

> N/A = not present, R = Required, O = Optional

### Responses ###

Servers SHALL be expected to produce the following responses for GP Connect FoT.

| Interaction | Response Content-Type | Body | Location | Content-Location | Versioning | Status Codes |
| ----------- | --------------------- | ---- | -------- | ---------------- | ---------- | ------------ |
| `read`   | R | R: Resource | N/A | R | `ETag` | `200`, `404`, `410` |
| `update` | R | R: Resource | N/A | R | `ETag` | `200`, `201`, `400`, `404` `405`, `409`, `412`, `422` |
| `delete` | R | R: Operation Outcome | N/A | N/A | N/A | `200`, `204`, `404`, `405`, `409`, `412` |
| `create` | R | R: Resource | R | R | `ETag` | `201`, `400`, `404` `405`, `422` |
| `search` | R | R: Bundle | N/A | N/A | N/A | `200`, `403` |
| `(operation)` | R | R: Parameters/Resource | N/A | N/A | N/A | `200`, `400`, `403`, `404`, `422`  |

> N/A = not present, R = Required, O = Optional

#### Response Codes ####

Servers SHALL produce the following main [HTTP Status Codes](http://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml). 

| HTTP Status Code | Description |
| ---------------- | ----------- |
| `200` | OK |
| `201` | Created |
| `400` | Bad Request |
| `401` | Unauthorized |
| `403` | Forbidden |
| `404` | Not Found |
| `405` | Method Not Allowed |
| `409` | Conflict |
| `410` | Gone |
| `412` | Precondition Failed |
| `415` | Unsupported Media Type |
| `422` | Unprocessable Entity |
| `500` | Internal Server Error |
| `501` | Not Implemented |

#### [Rejecting Updates](https://www.hl7.org/fhir/DSTU2/http.html#2.1.0.10.1) ####

[OperationOutcome](https://www.hl7.org/fhir/DSTU2/operationoutcome.html) may be returned with any HTTP `4xx` or `5xx` response, but is not required - many of these errors may be generated by generic server frameworks underlying a FHIR server.

Servers are permitted to reject update interactions because of integrity concerns or other business rules, and return HTTP status codes accordingly (usually a `422`).

As outlined in the FHIR specification, any of these errors SHOULD be accompanied by an [OperationOutcome](https://www.hl7.org/fhir/DSTU2/operationoutcome.html) resource that provides additional detail concerning the issue.

Refer to [FHIR Guidance - Error Handling](development_fhir_error_handling_guidance.html) for full details of error codes that SHALL be used when returning an operation outcome error.

### Compartment Based Access ###

```
VERB [base]/[compartment_type]/[id]/[type]{?_format=[mime-type]}
```

Each resource type may belong to one or more logical [compartments](http://hl7.org/fhir/DSTU2/compartments.html). A compartment is a logical grouping of resources which share a common property.

Servers SHALL support the `Patient` compartment for `Appointment` access.

The patient compartment includes any resources where the `subject` of the resource is the patient.

For example, to retrieve a list of all of a patient's appointments, use the URL:

```http
GET [base]/Patient/[id]/Appointment
```

Servers SHALL support searching within this compartment by `start` and `end` date/time, for example:

```http
GET [base]/Patient/[id]/Appointment?start=[{search_prefix}start_date]{&start=[{search_prefix}end_date]}
```


## Read Resource ##

A [resource read](https://www.hl7.org/fhir/DSTU2/http.html#read) takes the following format:

```http
GET [base]/[type]/[id]{?_format=[mime-type]}
```

The returned resource SHALL have an `id` element with a value that is the [id].

Servers SHALL return an `ETag` header with the `version Id` of the resource.

### Available for Resources ###

| Capability   | Resource(s) |
| ------------ | ----------- |
| **Foundations**     | `Patient`, `Practitioner`, `Organization` |
| **Access Record**   | TBC |
| **Appointments**    | `Appointment`, `Schedule`, `Slot`, `Location` |
| **Tasks**           | `Order` |

{% include important.html content="In workshop discussions with all Principal GP system vendors it has been agreed that record locking (inside the GP system) will not impact on the ability of clients to query the GP Connect APIs and to obtain the latest saved/committed clinical and administrative data." %}

### Retrieving a patient resource by logical Id ###

#### Request ####

```http
GET [base]/Patient/[id]
```

For example:

```http
GET [base]/Patient/1A6E1B1C-6340-4663-926C-9CD1306EAAF8?_format=application/xml+fhir
```

{% include tip.html content="In this example the response format has been specified in the request URL using the `_format` parameter. This could also have been specified using the HTTP Accept Header mechanism." %} 

#### Response ####

```xml
<Patient xmlns="http://hl7.org/fhir">
	<id value="1A6E1B1C-6340-4663-926C-9CD1306EAAF8" />
	<meta>
		<profile value="http://fhir.nhs.net/StructureDefinition/gpconnect-patient-1" />
	</meta>
	<identifier>
		<system value="http://fhir.nhs.net/Id/nhs-number" />
		<value value="9900002831" />
	</identifier>
	<identifier>
		<type>
			<coding>
				<system value="http://fhir.nhs.net/ValueSet/gpconnect-patient-identifier-type-1" />
				<code value="Local" />
				<display value="Local identifier" />
			</coding>
		</type>
		<system value="http://fhir.nhs.net/Id/local-identifier" />
		<value value="L12345" />
	</identifier>
	<name>
		<use value="usual" />
		<family value="Taylor" />
		<given value="Sally" />
		<prefix value="Mrs" />
	</name>
	<birthDate value="1947-06-09" />
	<address>
		<use value="home" />
		<type value="both" />
		<line value="42, Grove Street" />
		<city value="Overtown" />
		<district value="West Yorkshire" />
		<postalCode value="LS21 1PF" />
	</address>
</Patient>
```

## Create Resource ##

To [create](https://www.hl7.org/fhir/DSTU2/http.html#create) a new resource a RESTful **POST** operation with a request body SHALL be utilised.   

```http
POST [base]/[resourcetype]
```

When the server creates a new resource and returns a `201` **Created** HTTP status code, it SHALL also return a `Location` header which contains the new `logical Id` and `version Id` of the created resource.

```http
Location: [base]/[type]/[id]/_history/[vid]
```

[id] and [vid] are the newly created `logical Id` and `version Id` for the resource version. An `ETag` header with the `version Id` and a `Last-Modified` header will also be included in the response.

### Available for Resources ###

| Capability   | Resource(s) |
| ------------ | ----------- |
| **Foundations**   | &nbsp; |
| **Access Record** | &nbsp; |
| **Appointments**  | `Appointment` |
| **Tasks**         | `Order` |

{% include important.html content="In workshop discussions with all Principal GP system vendors it has been agreed that record locking (inside the GP CIS) will not impact on the ability of clients to query the GP Connect APIs and obtain the latest saved patient data." %}

### Create Example: Book an appointment for a patient ###

Refer to [Book an appointment](appointments_use_case_book_an_appointment.html) for request and response body examples for a create request.

## Update Resource ##

To [update](https://www.hl7.org/fhir/DSTU2/http.html#update) an existing resource, a RESTful **PUT** operation with a request body SHALL be utilised.

```http
PUT [base]/[resourcetype]/[id]
```

The PUT operation will only be used to update existing resources, if the specified resource within the url does not exist on the provider system an error SHALL be returned.

| Capability       | Resource(s) | Field(s) |
| ------------ | ----------- | -------- |
| **Foundations**   | &nbsp; | &nbsp; |
| **Access Record** | &nbsp; | &nbsp; |
| **Appointments**  | `Appointment` | reason, description, comment |
| **Tasks**         | &nbsp; | &nbsp; |

### Update Example: Modify the appointment booking reason ###

Refer to [Amend an appointment](appointments_use_case_amend_an_appointment.html) for request and response body examples for a request which updates a resource using the PUT HTTP verb.

## Delete Resource ##

To [delete](https://www.hl7.org/fhir/DSTU2/http.html#delete) an existing resource, a RESTful **DELETE** operation with no request body SHALL be utilised.

```http
DELETE [base]/[resourcetype]/[id]
```

| Capability   | Resource(s) |
| ------------ | ----------- |
| **Foundations**   | &nbsp; |
| **Access Record** | &nbsp; |
| **Appointments**  | &nbsp; |
| **Tasks**         | &nbsp; |

{% include important.html content="GP Connect clients and servers are currently not expected to utilise the ability to delete a resource using the RESTful DELETE operation. However, this section is included as implementers should ensure that their implementation choices don't preclude the use of this HTTP verb in future release." %}

## Operations ##

[Operations](https://www.hl7.org/fhir/DSTU2/operations.html) are used (a) where the server needs to play an active role in formulating the content of the response, not merely return existing information, or (b) where the intended purpose is to cause side effects such as the modification of existing resources, or creation of new resources.

As outlined in the [Extend and Restricting the API](https://www.hl7.org/fhir/DSTU2/profiling.html#api) section of the FHIR&reg; standard, the NHS Digital has decided to prefix it's operation names with a short prefix (e.g. `gpc`) followed by a "." to reduce the likelihood of name conflicts.

## Search Resources ##

A simple [search](https://www.hl7.org/fhir/DSTU2/http.html#search) is executed by performing a `GET` request optionally accompanied by zero or more name-value URL encoded parameters:

```http
GET [base]/[resourcetype]?name=value&...
```

In order to enable searching by date/time range, servers SHALL support the following prefixes as defined in the base FHIR specification for date parameters: eq, gt, lt, ge, le.

To search for all the appointments for a patient that occurred over a 2 year period:

```http
GET [base]/Patient/1A6E1B1C-6340-4663-926C-9CD1306EAAF8/Appointment?start=ge2014-01-01&start=le2015-12-31
```

### Chained Parameters ###

Servers SHALL support searching by a [chained](https://www.hl7.org/fhir/DSTU2/search.html#2.1.1.4.13) `Patient` identifier parameter for references to `Patient` resources that conform to the `GP-Patient` profile (and therefore have an NHS Number identifier). For example:

```http
GET [base]/AllergyIntolerance?patient.identifier=http://fhir.nhs.net/Id/nhs-number|1234569876
```

{% include important.html content="GP Connect clients and servers are not expected to support arbitrary adhoc searching." %}

### Search Example: Search for a patient resource by business Id ###

#### Request ####

```http
GET [base]/Patient?identifier=http://fhir.nhs.net/Id/nhs-number|9900002831 
```

If a Patient resource for NHS number 9900002831 exists then the server SHALL return a Bundle containing all Patient resources with the specified NHS number identifier.

#### Response ####

```xml
<Bundle>
	<Patient xmlns="http://hl7.org/fhir">
		<id value="1A6E1B1C-6340-4663-926C-9CD1306EAAF8" />
		<meta>
			<profile value="http://fhir.nhs.net/StructureDefinition/gpconnect-patient-1" />
		</meta>
		<identifier>
			<system value="http://fhir.nhs.net/Id/nhs-number" />
			<value value="9900002831" />
		</identifier>
		<name>
			<use value="usual" />
			<family value="Smith" />
			<given value="Mike" />
			<prefix value="Mr" />
		</name>
		<birthDate value="1977-01-09" />
		<address>
			<use value="home" />
			<type value="both" />
			<line value="2, The Green" />
			<city value="Harrogate" />
			<district value="Yorkshire" />
			<postalCode value="HG1 4AF" />
		</address>
	</Patient>
</Bundle>
```

### Advanced Search ###

Servers SHALL implement the [_query](https://www.hl7.org/fhir/DSTU2/search.html#query) search parameter to enable custom named search profiles to be defined and used which describe a specific query operation.

```http
GET [base]/[resourcetype]?_query=[query_name]&name=value&...
```

Servers SHOULD implement the standard search equivalent of the advanced custom named search for queries defined under the GP Connect FoT.

#### Request ####

```http
GET [base]/Schedule?_query=getschedule&date=ge2016-05-12&date=le2016-05-26
```

#### Response ####

```xml
<Bundle xmlns="http://hl7.org/fhir">
	 <type value="searchset"/>
</Bundle>
```

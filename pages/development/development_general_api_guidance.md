---
title: General API guidance
keywords: fhir development
tags: [fhir,development]
sidebar: overview_sidebar
permalink: development_general_api_guidance.html
summary: "Implementation guidance for developers - focusing on general API implementation guidance"
---

## Purpose ##

This document is intended for use by software developers looking to build a conformant GP Connect API interface utilising the FHIR&reg; standard, with a focus on general API implementation guidance.

### Notational conventions ###

The keywords "**MUST**", "**MUST NOT**", "**REQUIRED**", "**SHALL**", "**SHALL NOT**", "**SHOULD**", "**SHOULD NOT**", "**RECOMMENDED**", "**MAY**", and "**OPTIONAL**" in this document are to be interpreted as described in [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt).



## Maturity roadmap ##

At a high-level the maturity roadmap of a compliant Principal GP system is expected to follow the following FHIR and business capability maturity stages.

Refer to [Design - Design Principles - Maturity Model](designprinciples_maturity_model.html) for full details.

## Internet standards ##

Clients and servers SHALL be conformant to the following Internet Engineering Task Force (IETF) Request for Comments (RFCs) which are the principal technical standards that underpin the design and development of the internet and thus FHIR's APIs.

- Transport level integration SHALL be via HTTP as defined in the following RFCs: [RFC 7230](https://tools.ietf.org/html/rfc7230), [RFC 7231](https://tools.ietf.org/html/rfc7231), [RFC 7232](https://tools.ietf.org/html/rfc7232), [RFC 7233](https://tools.ietf.org/html/rfc7233), [RFC 7234](https://tools.ietf.org/html/rfc7234) and [RFC 7235](https://tools.ietf.org/html/rfc7235).
- Transport level security SHALL be via TLS/HTTPS as defined in [RFC 5246](https://tools.ietf.org/html/rfc5246) and [RFC 6176](https://tools.ietf.org/html/rfc6176).
- HTTP Strict Transport Security (HSTS) as defined in [RFC 6797](https://tools.ietf.org/html/rfc6797) SHALL be employed to protect against protocol downgrade attacks and cookie hijacking.

{% include roadmap.html content="The NHS Digital is currently evaluating how [Cross-Origin Resource Sharing](http://www.w3.org/TR/cors/) (CORS) will be handled for web and mobile based applications." %}

## Endpoint resolution ##

Clients SHALL perform a sequence of query operations against existing Spine services to enable FHIR endpoint resolution.

1. Clients SHALL perform (or have previously performed) a PDS lookup for a patient.
	1. Using the PDS results the client SHALL determine the patient's primary GP organisation. 
2. Clients SHALL perform (or have previously performed) a SDS lookup using the ODS code of the patient's primary GP organisation.
	1. Using the SDS results the client SHALL determine the Principal GP system responsible for hosting the most up to date GP care record.
		1. [EMIS Health](http://www.emishealth.com/)
		2. [INPS](http://www.inps.co.uk/)
		3. [Micotest](http://www.microtest.co.uk/)
		4. [TPP](http://www.tpp-uk.com/)
2. Clients SHALL construct a [FHIR Service Root URL](#ServiceRootURL) suitable for access to a GP vendor's FHIR server. For GP Connect access to the Principal GP systems will be via the [Spine Security Proxy](#SpineSecurityProxy) and as such the URL will need to be pre-pended with a Proxy Service Root URL.

{% include tip.html content="Where a practitioner (with a valid SDS User ID) or organisation (with a valid ODS Code) record already exists with-in the local system the details associated with these existing records may be used for display purposes." %}


## RESTful API ##

The [RESTful API](https://www.hl7.org/fhir/DSTU2/http.html) described in the FHIR&reg; standard is built on top of the Hypertext Transfer Protocol (HTTP) with the same HTTP verbs (`GET`, `POST`, `PUT`, `DELETE`, etc.) commonly used by web browsers. Furthermore, FHIR exposes resources (and operations) as Uniform Resource Identifiers (URIs). For example, a `Patient` resource `/fhir/Patient/1`, can be operated upon using standard HTTP verbs such as `DELETE /fhir/Patient/1` to remove the patient record.

The FHIR RESTful API style guide defines the following URL conventions which are used throughout the remainder of this document:

- URL pattern content surrounded by **[ ]** are mandatory.
- URL pattern content surrounded by **{ }** are optional.

### Service root URL ###

The [Service root URL](https://www.hl7.org/fhir/DSTU2/http.html#general) is the address where all of the resources defined by this interface are found. 

The Service root URL is the `[base]` portion of all FHIR APIs.

{% include important.html content="All URLs (and ids that form part of the URL) defined by this specification are case sensitive." %}

### FHIR API Versioning ###
FHIR APIs SHALL be versioned according to  [Semantic Versioning](http://semver.org/spec/v2.0.0.html) in the server's Service Root URL to provide a clear distinction between API versions that are incompatible (i.e. contain breaking changes) vs. backwards-compatible (i.e. contain no breaking changes).

Provider systems are required to publish Service Root URLs for major versions of FHIR APIs in the Spine Directory Service in the following format:

{% include callout.html content="`https://[FQDN of FHIR Server]/[ODS Code]/[FHIR_VERSION_NAME]/{PROVIDER_MAJOR_VERSION}`" %}


- `[FQDN of FHIR Server]` is the fully qualified domain name where traffic will be routed to the logical FHIR server for the organisation in question

- `[ODS Code]` is the [Organisation Data Service](https://digital.nhs.uk/organisation-data-service) code which uniquely identifies the GP Practice organisation

- `[FHIR_VERSION_NAME]` refers to the textual name identifying the major FHIR version, examples being `DSTU2` and `STU3`. The FHIR Version name SHALL be specified in UPPERCASE characters.

- `{PROVIDER_VERSION}` identifies the major version number of the provider API. Where the provider version number is omitted, the major version SHALL be assumed to be 1.
  
- The FHIR Base URL SHALL NOT contain a trailing `/`

#### Example server root URL

The provider will publish the server root URL to Spine Directory Services as follows:

`https://provider.nhs.uk/GP0001/DSTU2/2`

Consumer systems are required to construct a [Service Root URL containing the SSP URL followed by the FHIR Server Root URL of the logical practice FHIR server](integration_spine_security_proxy.html#proxied-fhir-requests) that is suitable for interacting with the SSP service. API provider systems will be unaware of the SSP URL prefix as this will be removed prior to calling the provider API endpoint.

The consumer system would therefore issue a request to the new version of the provider FHIR API  to the following URL:

`https://[ssp_fqdn]/https://provider.nhs.uk/GP0001/STU3/2`


### Resource URL ###

The [Resource URL](http://www.hl7.org/implement/standards/fhir/http.html#2.1.0) will be in the following format:

	VERB [base]/[type]/[id] {?_format=[mime-type]}

Clients and servers constructing URLs SHALL conform to [RFC 3986 Section 6 Appendix A](https://tools.ietf.org/html/rfc3986#appendix-A) which requires percent-encoding for a number of characters that occasionally appear in the URLs (mainly in search parameters).

### HTTP verbs(http://hl7.org/fhir/valueset-http-verb.html ###

The following [HTTP verbs](http://hl7.org/fhir/valueset-http-verb.html) SHALL be supported to allow RESTful API interactions with the various FHIR resources:

- **GET**
- **POST**
- **PUT**
- **DELETE**

{% include tip.html content="Please see later sections for which HTTP verbs are expected to be available for which FHIR resources." %}

<p/>

{% include roadmap.html content="In a future version of the FHIR&reg; standard it is expected that the **PATCH** verb will also be supported." %}

#### Resource types ####

GP Connect provider systems SHALL support FHIR [resource types](http://hl7.org/fhir/resourcelist.html) as profiled within the [GP Connect FHIR Resource Definitions](http://developer.nhs.uk/downloads-data/fhir-resource-definitions-library/). 

#### Resource ID ####

This is the [logical Id](http://hl7.org/fhir/resource.html#id) of the resource which is assigned by the server responsible for storing it. The logical identity is unique within the space of all resources of the same type on the same server, is case sensitive and can be up to 64 characters long.

Once assigned, the identity SHALL never change. `logical Ids` are always opaque, and external systems need not and should not attempt to determine their internal structure.

{% include important.html content="As stated above and in the FHIR&reg; standard, `logical Ids` are opaque and other systems should not attempt to determine their structure (or rely on this structure for performing interactions). Furthermore, as they are assigned by each server responsible for storing a resource they are usually implementation specific. For, example: NoSQL document stores typically preferring a GUID key (e.g. 0b28be67-dfce-4bb3-a6df-0d0c7b5ab4) whilst Relational Database stores typically preferring a integer key (e.g. 2345)." %} 

For further background, refer to principles of [resource identity as described in the FHIR standard](http://www.hl7.org/implement/standards/fhir/dstu2/resource.html#id)  

#### External resource resolution ####

Inline with work being undertaken in other jurisdictions (see the [Argonaut Implementation Guide](http://argonautwiki.hl7.org/index.php?title=Implementation_Guide) for details) GP Connect provider systems are not expected to resolve full URLs that are external to their environment.

### Content types ###

Servers SHALL support both formal [MIME-types](https://www.hl7.org/fhir/DSTU2/http.html#mime-type) for FHIR resources:

- XML: `application/xml+fhir`
- JSON: `application/json+fhir`

Servers SHALL support the optional `_format` parameter in order to allow the client to specify the response format by its MIME-type. If both are present, the `_format` parameter overrides the `Accept` header value in the request.

Servers SHALL prefer the encoding specified by the `Content-Type` header if no explicit `Accept` header has been provided by a client application.

### Wire format representations ###

Servers SHALL support two [wire formats](https://www.hl7.org/fhir/DSTU2/formats.html#wire) as ways to represent resources when they are exchanged:

- [XML](https://www.hl7.org/fhir/DSTU2/xml.html)
- [JSON](https://www.hl7.org/fhir/DSTU2/json.html)

{% include important.html content="The FHIR standard outlines specific rules for formatting XML and JSON on the wire. It is important to read and understand in full the differences between how XML and JSON are required to be represented." %}

Consumers SHALL ignore unknown extensions and elements in order to foster [forwards compatibility](https://www.hl7.org/fhir/DSTU2/compatibility.html#1.10.3) and declare this by setting [Conformance.acceptUnknown](https://www.hl7.org/fhir/DSTU2/conformance-definitions.html#Conformance.acceptUnknown) to 'both' in their conformance profile.

Systems SHALL declare which format(s) they support in their Conformance Statement. If a server receives a request for a format that it does not support it SHALL return a http status code of `415` indicating an `Unsupported Media Type`.

### Transfer encoding ###

Clients and servers SHALL support the HTTP [Transfer-Encoding](https://www.hl7.org/fhir/DSTU2/http.html#mime-type) header with a value of `chunked`. This indicates that the body of a HTTP response will returned as an unspecified number of data chunks (without an explicit `Content-Length` header).

### Character encoding ###

Clients and servers SHALL support the `UTF-8` [character encoding](https://www.hl7.org/fhir/DSTU2/http.html#mime-type) as outlined in the FHIR standard.

> FHIR uses `UTF-8` for all request and response bodies. Since the HTTP specification (section 3.7.1) defines a default character encoding of `ISO-8859-1`, requests and responses SHALL explicitly set the character encoding to `UTF-8` using the `charset` parameter of the MIME-type in the `Content-Type` header. Requests MAY also specify this charset parameter in the `Accept` header and/or use the `Accept-Charset` header.

### Content compression ###

To improve system performances clients/servers SHALL support GZIP compression.

Compression is requested by setting the `Accept-Encoding` header to `gzip`.

{% include tip.html content="Applying content compression is key to reducing bandwidth needs and improving battery life for mobile devices." %} 

### [Inter-version compatibility](https://www.hl7.org/fhir/DSTU2/compatibility.html) ###

Unrecognized search criteria SHALL always be ignored. As search criteria supported in a query are echoed back as part of the search response there is no risk in ignoring unexpected search criteria.

### HTTP headers ###

#### Proxying headers ####

Additional HTTP headers SHALL be added into the HTTP request/response for the purpose of allowing the proxy system to disclose information lost in the proxying process (e.g. the originating IP address of a request). Typically, this information is added to proxy forwarding headers as defined in [RFC 7239](http://tools.ietf.org/html/rfc7239).

#### Cross organisation provenance and audit headers ####

In order to meet auditing and provenance requirements (which are expected to be closely aligned with the IM1 requirements), clients SHALL provide an oAuth 2.0 Bearer token in the HTTP Authorization header (as outlined in [RFC 6749](http://tools.ietf.org/html/rfc6749)) in the form of a JSON Web Token (JWT) as defined in [RFC 7519](http://tools.ietf.org/html/rfc7519).

{% include tip.html content="We are using an open standard (i.e. JWT) to provide a container for the provenance and audit data for ease of transport between the consumer and provider systems. It is important to note that these tokens (for GP Connect FoT) will **not** be centrally issued and are not signed or encrypted (i.e. are constructed of plain-text). There are JWT libraries available for most programming languages simplify generation of this data in JWT format." %}

Refer to [Integration - Cross Organisation Audit & Provenance](integration_cross_organisation_audit_and_provenance) for full details of the JWT claims that SHALL be used for passing audit and provenance details between systems.

{% include important.html content="We have defined a small number of additional headers which are also required to be included in NHS Digital defined custom headers." %}

Clients SHALL add the following Spine proxy headers for audit and security purposes:

- `Ssp-TraceID` - TraceID (generated per request) which identifiers the sender's message/interaction (i.e. a GUID/UUID).
- `Ssp-From` - ASID which identifies the sender's FHIR endpoint.
- `Ssp-To` - ASID which identifies the recipient's FHIR endpoint.
- `Ssp-InteractionID` - identifies the FHIR interaction that is being performed.<sup>1</sup>

<sup>1</sup> please refer to the [Development - FHIR API Guidance - Operation Guidance](development_fhir_operation_guidance.html) for full details.

The Spine Security Proxy (SSP) SHALL perform the following checks to authenticate client request:

- Get the CN from the TLS session and compare the host name to the declared endpoint.
- Check that the client/sending endpoint has been registered (and accredited) to initiate the given interaction.
- Check that the server/receiving endpoint has been registered (and accredited) to receive/process the given interaction.   

#### Caching headers ####

Providers SHALL use the following HTTP Header to ensure that no intermediaries cache responses: `Cache-Control: no-store`


### [Managing Return Content](https://www.hl7.org/fhir/DSTU2/http.html#return) ###

Provider SHALL maintain resource state inline with the underlying system, including the state of any associated resources.

For example: 

_If the practitioner associated with a schedule is changed on the providers system, such as when a Locum is standing in for a regular doctor, this should be reflected in all associated resources to that schedule. The diagram below shows the expected change to the appointment resources for this scenario._

_When the appointment is booked, the appointment resource is associated with a slot resource and references the practitioner resource associated with the schedule in which the slot resides. If the schedule is then updated within the provider system to reflect the change of practitioner from the original doctor to a Locum doctor then the practitioner reference with the schedule will be updated. If a consumer then performs a read of the appointment the returned appointment resource should reflected the updated practitioner on the schedule._

![Diagram of reflection of state](images/development/Reseource Reflection of state.png)

Severs SHALL default to the `return=representation` behaviour (i.e. returning the entire resource) for interactions that create or update resources.

Servers SHOULD honour a `return=minimal` or `return=representation` preference indicated in the `Prefer` request header, if present.

### Managing Resource Contention ###

To facilitate the management of [resource contention](http://hl7.org/fhir/http.html#concurrency), Servers SHALL always return an `ETag` header with each resource including the resources `versionId`:

```http
HTTP 200 OK
Date: Sat, 09 Feb 2013 16:09:50 GMT
Last-Modified: Sat, 02 Feb 2013 12:02:47 GMT
ETag: W/"23"
Content-Type: application/json+fhir
```

`ETag` headers which denote resource `version Id`s SHALL be prefixed with `W/` and enclosed in quotes, for example:

```http
ETag: W/"3141"
```

Clients SHALL submit update requests with an `If-Match` header that quotes the `ETag` from the server.

```http
PUT /Patient/347 HTTP/1.1
If-Match: W/"23"
```

If the `version Id` given in the `If-Match` header does not match, the server returns a `409` **Conflict** status code instead of updating the resource.

For server's that don't persist historical versions of a resource (i.e. any resource other than the currently available/latest version) then they SHALL operate in-line with the guidance provided in the following [Hay on FHIR - FHIR versioning with a non-version capable back-end](https://fhirblog.com/2013/11/21/fhir-versioning-with-a-non-version-capable-back-end/) blog post. This is to ensure that GP Connect servers will be compatible with version-aware clients, even though the server itself doesn't support the retrieval of historical versions.

### Managing Return Errors ###

In order to [manage return errors](http://hl7.org/fhir/http.html#2.1.0.4), FHIR defines an [OperationOutcome](http://hl7.org/fhir/operationoutcome.html) resource that can be used to convey specific detailed processable error information. An `OperationOutcome` may be returned with any HTTP `4xx` or `5xx` response, but is not always required.



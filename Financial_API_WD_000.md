<div align='right'>
<p style='font-weight: bold'>OIDF Financial API WG N <span style='font-size:150%'>WGSXXX</span></p>
<p>
Date: 2016-07-04
</p>
<p style='font-weight: bold'>ISO/WD1-1</p>
<p>OIDF FAPI WG</p>
</div>



#<p align='center'>Financial Services – Financial API </p>

<div style='width:100%; border:2px solid #000000; color: #0000FF'>
<p style='text-align:center'><span style='font-weight:bold'> Warning </span>
</p>

<p>
This document is not an OIDF International Standard. It is distributed for review and comment. It is subject to change without notice and may not be referred to as an International Standard.
</p>

<p>
Recipients of this draft are invited to submit, with their comments, notification of any relevant patent rights of which they are aware and to provide supporting documentation.
</p>

</div>


<br/>
<br/>

<div style='width:100%; border:2px solid #0000FF; color: #0000FF'>
<p style='text-align:center'><span style='font-weight:bold'> Copyright notice </span>
</p>

<p>
This document is not an OIDF International Standard. It is distributed for review and comment. It is subject to change without notice and may not be referred to as an International Standard.
</p>

<p>
The technology described in this specification was made available from contributions from various sources, including members of the OpenID Foundation and others. Although the OpenID Foundation has taken steps to help ensure that the technology is available for distribution, it takes no position regarding the validity or scope of any intellectual property or other rights that might be claimed to pertain to the implementation or use of the technology described in this specification or the extent to which any license under such rights might or might not be available; neither does it represent that it has made any independent effort to identify any such rights. The OpenID Foundation and the contributors to this specification make no (and hereby expressly disclaim any) warranties (express, implied, or otherwise), including implied warranties of merchantability, non-infringement, fitness for a particular purpose, or title, related to this specification, and the entire risk as to implementing this specification is assumed by the implementer. The OpenID Intellectual Property Rights policy requires contributors to offer a patent promise not to assert certain patent claims against other contributors and against implementers. The OpenID Foundation invites any interested party to bring to its attention any copyrights, patents, patent applications, or other proprietary rights that may cover technology that may be required to practice this specification.
</p>

</div>



##Foreword

OIDF (OpenID Foundation) is an international standardizing body comprised by over 160 participating entities (working group participants). The work of preparing international standards is carried out through OIDF working groups according to OpenID Process. Each participants interested in a subject for which a working group has been established has the right to be represented on that working group. International organizations, governmental and non-governmental, in liaison with OIDF, also take part in the work. OIDF collaborates closely with other standardizing bodies in the related fields.

International standards are drafted in accordance with the rules given in the OpenID Process.

The main task of working group is to prepare Implementers Draft and Final Draft. Final Draft adopted by the Working Group through consensus are circulated to the OIDF members for voting. Publication as an OIDF Standard requires approval by at least 50 % of the member bodies casting a vote.

<span style='color:red'>
Attention is drawn to the possibility that some of the elements of this document may be the subject of patent rights. OIDF shall not be held responsible for identifying any or all such patent rights.
</span>

 Financial API consists of the following parts, under the general title Financial Services — Financial API:


##Introduction
**// TODO**

* Write Introduction



#**Financial Services – Financial API**

## Scope
**// TODO**

* Write Scope

## Normative references
The following referenced documents are indispensable for the application of this document. For dated references, only the edition cited applied. For undated references, the latest edition of the referenced document (including any amendments) applies.

RFC 6749 - The OAuth 2.0 Authorization Framework

RFC 7636 - Proof Key for Code Exchange by OAuth Public Clients

RFC 5246 - The Transport Layer Security (TLS) Protocol Version 1.2

RFC 6125 - Representation and Verification of Domain-Based Application Service Identity within Internet Public Key Infrastructure Using X.509 (PKIX) Certificates in the Context of Transport Layer Security (TLS)

OpenID Connect Core 1.0 incorporating errata set 1

OpenID Connect Discovery 1.0 incorporating errata set 1

Open Financial Exchange 2.2
“HTML 4.01 Specification,” World Wide Web Consortium Recommendation REC-html401-19991224, December 1999



## Terms and definitions
For the purpose of this standard, the terms defined in RFC6749, RFC6750, RFC7636, OpenID Connect Core applies.


## Symbols and Abbreviated terms

API – Application Programming Interface

FI – Financial Institution

HTTP – Hyper Text Transfer Protocol

REST – Representational State Transfer

TLS – Transport Layer Security

## API Overview

### Message Transport
Financial API requires the use of HTTP, which a reliable synchronous stateless message protocol. REST is preferred because it decouples the message syntax from the transport concerns. REST supports content type negotiation, conditional fetches, and compression.

Since confidential information is being exchanged, all interactions must be encrypted with TLS/SSL (HTTPS). At the time of this writing, TLS version 1.2 is the most recent version, but has very limited actual deployment, and might not be readily available in implementation toolkits. TLS version 1.0 is the most widely deployed version, and will give the broadest interoperability.

To protect against information disclosure and tampering, confidentiality protection MUST be applied using TLS with a cipher suite that provides confidentiality and integrity protection.

Whenever TLS is used, a TLS server certificate check MUST be performed, per RFC 6125.


##	Service Delivery Expectations
The Financial API server response to requests must start within 30 seconds. The server may use HTTP 100 continue or 200 chunked encoding response to extend the response time for large data sets. Server responses should not last longer than 120 seconds to prevent long running transactions.

##	Message Syntax

Financial API supports the JSON syntax.

##	Security
### Model
Financial API client obtains an OAuth access token are covered in Section 3.1.3 of OpenID Connect Core 1.0. The Financial API client must have the following information to successfully interact with a Financial API server:

1. OpenID Connect authorization server
    1. Authorization endpoint, e.g. https://oauth.example.com/authorization
    2. Client identifier, e.g. intuit.com
    3. Requested scope ("accounts", "customer", "images", "transfer", "transactions")
    4. Allowed redirection client URI, e.g. https://oauth.intuit.com/client
    5. Token endpoint, e.g. https://oauth.example.com/authorization
    6. Client authorization (JWT or shared secret)
    7. Optional client authentication certificate
    8. Authorization Server Certifying Authority public key chain
2. Financial API Service (OAuth resource server)
    1. Endpoint, e.g. https://data.example.com
    2. client authorization (Bearer or MAC token)
    3. (Optional) client authentication certificate. Use mutual authentication for access by the client agents in addition to the refresh or access token.
    4. Resource Server Certifying Authority public key chain. Client will need to make sure server SSL certificate CA is in their trust store.

The FI’s Authorization Server MUST support Authorization Code Grant OAuth as defined in section 4.1 of RFC6749 with PKCE [RFC7636] extension, and highly RECOMMENDED to support “code id_token“ response type as defined in OAuth 2.0 Multiple Response Type Encoding Practices.

Authorization Server SHOULD publish this information through a discovery file defined in clause 4 of OpenID Connect Discovery.


### Client Authentication
The recommended approach to securely communicate between an Aggregator and FI is through use of both network transport mutual authentication and message security as defined by the use of the OAuth 2.0 Authorization Code Grant and Bearer Token model. Alternative supported methods are outlined below.

Network transport mutual authentication will consist of a two way TLS/SSL network connection used for all web service calls made between the Aggregator and FI for both OAuth token acquisition and data aggregation operations. The X.509 certificates must be issued and validated by an authorized certificate authority. This will provide data origin authentication, data integrity, and data confidentiality between the Aggregator and FI.

The Aggregator system must be capable of maintaining the confidentiality of their credentials for each FI (e.g. client implemented on a secure server with restricted access to the client credentials).

X = Recommended x = Alternatives supported

//TODO

// Table 6.2

| Network Transport TLS/SSL                                             | Client Authentication              |       Token Type      |
|-----------------------------------------------------------------------|------------------------------------|:---------------------:|
|   Server Side               Mutual Authentication      Authentication | Shared Secret         JWT          |   Bearer         Mac  |
|                                        X                              |          X                         |    X                  |
|           X                                                           |          X                         |    X                  |
|           X                                                           |                                 X  |                    X  |

Server Side Authentication – Only the Server authenticates itself, assuring its identity to the client across the network transport. Mutual Authentication – Enables both Client and Server to authenticate to each other, assuring each other's identity across the network transport.

In line with FFIEC (Federal Financial Institutions Examination Council) guidance on Authentication to mitigate security risks.

When invoking the FI's Financial API data service, the Financial API client will provide an Authorization header with the access tokens encoded per the agreed encoding.

Clients that support multiple Authorization Servers MUST use a unique redirect_uri for each Authorization Server.  This will mitigate security issues where there is a mix-up of the issuer of the ID Token and Authorization Server.

Clients running on “mobile devices” MUST send PKCE enabled authorization and token endpoint requests as described in section 4 of RFC 7636.


### Token Scope
The Financial API allows access to the user's private financial information while the user is offline. To obtain consent and authorization for an access token that  can be used while the user Is offline, the authorization request must contain the openid and offline_access values in the scope parameter. A refresh token will be returned in the authorization response that can be exchanged for an access token as described in Section 12 of OpenID Connect Core 1.0.

The Financial API client application will include a list of desired scopes when requesting an authorization token. The following scopes are defined for Financial API data service.

| Primary Entity | Allowed Actions                                              | Token Scope          |
|----------------|--------------------------------------------------------------|----------------------|
| Account        | Read only Access to summary account information              | FinancialInformation |
| Customer       | Read only Access to customer information, including PII      | FinancialInformation |
| Image          | Read only Access to transaction images (checks and receipts) | FinancialInformation |
| Statement      | Read only Access to statement image                          | FinancialInformation |
| Transfer       | Transfer of money between accounts                           | Transfer             |
| Transaction    | Read only Access to transaction information                  | FinancialInformation |

The Financial API server will return the list of allowed scopes with the issued authorization token.

The Financial API server may limit the scopes for the purpose of not implementing certain APIs.

The Financial API server may also present scopes in the access confirmation page after end user login to have them determine each account(s) access for the requesting application.

// TODO

// Figure 6.3

## Logical Data Model
Financial API will eventually encompass multiple financial data domains. At this point, entities and messages are required to support the aggregation of personal financial data. The logical data model consists of User, Login, Account, Transaction, Detail, Statement and Image entities.

// TODO
Figure 7

### Entity Identifier
The User entity is not expressed in Financial API messages. The User entity is available from the ID Token obtained as part of the authorization or token endpoint response. The Login entity has an identifier unique to its owning Institution. The Login identifier is usually the username part of a username / password login. The Login surrogate identifier is the OAuth token obtained from the Financial Institution. The Account entity has an identifier that is unique to the owning Institution. The Transaction entity has an identifier that is unique to the owning Account and is usually unique to the owning Institution.

The entity identifier (or surrogate identifier) is required when transmitting the entities and is used to relate the entities. Financial API identifier properties have a maximum of 32 characters. (IBAN account identifiers are 31 characters, ACH has 9 digits for routing and 17 digits for account number, OFX 2.0 allowed &lt;FITID&gt; with up to 255 characters but recommended 32 or fewer).

### Surrogate Identifier
OAuth creates a surrogate identifier for a Login – a Financial API server does not expose the financial institution’s principal identifier of the Login. To limit the exposure of personally identifiable information, the other identities transmitted by the Financial API server should be a surrogate identifier. Surrogates must provide the same uniqueness constraints on the entity relationships as described above. Any surrogate identities must be long term persistent.

## Residual Data
Residual data is defined as data that is no longer being used, for example if an account has been closed. Aggregators should delete residual data from their systems within 180 days.

## Protocol
The Financial API client requests data using HTTP GET and POST methods. The request includes an appropriate Request-URI. Requests must include an OAuth token in the authorization header. The following is a sample of the headers provided in a typical request.

    GET /accounts HTTP/1.1
    Host: example.com
    Authorization: Bearer w0mcJylzCn-AfvuGdqkty2-KP48=
    Accept: application/json
    Accept-Charset: UTF-8
    Accept-Encoding: gzip

### Message Serializations
Messages are serialized using one of the following methods:

1.	Query String Serialization
2.	Form Serialization
3.	JSON Serialization

#### Query String Serialization
In order to serialize the parameters using the Query String Serialization, the client constructs the string by adding the parameters and values to the query component of a URL using the application/x-www-form-urlencoded format as defined by the HTML 4.01 specification. Query String Serialization is typically used in HTTP GET requests. The same serialization method is also used when adding parameters to the fragment component of a URL.

#### Form Serialization
Parameters and their values are Form Serialized by adding the parameter names and values to the entity body of the HTTP request using the application/x-www-form-urlencoded format as defined by the HTML 4.01 specification. Form Serialization is typically used in HTTP POST requests.

#### JSON Serialization
The parameters are serialized into a JSON object structure by adding each parameter at the highest structure level. Parameter names and string values are represented as JSON strings. Numerical values are represented as JSON numbers. Boolean values are represented as JSON booleans. Omitted parameters and parameters with no value SHOULD be omitted from the object and not represented by a JSON null value, unless otherwise specified. A parameter MAY have a JSON object or a JSON array as its value.

### Headers

#### Transport Security
All Financial API communication must be secured from network sniffing with SSL/TLS. Using TLS will secure the entire request and response including any headers. We recommend that both the Financial API client and server use certificates. Additionally, Financial API server responses should include Cache-Control headers to prevent any caching or storing of the response.

    Cache-Control: no-cache, no-store


#### Request Authorization
The Financial API client does not identify a User to the Financial API server. Instead, the User’s financial institution Login is implied via an OAuth token. The data returned by any Financial API request is limited to what the User could see using his/her Login and further limited by the scope of the OAuth token.

The Financial API client uses the Authorization request header with a Bearer or MAC token. Bearer tokens are recommended although the server has option to issue MAC tokens as an alternative if the client supports it. How to obtain this token was detailed in the Security Model section.

    Authorization: Bearer w0mcJylzCn-AfvuGdqkty2-KP48=


#### Content Negotiation
Financial API clients and servers use standard HTTP headers to negotiate transport options.
The Financial API client uses the Accept request header to ask for its preferred syntax. The server must respond with one of the requested syntaxes or with a 406 status code. The following formats are defined by this document:

* application/json

The application/json format is returned by default.

    Accept: application/json, application/xml;q=0.5

The Financial API client uses the Accept-Charset request header to ask for its preferred character set. The server must respond with the body encoded in one of the requested character sets or with a 406 status code.

    Accept-Charset: UTF-8

The Financial API server uses the Content-Type response header to inform the client of the response syntax and charset.

    Content-Type: application/json; charset=UTF-8

The Financial API client uses the Accept-Encoding request header to ask for its preferred compression encoding. The server must either respond with the body compressed with one of the requested compressions, or with the body not compressed.

    Accept-Encoding: compress, gzip

The Financial API server uses the Content-Encoding response header to inform the client of the response encoding.

    Content-Encoding: gzip

For queries, the Financial API client may use the If-Modified-Since request header to ask for a data response only if the data has been modified since the given date. If the server supports this header and the data has not been modified, a 304 HTTP response code will be returned to the client.

    If-Modified-Since: Wed, 12 Sep 2012 06:00:00 GMT

#### Server Environment
The Financial API server returns a Date header with every response.

    Date: Tue, 11 Sep 2012 19:43:31 GMT

#### Host
The Host request header field specifies the Internet host and port number of the resource being requested. A Host header without any trailing port information implies the default port for the service requested (e.g. "80" for an HTTP URL).

    Host: example.com

#### Client Identifier
The Financial API client supplies a User-Agent header with every request. This header should not be used to change the content of the response. This header is designed to only collect statistics on the products using the Financial API data service. The first token is the aggregator and aggregator version. The second token is the product and product version.

    User-Agent: Intuit/1.2.3 Mint/4.3.1

#### Customer Identifier
The Financial API client can optionally supply a customer identifier with request header DDA-CustomerId. This value identifies the user for whom the OAuth 2.0 token was issued. The value of DDA-CustomerId must be the same as the the user_id parameter returned by the OAuth 2.0 response and the value of the CustomerId field in the Customer Entity (if the Financial API server implements the customer operations).

    DDA-CustomerId: a237cb74-61c9-4319-9fc5-ff5812778d6b

#### Customer Last Login Time
The Financial API client can optionally supply the last time the customer logged into the aggregator product if this data is available.

    DDA-CustomerLastLoggedTime: Tue, 11 Sep 2012 19:43:31 GMT

#### Customer IP Address
The Financial API client optionally can supply the customer’s IP address if this data is available or applicable.

    DDA-CustomerIPAddress: 0.0.0.0

9.2.10	Interaction Tracking
The Financial API client may send the DDA-InteractionId request header to the server to help correlate log entries between client and server. Example:

    DDA-InteractionId: c770aef3-6784-41f7-8e0e-ff5f97bddb3a


The Financial API server must include the value of this header in its log entries. The Financial API server must also send DDA-InteractionId as a response header with value equal to the value sent by the client, or a unique value generated by the server if the client did not send DDA-InteractionId.

#### Financial Institution Identification
If the Financial API service is provided by a service bureau which uses the same end point for multiple institutions, the Financial API client must provide a header the identifies the desired financial institution. The service bureau defines this value. For example, it OIDFften the financial institution’s routing number (RTN).

    DDA-FinancialId: 123456789

###	Errors
When Financial API servers are unable to fulfill a request, they should send Error Entity as the response payload along with an appropriate HTTP Status Code. Error messages should contain just enough information for an end user to understand what went wrong without compromising security.


| Error Code | Error Message                                       | HTTP Status Code |
|------------|-----------------------------------------------------|------------------|
| 601        | Customer not found                                  | 404              |
| 602        | Customer not authorized                             | 401              |
| 701        | Account not found                                   | 404              |
| 702        | Invalid start or end date                           | 400              |
| 703        | Invalid date range                                  | 400              |
| 901        | Source account not found                            | 404              |
| 902        | Source account closed                               | 404              |
| 903        | Source account not authorized for transfer          | 401              |
| 904        | Destination account not found                       | 404              |
| 905        | Destination account closed                          | 404              |
| 906        | Destination account not authorized for transfer     | 401              |
| 907        | Invalid amount                                      | 404              |
| 908        | Duplicate transfer request                          | 409              |
| 909        | Transfer not available due to end of day processing | 503              |
| 910        | Insufficient funds                                  | 400              |
| 911        | Transaction limit exceeded                          | 400              |
| 950        | Transfer not found                                  | 404              |

### Resources

When implementing Financial API, the client and server maintainers must agree on the data service endpoint. All resource URIs may be prefixed by a base URI, for example https://example.com/dda/1.0. The base URI should include the version of Financial API that the server implements.

For security reasons identifiers should not be part of the URI and should be part of the HTTP body to prevent any inadvertent information disclosure in server audit logs. For this reason some requests are implemented as POST rather than GET with parameters serialized using Form Serialization as described in Section 9.1.2. If using the HTTP GET method, the request parameters are serialized using URI Query String Serialization as described in Section 9.1.1.

### Resources Endpoint Discovery
This document defines a mechanism for discovering the various resources endpoints for requesting the user's financial data. Building upon the discovery mechanism described in OpenID Connect Discovery 1.0, this document defines the following parameters to the OpenID Discovery response:

| Parameter      | Type  | Description                                                                                |
|----------------|-------|--------------------------------------------------------------------------------------------|
| ofx (optional) | Array | JSON object containing a collection of the API resources endpoints and their URL locations |

The ofx parameter contains the following parameters:

| Parameter                              | Type   | Description                                                                        |
|----------------------------------------|--------|------------------------------------------------------------------------------------|
| account_endpoint optional              | String | URL for getting account information                                                |
| statement_endpoint optional            | String | URL for retrieving a statement document                                            |
| statement\_list\_endpoint optional       | String | URL for getting list of statements                                                 |
| transaction\_document\_endpoint optional | String | URL for getting a transaction document                                             |
| transaction\_list\_endpoint optional     | String | URL for getting list of transactions                                               |
| account\_list\_endpoint optional         | String | URL for getting list of accounts                                                   |
| account\_details\_endpoint optional      | String | URL for getting account information (details & transactions) for the current token |
|  availability_endpoint optional        | String | URL for getting information about this API's availability                          |
| capability_endpoint optional           | String | URL for getting informtion about this API's capabilities                           |
| customer_endpoint optional             | String | URL for getting about the customer within the authorization scope                  |
| transfer_endpoint optional             | String | URL for creating a transfer between accounts                                       |
| transfer\_status\_endpoint optional      | String | URL for getting the status of a transfer between accounts                          |

# SSO

## Terminology

- JWT: JSON Web Token; starts `eyJ` and has 3 parts separated by `.`s (header, payload & signature)
- OAuth Authorization Server: the place where the resource owner already has an account
- OAuth Resource Owner: the user (because it's the user's data)
- OAuth Resource Server: the API or service that the client wants to use on behalf of the Resource Owner
- OAuth Scope: permissions, e.g. read item
- OAuth client: usually the app requesting access
- OAuth grant:
- OAuth: open standard dealing with access delegation
- OpenID: OpenID Connect; layer on top of OAuth; AD says use this + OAuth by preference for SSO
- OpenID:
- SAML ACS: Assertion Consumer Service; URL which IDP POST's to with SAML response upon successful login
- SAML Binding: maps
- SAML IDP: Identity Provider, e.g. Active Directory
- SAML Profile: how SAML assertions, protocols & bindings combine to support a use case
- SAML Response: base 64 encode XML containing user login details
- SAML SP: Service Providers (usually the application)
- SAML: Security Assertion Markup Language; open standard for exchanging
- SLO: Single Log Out; the converse of SSO
- SSO: Single Log On
- authn: authentication; validing a person is who they say they are (e.g. with a password)
- authz: authorisation; says what permissions a person has (e.g. read / write)
- bearer token: token allowing access; you don't need proof of ID to use this

## SAML

- Security Assertion Markup Language
- created in 2001
- version 2.0 was 2005
- open standard for ID federation
- provides authentication (authn) & authorisation (authz)
- XML based markup language
- pre-establish trust relationship between IDP & SP

### Parties

- User Agent: typically the browser
- IDP: knows users, passwords, attributes
- SP: the application the user wants to access

### Basic Flow

- user authenticates with IDP first
- IDP generates a SAML assertion
- IDP replies with SAML assertion
- user sends SAML assertion to SP
- SP checks the assertion and the session starts

### IDP / SP Configuration

done via xml metadata file installed on both IDP & SP, containing:

- certificate establishing trust
- which user attributes go in the SAML assertion
- SAML 2.0 bindings

### SAML Bindings

- HTTP POST (most common)
- HTTP Redirect (GET)
- SAML Artifact
  - IDP generates assertion AND an artifact
    - artifact contains a unique ID
  - the artifact is sent to User Agent (not the assertion)
  - the SP sends artifact to IDP
  - IDP sends assertion directly to SP
- others...

### The Assertion

- The IDP creates the assertion.
- IDP signs the assertion so that SP can validate/trust it

#### Assertion Contents

- attributes, e.g. email
  - IDP & SP are both configured to agree on which attributes are included
    - both also configure attribute formats
  - SP uses these attributes in the application, e.g. display user name
- method of authn
  - SP can decide whether to accept assertion based on level of authn used
  - different authn types are described by SAML context classes
    - PASSWORDPROTECTEDTRANSPORT is most common
- conditions
  - valid time
  - intended audience
- issuer id - identifiers which IDP generated the assertion

## OAuth

### OAuth 2.0 With Cognito Grant Flows

Grant types:

- authorisation code: Preferred method for users.
- implicit: User pools exposed to end user. Use authorisation code grant instead.
- client credentials: simpler, for machine-to-machine requests

Authorisation Code Grant Flow
TODO...

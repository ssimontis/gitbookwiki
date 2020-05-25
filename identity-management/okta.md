---
description: 'Workforce and Customer AuthX/Z, integrations'
---

# Okta

## API Access Management

* OAuth 2.0 implementation
* Integrates with OpenID Connect for authentication
* Client management API allows client apps to be set up and torn down
* Security of APIs
  * Custom Authorization Servers
  * Definition of scopes and claims
  * Policy and rule engines to govern access control

### Tokens

* OpenID Connect oversees ID Tokens for authentication
* OAuth 2.0 oversees Access Tokens
* JWT Specification allows inclusion of custom claims in  ID and access tokens
  * Reduces number of lookup calls required to identity provider

### OAuth Clients

* Client secrets are passwords
* Clients are configured to support grant types for specific use cases
* Tokens and authorization codes are sent to a redirect URI bound to the application's client ID
  * Redirect URI should be random to prevent replay attacks

### OAuth Servers

* Oversee issue of tokens and configuration of expiration times
* Manage scopes
* Manage authorization policies
* Audience claim \(aud\) identifies the server's scope
* Client ID claim \(cid\) identifies which tokens map to which APIs
* Unique issuer URI
* Unique signing key

#### Applications

* Access tokens and refresh tokens should be treated as protected secrets
  * Never store client-side or access from frontend code
* Application should never inspect contents of access token
  * Token is meant to be possessed, not read, by client
  * Backend is responsible for all token processing
  * Provide introspection endpoint if client needs metadata
* Use HTTP AuthZ header instead of URL or payload encoding
* Avoid using resource owner password grant type. Prefer instead
  * Authorization code
  * Implicit
  * Hybrid
* Mobile apps and SPAs should use authorization code grant type with PKCE
  * Hybrid or implicit grant type is second-best choice

### Authentication

#### Sign-In Widget

* Embeddable JS sign-in implementation
* Feature parity with Okta tenant sign-on page
* Password reset
* Forgotten password
* Strong authentication flow
* All features configured via policy; no coding required
* Social providers also supported by widget

#### Identity Providers

* Okta Universal Directory becomes single system of reference
  * Can capture profile attributes from IDP user and store attributes in Okta UD
  * If user updates profile at IDP level, changes reflected inside Okta next time they sign in again
  * Multiple IDPs can be linked to single Okta user

### Inline Hooks

* Outbound calls from Okta to custom code triggered at well-defined points in process flows
* Allow integration of custom functionality into flows
* Custom code must be deployed as Internet-accessible web service external to Okta
* Okta defines REST API contract for requests sent to custom code along with response data contracts expected
* Outbound call from Okta is hook, which is received by external service
* Synchronous call
  * Blocks flow until code returns
* Current hook types
  * Token Inline Hooks
    * Customize tokens returned by API Access Management
  * User Import Inline Hook
    * Customize logic run while importing users
  * SAML Assertion Inline Hook
    * Customize SAML assertions returned by Okta
  * Registration Inline Hook
    * Customize handling of registration requests in Self-Service Registration
  * Password Import Inline Hook
    * Verifies user-supplied password to allow users to migrate to Okta

### OAuth 2.0 Overview

* Protects resources such as applications or API services
* Delegates authorization
* Authorization server issues access tokens
  * Okta
* Resource owner grants permission to access resource server with an access token
  * Application end user
* Client application requests access token from Okta and passes to resource server
* Resource server accepts access token and verifies its validity
  * Your application
* Grant is authorization given to client by the user
* Access token is issued by auth server Okta in exchange for a grant
* Refresh token is optional token exchanged for new access token if previous token expires
* General grant flow
  * Client requests authorization from resource owner \(user\)
  * User gives authorization and client passes authorization grant to authorization server \(Okta\)
  * If grant is valid, authorization server returns access token 
    * Optional ID and refresh tokens
  * Client uses access token to access resource server
* Authorization server drives flows
  * Token minting engine
  * Unique issuer URI and own signing key

### OpenID Connect

* Authentication standard built on top of OAuth 2.0
* Adds ID Token
* OpenID provider is authorization server that issues ID token
  * Okta
* End user has information contained in ID token
* Relying party is client application requesting ID token from Okta
* ID token is issued by OpenID Provider and contains End User info in form of claims
* A claim is a piece of information about the End User

| Application Type | OAuth 2.0 Flow |
| :--- | :--- |
| Server-side | Authorization Code Flow |
| SPA | Authorization Code Flow with PKCE or Implicit Flow for legacy browsers |
| Native | Authorization Code Flow with PKCE |
| Trusted | Resource Owner Password Flow |
| Service | Client Credentials |

#### Redirect Flow

* User attempts to access a protected resource or sign in
* Application redirects browser to Okta-hosted sign-in page for user to authenticate
* Okta redirects browser back to specific application route 
  * Callback route
  * Redirect URI
  * Okta sends user info along with request
* Okta payload is processed and application may make additional requests to Okta to get more info about user
* Application redirects browser to user's final destination
  * User's original request for protected resource
  * Home page
  * Custom redirect

### Universal Directory

* User profile management and authentication/provisioning user base
* Attribute mapping between apps and users
* 31 base attributes for all users in an organization
* App user profiles represent user in 3rd party app, directory, or Identity Provider
  * Okta can R and W to 3rd party attributes in applications and directories
  * Okta can R 3rd party IDP
* Based upon Core Schema for SCIM


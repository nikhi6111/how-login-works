# OpenID Connect Login: Clean Diagram

Conceptual authorization-code flow for a confidential server-side web application, with PKCE. Microsoft Entra ID is the identity provider and Salesforce is the example relying party. All network messages use HTTPS.

```mermaid
sequenceDiagram
    participant RP as Salesforce (application server)
    participant B as User browser
    participant I as Microsoft Entra ID
    participant API as Microsoft Graph (optional API)
    Note over RP,I: Application registration, redirect URI and trusted issuer configured beforehand
    B->>RP: 1. Open application
    Note over RP: Save state, nonce and PKCE verifier for this login attempt
    RP->>B: 2. Redirect to authorization endpoint with client ID,<br/>redirect URI, scope openid, state, nonce and PKCE challenge
    B->>I: 3. Follow authorization redirect
    I->>B: Show sign-in and MFA prompts if needed
    B->>I: Authenticate to Entra only
    I->>B: 4. Redirect to registered callback with code and state
    B->>RP: 5. Follow callback carrying authorization code and state
    Note over RP: Validate state and correlate login attempt
    RP-->>I: 6. Back-channel POST to token endpoint:<br/>code, redirect URI, PKCE verifier and application authentication
    Note over I: Validate code, single use, expiry, application,<br/>redirect URI, PKCE and required client authentication
    I-->>RP: 7. Return ID token and access token directly
    Note over RP: Validate ID-token signature, issuer, audience,<br/>expiry, nonce and other required conditions
    Note over RP: Check application access and create local session
    RP->>B: 8. Set protected session cookie and return application page
    B->>RP: Later application requests carry session cookie
    opt Application needs an API and has appropriate permission
        RP->>API: Send access token intended for Microsoft Graph
        Note over API: Validate access token and enforce granted permissions
        API->>RP: Return authorized API response
    end
```

**Dashed arrows 6 and 7 identify the direct back-channel exchange.** They do not travel through the browser. Solid arrows show the other illustrated network requests and responses; the API call is also server-to-server.

- The authorization code is short-lived and single-use.
- The ID token supplies identity proof to its intended application.
- The access token is for its intended API; it is not the browser's application-session cookie.
- The application session is maintained separately by the session cookie.
- PKCE and client authentication protect different aspects of the code exchange.

This clean diagram was prepared with assistant help from the [original hand drawing](oidc-flow-handdrawn.png) and the corrections discussed during the lab. It illustrates a server-side web application flow, not every OIDC client architecture or a deployed Salesforce integration.

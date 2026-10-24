# SAML Login: Clean Diagram

Conceptual SP-initiated browser redirect/POST flow. Microsoft Entra ID is the IdP and Salesforce is the example service provider. All network messages use HTTPS.

```mermaid
sequenceDiagram
    participant SP as Salesforce (SP)
    participant B as User browser
    participant I as Microsoft Entra ID (IdP)
    Note over SP,I: Trusted issuer, SP entity ID, ACS address and signing certificate configured beforehand
    B->>SP: 1. Request application page
    SP->>B: 2. Redirect with SAML AuthnRequest
    B->>I: 3. Follow redirect carrying AuthnRequest
    I->>B: Show sign-in and MFA prompts if needed
    B->>I: Authenticate to Entra only
    Note over I: Authenticate user and build signed assertion
    I->>B: 4. Return form containing SAML Response and signed assertion
    B->>SP: 5. POST SAML Response to approved ACS address
    Note over SP: Validate signature, issuer, audience, destination,<br/>validity, request correlation and replay conditions
    Note over SP: Check application access and create local session
    SP->>B: 6. Set protected session cookie and return application page
    B->>SP: Later requests carry session cookie
```

The application does not receive the user's Entra password. In this redirect/POST flow the browser carries the sign-in messages; no direct SP-to-IdP token exchange occurs.

This clean diagram was prepared with assistant help from the [original hand drawing](saml-flow-handdrawn.png) and the corrections discussed during the lab. It illustrates the flow, not a deployed Salesforce integration.

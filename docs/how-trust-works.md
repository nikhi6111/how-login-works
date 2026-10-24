# How Federation Trust Works

These notes summarize concepts practised during guided Lab 3 discussions. Microsoft Entra ID is the example identity provider, and Salesforce is the example application. This is a conceptual design, not a deployed Salesforce integration.

## Trust is configured before login

The application does not trust a message just because it says it came from Entra. Administrators configure the expected identity provider, application identifiers, response addresses and signing keys before users sign in.

| Configuration | Purpose |
|---|---|
| Application identifier | Identifies the requesting application. OIDC uses a client ID; SAML commonly uses an SP entity ID. The intended audience must be checked in the returned proof. |
| Reply address | Defines where the response may be sent. OIDC uses a registered redirect URI; the SAML response is delivered to an approved assertion consumer service (ACS) endpoint. These addresses must be validated according to the protocol. |
| Trusted issuer | Identifies which identity provider and tenant the application expects. An arbitrary issuer must not be accepted. |
| Public signing certificate or keys | Let the recipient verify signatures. Entra signs with a private key that it does not share; the recipient uses the matching trusted public key. |
| Claims and permissions configuration | Determines which identity information is provided and which API permissions are requested and granted. An application still enforces its own access rules. |

OIDC discovery publishes configuration, including endpoint addresses and a jwks_uri from which public signing keys can be retrieved. It helps applications obtain current keys while validating the expected issuer.

## Signing keys and rotation

A signing key is not a new key for every login. One key can sign many tokens for many users. Keys are replaced over time through rotation.

If an application has only an old public key, it cannot verify a token signed by a different new private key. It must obtain the matching trusted public key or reject the token. A planned rollover publishes new keys in advance and provides appropriate overlap and metadata refresh.

For a SAML integration that relies on an expiring signing certificate, failure to plan renewal can cause sign-in failures in relying applications whose validation or trust configuration rejects that certificate. The effect depends on each integration's validation behavior; it is not necessarily every application failing at exactly the same instant. Inventory integrations, monitor expiry, coordinate updates, and test the rollover.

## What can go wrong

### Token theft
A bearer access token is a credential. An attacker who obtains a valid token may use the access it permits without knowing the user's password. It does not automatically grant access to every API or administrator function. Audience, permissions, validity and other enforced controls limit its use.

### Unprotected transport or storage
Every leg of the login flow needs HTTPS/TLS to protect information in transit against interception and tampering. A token signature does not encrypt the token. Protect stored tokens, keep them out of logs and repositories, and protect application session cookies as well.

### Tokens valid for too long
Short lifetimes limit the time a stolen token may remain useful. Expiry is one layer, not a complete defense: refresh tokens, sessions and revocation behavior also matter. The resource must enforce token validity.

### Wrong audience
A recipient must reject proof intended for another application or API. Otherwise, an attacker could try to reuse credentials issued for a different recipient. A Salesforce ID token is not a Microsoft Graph access token, and a Graph access token is not Salesforce's login proof.

### Missing signature validation
For the signed assertions and JWTs studied here, the recipient must verify the signature using trusted keys, plus validate issuer, audience, time limits and other required protocol conditions. Accepting unsigned or unverified attacker-controlled claims would allow fabricated identity information to be trusted. Decoding a token alone does not validate it.

### MFA and stolen tokens
MFA strengthens the initial authentication. It does not by itself prevent an attacker from using a bearer token stolen after authentication. Protect tokens and sessions alongside sign-in controls.

## Application session
After validating identity proof and checking access, the application creates its own local session. The browser normally uses a session cookie for later application requests, rather than exchanging the original authorization code repeatedly. Authorization codes are short-lived and single-use.

## Practical lesson
Use established authentication libraries, request only needed permissions, and treat bearer tokens as temporary credentials. The Graph Explorer practical confirmed that an access token has an intended API, issuer, expiry and granted scopes; no real token is published here.

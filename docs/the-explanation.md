# How a Work Login Happens

Guided draft prepared with assistant help from the concepts practised during Lab 3. Salesforce is a conceptual example, not an integration configured in this lab. Review and adapt this explanation into your own words before the independent explain-back.

## Explanation

When I open Salesforce without an application session, it needs to establish who I am. In this example, Salesforce trusts Microsoft Entra ID to authenticate me. That trust was configured beforehand using application identifiers, an approved redirect URI, an expected issuer and trusted signing keys.

In the OpenID Connect authorization-code flow, Salesforce redirects my browser to Entra's authorization endpoint. The request identifies the application and includes its redirect URI, the openid scope, state, a nonce and a PKCE challenge. Salesforce saves the values needed to correlate the response and complete the exchange.

Entra checks my credentials and any required MFA, or uses an existing suitable sign-in session. My Entra password is not given to Salesforce. After successful authentication and any necessary consent, Entra redirects my browser to Salesforce's registered callback address carrying a short-lived authorization code and state.

Salesforce checks state and sends the code directly to Entra's token endpoint over HTTPS. This back-channel exchange includes the PKCE verifier and, for this confidential application, client authentication. Entra validates the request, including the code's expiry, single-use status and application binding, before returning tokens directly to Salesforce.

Salesforce validates the ID token's signature, issuer, audience, expiration, nonce and other required conditions. It then checks application access, creates a local session and sends my browser a protected session cookie. Later page requests use that cookie rather than exchanging the authorization code again.

If Salesforce needs to call Microsoft Graph, it uses an appropriate access token intended for Graph with the required granted permissions. The ID token, access token and session cookie have different purposes. HTTPS protects credentials in transit, while signature and audience checks prevent untrusted or misdirected proof from being accepted.

## My main learning challenge

During guided practice, I initially confused the authorization code with the access token and the application session. The distinction became clearer when I separated the browser's code delivery, the server-to-server token exchange, and later browser requests using a session cookie.

## Explain-back status

The flow has been practised with prompts. An independent 90-second explanation without prompts has not yet been confirmed.

# Federated Login Vocabulary

These notes summarize the definitions I practised and refined during Lab 3, using Microsoft Entra ID as the identity provider and Salesforce as the example application.

## Identity provider (IdP)
A system that verifies who you are and provides identity proof that an application can validate. In this lab example, Microsoft Entra ID is the IdP.

## Service provider (SP) / relying party (RP)
An application that checks identity proof from the IdP without receiving the user's IdP password. Salesforce is the example SP in the SAML flow and relying party in the OIDC flow.

## Claim
One statement about a user or authentication event, such as the user's name, department, or completion of MFA. A role can be a claim if included in the token; the application still applies its own access rules.

## Token
A credential or package of information issued for a particular purpose that its intended recipient validates. An ID token tells the application about the authenticated user; an access token is presented to its intended API for permitted access. A digital signature does not make a token encrypted, and a token should not contain the user's password.

## Assertion
In SAML, an assertion contains statements about the user's identity and authentication. In the flow studied here, the SAML Response carries a signed assertion through the browser to the application, which validates it before creating a session.

## Federation
A trust arrangement where an application relies on an identity provider to authenticate users. The application and IdP are configured to establish this trust before users sign in.

## Distinction to remember
- ID token: identity information for the application that requested sign-in.
- Access token: access to the API it was issued for, within granted permissions.
- Session cookie: lets the browser continue an existing application session.

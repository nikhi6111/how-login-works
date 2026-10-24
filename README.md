# How a Login Actually Works

Lab 3: understanding federated login using Microsoft Entra ID and Salesforce as an example application. This combines a conceptual study with a Microsoft Graph Explorer practical exercise; it is not a configured Salesforce integration.

## Work so far
- [Written login explanation (guided draft)](docs/the-explanation.md)
- [Graph Explorer practical and token inspection](docs/token-inspection.md)
- [Trust configuration and security lessons](docs/how-trust-works.md)
- [Six vocabulary terms](docs/vocabulary.md)
- [SAML and OIDC flows, answers and comparison](docs/saml-and-oidc.md)
- [Clean SAML diagram](diagrams/saml-flow.md)
- [Clean OIDC diagram](diagrams/oidc-flow.md)
- [Original SAML drawing](diagrams/saml-flow-handdrawn.png)
- [Original OIDC drawing](diagrams/oidc-flow-handdrawn.png)

The original hand drawings are preserved unchanged. The flow notes explain label clarifications and validation steps.

## What I Found Hardest
Separating the authorization code, ID token, access token and session cookie. The browser returns the code; the application exchanges it directly for tokens; later browser requests use an application session cookie.

## Remaining work
- Review and personalize the guided written explanation.
- Deliver a 90-second explain-back without prompts.

Status: documentation and practical exercise recorded; independent explain-back pending.

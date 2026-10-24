# Practical: Microsoft Graph and Token Inspection

## Exercise performed
Signed in to Microsoft Graph Explorer as a regular VDR lab user and successfully requested the signed-in user's own profile:

```http
GET https://graph.microsoft.com/v1.0/me
```

Then selected specific fields:

```http
GET https://graph.microsoft.com/v1.0/me?$select=displayName,jobTitle,department,companyName
```

The selected response showed the lab user's display name, IT Analyst job title, IT department, and a null companyName. A null value means no value was returned for that property. The response body is API data, not the access token. `$select` controls the returned properties; it does not grant permissions.

## Permission observed
The Modify Permissions tab showed User.Read with an Unconsent control, indicating it was already granted. The request returned 200 OK. No broader permission was needed for this exercise.

## Claims inspected privately
Decoded the access token using Microsoft's jwt.ms viewer. The following findings were confirmed during the exercise:

| Claim | Observation and meaning |
|---|---|
| scp | Included openid, profile, User.Read and email. User.Read permits reading the signed-in user's profile. The email scope does not grant inbox access. |
| aud | Matched Microsoft Graph's application identifier. This access token is intended for Graph, not a different API or Salesforce login. |
| iss | Appeared as a URL identifying the issuer. The actual issuer and tenant identifiers are omitted here. |
| exp | Appeared as a numeric Unix timestamp, specifying the token's expiration time. |

## Lessons
- An access token is presented to its intended API; an ID token is validated by the application to establish the authenticated identity.
- Decoding reveals contents but does not verify a token's signature or validity.
- Signed does not mean encrypted.
- Bearer tokens are credentials: possession can permit access within their validity and granted permissions.
- MFA protects authentication but does not by itself prevent use of a token stolen afterward.
- This inspection is a learning exercise. Client code should not depend on the internal format or claims of access tokens issued for an API it does not own.

## Privacy
No real token, user object ID, user principal name, tenant-specific issuer URL, or token screenshot is included in this repository.

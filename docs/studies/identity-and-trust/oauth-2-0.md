---
title: "OAuth 2.0"
weight: 20
planes: ["Security"]
pillars: ["Identity", "Applications and Workloads"]
---

An application needs to read something a person owns. The direct way is for the
person to hand over their password, and every property of that arrangement is
bad: the application can now do anything the person can, forever, and the person
cannot withdraw the access without changing the password everywhere.

OAuth 2.0 replaces the shared credential with an issued one. The person
authenticates at a server they already trust, that server issues the
application a token describing a narrower permission, and the application
presents the token instead of the password. The password never reaches the
application.

Everything below follows from that substitution, in the same way that the
properties of a [JSON Web Token](../json-web-tokens/) follow from replacing a
lookup with a signature. What makes OAuth harder to hold in the head is the
number of actors and channels involved. In the authorization code grant, one
part of the exchange runs through a browser the authorization server does not
control, so the protocol has to protect the code until the client redeems it.

## Where Delegation Shows Up in Platform Work

On this platform the arrangement is concrete. Keycloak runs as a platform
service at `sso-on-prem.zavestudios.com/auth`, configured in
`gitops/bigbang/foundation/values-foundation.yaml`, and it holds the realm
`zavestudios`. The tenant workload `panchito` is registered in that realm as a
client named `panchito`, which is visible in its deployment manifest at
`gitops/tenants/panchito/deployment.yaml`:

```yaml
- name: OIDC_ISSUER_URL
  value: https://sso-on-prem.zavestudios.com/auth/realms/zavestudios
- name: OIDC_CLIENT_ID
  value: panchito
- name: APP_BASE_URL
  value: https://panchito-on-prem.zavestudios.com
- name: OIDC_CLIENT_SECRET
  valueFrom:
    secretKeyRef:
      name: panchito-app
      key: OIDC_CLIENT_SECRET
```

Four of the five things a reader needs are already in that fragment: which
server issues, which workload consumes, what identifies the consumer, and where
its secret comes from. The secret is not in Git — `panchito-app` is produced by
an ExternalSecret that pulls `tenants/panchito/app` from Vault, declared in
`gitops/tenants/panchito/panchito-app.external-secret.yaml`. The fifth is the
redirect URI registered in Keycloak, which is not present in the deployment
fragment or represented by a registration record in Git.

What breaks when this is wrong is specific rather than general. A wrong
`OIDC_CLIENT_ID` or a redirect URL that does not match what Keycloak has
registered does not degrade the login — the authorization server refuses the
request before any user sees a password prompt, and it refuses it with an error
page rather than a redirect. Confirming that is a single unauthenticated
request, and the results are in
[Where This Platform Diverges](#where-this-platform-diverges) below.

## Four Roles, and Why They Are Worth Separating

RFC 6749 names four roles, and conflating any two of them is the source of most
confusion about what OAuth is doing.

| Role | In the panchito case |
|---|---|
| Resource owner | The person logging in |
| Client | `panchito`, the Flask application requesting access |
| Authorization server | Keycloak, realm `zavestudios` |
| Resource server | No separate `panchito` resource server; its API accepts a local session, while Keycloak's UserInfo endpoint is a fallback |

OAuth permits the authorization server and resource server to be the same
server or separate entities, and the interaction between them is explicitly
out of scope. Token introspection later standardized one way for a resource
server to ask an authorization server about a token, in RFC 7662 in 2015.

The client is the party being constrained. The threat model includes
compromised and malicious clients, and the protocol avoids giving a client the
resource owner's credentials so that a compromise exposes delegated
credentials rather than the owner's password.

## The Authorization Code Grant, Start to Finish

One grant type carries nearly all interactive use, and following it once in
full is worth more than a tour of all four. The client is `panchito`, the
authorization server is the `zavestudios` realm, and the endpoints are the ones
the realm actually advertises at
`/auth/realms/zavestudios/.well-known/openid-configuration`.

```mermaid
sequenceDiagram
  participant RO as Person (resource owner)
  participant UA as Browser
  participant C as panchito (client)
  participant AS as Keycloak (authorization server)

  RO->>C: Requests a protected page
  C->>UA: Redirect to /protocol/openid-connect/auth<br/>client_id, redirect_uri, scope, state
  UA->>AS: Authorization request
  AS->>RO: Authenticates the person
  RO->>AS: Grants access
  AS->>UA: Redirect to redirect_uri<br/>code, state
  UA->>C: Delivers code to /auth/callback
  C->>AS: POST /protocol/openid-connect/token<br/>code, redirect_uri, client_id + client secret
  AS->>AS: Verifies code, client, and redirect_uri
  AS->>C: access_token, token_type, id_token, expires_in, optional refresh_token
```

Two channels are in play, and the distinction between them is the reason the
flow has this shape. The browser carries the authorization request and the
code: that path runs through software the authorization server does not
control, can be observed in a URL bar, and can be pointed somewhere else by an
attacker. The token request runs directly from the client to the authorization
server over TLS, with the client's own secret attached, and is the only channel
on which a token travels.

For this confidential client, an intercepted code cannot be redeemed directly
without the client's secret. Client authentication does not stop every code
injection attack, however, which is why current guidance still recommends PKCE
for confidential clients. A description that does not distinguish the browser
channel from the direct token request leaves the reader unable to say why the
implicit grant is weaker.

### What Protects the Code

The code is protected by four separate checks, each of which the authorization
server performs and any of which a deployment can weaken.

**It is bound to the client and to the redirect URI.** RFC 6749 states that
"the authorization code is bound to the client identifier and redirection URI,"
and the token endpoint re-verifies the redirect URI it already verified at the
authorization endpoint — if `redirect_uri` was in the authorization request, it
is required again in the token request and the two "MUST be identical."
Checking it twice looks redundant until you notice the checks defend against
different things: the first stops the code being delivered to an attacker, the
second stops a code obtained some other way being redeemed against a different
callback.

**It is single-use and short-lived.** The specification requires a code to
"expire shortly after it is issued," recommends a maximum lifetime of ten
minutes, and requires the authorization server to deny a second use. On reuse
it also says the server SHOULD revoke every token already issued from that
code — a *should*, and only "when possible," so a deployment that cannot do so
is still conforming.

**The client authenticates.** A confidential client must authenticate at the
token endpoint, and the `panchito` client does so with the secret pulled from
Vault. The `client_id` is not part of this: RFC 6749 is explicit that it "is not
a secret" and "MUST NOT be used alone for client authentication."

**Failures do not redirect.** If the redirect URI is missing, invalid, or
mismatched, or the client identifier is unknown, the authorization server "MUST
NOT automatically redirect the user-agent to the invalid redirection URI." This
is what makes a misconfigured client fail visibly at the authorization server
instead of quietly delivering a code to whatever host was named in the request.

## PKCE Is Not Only for Mobile Applications

Proof Key for Code Exchange closes the remaining gap: a client that cannot hold
a secret has nothing to prove with at the token endpoint, so an intercepted
code is redeemable by whoever holds it.

The mechanism is one hash. Before redirecting, the client generates a
high-entropy `code_verifier` — RFC 7636 requires 43 to 128 unreserved
characters and recommends 32 random octets, base64url-encoded — and sends only
its digest as `code_challenge`. At the token endpoint it sends the verifier, and
the authorization server recomputes:

```
BASE64URL-ENCODE(SHA256(ASCII(code_verifier))) == code_challenge
```

A code intercepted in the browser is now useless without the verifier, which
never crossed the browser channel. It leaves the client only in the direct
request to the token endpoint.

Three details decide whether this is real in a given deployment.

The challenge method defaults to `plain` when omitted, in which case the
challenge *is* the verifier and an attacker who read the authorization request
has both. RFC 9700 says clients should use methods that do not expose the
verifier and states that "currently, S256 is the only such method." Omitting
`code_challenge_method` is therefore not the same as omitting PKCE, and is
arguably worse, because it looks like protection in a log.

The requirement differs by client type. Public clients MUST use PKCE;
for confidential clients it is RECOMMENDED. RFC 9700 adds the note that
matters for server-side web applications: "Although PKCE was designed as a
mechanism to protect native apps, this advice applies to all kinds of OAuth
clients, including web applications." For a confidential client, PKCE also
provides cross-site request forgery protection when the client has ensured that
the authorization server supports and enforces it.

Support has to be detectable. Authorization servers MUST support PKCE and are
recommended to publish `code_challenge_methods_supported` in their metadata, so
a client can tell rather than assume.

## Client Credentials: No User Delegation

Not every caller acts for a person. When a service calls another service on its
own behalf, the client may also be the resource owner. The authorization server
and resource server remain separate roles, and access may instead rest on an
arrangement made with the authorization server. There is no browser and no
authorization code.

```
POST /token HTTP/1.1
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
```

The client's authentication *is* the grant. Two constraints follow and are
worth remembering because violating either is common: the grant "MUST only be
used by confidential clients," since a public client's credentials are
extractable and would be the whole authorization; and a refresh token "SHOULD
NOT be included," because a client that can authenticate can simply ask again.

This is the grant that machine-to-machine access on a platform usually wants,
and the one that gets replaced by a static API key because it looks like more
work. The difference is that the token expires and the credential can be
rotated centrally. The caller must still receive the replacement credential and
reload it before the old one is retired.

## Scopes Are a Request, Not a Decision

A scope is a space-delimited string whose values are "defined by the
authorization server." OAuth itself gives them no meaning. The values that the
`panchito` client requests by default — `openid profile email`, from
`panchito/backend/app/config.py` — are standard OpenID Connect scopes. The realm
still decides which scopes it supports and which claims it releases.

Two properties get misread.

Scope is what the client asks for, and the authorization server "MAY fully or
partially ignore" it; if what it grants differs from what was asked, it MUST
return a `scope` parameter saying so. A client that sends three scopes and
assumes it received three is wrong in a way no error surfaces.

Scope is also not the authorization decision. It narrows what a token may
carry, but the resource server still decides whether this caller may perform
this operation on this object. RFC 9700 is direct about the resource server's
obligation: an access token should be audience-restricted, and "every resource
server is obliged to verify, for every request, whether the access token sent
with that request was meant to be used for that particular resource server. If
it was not, the resource server MUST refuse to serve the respective request."
That is the same audience check the JSON Web Tokens study identifies as the one
most often skipped, stated here as a requirement on the resource server rather
than a property of the token.

## Refresh Tokens Reintroduce Authorization-Server State

Short access-token lifetimes limit the damage from a leak, but without a refresh
token the client must obtain another grant when the token expires. The
authorization server may be able to reuse an existing login session, but it may
also have to involve the user again. A refresh token avoids that interaction: it
is typically a longer-lived credential that the client redeems at the token
endpoint for a new access token.

The constraints are tighter than for access tokens. A refresh token is bound to
the client it was issued to, and a confidential client must authenticate to use
one. Its scope cannot grow — a refresh request "MUST NOT include any scope not
originally granted by the resource owner." And when a new refresh token is
issued, the client "MUST discard the old refresh token."

That last rule is the basis of rotation, which RFC 9700 presents as one of two
acceptable ways to detect replay by a public client. Issue a new refresh token
on every refresh, invalidate the previous one, and remember the relationship: if
both an attacker and the legitimate client use the same token, one of them
presents an invalidated value and the authorization server learns of the breach.
It cannot tell which party is which, so it revokes the active token — the attack
stops at the cost of the real user logging in again. The alternative is a
sender-constrained refresh token, which requires the client to prove possession
of a key rather than merely hold a string, and that is the subject of
Mutual TLS, a study not yet written.

A refresh sends the client back to the authorization server, putting that
server on the sensitive path again. It does not necessarily require a database
lookup: grant information may be encoded in an integrity-protected refresh
token. Rotation and revocation still require the server to retain enough state
to reject a replayed or withdrawn credential. This is the same state trade the
JSON Web Tokens study describes from the token's side, visible here as a
protocol feature.

## What OAuth 2.0 Does Not Define

Four absences explain most of the ecosystem built on top of it.

**Token format.** Nothing in RFC 6749 says an access token is a JWT. It may be
an opaque string that means nothing outside the authorization server. A
resource server cannot validate such a token from the string alone, which is
why RFC 7662 defines an introspection endpoint — a POST that returns "meta
information surrounding the token, including whether this token is currently
active," with the definition of active left to the authorization server.
Self-contained tokens can remove an authorization-server call from the request
path, while immediate revocation adds state or another check. Introspection
provides an authoritative answer at the cost of a network call, although a
resource server may cache responses. The `zavestudios` realm advertises an
introspection endpoint; that metadata does not reveal which access-token format
it issues.

**Authentication of the user.** OAuth authorizes a client; it says nothing
about who the person was. In a user-mediated flow, a client that receives an
access token has learned that a request was authorized, not who authenticated;
in the client-credentials grant, no person is involved at all. Reading identity
out of a successful OAuth flow is an inference, and the reason
OpenID Connect exists is to make it a statement instead,
by adding an ID token with an audience and a subject. That distinction is the
most consequential one in this study, and it is also where this platform's
configuration sits: `panchito` is registered as an OIDC client, not a plain
OAuth client.

**How a token gets used.** Presenting it is specified separately, in RFC 6750,
which defines the `Authorization: Bearer` scheme and the term that carries the
whole risk model: a bearer token is one where "any party in possession of the
token can use the token in any way that any other party in possession of it
can," with no proof of key material required.

**The authorization server's relationship to the resource server.** Out of
scope, as noted above. Everything about which tokens a resource server accepts,
how it learns the signing keys, and what audience value it demands is
deployment convention or a later specification.

## The Grants the Specification Left Behind

RFC 6749 defines four grants. Two are no longer acceptable, and the two
judgments are not equally strong — a distinction worth keeping straight,
because the current guidance is a BCP that updates rather than replaces the
original specification.

The **resource owner password credentials** grant, where the client collects
the user's password and posts it to the token endpoint, "MUST NOT be used." The
stated reasons are that it exposes the credential to the client, widens where
it can leak, trains users to type passwords into applications, and cannot
accommodate multi-factor or multi-step authentication.

The **implicit** grant, where the authorization server returns an access token
directly in the redirect, draws a softer verdict: clients "SHOULD NOT" use it
unless token injection and the leakage vectors are mitigated. The problems are
that the token travels through the browser in a URL, no standardized way exists
to sender-constrain a token issued that way, and a leaked one is directly
usable. The recommended replacement is the authorization code grant, which is
what PKCE made viable for the public clients implicit was invented for.

An authorization server can still advertise both. The `zavestudios` realm, read
on 2026-10-12, lists `implicit` and `password` in `grant_types_supported`
alongside `authorization_code`, `client_credentials`, `refresh_token`, and four
extension grants: device code, token exchange, UMA ticket, and CIBA. Keycloak is
advertising realm capability, not client policy — which grants a given client
may use is per-client registration. The distinction is worth internalizing: a
realm's metadata document is a menu, and reading it as a statement of what is
in use will mislead anyone auditing the deployment.

---

## How This Platform Implements It

One authorization server, one client visible in repository configuration, and
no OAuth resource server of its own.

Keycloak is a platform service, not a tenant workload. It is deployed through
Big Bang with the hostname override `sso-on-prem` and the argument
`--hostname=https://sso-on-prem.zavestudios.com/auth`
(`gitops/bigbang/foundation/values-foundation.yaml`), its image pinned by
digest to `ghcr.io/zavestudios/keycloak`, and its database and admin
credentials supplied as ExternalSecrets from Vault paths
`platform/services/keycloak/db` and `platform/services/keycloak/admin`
(`gitops/platform/keycloak/external-secrets.yaml`). TLS terminates at
Cloudflare rather than in the cluster.

The client half is `panchito`, and its implementation is short enough to read
in full at `panchito/backend/app/auth.py`. It registers an authlib client
against the realm's discovery document rather than hardcoding endpoints:

```python
oauth.register(
    name="keycloak",
    client_id=app.config["OIDC_CLIENT_ID"],
    client_secret=app.config["OIDC_CLIENT_SECRET"],
    server_metadata_url=f"{issuer}/.well-known/openid-configuration",
    client_kwargs={"scope": app.config["OIDC_SCOPES"]},
)
```

The flow is then two route handlers. `/auth/login` calls
`authorize_redirect(redirect_uri)`, which builds the authorization request and
sends the browser to Keycloak. `/auth/callback` calls
`authorize_access_token()`, which performs the token request described above —
the code exchange, the client secret, and the redirect URI check are all inside
that one call. The library is doing the protocol; the application is choosing
the client type and the scopes.

Two platform mechanisms are load-bearing here without appearing in the
protocol. The client secret reaches the pod only because External Secrets
projects it from Vault on a one-hour refresh. Rotation starts in Vault rather
than in a manifest, but the secret is consumed through an environment variable,
so the pod must be replaced to load the new value. The callback URL the client
sends is derived at runtime from forwarded headers, which is the subject of the
next section.

## Where This Platform Diverges

Six findings: three confirmed by unauthenticated endpoint or metadata requests
on 2026-10-12, and three read out of the source.

**The redirect URI registration is strict, and that is confirmed rather than
assumed.** RFC 9700 requires exact string matching for registered redirect
URIs. Sending `client_id=panchito` with the expected
`https://panchito-on-prem.zavestudios.com/auth/callback` returns HTTP 200 and a
login page. Each of these returns HTTP 400 with `Invalid parameter:
redirect_uri`, and no redirect:

| Variation on the redirect URI | Result |
|---|---|
| `…/auth/callback` (as registered) | HTTP 200, login page |
| `…/some-other-path` | HTTP 400 |
| `…/auth/callback?x=1` | HTTP 400 |
| `http://…/auth/callback` | HTTP 400 |
| `https://panchito-on-prem.zavestudios.com.attacker.example/auth/callback` | HTTP 400 |

The last row is the one that matters most: a lookalike hostname with the
registered host as a prefix is rejected, which is the failure mode loose
suffix matching produces. An unknown `client_id` is also a 400.

**The application has a separate post-login open redirect.** The registered
OAuth callback remains fixed, but `/auth/login` accepts any `next` query value,
stores it in the session, and passes it directly to Flask's `redirect` after the
code exchange. A request such as `/auth/login?next=https://attacker.example`
therefore sends the user to that host after a successful login. This does not
leak the authorization code — the application has already redeemed it — but it
creates a phishing boundary that the strict Keycloak callback registration does
not address.

**The client sends no PKCE challenge.** The same probe without a
`code_challenge` parameter is accepted and returns a login page, so nothing in
the client's registration requires PKCE. The authlib registration above sets no
`code_challenge_method`, which means none is sent. For a confidential client
PKCE is RECOMMENDED rather than required, so this is a gap rather than a
violation — but it is the specific gap RFC 9700 singles out as applying to web
applications and not only to native apps. Closing it means setting Authlib's
`code_challenge_method` to `S256` and requiring PKCE in the Keycloak client
registration.

**The realm advertises `plain` as a challenge method.**
`code_challenge_methods_supported` lists `plain` and `S256`. Nothing is using
either today, so this is latent rather than live. A client that explicitly
requests `S256` is not weakened by the presence of `plain`. A client that sends
a challenge without naming a method gets `plain` by default, which still
protects against response-only code interception but not an attacker who can
read the authorization request.

**The access token is discarded after identity is resolved.** This is the
largest divergence from what the protocol is for. Authlib normally derives
`userinfo` from the OIDC ID token; if it is absent, the fallback call to
Keycloak's UserInfo endpoint uses the access token. After that step,
`auth_callback` keeps four user attributes and nothing else:

```python
session["user"] = {
    "sub": userinfo.get("sub"),
    "preferred_username": userinfo.get("preferred_username"),
    "email": userinfo.get("email"),
    "name": userinfo.get("name"),
}
```

From then on, authorization is a Flask session cookie
(`SESSION_COOKIE_HTTPONLY`, `SameSite=Lax`, `Secure` in production, per
`panchito/backend/app/config.py`). The access token is not stored or presented
to `panchito`'s API; the client and protected application are the same process.
What the platform is using the authorization code flow for is authentication,
which is OIDC's job rather than OAuth's. OIDC carries its authenticated identity
result through that flow. That is a legitimate and extremely common pattern,
but it means a reader studying OAuth access-token use beyond the optional
UserInfo call will not find it exercised here. There is no client-credentials
client in Git either, and no application component validating a bearer token.

**The redirect URI depends on a reverse proxy being trusted correctly.** The
client does not have its callback URL configured; it computes one with
`url_for("auth.auth_callback", _external=True)`. Behind Cloudflare the pod sees
plain HTTP, so that call produces an `https` URL only because
`panchito/backend/app/__init__.py` wraps the application in
`ProxyFix(app.wsgi_app, x_proto=1, x_host=1)` and trusts the forwarded scheme
and host. Remove that line and the computed redirect URI becomes
`http://…/auth/callback`, which the table above shows is rejected — the login
breaks at the authorization server with a 400, and nothing in the application
logs explains why.

The same trust has a security edge, which RFC 9700 raises in its section on
TLS-terminating reverse proxies: a proxy that forwards client-supplied headers
unchanged lets an attacker set them, and "a reverse proxy MUST therefore
sanitize any inbound requests to ensure the authenticity and integrity of all
header values relevant for the security of the application servers." Here that
obligation sits with the Cloudflare tunnel and the ingress path in front of the
pod, not with the application, and it was not verified as part of this study.

## What Has Not Been Verified

- The `panchito` client's registration as Keycloak holds it — its registered
  redirect URIs, whether it is marked confidential, and its PKCE setting. The
  behavior above was inferred from the authorization endpoint's responses, which
  is evidence about acceptance, not a reading of the record. Confirming it needs
  the Keycloak admin API and an admin credential.
- That a complete flow issues a token. Every probe here stopped at the login
  page; no authorization code was obtained, exchanged, or replayed. The
  single-use rule and the `invalid_grant` response on reuse are quoted from RFC
  6749 and were not observed on this deployment.
- Which grants the `panchito` client is permitted. The realm advertises nine;
  the client almost certainly allows one or two, and that is per-client
  configuration this study did not read.
- The running Keycloak version. The manifest labels the pod as version `26.4.7`
  and pins the image by digest, but the label does not prove which build the
  digest contains. The behavior described is that of the deployed endpoint as
  observed on 2026-10-12.
- Whether anything was registered in the realm outside Git. A client created
  directly in the admin console leaves no manifest, which is the exact drift
  recorded in `gitops/docs/keycloak-drift-incident-2026-03-30.md`. The
  repository shows `panchito` configured as a consumer; it does not establish
  the realm's complete client inventory.

## The Choices This Leaves You

Reading a client registration, four fields decide nearly everything. Whether
the client can hold a secret determines its type, and its type determines
whether PKCE is optional. The registered redirect URIs are the boundary the
authorization server enforces on the browser channel, and web-client URIs
should be exact rather than prefixed. The permitted grants should be the one or
two the client uses, not whatever the realm advertises. And the scopes are what
the client may ask for, not what it is entitled to.

Choosing a grant is nearly mechanical once the roles are clear. A person is
present and a browser is available: authorization code, with PKCE and `S256`.
No person is present: client credentials, and only from a client that can
authenticate. A person is present but the client cannot hold a secret:
authorization code with PKCE, never implicit. The client wants to collect the
password itself: no.

The judgment that remains is about tokens rather than grants. A self-contained
token lets a resource server validate locally when it has the required keys;
immediate revocation then requires added state or another check. Introspection
keeps the authorization server authoritative but adds a network dependency,
partly reducible through caching. Short access-token lifetimes narrow the
revocation window, and refresh tokens preserve the user experience at the price
of a longer-lived credential whose replay must be detected. There is no
arrangement in which a stolen bearer token is harmless, which is what
sender-constrained credentials exist to change, and why Mutual TLS is the next
question rather than a different subject.

## References

- [RFC 6749: The OAuth 2.0 Authorization Framework](https://www.rfc-editor.org/rfc/rfc6749.txt) — sections 1.1, 2.1, 2.2, 3.3, 4.1, 4.4, 6
- [RFC 6750: The OAuth 2.0 Authorization Framework: Bearer Token Usage](https://www.rfc-editor.org/rfc/rfc6750.txt) — sections 1.2, 2.1
- [RFC 7636: Proof Key for Code Exchange by OAuth Public Clients](https://www.rfc-editor.org/rfc/rfc7636.txt) — sections 4.1 through 4.6
- [RFC 7662: OAuth 2.0 Token Introspection](https://www.rfc-editor.org/rfc/rfc7662.txt) — section 2
- [RFC 8628: OAuth 2.0 Device Authorization Grant](https://www.rfc-editor.org/rfc/rfc8628.txt)
- [RFC 8693: OAuth 2.0 Token Exchange](https://www.rfc-editor.org/rfc/rfc8693.txt)
- [RFC 9700: Best Current Practice for OAuth 2.0 Security](https://www.rfc-editor.org/rfc/rfc9700.txt) — sections 2.1, 2.1.1, 2.1.2, 2.3, 2.4, 4.13, 4.14.2
- [OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0.html) — sections 2, 3.1.3, 5.4
- [User-Managed Access 2.0 Grant for OAuth 2.0 Authorization](https://docs.kantarainitiative.org/uma/wg/rec-oauth-uma-grant-2.0.html)
- [OpenID Connect Client-Initiated Backchannel Authentication Flow 1.0](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html)
- Realm metadata at `https://sso-on-prem.zavestudios.com/auth/realms/zavestudios/.well-known/openid-configuration`, read 2026-10-12
- `gitops/bigbang/foundation/values-foundation.yaml`, `gitops/platform/keycloak/external-secrets.yaml`, `gitops/tenants/panchito/deployment.yaml`, `gitops/tenants/panchito/panchito-app.external-secret.yaml`
- `panchito/backend/app/auth.py`, `panchito/backend/app/__init__.py`, `panchito/backend/app/config.py`

---

Applied on this platform in
[Platform Services](../../../architecture/platform-services/), where Keycloak is
a shared capability rather than a per-workload integration.

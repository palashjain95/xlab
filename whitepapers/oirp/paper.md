**Toward Intent-Based Naming: An Open Protocol for a Post-URL Web**
*White Paper · July 2025*

---

### Abstract

The Domain Name System (DNS) solved global addressing for the early web by enforcing one-to-one, globally unique host names. Thirty years later the model shows strain: rampant domain speculation, rising acquisition costs, and user experiences that increasingly bypass the URL bar in favor of AI assistants. This paper frames the scarcity problem, surveys current technology shifts—particularly the rise of agent-driven browsers—and proposes **OIRP (Open Intent Resolution Protocol)**, a lightweight, verifiable standard that decouples human-friendly aliases from hard-to-obtain DNS labels while remaining fully backward-compatible with today’s routing fabric.

---

## 1  Introduction

The web’s original trust model was “*If you own the name, you must be the right party*.”
That premise breaks when:

* multiple legitimate actors share the same human name or brand;
* short, English-dictionary strings command aftermarket prices of \$5 000–\$50 000;
* emergent user interfaces translate natural-language intent directly into an API call, skipping the URL entirely.

AI-powered browsers—from Arc’s *Browse for Me* to Apple Intelligence and Gemini-in-Chrome—illustrate a clear trajectory: **human input shrinks to a few words of intent, and the agent decides which endpoint to contact.** Naming must evolve accordingly.

---

## 2   Problem Statement

1. **Artificial Scarcity** – DNS enforces global uniqueness on human-readable labels even though identity is contextual in the real world.
2. **Economic Distortion** – Speculators capture outsized value while productive builders face steep acquisition or legal costs.
3. **UX Mismatch** – In intent-driven flows the end user never sees (nor needs) the canonical domain; yet the developer still must acquire one for compatibility.
4. **Governance Friction** – Proposals to limit bulk registration or apply “use-it-or-lose-it” rules move slowly through ICANN and often clash with property-rights arguments.

---

## 3   Landscape Review

| Layer (2025)                 | Gatekeeper                       | Stress Signal                                                        |
| ---------------------------- | -------------------------------- | -------------------------------------------------------------------- |
| **DNS root / TLDs**          | ICANN, Verisign, ccTLD operators | .com saturated (\~156 M domains); next gTLD window delayed to 2026   |
| **Registrars / Aftermarket** | GoDaddy, Sedo, DAN               | Short .ai names priced \$5 k–\$10 k; Squat-to-sell portfolios rising |
| **Browsers**                 | Chrome, Safari, Edge             | Sidebar LLMs and integrated copilots obscure the address bar         |
| **Alt-roots**                | ENS, Unstoppable                 | Millions of registrations, low mainstream resolution, high churn     |

Simultaneously, **tool-calling APIs** (OpenAI Assistants, Gemini Actions, iOS App Intents) now expose a uniform pattern: *intent ➜ structured tool call ➜ endpoint*. A naming resolver can be slotted into that pipeline with minimal friction.

---

## 4   Design Hypothesis

> *If we treat the endpoint selection step as a verifiable, open protocol—rather than a paid asset transaction—developers can claim memorable aliases instantly, agents can resolve them safely, and legacy clients still reach a working host.*

### 4.1 Core Requirements

| ID | Requirement                 | Rationale                                                                                        |
| -- | --------------------------- | ------------------------------------------------------------------------------------------------ |
| R1 | **Contextual uniqueness**   | Multiple “mocha” aliases may coexist if bound to distinct scopes (app, café, API) or identities. |
| R2 | **Cryptographic ownership** | A signed proof ties the alias to a DID or wallet; no central registrar decides.                  |
| R3 | **Resolver pluralism**      | Any party may run a resolver node; trust anchors live in client “root stores” akin to TLS CAs.   |
| R4 | **HTTP & IP unchanged**     | After resolution, traffic flows through standard DNS → IP → TLS.                                 |
| R5 | **Graceful fallback**       | Optional DNS hostnames keep the service reachable by URL-only clients.                           |

---

## 5   Open Intent Resolution Protocol (OIRP)

### 5.1  Data Model

```jsonc
// Alias Claim  (JWS-signed)
{
  "alias":   "mocha",
  "scope":   "app",
  "owner":   "did:key:z6Mki...pW",
  "primary": "https://app.mocha.dev",
  "extras":  { "api": "https://api.mocha.dev/v1" },
  "iat":     1721587200,
  "exp":     1753123200
}
```

* **Alias + Scope** form the logical key.
* **Owner** is any W3C DID or wallet address.
* **Primary** is a routable HTTPS origin; optional fields add API or asset URLs.
* Proofs are logged in a **CT-style transparency log** and mirrored by resolver nodes.

### 5.2  Protocol Endpoints

| Path                        | Verb | Description                                                    |
| --------------------------- | ---- | -------------------------------------------------------------- |
| `/resolve`                  | GET  | `?alias=mocha&scope=app` → JSON with endpoint + embedded proof |
| `/aliases`                  | POST | Submit a new signed claim or an update                         |
| `/.well-known/resolve.json` | GET  | Self-hosted mirror of the latest claim for extra assurance     |

### 5.3  Reference Flow

1. **Claim** – Developer signs and submits claim; log returns an immutable proof URL.
2. **Publish** – Developer hosts `/.well-known/resolve.json` at the *primary* origin.
3. **Resolve** – AI agent (browser, Siri, Gemini) hits its preferred resolver ➜ receives endpoint ➜ verifies JWS + log inclusion ➜ connects via ordinary HTTPS.
4. **Update** – Owner re-signs with higher `iat`; resolvers treat newest non-expired claim as canonical.

---

## 6   Governance & Anti-Abuse

* **No mandatory fees.** Squatting deterred by *use-it-or-lose-it*: claim expires unless the endpoint remains live or the owner posts a renewal with evidence.
* **Collision Policy.** First valid claim wins within a scope. Disputes resolved by presenting newer mutually-signed consent or, failing that, escalated to resolver-root maintainers.
* **Transparency Logs.** All writes are publicly auditable, minimizing opaque take-downs.

---

## 7   Compatibility & Migration

| Legacy Component      | Interaction                                                    |
| --------------------- | -------------------------------------------------------------- |
| **DNS**               | `primary` may carry any hostname; A/AAAA ⇢ IP unchanged.       |
| **Routers / BGP**     | No awareness of aliases; packets flow by IP as before.         |
| **Browsers w/o OIRP** | Users visit `https://app.mocha.dev` directly.                  |
| **AI Agents**         | Single REST call to `/resolve` replaces hard-coded link lists. |

A small browser extension or native intent-resolver module suffices for early adoption; long-term, vendor integration mirrors how browsers eventually embedded OCSP stapling or Service Workers.

---

## 8   Illustrative Use Case – *mocha*

| Step          | Old World (.ai aftermarket)     | OIRP Path                                      |
| ------------- | ------------------------------- | ---------------------------------------------- |
| Acquire name  | Bid \$7 200 for `mocha.ai`      | Submit free signed claim `mocha#app`           |
| Ship product  | Configure DNS, SSL, pay renewal | Host app at `app.mocha.dev`; publish proof     |
| Discovery     | SEO / ads drive to URL          | Users say “Open mocha”; agent resolves alias   |
| Future rename | Domain lock-in                  | Update claim’s `primary`; alias stays constant |

Result: fully routable service, zero dependency on a premium TLD, future-proof against AI UX shifts.

---

## 09   Future Work

* **Trust Roots Governance** – Community working group to curate root resolver lists, akin to Mozilla’s CA program.
* **Layer-2 Routing Enhancements** – Leverage SVCB/HTTPS RRs for protocol-optimized endpoints (HTTP/3-only, ECH).
* **Alias Leasing Markets** – Build secondary services that let an owner time-share aliases (e.g., `snow` for a winter campaign).
* **Contextual Resolution** – Agents weigh user locale, history, or auth state when multiple valid aliases exist.

---

## 10   Conclusion

As web interaction compresses from explicit navigation to conversational intent, DNS’s global-uniqueness constraint becomes an anachronism. **OIRP offers a pragmatic bridge**: a signed, federated alias layer that co-exists with DNS/IP routing yet frees developers and users from the tyranny of vanity-domain scarcity. By launching a minimal open standard—backed by transparent logs and optional hosted resolvers—we can seed an ecosystem where names reflect *meaning* rather than market distortions, and where any developer can claim a memorable identity in minutes, not auctions.

---

### Call to Collaborate

Invite browser vendors, AI-assistant providers, domain registries, and independent developers to critique, fork, or pilot OIRP. 

Let's ensure ensure the next chapter of the web values accessibility and innovation over scarcity and speculation.

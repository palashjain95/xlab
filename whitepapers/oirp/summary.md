# Open Intent Resolution Protocol (OIRP) – Executive Summary

**Why this paper matters**  
The Domain Name System (DNS) solved global addressing for the early web, but its one-name-per-label rule now creates artificial scarcity and rising costs. At the same time, AI-first browsers (Perplexity, Gemini, ChatGPT) are turning navigation into pure intent: users say ***“Open mocha"*** and an agent chooses the endpoint. OIRP proposes a naming layer purpose-built for that future.

---

## Key Insights
| Pain Point | Evidence |
|------------|----------|
| **Artificial scarcity** | .com saturated (≈156 M domains); short .ai names cost \$5k–\$10k. |
| **Economic distortion** | Speculators capture value while builders pay premiums. |
| **UX mismatch** | AI agents hide URLs; developers still need pricey domains for compatibility. |

---

## OIRP in One Sentence  
*A lightweight, cryptographically-verifiable protocol that lets multiple actors claim the **same human-friendly alias** (e.g., “mocha”) under different scopes, while routing traffic over existing DNS/IP.*

---

## How It Works
1. **Alias Claim** (JSON + JWS) maps `{alias, scope} → primary HTTPS endpoint` and is logged in a public transparency log.  
2. **Resolver API** (`GET /resolve?alias=mocha&scope=app`) returns the endpoint plus embedded proof.  
3. **Verification**: Agents validate the signature and log inclusion; packets then flow via normal DNS → IP → TLS.  
4. **Updates**: Owners publish a new signed claim with a higher timestamp; resolvers treat the latest non-expired claim as canonical.

---

## Benefits
- **Contextual Uniqueness** – “mocha” can exist for an app, a café, and an API without collision.  
- **Zero Auctions** – Free or minimal-cost claims; squatting deterred by use-it-or-lose-it expiry.  
- **Agent-Ready** – Fits naturally into OpenAI Tools, Gemini Actions, Siri Shortcuts.  
- **Backward-Compatible** – Legacy browsers still reach the `primary` hostname; routing fabric unchanged.

---

## Future Roadmap
- **Governance**: community-curated root resolver lists (CA-style).  
- **Layer-2 Enhancements**: leverage SVCB/HTTPS resource records, encrypted client hello (ECH).  
- **Alias Leasing Markets**: explore time-based or fractional alias rentals.  
- **Contextual Resolution**: agents pick endpoints based on user locale or history.

---

## Call to Collaborate
Invite browser vendors, AI-assistant providers, domain registries, and independent developers to critique, fork, or pilot OIRP so the next chapter of the web prizes accessibility and innovation over scarcity and speculation.

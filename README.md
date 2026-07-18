# BoardBrief AI — MCP Server

AI-powered board deck generation and portfolio company governance reporting for private equity firms.

**MCP Endpoint:** `https://mcp.boardbriefai.io/mcp`
**Transport:** Streamable HTTP (MCP spec 2025-03-26)
**Authentication:** OAuth 2.0 Authorization Code with PKCE
**Documentation:** [boardbriefai.io/docs](https://boardbriefai.io/docs)

---

## What is BoardBrief AI?

BoardBrief AI is an MCP connector that lets Claude generate PE-sponsor-format board decks for portfolio companies. It connects to BoardBrief AI's deck generation engine, giving Claude the ability to manage portfolio companies, generate quarterly board presentations, and retrieve past decks — all through natural conversation.

**Built for:** CFOs, Chiefs of Staff, and finance leaders at PE-backed portfolio companies who prepare quarterly board decks.

## Available Tools

| Tool | Type | Description |
|------|------|-------------|
| `list_companies` | Read | List all portfolio companies for the authenticated user |
| `get_company` | Read | Get detailed information for a single portfolio company by ID |
| `create_company` | Write | Add a new portfolio company (subject to plan tier limits) |
| `generate_deck` | Write | Generate a PE board deck (PPTX) for a company and quarter |
| `list_decks` | Read | List previously generated board decks |
| `get_deck_download` | Read | Get a time-limited download URL for a generated deck |

### Tool Details

#### `list_companies`

Returns company name, industry, revenue range, PE sponsor name, and fiscal year end for each portfolio company.

**Parameters:** None

---

#### `get_company`

Returns all profile fields and metadata for a single company.

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `company_id` | string (UUID) | Yes | UUID of the portfolio company |

---

#### `create_company`

Creates a new portfolio company. Subject to subscription tier limits (free: 1 company, single_portco: 1, multi_deck: 4).

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `name` | string | Yes | Company name |
| `industry` | string | No | Industry or sector (e.g., "Industrial Manufacturing") |
| `revenue_range` | string | No | Annual revenue range (e.g., "$10M-$50M") |
| `pe_sponsor_name` | string | No | PE sponsor / fund name |
| `fiscal_year_end` | string | No | Fiscal year end (e.g., "December") |

---

#### `generate_deck`

Generates a 10-slide PE-sponsor-format board deck (PPTX). Calls the Claude API for content generation — takes 30-60 seconds. Provide as much operating data as possible for higher-quality output.

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `company_id` | string (UUID) | Yes | UUID of the portfolio company |
| `quarter` | integer (1-4) | Yes | Fiscal quarter |
| `year` | integer (2020-2030) | Yes | Fiscal year |
| `kpi_data` | object | No | KPI data (revenue, ebitda, ebitda_margin, customer_count, etc.) |
| `financials` | object | No | Financial summary (revenue, cogs, gross_margin, opex, ebitda, capex, etc.) |
| `risks` | object | No | Risk register (array of risk, severity, likelihood, mitigation, owner) |
| `projects` | object | No | Capital projects (array of name, status, budget, spent, completion_pct, target_date) |

**Deck slides:** Executive Summary, KPI Dashboard, Financial Summary, Operational Highlights, Risk Register, Capital Projects, Vendor Risk Summary, Strategic Initiatives, Appendix.

---

#### `list_decks`

Lists all previously generated decks, optionally filtered by company.

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `company_id` | string (UUID) | No | Filter by company (omit for all decks) |

---

#### `get_deck_download`

Returns an authenticated download URL for a generated deck. Download files are temporary — download promptly after generation.

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `deck_id` | string (UUID) | Yes | UUID of the generated deck |

## Authentication

BoardBrief AI uses **OAuth 2.0 Authorization Code flow with PKCE**.

1. **Authorization** — Claude redirects you to BoardBrief AI's login page
2. **Consent** — You review and approve the requested permissions
3. **Token exchange** — BoardBrief AI issues an access token (1-hour expiry) and refresh token (30-day expiry)
4. **Auto-refresh** — Claude automatically refreshes expired access tokens

Tokens can be revoked at any time. Revoking a token immediately disconnects the MCP connector.

**OAuth Endpoints:**

| Endpoint | URL |
|----------|-----|
| Authorization | `https://mcp.boardbriefai.io/oauth/authorize` |
| Token | `https://mcp.boardbriefai.io/oauth/token` |
| MCP Server | `https://mcp.boardbriefai.io/mcp` |
| Health Check | `https://mcp.boardbriefai.io/health` |

## Configuration

### Claude Desktop / Claude.ai

BoardBrief AI is available in Claude's MCP connector directory. Search for "BoardBrief AI" and click Connect.

### Other MCP Clients

Add to your MCP client configuration:

```json
{
  "mcpServers": {
    "boardbrief-ai": {
      "type": "url",
      "url": "https://mcp.boardbriefai.io/mcp",
      "name": "BoardBrief AI"
    }
  }
}
```

## Example Usage

Once connected, ask Claude naturally:

> "Generate a Q2 2026 board deck for my company with revenue of $12.4M actual vs $13.0M target, EBITDA margin of 18.2%, and a supply chain risk on the Q3 expansion project."

Claude will use the `generate_deck` tool to produce a 10-slide PE-format PPTX with executive summary, KPI dashboard with RAG status, financial summary, risk register, and more.

**Typical workflow:**
1. `list_companies` → see your portfolio
2. `create_company` → add a new portco (if needed)
3. `generate_deck` → generate the quarterly board deck
4. `get_deck_download` → retrieve the PPTX download link

## Security

- All traffic encrypted via TLS 1.2+ (HTTPS enforced)
- OAuth 2.0 with PKCE, short-lived access tokens, revocable refresh tokens
- Data encrypted at rest (AES-256)
- No storage of raw accounting data — only the KPIs and summaries you provide
- Row-level security enforces strict data isolation between users
- SOC 2 Type II readiness controls in place

## Pricing

| Plan | Companies | Decks | Price |
|------|-----------|-------|-------|
| Free Trial | 1 | 1 deck | Free |
| Single Portco | 1 | Unlimited | $29/mo |
| Multi-Deck | 4 | Unlimited | $79/mo |

Sign up at [boardbriefai.io](https://boardbriefai.io).

## Links

- **App:** [boardbriefai.io](https://boardbriefai.io)
- **Documentation:** [boardbriefai.io/docs](https://boardbriefai.io/docs)
- **Privacy Policy:** [boardbriefai.io/privacy](https://boardbriefai.io/privacy)
- **Terms of Service:** [boardbriefai.io/terms](https://boardbriefai.io/terms)
- **Security FAQ:** [boardbriefai.io/security](https://boardbriefai.io/security)
- **Support:** support@gallandigital.com

## License

MIT — see [LICENSE](LICENSE).

---

© 2026 Gallan Holdings LLC. All rights reserved.

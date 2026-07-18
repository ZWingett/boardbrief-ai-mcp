# BoardBrief AI — MCP Server Installation

## Endpoint

```
https://mcp.boardbriefai.io/mcp
```

## Authentication

OAuth 2.0 Authorization Code flow with PKCE. No API keys required — you authenticate with your BoardBrief AI account credentials through a browser-based login flow.

**Create an account:** [boardbriefai.io](https://boardbriefai.io) (free trial available, no credit card required).

## Configuration

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

## OAuth Endpoints

| Endpoint | URL |
|----------|-----|
| Authorization | `https://mcp.boardbriefai.io/oauth/authorize` |
| Token | `https://mcp.boardbriefai.io/oauth/token` |

- Access tokens expire after 1 hour (auto-refreshed by the MCP client)
- Refresh tokens expire after 30 days
- PKCE is required for all authorization requests

## Available Tools

| Tool | Type | Description |
|------|------|-------------|
| `list_companies` | Read | List all portfolio companies for the authenticated user |
| `get_company` | Read | Get detailed information for a single portfolio company by ID |
| `create_company` | Write | Add a new portfolio company (subject to plan tier limits) |
| `generate_deck` | Write | Generate a PE board deck (PPTX) for a company and quarter |
| `list_decks` | Read | List previously generated board decks |
| `get_deck_download` | Read | Get a time-limited download URL for a generated deck |

## Example Usage

Once connected, the typical workflow is:

```
list_companies → create_company (if needed) → generate_deck → get_deck_download
```

Ask your AI assistant to generate a board deck by providing quarterly operating data:

> "Generate a Q2 2026 board deck for Apex Industrial with revenue $12.4M vs $13.0M target, EBITDA margin 18.2%, and a supply chain risk on the Q3 expansion."

The `generate_deck` tool produces a 10-slide PE-sponsor-format PPTX with executive summary, KPI dashboard, financial summary, risk register, capital projects, and more.

## Health Check

Verify the server is operational:

```
GET https://mcp.boardbriefai.io/health
→ {"status":"ok"}
```

## Support

- **Documentation:** [boardbriefai.io/docs](https://boardbriefai.io/docs)
- **Email:** support@gallandigital.com
- **Privacy:** [boardbriefai.io/privacy](https://boardbriefai.io/privacy)

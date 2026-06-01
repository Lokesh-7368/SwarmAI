# SwarmAI

SwarmAI is a multi-agent personal assistant built with n8n. The assistant receives user requests through Telegram, understands text, voice, and image inputs, routes each request to a specialist agent, and returns a clean response to the user.

The project demonstrates multi-agent orchestration, workflow automation, API integration, webhook-based communication, and containerized deployment.

## Project Objective

The objective of SwarmAI is to build a practical AI assistant that can handle different personal productivity tasks through separate specialist agents instead of relying on one large workflow for every operation.

The system is designed to:

- Receive user requests from Telegram.
- Identify the intent of each request.
- Route the task to the correct specialist agent.
- Connect AI reasoning with real tools such as Gmail, Google Calendar, travel search, and web research.
- Support simple and compound requests in a single assistant experience.

## Core Features

- Telegram-based conversational interface.
- Text message support.
- Voice message handling through transcription.
- Image message analysis.
- Main Router Agent for intent detection and task delegation.
- Email Agent for Gmail operations.
- Calendar Agent for Google Calendar operations.
- Travel Agent for flight and hotel search.
- Research Agent for factual and web research.
- Per-user memory in n8n workflows.
- Docker-based local setup.
- Deployment-ready structure for a public webhook URL.

## Multi-Agent Architecture

SwarmAI is divided into five major workflows.

### 1. Main Router Agent

The Main Router Agent is the entry point of the system. It receives Telegram input, converts supported input types into text, understands the user's intent, and calls the required specialist workflow.

Supported routing examples:

- Email task -> Email Agent
- Calendar task -> Calendar Agent
- Flight or hotel search -> Travel Agent
- Research or factual query -> Research Agent
- Multi-step query -> one or more agents in sequence

### 2. Email Agent

The Email Agent handles Gmail-based tasks such as:

- Send email
- Read email
- Search inbox
- Reply to email
- Delete email

### 3. Calendar Agent

The Calendar Agent handles Google Calendar tasks such as:

- Create events
- List events
- Update events
- Delete events
- Check availability

### 4. Travel Agent

The Travel Agent handles travel search tasks such as:

- Flight search
- Hotel search
- Travel option summaries

### 5. Research Agent

The Research Agent handles information retrieval tasks through:

- Tavily web search
- Wikipedia knowledge lookup

## High-Level Flow

```text
Telegram User
      |
      v
Telegram Bot Webhook
      |
      v
n8n Main Router Workflow
      |
      +--> Email Agent --> Gmail API
      |
      +--> Calendar Agent --> Google Calendar API
      |
      +--> Travel Agent --> SerpAPI
      |
      +--> Research Agent --> Tavily and Wikipedia
      |
      v
Formatted Telegram Reply
```

## Technology Stack

| Area | Technology |
|---|---|
| Workflow orchestration | n8n |
| Local runtime | Docker and Docker Compose |
| User interface | Telegram Bot |
| Chat model | Gemini Flash or OpenAI gpt-4o-mini fallback |
| Voice and image processing | Google Gemini |
| Email integration | Gmail API through n8n |
| Calendar integration | Google Calendar API through n8n |
| Travel search | SerpAPI |
| Web research | Tavily |
| Knowledge lookup | Wikipedia |
| Source control | GitHub |
| Final deployment path | Oracle Cloud Always Free VM with Docker Compose and HTTPS reverse proxy |

## Repository Structure

```text
SwarmAI/
  deployment/
    .env.example
    docker-compose.local.yml
    docker-compose.production.yml
    Caddyfile
  docs/
    00-complete-step-by-step-implementation-guide.md
    01-local-docker-setup.md
    02-import-workflows.md
    03-credentials-checklist.md
    04-free-online-deployment.md
    05-github-repository-guide.md
    06-team-sharing-guide.md
    07-demo-and-viva-script.md
    08-troubleshooting.md
    09-project-architecture.md
    10-model-cost-strategy.md
  workflows/
    SwarmAI-Main-Router.json
    SwarmAI-Email-Agent.json
    SwarmAI-Calendar-Agent.json
    SwarmAI-Travel-Agent.json
    SwarmAI-Research-Agent.json
  workflows-gemini-economy/
    SwarmAI-Main-Router.json
    SwarmAI-Email-Agent.json
    SwarmAI-Calendar-Agent.json
    SwarmAI-Travel-Agent.json
    SwarmAI-Research-Agent.json
  scripts/
    backup-n8n.ps1
    validate-workflows.ps1
    switch-to-gemini-economy.ps1
  README.md
```

## Prerequisites

Install or prepare the following before running the project:

1. Docker Desktop
2. Git
3. Telegram account
4. Google account for Gmail and Google Calendar
5. n8n running locally or on a server
6. API keys and OAuth credentials required by the workflows

## Required Credentials

Configure credentials inside n8n. Do not commit credentials to GitHub.

| Service | Purpose |
|---|---|
| Telegram Bot API | Receive and send Telegram messages |
| Google Gemini API | Gemini chat option, voice transcription, image analysis |
| OpenAI API | Low-cost fallback model option |
| Gmail OAuth2 | Email operations |
| Google Calendar OAuth2 | Calendar operations |
| SerpAPI | Flight and hotel search |
| Tavily API | Web research |

## Recommended Model Strategy

To reduce credit usage during testing and presentation preparation:

1. Prefer Gemini Flash-Lite or Gemini Flash in the Gemini economy workflows.
2. Keep OpenAI `gpt-4o-mini` as fallback only.
3. Avoid using GPT-4o or Claude for regular demo runs.
4. Keep responses concise.
5. Reduce long memory windows during repeated testing.

Free model access usually has quotas and rate limits, so the project should not depend on a promise of unlimited free model usage.

## Execution Steps

Follow these steps in order.

### Step 1: Clone The Repository

```bash
git clone https://github.com/Lokesh-7368/SwarmAI.git
cd SwarmAI
```

### Step 2: Start n8n Locally With Docker

Run:

```powershell
docker compose -f .\deployment\docker-compose.local.yml up -d
```

Open n8n in the browser:

```text
http://localhost:5678
```

Create the n8n owner account if prompted.

### Step 3: Choose Workflow Version

Use one of these workflow sets:

- `workflows-gemini-economy` for lower model cost during demo preparation.
- `workflows` for the original OpenAI-active workflow version.

Recommended starting choice:

```text
workflows-gemini-economy
```

### Step 4: Import Workflows Into n8n

Import workflows in this order:

1. Email Agent workflow
2. Calendar Agent workflow
3. Travel Agent workflow
4. Research Agent workflow
5. Main Router workflow

Import specialist workflows first because the Main Router calls them as tool workflows.

### Step 5: Reconnect Main Router Tool Workflows

After import, workflow IDs may change inside your n8n instance.

Open the Main Router workflow and reselect:

1. Email tool node -> imported Email Agent workflow
2. Calendar tool node -> imported Calendar Agent workflow
3. Travel tool node -> imported Travel Agent workflow
4. Research tool node -> imported Research Agent workflow

Save the Main Router workflow after reconnecting all tool workflows.

### Step 6: Configure Credentials

Create and assign the required credentials in n8n:

1. Telegram Bot credential
2. Gemini API credential
3. OpenAI API credential if fallback is needed
4. Gmail OAuth2 credential
5. Google Calendar OAuth2 credential
6. SerpAPI credential
7. Tavily credential

Open every node that shows a missing credential warning and assign the correct credential.

### Step 7: Configure The Chat Model

For the economy workflow version:

1. Open each workflow.
2. Open the Gemini Chat Model node.
3. Select the Gemini credential.
4. Select a Gemini Flash-Lite or Gemini Flash model.
5. Keep the OpenAI model node disabled unless fallback is required.

### Step 8: Activate Workflows

Activate specialist workflows first:

1. Email Agent
2. Calendar Agent
3. Travel Agent
4. Research Agent

Then activate:

5. Main Router Agent

### Step 9: Test Workflows Inside n8n

Before Telegram public webhook testing, verify core workflow behavior:

- Test a simple research query.
- Test a travel search query.
- Test a calendar event flow using a safe test calendar.
- Test an email flow using a real test email address.

### Step 10: Make Local n8n Reachable By Telegram

Telegram needs a public HTTPS webhook URL. Localhost alone is not enough.

For temporary testing use one of these:

- Cloudflare Tunnel
- ngrok

Temporary test flow:

1. Start n8n locally.
2. Start a tunnel to `http://localhost:5678`.
3. Copy the public HTTPS URL.
4. Set n8n `WEBHOOK_URL` to the public HTTPS URL.
5. Restart n8n.
6. Reactivate the Telegram Trigger workflow if needed.
7. Send a Telegram message to the bot.

### Step 11: Run Demo Queries

Use safe demo queries such as:

```text
Hi Nikki
```

```text
Research benefits of workflow automation in 5 points
```

```text
Find flights from Delhi to Mumbai on 22 May 2026
```

```text
Schedule project discussion tomorrow at 4 PM
```

```text
Send an email to your-test-email@gmail.com with subject SwarmAI Demo and body The project demo is ready.
```

## Final Deployment Recommendation

For a final online demo, use:

1. Oracle Cloud Always Free VM
2. Docker Compose
3. Caddy or another HTTPS reverse proxy
4. Stable domain or subdomain
5. Persistent n8n data volume

This path is preferred because Telegram webhook flows need an always-available public HTTPS endpoint.

## Security Guidelines

Do not commit the following:

- API keys
- OAuth client secrets
- Telegram bot token
- Gmail tokens
- Calendar tokens
- n8n encryption key
- `.env` files with real values
- Runtime database files

Use `.env.example` for templates and store real secrets privately.

## Team Sharing Guidelines

For group collaboration:

1. Keep this GitHub repository as the shared project source.
2. Ask all members to clone the same repository.
3. Share workflow JSON files through GitHub.
4. Do not share secrets through GitHub.
5. Keep one final production deployment for presentation stability.
6. Let each member run a local Docker copy for practice and explanation.

## Troubleshooting

### n8n Does Not Open

Check Docker Desktop, then start the compose stack again.

### Telegram Bot Does Not Reply

Check:

- Main Router workflow is active.
- Telegram credential is valid.
- Public HTTPS webhook URL is configured.
- Only one active Telegram trigger instance is using the bot.

### Main Router Cannot Find Specialist Workflows

Reopen the Main Router tool nodes and reselect the imported specialist workflows.

### Gmail Or Calendar Action Fails

Check OAuth credentials, scopes, account permissions, and node credential selection.

### Travel Search Fails

Check SerpAPI key, quota, and travel search parameters.

### Research Search Fails

Check Tavily key and research tool node availability.

## Demo Explanation

SwarmAI is a Telegram-based multi-agent assistant built with n8n. Telegram sends the user request to the Main Router workflow. The Main Router identifies the intent and delegates the request to specialist workflows for email, calendar, travel, or research operations. Each specialist workflow uses its own tools and returns a result. The Main Router formats the final response and sends it back to the user through Telegram.

## Maintainer

SwarmAI Team


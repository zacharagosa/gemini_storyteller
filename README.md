# Gemini Storyteller Looker Visualization

![Gemini Storyteller Demo](storyteller_viz.gif)

A custom Looker visualization that uses Google's Gemini AI to transform raw event logs into compelling gameplay narratives. This visualization takes structured data from Looker queries and uses generative AI to provide qualitative context and storytelling.

## 🚀 Quick Start with Demo Data

We've provided a sample dataset to help you test the visualization immediately.

1.  Download `demo_data.csv` from this repository.
2.  Upload it to your Looker instance as a [File-based Explore](https://cloud.google.com/looker/docs/create-explores-from-files).
3.  Select the **Gemini Storyteller** visualization.
4.  Experience the narrative of "Void Weaver: The Crystal Loom"!


## Features

-   **✨ AI-Powered Summarization**: Automatically generates a concise overview of the session.
-   **📊 Dynamic Metrics**: Extracts key performance indicators and highlights from the raw log data.
-   **📖 Chronological Narrative**: Reconstructs events into a readable, engaging story.
-   **🎭 Customizable Persona**: Control the "voice" of the AI through the visualization settings.

## Architecture

This visualization uses a **secure backend proxy** pattern to call the Gemini API. Instead of exposing an API key in the client-side JavaScript, all AI requests are routed through an authenticated backend server.

```
┌──────────────┐     HTTPS      ┌──────────────────┐    Vertex AI    ┌─────────────┐
│    Looker    │ ──────────────► │  Cloud Run       │ ──────────────► │  Gemini API │
│  (Browser)   │  Bearer Token  │  Backend Server  │  Service Acct   │             │
└──────────────┘                └──────────────────┘                 └─────────────┘
```

**Benefits:**
- 🔒 No API keys exposed in client-side code
- 🔑 Backend authenticates via Google Cloud service account (Vertex AI)
- 🛡️ Optional `NARRATIVE_SECRET_TOKEN` to restrict who can call the proxy endpoint
- 🌐 HTTPS endpoint required by Looker custom visualizations

## Setup Instructions

### 1. Requirements

-   **Looker**: Access to add custom visualizations (Admin permissions usually required).
-   **Backend Server**: A deployed instance of the backend proxy (e.g., on Google Cloud Run). See [Backend Setup](#backend-setup) below.

### 2. Adding to Looker

1.  Log in to your Looker instance and navigate to **Admin > Visualizations**.
2.  Click **Add Visualization**.
3.  Fill in the following details:
    -   **Id**: `gemini_storyteller`
    -   **Label**: `Gemini Storyteller`
    -   **Main**: The HTTPS URL where `gemini_narrator.js` is hosted.
        -   Example: `https://your-cloud-run-service.run.app/gemini_narrator.js`
        -   ⚠️ **Looker requires HTTPS** for custom visualization bundles. HTTP URLs will not work.

### 3. Configuration

In the visualization edit panel within Looker:

| Setting | Description | Example |
|---------|-------------|---------|
| **Backend Server URL** | The HTTPS URL of your backend proxy server | `https://your-service.run.app` |
| **Backend Auth Token** | Secret token to authenticate with the backend (must match `NARRATIVE_SECRET_TOKEN` on the server) | `my-secret-token-123` |
| **Context / Persona** | *(Optional)* Define how the AI should tell the story | `"You are a tactical commander briefing a team"` |
| **Model Name** | The Gemini model to use for generation | `gemini-3.5-flash` |

## Backend Setup

The backend proxy server handles secure communication with the Gemini API via Vertex AI.

### Environment Variables

Create a `.env` file in the backend server directory (never commit this file):

```bash
PROJECT_ID=your-gcp-project-id
NARRATIVE_SECRET_TOKEN=your-secret-token-here
```

### Deploy to Cloud Run

```bash
# From the backend server directory
./deploy_cloud_run.sh
```

This will:
1. Build the frontend assets
2. Build a Docker container with the Flask server
3. Deploy to Cloud Run with the necessary environment variables
4. Provide an HTTPS URL for both the API and the visualization script

### API Endpoint

The backend exposes:

- **`GET /gemini_narrator.js`** — Serves the visualization JavaScript bundle over HTTPS
- **`POST /api/generate-narrative`** — Proxies narrative generation requests to Vertex AI

**Request format:**
```json
{
  "prompt": "Your compiled prompt with event data...",
  "model_name": "gemini-3.5-flash"
}
```

**Headers:**
```
Authorization: Bearer <NARRATIVE_SECRET_TOKEN>
Content-Type: application/json
```

## How to Use

1.  **Create a Query**: Build a Looker Explore query that includes event logs. We recommend including fields for:
    -   Timestamp
    -   Event Type
    -   Event Description
    -   User/Player ID
2.  **Select the Viz**: Choose the **Gemini Storyteller** from the visualization picker.
3.  **Configure Settings**: Set the Backend Server URL and Auth Token in the visualization settings panel.
4.  **Run & Analyze**: Click the **Analyze Session** button within the visualization to send the data to Gemini and generate your story.

## Security Notes

- **`.env` files are gitignored** and must never be committed
- The `NARRATIVE_SECRET_TOKEN` is passed as an environment variable at deploy time, not hardcoded
- The backend uses **Google Cloud service account credentials** (via Vertex AI) — no API keys are stored
- The visualization script contains **no secrets** — the auth token is entered by the user in Looker's visualization settings panel

---

*Built with ❤️ using Looker and Google Gemini.*

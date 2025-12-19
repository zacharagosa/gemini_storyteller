# Gemini Storyteller Looker Visualization

A custom Looker visualization that uses Google's Gemini AI to transform raw event logs into compelling gameplay narratives. This visualization takes structured data from Looker queries and uses generative AI to provide qualitative context and storytelling.

## Features

-   **✨ AI-Powered Summarization**: Automatically generates a concise overview of the session.
-   **📊 Dynamic Metrics**: Extracts key performance indicators and highlights from the raw log data.
-   **📖 Chronological Narrative**: Reconstructs events into a readable, engaging story.
-   **🎭 Customizable Persona**: Control the "voice" of the AI through the visualization settings.

## Setup Instructions

### 1. Requirements

-   **Looker**: Access to add custom visualizations (Admin permissions usually required).
-   **Google Gemini API Key**: Obtain a free API key from [Google AI Studio](https://aistudio.google.com/).

### 2. Adding to Looker

1.  Log in to your Looker instance and navigate to **Admin > Visualizations**.
2.  Click **Add Visualization**.
3.  Fill in the following details:
    -   **Id**: `gemini_storyteller`
    -   **Label**: `Gemini Storyteller`
    -   **Main**: Upload or host the `gemini_narrator.js` file and provide the URL. 
        -   *Note: For testing, you can drag the `.js` file directly if your environment supports it, but hosting on a CDN (like GitHub Pages or a public bucket) is recommended for production.*

### 3. Configuration

In the visualization edit panel within Looker:

-   **Gemini API Key**: Paste your key from Google AI Studio.
-   **Context / Persona**: (Optional) Define how the AI should tell the story (e.g., "You are a tactical commander briefing a team" or "You are a fantasy bard recountng a legend").
-   **Model Name**: Defaults to `gemini-3-pro-preview` for high-quality narrative generation.

## How to Use

1.  **Create a Query**: Build a Looker Explore query that includes event logs. We recommend including fields for:
    -   Timestamp
    -   Event Type
    -   Event Description
    -   User/Player ID
2.  **Select the Viz**: Choose the **Gemini Storyteller** from the visualization picker.
3.  **Run & Analyze**: Click the **Analyze Session** button within the visualization to send the data to Gemini and generate your story.

---

*Built with ❤️ using Looker and Google Gemini.*

# AI Marketing Email Generator

A simple single-page application for generating AI-powered marketing emails in multiple locales.

**Live Demo**: https://juction.luchang.xyz

## Features

- Generate marketing emails based on campaign briefs
- Optional PDF upload for additional context
- Multi-locale support with live preview
- Iterative refinement with additional instructions
- Send generated emails to recipients

## Technology Stack

- **Frontend**: Vue 3, TypeScript, Vite
- **Backend**: n8n AI workflow with Gemini agents and GCP

## Usage

1. **Enter Marketing Brief**: Provide campaign details, target audience, and key messages
2. **Upload PDF (Optional)**: Add supporting documents if needed
3. **Generate Emails**: Click "Generate Emails" to create variants for different locales
4. **Send**: Enter recipient email and send all variants

## Backend & AI Integration

This project uses a powerful backend workflow orchestrated by **n8n**, integrating Google's AI and communication services.

![workflow](./doc/workflow.png 'workflow')

### Architecture

1.  **n8n Workflow**: The core logic is handled by an n8n workflow (configuration available in `n8n_config.json`).
2.  **Google Gemini**: We utilize the **Gemini Pro** model (via Google AI Studio) to generate high-quality, context-aware marketing copy and HTML email templates in multiple languages.
3.  **Google Workspace (Gmail)**: The generated emails are automatically sent to the recipient using the Gmail API.

### Setup

To deploy the backend:

1.  **n8n**: Import `n8n_config.json` into your n8n instance.
2.  **Credentials**:
    - **Google Gemini API**: Obtain an API key from Google AI Studio and configure the `googlePalmApi` credential in n8n.
    - **Gmail OAuth2**: Set up a project in Google Cloud Console, enable the Gmail API, and create OAuth2 credentials. Configure the `gmailOAuth2` credential in n8n.
3.  **Webhook**: Ensure the n8n Webhook node URL matches the `GENERATE_AND_SEND_URL` in the frontend code.

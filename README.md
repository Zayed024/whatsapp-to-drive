#  WhatsApp-Driven Google Drive Assistant

## Project Objective

This project is an n8n workflow designed to act as a backend assistant for Google Drive, triggered by simple text commands sent via WhatsApp. The workflow parses these commands and performs a variety of actions, from listing files to generating document summaries with an AI model.

## Features Implemented

- **Inbound Channel**: Uses Twilio's WhatsApp Sandbox as the entry point for all commands
- **Command Parsing**: The workflow parses simple text commands for a variety of actions
- **Google Drive Integration**: Authenticates via OAuth2 and performs actions within the authenticated user's Google Drive
- **AI Summarization**: Calls an external AI API (Gemini 2.5 Flash) to produce concise, bulleted summaries of documents
- **Logging & Safety**: Maintains an audit log in a Google Sheet and includes a safety check to guard against accidental deletion
- **Ready-to-Import Workflow**: The project includes a workflow.json file that can be easily imported into any n8n instance

## Supported Commands

The following commands are supported by the workflow. All commands must be sent from the WhatsApp number registered with the Twilio Sandbox.

| Command Syntax | Action |
|----------------|--------|
| `LIST /<folder_name>` | Lists all files and folders in the specified folder |
| `DELETE /<file_name> --confirm` | Deletes the specified file. The `--confirm` flag is required as a safety check |
| `MOVE /<file_name> /<destination_folder_name>` | Moves a file from the root to the specified destination folder |
| `SUMMARY /<file_name>` | Provides a bulleted summary of a .txt, .pdf, or .docx file's content using an AI model |

## Setup Instructions

### 1. Run n8n

The recommended way to run n8n for this project is using Docker.

```bash
docker run -it --rm \
    -p 5678:5678 \
    -v ~/.n8n:/home/node/.n8n \
    n8nio/n8n \
    start --tunnel
```

This command starts n8n and creates a public tunnel URL (e.g., `https://your-unique-url.hooks.n8n.cloud/`). You will need this URL for the Twilio setup.

### 2. Configure Twilio Sandbox

1. Go to your Twilio console and access your WhatsApp Sandbox settings
2. Copy the full webhook URL from your n8n Webhook node (e.g., `https://your-unique-url.hooks.n8n.cloud/webhook/your-path`)
3. Paste this URL into the "WHEN A MESSAGE COMES IN" field in your Twilio settings
4. Ensure the method is set to POST

### 3. Set Up Google Credentials

1. Follow the steps in the Google Cloud Console to create OAuth2 Desktop App credentials
2. In n8n, create a new Google Drive OAuth2 credential and a Google Sheets OAuth2 credential using the credentials from your Google Cloud Console
3. Ensure the following Google APIs are enabled for your project:
   - Google Drive API
   - Google Sheets API

### 4. Import the Workflow

1. In the n8n editor, go to **File > Import from File**
2. Upload the provided `My workflow.json` file
3. Ensure the credentials are correctly linked in the imported nodes
4. Activate the workflow

## Evaluation Hints
- Did not add many specific error messages to send to the user keeping in mind twilio credits and rate limits for free tier
- **Workflow Clarity**: The workflow uses a modular Switch node to route commands, making it clear and easy to extend for new commands
- **Error Handling & User Feedback**: The workflow provides user-friendly error messages on WhatsApp for failed commands, ensuring a good user experience
- **AI Integration**: The AI summarization branch demonstrates an elegant use of a large language model to provide useful information from document content
- **Security**: OAuth2 is used for all Google Drive and Google Sheets operations, protecting user tokens and scopes

## Project Structure

```
├── README.md
├── workflow.json
└── .env.sample
```


## License

This project is licensed under the MIT License - see the LICENSE file for details. 

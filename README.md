# Automated Certificate Email Distribution with n8n

An automated email distribution system built with n8n that sends personalized certificates to multiple recipients via Gmail. This project uses Docker Compose for easy deployment and includes a complete n8n workflow for batch email processing.

## 🎯 Features

- **Automated Email Distribution**: Send certificates to multiple teams with a single workflow execution
- **CSV-Based Input**: Read team information from a structured CSV file
- **Dynamic Certificate Matching**: Automatically matches certificate images (1.jpg, 2.jpg, etc.) based on row index
- **Personalized Emails**: Each email is customized with the team name and team lead's name
- **Anti-Spam Protection**: Random delays (2-5 seconds) between emails to prevent Gmail rate limiting
- **Batch Processing**: Processes teams one at a time to ensure accurate certificate attachment
- **Docker Deployment**: Easy setup with Docker Compose
- **Error Handling**: Validates data before processing to prevent missing or duplicate emails

## 📋 Prerequisites

### Required Software
- **Docker Desktop** (Windows/Mac) or **Docker Engine** (Linux) - Version 20.10 or later
- **Docker Compose** - Version 2.0 or later (included with Docker Desktop)
- **Gmail Account** - With OAuth2 credentials configured
- **ngrok** (optional) - For exposing n8n to the internet for webhooks

### Required Files
1. **CSV File**: Team participant data (`data/participants.csv`)
2. **Certificate Images**: Individual certificate files in `data/certificates/` (1.jpg, 2.jpg, 3.jpg, etc.)

## 🗂️ Project Structure

```
emails-automation-n8n/
├── docker-compose.example.yml    # Example Docker Compose configuration
├── docker-compose.yml            # Your Docker Compose config (create from example)
├── Email Certificates.json       # n8n workflow (import this into n8n)
├── data/                         # Data directory (mounted to container)
│   ├── participants.csv.example  # Example CSV template
│   └── certificates/             # Certificate images folder
│       ├── 1.jpg
│       ├── 2.jpg
│       ├── 3.jpg
│       └── ...
├── .gitignore                  # Git ignore rules
└── README.md                   # This file
```

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/emails-automation-n8n.git
cd emails-automation-n8n
```

### 2. Set Up Docker Volume

Create the external Docker volume for n8n data persistence:

```bash
docker volume create n8n_data
```

### 3. Configure Docker Compose

Copy the example configuration file and customize it:

```bash
cp docker-compose.example.yml docker-compose.yml
```

Edit `docker-compose.yml` and update the following:

- **Timezone**: Change `UTC` to your timezone (e.g., `Asia/Karachi`, `America/New_York`)
- **Base URLs**: 
  - For local development: Keep `http://localhost:5678`
  - For ngrok: Replace with your ngrok HTTPS URL (e.g., `https://xxxxx.ngrok-free.app`)
  - Update both `N8N_EDITOR_BASE_URL` and `WEBHOOK_URL` with the same value

### 4. Prepare Your Data

1. **CSV File**: Update `data/participants.csv` with your team data
2. **Certificates**: Place your certificate images in `data/certificates/` numbered as `1.jpg`, `2.jpg`, `3.jpg`, etc.

### 5. Start n8n

```bash
docker-compose up -d
```

n8n will be available at: `http://localhost:5678`

### 6. Access n8n and Import Workflow

1. Open your browser and navigate to `http://localhost:5678`
2. Complete the initial n8n setup
3. Click **"Workflows"** >> **"Import from File"**
4. Select `Email Certificates.json` from the project root
5. The workflow will be imported with all nodes configured

### 7. Configure Gmail OAuth2

#### Set Up Google Cloud OAuth

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select an existing one
3. Enable the **Gmail API**
4. Configure OAuth consent screen:
   - User Type: External (for personal use) or Internal (for Google Workspace)
   - Add required information (App name, support email)
   - Add authorized domain: Your ngrok domain or `localhost` for local testing
5. Create OAuth 2.0 credentials:
   - Application type: Web application
   - Add authorized redirect URI: `http://localhost:5678/rest/oauth2-credential/callback` (or your ngrok URL)
6. Save the **Client ID** and **Client Secret**

#### Connect Gmail in n8n

1. In the imported workflow, click on the **"Send Email"** node
2. Click **"Create New Credential"** → **"Gmail OAuth2"**
3. Enter your Client ID and Client Secret
4. Click **"Connect my account"** and authorize with Google
5. Save the credential

### 8. Run the Workflow

1. Ensure your CSV file is populated with team data
2. Ensure certificate files exist and are numbered correctly
3. Open the "Email Certificates" workflow in n8n
4. Click the **"Execute Workflow"** button on the Manual Trigger node
5. Monitor the execution in the workflow view

## 📝 CSV File Format

Your `participants.csv` file should have the following structure:

```csv
Team Name,Team Lead,Email
Team Alpha,John Doe,john.doe@example.com
Team Beta,Jane Smith,jane.smith@example.com
Team Gamma,Bob Johnson,bob.johnson@example.com
```

**Important Notes:**
- The first row MUST be the header row with column names: `Team Name`, `Team Lead`, `Email`
- Certificates are matched by row index (first data row = 1.jpg, second = 2.jpg, etc.)
- Ensure certificate files are numbered starting from 1
- Email addresses must be valid and accessible


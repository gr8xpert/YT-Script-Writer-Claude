# YouTube Script Writer - n8n Workflow

Automated YouTube script generation workflow using Claude AI, Google Sheets, and Google Docs.

## What It Does

1. **Monitors Google Sheet** - Triggers when new rows are added with Status = "Pending"
2. **Generates Script** - Uses Claude AI to write 32,000-38,000 character YouTube scripts
3. **Creates Google Doc** - Saves the generated script to a new Google Doc with the title
4. **Updates Sheet** - Adds the Google Doc link, character count, and timestamp back to the sheet

## Google Sheet Structure

Your sheet needs these columns:

| ID | Title | MasterPrompt | Status | DocLink | CharCount | GeneratedAt |
|----|-------|--------------|--------|---------|-----------|-------------|
| 1 | Video Title Here | Your prompt/instructions | Pending | (auto-filled) | (auto-filled) | (auto-filled) |
| 2 | Another Title | Another prompt | Pending | | | |

- **ID** - Unique identifier (1, 2, 3...) - required for matching
- **Title** - YouTube video title
- **MasterPrompt** - Instructions for Claude to generate the script
- **Status** - Set to "Pending" to trigger generation
- **DocLink** - Auto-filled with Google Doc URL
- **CharCount** - Auto-filled with character count
- **GeneratedAt** - Auto-filled with timestamp

## Setup Instructions

### 1. Import Workflow

1. Download `n8n-youtube-script-workflow.json`
2. Open n8n
3. Go to **Workflows** → **Import from File**
4. Select the JSON file

### 2. Configure Credentials

You'll need to set up these credentials in n8n:

#### Google Sheets & Docs (OAuth2)
1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select existing
3. Enable Google Sheets API and Google Docs API
4. Create OAuth 2.0 credentials
5. Add credentials in n8n

#### Anthropic API Key
1. Get your API key from [Anthropic Console](https://console.anthropic.com/)
2. In the **"Generate Script with Claude"** node, replace `YOUR_ANTHROPIC_API_KEY` with your key

### 3. Configure Nodes

Update these placeholders in the workflow:

| Node | Field | Replace With |
|------|-------|--------------|
| Google Sheets Trigger | Document ID | Your Google Sheet ID |
| Create Google Doc | Folder ID | Your Google Drive folder ID |
| Update Sheet with Doc Link | Document ID | Your Google Sheet ID |

**How to find IDs:**
- **Sheet ID**: From URL `https://docs.google.com/spreadsheets/d/[SHEET_ID]/edit`
- **Folder ID**: From URL `https://drive.google.com/drive/folders/[FOLDER_ID]`

### 4. Activate Workflow

1. Connect all credentials to their respective nodes
2. Click **"Activate"** to start the workflow
3. Add rows to your sheet with Status = "Pending"

## Customization

### Change Script Length

In the **"Generate Script with Claude"** node, modify the prompt:
```
The script MUST be between 32,000 to 38,000 characters long
```

Change to your desired character count.

### Change AI Model

In the **"Generate Script with Claude"** node, change the model:
```json
"model": "claude-sonnet-4-20250514"
```

Available models:
- `claude-sonnet-4-20250514` (recommended - fast & capable)
- `claude-opus-4-20250514` (most capable)
- `claude-haiku-3-5-20241022` (fastest & cheapest)

### Change Date Format

In the **"Update Sheet with Doc Link"** node, modify GeneratedAt:
```
={{ $now.format('DD, hh:mm') }}
```

Other formats:
- `YYYY-MM-DD HH:mm:ss` → 2024-02-11 19:22:20
- `MMM DD, YYYY hh:mm A` → Feb 11, 2024 07:22 PM

## Workflow Nodes

```
Google Sheets Trigger
        ↓
Filter Pending Rows
        ↓
Generate Script with Claude
        ↓
Extract Script Content
        ↓
Create Google Doc
        ↓
Prepare Doc Data
        ↓
Insert Script to Doc
        ↓
Update Sheet with Doc Link
```

## Troubleshooting

### "Can't determine which item to use"
- Ensure expressions use `.first()` instead of `.item`

### "Requested entity was not found"
- Check that Google Doc/Sheet IDs are correct
- Verify credentials have proper permissions

### Script not generating
- Check Anthropic API key is valid
- Verify API has sufficient credits
- Check timeout settings (default: 10 minutes)

## Requirements

- n8n (self-hosted or cloud)
- Google account with Sheets and Docs access
- Anthropic API key with credits

## License

MIT License - Feel free to use and modify.

## Author

Created with gr8xpert.

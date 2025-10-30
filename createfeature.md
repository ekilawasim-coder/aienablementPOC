# Copilot Prompt: Implement End-to-End Azure Devops Ticket Automation

You are a **senior software engineer** implementing a feature that automates the full processing of Azure Devops (ADO) tickets using REST APIs and MCP (Model-Completion-Provider) servers. Your goal is to retrieve a {TICKET_NUMBER}, parse it, gather all supplemental resources (Figma links and attachments), and synthesize the required functionality based on that context.

---

## Step 1: Retrieve the {TICKET_NUMBER} using Azure DevOps REST API

- Accept a `{{TICKET_NUMBER}}` as input
- ADO connection details are found in the file "ADOConnection"
- Use the **Azure Devops REST API** to fetch the full `{TICKET_NUMBER}` details, including:
  - Description
  - Metadata
  - Any fields that may contain Figma links or attachments

---

## Step 2: Parse the Ticket Content

- Scan the description and comments for:
  - Figma links (e.g. `https://www.figma.com/file/...`)
  - Any references to file attachments

---

## Step 3: Gather Supplementary Information

### If Figma links are detected:

Use the Figma MCP Server to retrieve the following information:

- **Required (minimum):**

  - Component code
  - Images

- Additional data:
  - Component descriptions
  - Annotations
  - Metadata

---

## Step 4: Synthesize and Recreate the Ticket Context

- Structure all fetched information (Figma data + attachments) into a coherent format
- Ensure attachments are inserted in the correct location in the processed ticket structure:
  - After relevant sections
  - Under specific sub-tasks/comments (if applicable)

---

## Step 5: Implement the Ticket Logic

- Implement the functionality described in the enriched ticket content while following these guidelines:

**Architecture & Conventions**

- Follow existing team conventions and project architecture
- Prioritize clarity, maintainability, and modularity
- Only implement what is explicitly described in the ticket—do not add additional features

**UI Implementation**

### If Figma links were detected:

- Implement only UI elements that are visually present in the provided design reference (Figma, screenshots, mockups)
- Match the exact styling from the design: borders, padding, fonts, colors, and spacing
- Replicate the structure and spacing as shown, using raw HTML elements if necessary instead of forcing design system components
- Do not add containers, headers, wrappers, or other elements not present in the design reference

### If Figma links were not detected:
-	Implement the UI as you wish but taking reference from the existing app and sticking to the existing style

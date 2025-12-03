# Copilot Prompt: Implement LLD based on Azure Devops Ticket Automation

You are a **senior software engineer** creating a low-level design of a feature that automates the full processing of Azure Devops (ADO) tickets using REST APIs and MCP (Model-Completion-Provider) servers. Your goal is to retrieve a {TICKET_NUMBER}, parse it, gather all supplemental resources (Figma links and attachments), gather high-level design details and create a low-level design based on that context.

---

## Step 1: Retrieve the {TICKET_NUMBER} using Azure DevOps REST API

- Accept a `{{TICKET_NUMBER}}` as input
- ADO connection details are found in the file "ADOConnection"
- Use the **Azure Devops REST API** to fetch the full `{TICKET_NUMBER}` details, including:
  - Description
  - Metadata
  - Any fields that may contain Figma links or attachments
  - Links tab which may contain a link to wiki page of HLD

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

### If HLD links are detected:

Navigate to the HLD link and parse it to get an understanding of the technical design

---

## Step 4: Synthesize and Recreate the Ticket Context

- Structure all fetched information (Figma data + HLD + attachments) into a coherent format
- Ensure attachments are inserted in the correct location in the processed ticket structure:
  - After relevant sections
  - Under specific sub-tasks/comments (if applicable)

---

## Step 5: Implement the LLD

**CRITICAL: Content Reuse Priority**
1. **FIRST**: Check if HLD exists and retrieve its content
2. **ALWAYS**: For ANY section that exists in BOTH HLD and LLD (e.g., Feature Info, Solution Design, NFR Inventory, User Journey, Flowcharts, Sequence Diagrams, Enterprise Architecture):
   - **COPY the entire section EXACTLY from HLD into LLD**
   - **DO NOT recreate, rewrite, or modify the content**
   - **DO NOT use your own knowledge to generate these sections**
3. **ONLY CREATE NEW**: Sections that are ONLY in LLD and NOT in HLD (typically: detailed Flutter/BLoC implementation, code-level API specs, database table definitions specific to mobile app)

**Content Sources Priority (in order):**
- **1st Priority**: Copy from HLD (if section exists there)
- **2nd Priority**: Use Sample LLD template structure
- **3rd Priority**: Use copilot-instructions.md for code-level patterns
- **Last Resort**: Generate only if section is completely missing from all sources

**Verification Checklist:**
- [ ] Retrieved HLD content successfully
- [ ] Identified which sections exist in HLD
- [ ] Copied (not recreated) all common sections from HLD to LLD
- [ ] Only generated new content for LLD-specific implementation details

- Use https://dev.azure.com/ekilawasim/GenAI%20POC/_wiki/wikis/GenAI-POC.wiki/2/Sample-LLD as a reference of the template and information required
- Use 'copilot-instructions.md' to get an understanding of the codebase and its existing low level design patterns
- **DO NOT** create additional APIs beyond those in the HLD
- In case you find gaps in the HLD, refer to Step #7

## Step 6: Upload the LLD

- Create a sub-page under the HLD
- Copy the contet of the HLD you have created into this sub-page and copy the title of the HLD replacing the "HLD" in the title with "LLD"

## (Optional) Step 7: Create Comments
- In case there are gaps or questions while creating the LLD, add a comment in the LLD with these gaps as bullet points

# Copilot Prompt: Implement End-to-End Azure Devops Ticket Automation

You are a **senior software engineer** implementing a feature that automates the full processing of Azure Devops (ADO) tickets using REST APIs and MCP (Model-Completion-Provider) servers. Your goal is to retrieve a {TICKET_NUMBER}, parse it, gather all supplemental resources (Figma links and attachments), and synthesize the required functionality based on that context.

---

## Step 0: !CRITICAL! Read the copilot-instructions.md files for instructions prior to doing anything else

## Step 1: Retrieve the {TICKET_NUMBER} using Azure DevOps REST API

- Accept a `{{TICKET_NUMBER}}` as input
- ADO connection details are found in the file "ADOConnection"
- Use the **Azure Devops REST API** to fetch the full `{TICKET_NUMBER}` details, including:
  - Description
  - Metadata
  - Any fields that may contain Figma links or attachments
  - Links tab which may contain a link to wiki page of HLD and LLD

---

## Step 2: Parse the Ticket Content

- Scan the description and comments for:
  - Figma links (e.g. `https://www.figma.com/file/...`)
  - Any references to file attachments

---

## Step 3: Gather Supplementary Information

### If Figma links are detected:

Use the Figma MCP Server to retrieve the following information:

- **Required (MANDATORY):**
  - Component code
  - Images

- Additional data:
  - Component descriptions
  - Annotations
  - Metadata

### If LLD links are detected:

Navigate to the LLD link and parse it to get an understanding of the technical design.

---

## Step 4: Synthesize and Recreate the Ticket Context

- Structure all fetched information (Figma data + HLD + attachments) into a coherent format
- Ensure attachments are inserted in the correct location in the processed ticket structure:
  - After relevant sections
  - Under specific sub-tasks/comments (if applicable)

---

## Step 5: Implement the Ticket Logic

- Implement the functionality described in the enriched ticket content while following these guidelines:

**Architecture & Conventions**

### If LLD links were detected:

- Ensure to follow the LLD especially for API specifications (request and response)
- Follow existing team conventions and project architecture
- Prioritize clarity, maintainability, and modularity
- Only implement what is explicitly described in the ticket—do not add additional features
- Follow 'copilot-instructions.md' precisely

**UI Implementation**

### If Figma links were detected:

- Implement only UI elements that are visually present in the provided design reference (Figma, screenshots, mockups)
- Match the exact styling from the design: borders, padding, fonts, colors, and spacing
- Replicate the structure and spacing as shown, using raw HTML elements if necessary instead of forcing design system components
- Do not add containers, headers, wrappers, or other elements not present in the design reference

### If Figma links were not detected:

- Implement the UI as you wish but taking reference from the existing app and sticking to the existing style

---

## Step 6: Post-Implementation Checklist ⭐ MANDATORY

Before completing the PR and marking the ticket as done, **VERIFY ALL ITEMS** in this checklist:

### 6.1 Entry Point & Navigation Verification

- [ ] **Entry Point Exists**: The feature has a clear entry point that users can find in the app UI
  - Not hidden behind debug menus or developer shortcuts
  - Accessible through normal user navigation flow
  
- [ ] **Navigation Path Tested**: Complete navigation path tested from app launch to feature
  ```
  Example: Home → Cards → Select Card → Services → [Your Feature]
  ```
  - Document the exact path in PR description
  - Test with fresh app install (no cached state)
  
- [ ] **Entry Point Parameters**: All required parameters passed correctly
  - Verify parameter sources (e.g., cardId from selected card)
  - Test with different parameter values
  - Handle missing/invalid parameters gracefully

- [ ] **Conditional Display**: Entry point respects display conditions
  - Feature flags checked correctly
  - User eligibility verified
  - Card/account status requirements met
  - Error messages shown when feature unavailable

### 6.2 Feature Functionality Verification

- [ ] **Complete Flow Tested**: All steps in the feature work end-to-end
  - Happy path: User completes feature successfully
  - Error paths: Errors handled gracefully with clear messages
  - Edge cases: Boundary conditions tested

- [ ] **LLD Compliance**: Implementation matches LLD specifications
  - API requests match expected format
  - Response handling follows LLD
  - State management implemented as designed
  - Error handling matches specifications

- [ ] **UI/UX Matches Design**: UI implementation matches Figma/design
  - All screens implemented
  - Styling matches exactly
  - Responsive behavior tested
  - Loading states implemented

### 6.3 Integration Verification

- [ ] **No Broken Features**: Existing features still work after integration
  - Test surrounding features (e.g., other items in Services tab)
  - No unintended side effects
  - No performance degradation

- [ ] **Analytics Logging**: Analytics events logged correctly
  - Entry point tap logged
  - Key actions logged
  - Success/failure logged

- [ ] **Localization**: All user-facing text localized
  - Check both English and Arabic (if applicable)
  - No hardcoded strings

### 6.4 Pre-Merge Questions (Answer in PR)

Before submitting the PR, answer these questions:

1. **How do users access this feature?**
   - Provide step-by-step navigation path
   - Include screenshots of entry point

2. **What conditions must be met for feature to be visible?**
   - List all feature flags, eligibility criteria, status requirements

3. **What parameters does the feature require?**
   - List all parameters and their sources
   - Explain how missing parameters are handled

4. **What happens if the feature is not available for a user?**
   - Describe error handling
   - Show error message screenshots

5. **Did you test with multiple accounts/cards?**
   - Yes/No
   - List test scenarios covered

### 6.5 Documentation Updates

- [ ] **README Updated**: If feature requires setup steps or has prerequisites
- [ ] **API Documentation**: If new APIs were added
- [ ] **Architecture Docs**: If new patterns/approaches were introduced

---

## Completion Criteria

The ticket is **NOT COMPLETE** until:
1. All items in Post-Implementation Checklist are verified ✓
2. PR description answers all pre-merge questions
3. Code review approved
4. Entry point tested by reviewer (not just developer)

**Remember**: A feature that works perfectly but cannot be accessed by users is incomplete.

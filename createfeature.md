# Copilot Prompt: Implement End-to-End Azure Devops Ticket Automation

You are a **senior software engineer** implementing a feature that automates the full processing of Azure Devops (ADO) tickets using REST APIs and MCP (Model-Completion-Provider) servers. Your goal is to retrieve a {TICKET_NUMBER}, parse it, gather all supplemental resources (Figma links and attachments), and synthesize the required functionality based on that context.

---

## Step 0: !CRITICAL! Read the copilot-instructions.md files for instructions prior to doing anything else

- Use read_file tool to read the ENTIRE file from line 1 to the last line
- Check the total line count in the file and ensure ALL lines are read
- Read the full file and understand it completely
- No skipping or partial reads
- Verify you have read to the end by checking for the last line content
- **CRITICAL: If this is a Flutter project with Figma links, you MUST follow the "Figma-to-Flutter Implementation Guidelines" section**
- **Verify understanding of all 13 rules in the Figma guidelines before proceeding with implementation**

---

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
  - Figma links (e.g. `https://www.figma.com/file/...` or `https://www.figma.com/design/...`)
  - Any references to file attachments
  - Extract file key and node ID from Figma URLs
  - Note: Figma URLs format: `https://www.figma.com/design/{fileKey}/{fileName}?node-id={nodeId}`

---

## Step 3: Gather Supplementary Information

### If Figma links are detected:

**STOP and follow this exact sequence:**

1. **MANDATORY FIRST STEP - Visual Reference:**
   - Call `mcp_figma_get_screenshot` with extracted fileKey and nodeId (convert to colon format: "123-456" → "123:456")
   - Review the screenshot to understand the complete UI design

2. **MANDATORY SECOND STEP - Design Tokens:**
   - Call `mcp_figma_get_variable_defs` to get exact colors, fonts, and design tokens
   - Store these for implementation (hex colors, font styles, spacing values)

3. **MANDATORY THIRD STEP - Design Context (if tool is available):**
   - Call `mcp_figma_get_design_context` to get component code and asset URLs
   - Follow as strictly as possible

4. **SVG Asset Management (CRITICAL if design_context provides asset URLs):**
   - Download ALL SVG assets from the `mcp/asset` URLs to `assets/images/` directory
   - **MUST clean EVERY SVG using sed command:**
     ```bash
     sed 's/fill="var(--fill-0, \([^)]*\))"/fill="\1"/g' input.svg | \
     sed 's/stroke="var(--stroke-0, \([^)]*\))"/stroke="\1"/g' > output_clean.svg
     ```
   - Verify cleaning: `cat output_clean.svg | grep "var(--"` should return empty
   - Save only cleaned SVGs with `_clean.svg` or `_fixed.svg` suffix
   - **NEVER use SvgPicture.network()** - Figma URLs expire in 7 days

5. BEFORE writing ANY Flutter code - Data Extraction (CRITICAL):
   - Re-read the "Figma-to-Flutter Implementation Guidelines" section in copilot-instructions.md
   - **EXTRACT POSITIONS:** Parse design_context HTML for `left-[Xpx] top-[Ypx]` patterns and create position list
   - **VERIFY SVG DOWNLOADS:** Confirm all `src="mcp/asset/"` URLs downloaded and cleaned (Step 4 above)
   - Identify the Figma screen dimensions from the screenshot (commonly 375px or 390px width)
   - Calculate container offset if centered: `(screenWidth - containerWidth) / 2`
   - Identify if Figma coordinates are absolute (screen-based) or relative
   - Extract exact hex colors from variable definitions
   - **GATE CHECK:** Cannot proceed to Step 5 until position list created + all SVGs verified clean

### If LLD links are detected:

Navigate to the LLD link and parse it to get an understanding of the technical design.

---

## Step 4: Synthesize and Recreate the Ticket Context

- Structure all fetched information (Figma data + HLD + attachments) into a coherent format
- Ensure attachments are inserted in the correct location in the processed ticket structure:
  - After relevant sections
  - Under specific sub-tasks/comments (if applicable)
- Create a mental model of the implementation before coding

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

### If Figma links were detected (Flutter/Dart projects):

**⚠️ CRITICAL STOP POINT - Re-read Guidelines:**
- **STOP**: Re-read the "Figma-to-Flutter Implementation Guidelines" section in copilot-instructions.md COMPLETELY
- Do NOT proceed until you have verified understanding of ALL 13 rules

**Implementation Requirements:**

1. **Responsive Design (Rule #4):**
   - Get screen width: `final screenWidth = MediaQuery.of(context).size.width;`
   - Calculate container dimensions using ratios of Figma design width (e.g., 375px or 390px)
   - **ALL sizes and positions MUST use screenWidth multipliers**
   - Example: `containerWidth = screenWidth * (figmaElementWidth / figmaScreenWidth)`
   - **NEVER use hardcoded pixel values** in positioned or sized widgets

2. **Position Calculations (Rule #5):**
   - Figma provides ABSOLUTE screen coordinates
   - Convert to relative positions within parent container
   - Calculate container start position if centered
   - Express positions as ratios: `left: containerWidth * (relativeX / figmaContainerWidth)`

3. **Color Accuracy (Rule #9):**
   - Use EXACT hex colors from `mcp_figma_get_variable_defs` response
   - Format: `Color(0xFFHEXVALUE)` (e.g., `Color(0xFF3629B7)`)
   - Do NOT approximate or use Material Design colors unless exact match

4. **Circular Elements (Rule #4 & #6):**
   - Use containerWidth for BOTH width and height to maintain perfect circles
   - Example: `width: containerWidth * 0.15, height: containerWidth * 0.15`
   - Never use containerHeight for circular element heights

5. **SVG Implementation (Rule #2 & #7):**
   - If SVGs were downloaded, add to pubspec.yaml:
     ```yaml
     dependencies:
       flutter_svg: ^2.0.10+1
     flutter:
       assets:
         - assets/images/
     ```
   - Use: `SvgPicture.asset('assets/images/icon_clean.svg', fit: BoxFit.contain)`
   - Preserve aspect ratio using SVG viewBox dimensions

6. **UI Elements:**
   - Implement ONLY what is visually present in the Figma screenshot
   - Match exact styling: borders, padding, fonts, colors, spacing, border radius
   - Do NOT add decorative elements (backgrounds, borders) unless in Figma
   - Do NOT add containers, headers, wrappers not present in the design

7. **Before completing implementation:**
   - Complete the verification checklist (Rule #11) below
   - Test on multiple screen widths to verify responsive behavior
   - Use the implementation pattern from Rule #12 for all positioned elements

**Figma Implementation Verification Checklist (Rule #11):**
- [ ] Called `mcp_figma_get_screenshot` to get visual reference
- [ ] Called `mcp_figma_get_variable_defs` to get design tokens
- [ ] Downloaded and cleaned ALL SVG assets (if provided)
- [ ] Verified no CSS variables remain in SVGs: `grep "var(--" *.svg` returns empty
- [ ] All positions calculated relative to container, not screen
- [ ] All sizes use screenWidth-based multipliers (no hardcoded pixels)
- [ ] Circular elements use same containerWidth ratio for width and height
- [ ] Colors match exact Figma hex values from variable definitions
- [ ] Assets declared in pubspec.yaml (if using images/SVGs)
- [ ] Ran `flutter pub get` to install dependencies
- [ ] Layout maintains proportions on different screen sizes
- [ ] No hardcoded pixel values in production code

### If Figma links were NOT detected:

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

- [ ] **LLD Compliance**: Implementation matches LLD specifications (if LLD provided)
  - API requests match expected format
  - Response handling follows LLD
  - State management implemented as designed
  - Error handling matches specifications

- [ ] **UI/UX Matches Design**: UI implementation matches Figma/design
  - All screens implemented
  - Styling matches exactly
  - Responsive behavior tested on multiple screen sizes
  - Loading states implemented
  - All interactive elements functional

### 6.3 Integration Verification

- [ ] **No Broken Features**: Existing features still work after integration
  - Test surrounding features (e.g., other items in Services tab)
  - No unintended side effects
  - No performance degradation

- [ ] **Analytics Logging**: Analytics events logged correctly (if applicable)
  - Entry point tap logged
  - Key actions logged
  - Success/failure logged

- [ ] **Localization**: All user-facing text localized (if applicable)
  - Check all supported languages
  - No hardcoded strings in UI

### 6.4 Figma Implementation Verification (if Figma designs provided)

**CRITICAL - Verify ALL items if Figma was used:**

- [ ] **No Hardcoded Pixels**: Search codebase for hardcoded pixel values
  - Run: `grep -r "width: [0-9]" lib/` and verify all are using responsive calculations
  - Run: `grep -r "height: [0-9]" lib/` and verify all are using responsive calculations
  - All Container, SizedBox, Positioned widgets use screenWidth ratios

- [ ] **Color Accuracy**: All colors match exact Figma variable definitions
  - Compare implemented colors with `mcp_figma_get_variable_defs` output
  - No approximated or Material Design colors unless exact match

- [ ] **SVG Assets**: All SVG assets properly implemented (if applicable)
  - SVGs downloaded and cleaned (no CSS variables)
  - Assets declared in pubspec.yaml
  - flutter_svg dependency added and installed
  - Using SvgPicture.asset (NOT SvgPicture.network)

- [ ] **Responsive Layout**: Layout tested on multiple screen sizes
  - Test on small screen (iPhone SE, 375px width)
  - Test on medium screen (iPhone 14, 390px width)
  - Test on large screen (iPhone 14 Pro Max, 430px width)
  - All elements maintain correct proportions and positions

- [ ] **No Extra Elements**: Only elements from Figma design are implemented
  - No added borders, backgrounds, or decorations not in design
  - No extra containers or wrappers
  - Layout structure matches Figma hierarchy

### 6.5 Pre-Merge Questions (Answer in PR)

Before submitting the PR, answer these questions:

1. **How do users access this feature?**
   - Provide step-by-step navigation path
   - Include screenshots of entry point

2. **What conditions must be met for feature to be visible?**
   - List all feature flags, eligibility criteria, status requirements
   - If none, state "Always visible"

3. **What parameters does the feature require?**
   - List all parameters and their sources
   - Explain how missing parameters are handled
   - If none, state "No parameters required"

4. **What happens if the feature is not available for a user?**
   - Describe error handling
   - Show error message screenshots
   - If always available, state "N/A"

5. **Did you test with multiple accounts/cards?** (if applicable)
   - Yes/No
   - List test scenarios covered

6. **Figma Implementation Compliance** (if Figma was used):
   - Confirm all 13 Figma-to-Flutter rules were followed
   - Confirm verification checklist completed
   - Attach screenshots comparing Figma design vs implementation

### 6.6 Documentation Updates

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

**Special Completion Criteria for Figma Implementations:**
- Figma Implementation Verification (6.4) completed with all items checked
- Side-by-side screenshots of Figma design vs implementation show exact match
- Responsive behavior verified on at least 3 different screen sizes
- No hardcoded pixel values found in final code

**Remember**: A feature that works perfectly but cannot be accessed by users is incomplete. A Figma implementation that doesn't match the design exactly is also incomplete.

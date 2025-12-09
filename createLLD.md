# Copilot Prompt: Implement LLD based on Azure Devops Ticket Automation

You are a senior software engineer creating a low-level design of a feature that automates the full processing of
Azure Devops (ADO) tickets using REST APIs and MCP (Model-Completion-Provider)
servers. Your goal is to retrieve a {TICKET_NUMBER}, parse it, gather all
supplemental resources (Figma links and attachments), gather high-level design details
and create a low-level design based on that context.

---

## Step 1: Retrieve the {TICKET_NUMBER} using Azure DevOps REST API

• Accept a `{{TICKET_NUMBER}}` as input
• ADO connection details are found in the file "ADOConnection"
• Use the Azure Devops REST API to fetch the full `{TICKET_NUMBER}` details, including:
  ◦ Description
  ◦ Metadata
  ◦ Any fields that may contain Figma links or attachments
  ◦ Links tab which may contain a link to wiki page of HLD

---

## Step 2: Parse the Ticket Content

• Scan the description and comments for:
  ◦ Figma links (e.g. `https://www.figma.com/file/...`)
  ◦ Any references to file attachments

---

## Step 3: Gather Supplementary Information

### If Figma links are detected:

Use the Figma MCP Server to retrieve the following information:

• **Required (minimum):**
  ◦ Component code
  ◦ Images
• **Additional data:**
  ◦ Component descriptions
  ◦ Annotations
  ◦ Metadata

### If HLD links are detected:

Navigate to the HLD link and parse it to get an understanding of the technical
design

### If unable to access HLD or Figma:
Retry and then stop execution
**CRITICAL - DO NOT EXECUTE BASED ON ASSUMPTIONS OF HLD OR FIGMA**

---

## Step 4: Synthesize and Recreate the Ticket Context

• Structure all fetched information (Figma data + HLD + attachments) into a
coherent format
• Ensure attachments are inserted in the correct location in the processed ticket
structure:
  ◦ After relevant sections
  ◦ Under specific sub-tasks/comments (if applicable)

---

## Step 5: Structure the LLD Following the Sample Template

**CRITICAL - FOLLOW SAMPLE LLD STRUCTURE:**

1. Read the Sample LLD (https://dev.azure.com/ekilawasim/GenAI%20POC/_wiki/wikis/GenAI-POC.wiki/2/Sample-LLD) to identify ALL section headings and their numbers
2. Preserve the exact section numbering from the Sample LLD
3. Use Section 4 "Solution Technical Implementation" for platform-specific code details (e.g., Flutter/mobile, Java/backend)
  ◦ Do NOT create a new section for platform implementation
  ◦ Create a subsection of section 4 for the specific platform(s) (e.g. Flutter, Java, etc.)
  ◦ If needed, create platform-appropriate patterns (BLoC for Flutter, Spring services for Java, etc.) as subsections
  ◦ Keep the section number as "4", not "8" or any other number
5. Keep all other section numbers matching the Sample LLD:
  ◦ Section 5: ER Diagram (for database schema - mobile or backend)
  ◦ Section 6: Testing
  ◦ Section 7: Release Monitoring
  ◦ Section 8: Security checklist
  ◦ Section 9: PTB - ART Check list
  ◦ etc.

**CRITICAL - PLATFORM SPECIFIC INSTRUCTION:**

### **Flutter:**
1. Extract all images from the Figma nodes mentioned in the feature (using Figma API and credentials in the file "FigmaConnection")
2. Identify screens by looking at width size of 375 and Type of "Frame" or "Instance"
  ◦ Also identify them visually to see they look like a screen
3. Download these pictures to disk
4. Based on the description of the feature and the sequence diagram in the LLD, look at each image relate it to the functional flow and identifiy if it is in scope
  ◦ If the screen does not match the feature description or is not mentioned in the HLD, mark it as "Clarification Required"
5. Order and number the screens as per the chronological order of the workflow
  ◦ Keep screens with "Clarification Required" in the end
  ◦ When there are multiple variants of a screen (e.g. final result screen). Keep screen numbers as sub-numbers (e.g. success variant is 5a, pending is 5b, failure, is 5c, etc.)
6. For each screen, based on the feature, HLD understanding, copilot-instructions file, and codebase; identify whether a feature is Existing (Re-use), Modified or New

#### Screen Specification Templates

Below are the standardized templates to use when documenting screens in the LLD.

---

##### For MODIFY screens
- Screen number and title
- Actual screen PNG (attach image)
- Specific file paths to modify (use full repository paths, e.g. `src/screens/CheckoutScreen.tsx`)
- Exact changes needed for each component / CTA in the screen. Include the onLoad default screen state if relevant. Cover the following subsections:
  - UI / UX
    - Complete component list needed (specify whether to reuse existing components or create new ones)
  - Business logic
  - API integrations
  - Navigation flows
  - Error handling
  - Integration with SDKs (if applicable)
  - Constraints from HLD (if relevant)
  - Related steps / logic from sequence diagram
 
---

##### For BUILD NEW screens
- Screen number and title
- Actual screen PNG (attach image)
- New file paths to create (use full repository paths, e.g. `src/screens/NewFeatureScreen.tsx`)
- UI / UX
  - Complete component list needed (specify whether to reuse existing components or create new ones)
- Business logic
- API integrations
- Navigation flows
- Error handling
- Integration with SDKs (if applicable)
- Constraints from HLD (if relevant)
- Related steps / logic from sequence diagram

---

##### For REUSE screens
- Screen number and title
- Actual screen PNG (attach image)
- File path of the existing screen (use full repository path)
- Constraints from HLD (if relevant)
- Related steps / logic from sequence diagram
    
### **Java:**
TBC

**VERIFICATION CHECKLIST:**

- [ ] Section headings and numbers match the Sample LLD exactly (https://dev.azure.com/ekilawasim/GenAI%20POC/_wiki/wikis/GenAI-POC.wiki/2/Sample-LLD)
- [ ] No sections skipped or renumbered
- [ ] All Sample LLD sections present (Sections 4–9 etc.)
- [ ] Platform implementation is inside Section 4 "Solution Technical Implementation" as a subsection (do NOT add a new top-level section)

### Flutter (condensed — critical)
- [ ] Extract feature images from Figma using credentials referenced in "FigmaConnection"
- [ ] Identify screens: width = 375 and Type = "Frame" or "Instance" (plus visual verification)
- [ ] Download PNGs and attach to the LLD
- [ ] Map each screen to feature description + sequence diagram; mark non-matching screens "Clarification Required"
- [ ] Order screens in workflow sequence; use sub-numbering for variants (e.g., 5a, 5b)
- [ ] For every screen include:
  - Screen number & title, PNG
  - Repo path(s) (new or modify) — full repository paths
  - Classification: Reuse / Modify / New
  - Short notes: UI components, Business logic, API integrations, Navigation, Error handling, SDKs / HLD constraints

### Other platforms
- [ ] Java: TBC — document placeholder or expected deliverables if details pending
- [ ] If multiple platforms, each gets a Section 4 subsection with the above checks

### Attachments & traceability
- [ ] All images attached and accessible from the LLD
- [ ] Full repository paths used for code changes
- [ ] Clarification items listed separately with owner and required info
- [ ] Simple traceability: screen → sequence diagram step → code file

---

## Step 6: Implement the LLD

### CRITICAL: Content Reuse Priority

**1. FIRST: Check if HLD exists and retrieve its content**

**2. EXACT CONTENT COPY RULES (enforce strictly):**

For the following sections, if they exist in HLD, copy them EXACTLY with NO modifications:

| HLD Section | LLD Section | Copy Rule |
|-------------|-------------|-----------|
| Feature Info | Section 1 | Copy entire section exactly |
| - Delivery Context | Section 1 subsection | No changes |
| - Business Context | Section 1 subsection | No changes |
| - Constraints table | Section 1 subsection | **CRITICAL**: Copy table structure and all content exactly |
| Solution Design | Section 2 | Copy entire section exactly |
| - Epic | Section 2 subsection | No changes |
| - Features | Section 2 subsection | No changes |
| - User Stories | Section 2 subsection | No changes |
| NFR Inventory | Unnumbered after Section 2 | Copy table exactly |
| Enterprise Architecture | Unnumbered section | Copy diagrams/images exactly |
| Business Context Diagram | Part of Enterprise Architecture | Keep image references as-is |
| Sequence Diagrams | Unnumbered section | Copy all diagrams exactly |
| User Journey Flowcharts | As shown in HLD | Copy all flowcharts exactly |

**DO NOT:**
- ❌ Add implementation notes to HLD sections
- ❌ Add platform-specific details to constraints table (e.g., "Flutter Implementation:", "Java Impact:")
- ❌ Modify field names, values, or table structures
- ❌ Add new columns to tables
- ❌ Rewrite or paraphrase content
- ❌ Add code examples to HLD-copied sections
- ❌ Change measurement units or values in NFR Inventory

**ONLY ADD NEW CONTENT IN:**
- ✅ Section 4 (Solution Technical Implementation) - this is where ALL platform-specific implementation details go
- ✅ Any sections that don't exist in HLD at all

**3. Content Sources Priority (in order):**
• 1st Priority: Copy from HLD (if section exists there)
• 2nd Priority: Use Sample LLD template structure
• 3rd Priority: Use copilot-instructions.md for code-level patterns
• Last Resort: Generate only if section is completely missing from all sources

**4. Sequence Diagram Format:**

When copying sequence diagrams from HLD:
- HLD uses: `::: mermaid ... :::` (Azure DevOps wiki syntax)
- Keep the SAME format in LLD: `::: mermaid ... :::`
- **DO NOT convert to:** ` ```mermaid ... ``` ` (markdown code blocks)
- Reason: Both HLD and LLD are on Azure DevOps wiki, use consistent syntax

**5. Cross-Reference Format:**

When referencing items in the document:
- Constraints: "Constraint 1", "Constraint 2" (NOT "Constraint #1", "Constraint #5", or "#1")
- Sections: "Section 4", "Section 6.1" (NOT "Section #4")
- Requirements: Use descriptive text, avoid # symbols
- Reason: # symbol may conflict with issue trackers and markdown rendering

Example:
- ✅ "Feature toggle implementation (Constraint 2)"
- ✅ "See detailed implementation in Section 4"
- ✅ "Regula SDK integration (Constraint 9)"
- ❌ "Feature toggle implementation (Constraint #2)"  
- ❌ "See detailed implementation in Section #4"
- ❌ "Regula SDK integration (#9)"

**6. Scope Boundary - Requirements Only:**

Only implement features explicitly mentioned in:
1. HLD requirements
2. Ticket description
3. Acceptance criteria
4. HLD constraints

**DO NOT ADD:**
- ❌ "Future enhancements" sections
- ❌ "Optional" features or "Nice to have" capabilities
- ❌ "Refresh mechanisms" unless specified in requirements
- ❌ "Push notifications" unless specified in requirements
- ❌ "Deep linking" unless specified in requirements
- ❌ Monitoring/observability tools not mentioned in requirements (e.g., Firebase Performance Monitoring unless in HLD)
- ❌ Additional APIs beyond those in HLD

**If you identify gaps or enhancement opportunities:**
- Note them in Step 8 comments section
- Do NOT implement them in the LLD

**7. Additional Instructions:**

• Use 'copilot-instructions.md' to get an understanding of the codebase and its existing low level design patterns so you can define the low level design
• DO NOT create additional APIs beyond those in the HLD
• In case you find gaps in the HLD, refer to Step 8

**Verification Checklist:**
- [ ] Retrieved HLD content successfully
- [ ] Identified which sections exist in HLD
- [ ] Copied (not recreated) all common sections from HLD to LLD
- [ ] Only generated new content for LLD-specific implementation details (Section 4)
- [ ] No platform-specific details added to HLD-copied sections
- [ ] Constraints table matches HLD exactly
- [ ] Sequence diagrams use ::: mermaid syntax
- [ ] Cross-references use correct format (no # symbols)
- [ ] No enhancements added beyond requirements

---

## Step 6.1: Platform-Specific LLD Requirements (Section 4)

When implementing Section 4 "Solution Technical Implementation" for platform-specific features, include:

### 6.1.0 Feature Toggle Implementation (If Required by Constraints)

**When implementing feature toggles (if required by constraints):**

1. **Use existing application settings infrastructure**
   - DO NOT create new feature flag services/classes/BLoCs
   - Leverage existing config retrieval mechanisms
   - Follow the same pattern as other feature toggles in codebase

2. **Backend Configuration:**
   - Feature flag stored in backend database/config
   - Retrieved via existing app initialization API (e.g., appSettings, application.properties)
   - Single source of truth in backend

3. **Implementation Pattern (adapt to platform):**
   - Search codebase for existing feature toggle examples
   - Use same method/service to retrieve config
   - Apply consistent naming convention

**Example patterns by platform:**
- **Flutter**: `AppSettings.instance.current.getAppConfigs('featureName') == 'true'`
- **Java Spring**: `@Value("${feature.enabled}")` or `configService.getBoolean("feature.enabled")`
- **React**: `useFeatureFlag('featureName')` or `config.features.featureName`
- **Node.js**: `process.env.FEATURE_ENABLED` or `config.get('features.featureName')`

**Files to Modify:**
- Backend: Add config key to database/config file
- Frontend/App: Check config in entry point location using existing mechanism

**DO NOT:**
- ❌ Create new FeatureFlagBloc, FeatureFlagService, or similar classes
- ❌ Use SharedPreferences, AsyncStorage, or local caching for feature flags
- ❌ Add new API endpoints specifically for feature flags
- ✅ Use existing app settings/config infrastructure

---

### 6.1.1 User Access & Navigation ⭐ MANDATORY

**Should be under Flutter sub-section**

**Before specifying new UI components or navigation, search codebase for existing patterns:**

1. **Check for existing features:**
   - Search for similar screens (e.g., "History", "Service list", "Settings")
   - Look for existing entry points in the same location
   - Check existing navigation patterns

2. **If found, reference existing implementation:**
   - ✅ "Use existing ServiceHistoryScreen (already implemented)"
   - ✅ "Add entry point to existing ServicesTab"
   - ❌ "Create new History tile in Popular Services" (check if it already exists first)

3. **Search patterns:**
   - Existing screens that can be reused
   - Existing navigation flows
   - Existing UI components/widgets/views

**Entry Point Specification:**

• **Primary Entry Point Location:**
  ◦ Exact screen name and file path (e.g., `card_service_screen.dart`, `DashboardActivity.java`)
  ◦ Section/tab where entry point appears (e.g., "Services" tab)
  ◦ UI component type (CardActionTileWidget, ListTile, Button, RecyclerView item, etc.)

• **Navigation Flow Diagram:**
```
App Launch → [Screen 1] → [Action] → [Screen 2] → [Action] → [Your Feature]
```

• **Entry Point UI Specifications:**
  ◦ Icon: `IconType.featureName` or asset path
  ◦ Title: "User-Facing Title Text"
  ◦ Subtitle: "Description for user"
  ◦ Position in list (after/before which feature)

• **Required Parameters:**

| Parameter | Type | Description | Required |
|-----------|------|-------------|----------|
| cardId | String | Selected card | Yes |
| accountNumber | String | Card details | Yes |

• **Conditional Display Logic:**
  ◦ Feature flags that control visibility (using existing infrastructure)
  ◦ User eligibility criteria
  ◦ Card/account status requirements
  ◦ Error scenarios when feature unavailable

---

### 6.1.2 Architecture Pattern

**Should be under Flutter sub-section**

Specify the architecture for this feature:

• **State Management**: BLoC/Cubit/Provider/Riverpod (Flutter), MVVM/MVC (Java/Android), Redux/Context (React)
• **Pattern**: Describe pattern with events/states/actions
• **Navigation**: Standard navigation approach for platform

---

### 6.1.3 Project Structure

**Should be under Flutter sub-section**

Define the folder structure according to platform conventions:

**Flutter Example:**
```
lib/
└── features/
    └── [feature_name]/
        ├── bloc/
        │   ├── [feature]_bloc.dart
        │   ├── [feature]_event.dart
        │   └── [feature]_state.dart
        ├── models/
        │   ├── request/
        │   └── response/
        ├── repository/
        ├── views/
        └── widgets/
```

**Java/Spring Example:**
```
com.company.project/
└── feature/
    ├── controller/
    ├── service/
    ├── repository/
    ├── model/
    │   ├── request/
    │   └── response/
    └── exception/
```

---

### 6.1.4 State Management / Business Logic Specifications

**Should be under Flutter sub-section**

Define events/actions and states/responses according to platform pattern:

**Events/Actions:**
```
abstract class FeatureEvent extends Equatable {
  const FeatureEvent();
}

class EventName extends FeatureEvent {
  final String param;
  // Specify all events with parameters
}
```

**States/Responses:**
```
class FeatureState extends Equatable {
  final ApiStatus status;
  final ResponseModel? data;
  final String? errorMessage;
  // Specify all state fields
}
```

**Logic Flow:**
• Event handler flow for each event
• State transitions
• Error handling approach

---

### 6.1.5 Models & Data Layer

**Should be under main section 4 section**

**Request Models:**
• List all request DTOs with fields
• Serialization approach (json_serializable, Jackson, Gson, etc.)

**Response Models:**
• List all response DTOs with fields
• Nested objects structure

**Domain Models:**
• Internal data models
• Validation rules

---

### 6.1.6 Repository & API Integration

**Should be under main section 4 section**

**API Endpoint Completeness:**

When documenting APIs in this section:

1. **Include ALL APIs mentioned in:**
   - HLD sequence diagrams
   - HLD flowcharts
   - HLD constraints (backend integration scope)
   - Ticket acceptance criteria

2. **Mark API modification types:**
   - **NEW**: Brand new endpoint
   - **MODIFIED**: Existing endpoint with changes (e.g., new field added to response)
   - **EXISTING**: No changes, used as-is

3. **API Documentation Format:**

| Endpoint | Method | Purpose | Status | Request | Response |
|----------|--------|---------|--------|---------|----------|
| /api/feature/check | GET | Check eligibility | NEW | `{ cardId }` | `EligibilityResponse` |
| /api/history/list | GET | Get service history | MODIFIED | `{ page }` | `List<ServiceHistoryItem>` |
| /api/common/countries | GET | Get country list | EXISTING | - | `List<Country>` |

**Repository Methods:**
```
class FeatureRepository {
  Future<ResponseModel> methodName(RequestModel request);
  // List all methods with signatures
}
```

**Error Handling:**
• HTTP status code mapping
• Error message extraction
• Retry logic (specify if handled by frontend or backend)

---

### 6.1.7 Integration Checklist

**Should be under main Flutter subsection**

Files requiring modification for entry point:

- `path/to/screen.dart` - Add CardActionTileWidget
- `path/to/routes.dart` - Add route (if using named routes)
- `path/to/constants.dart` - Add string constants

---

### 6.1.8 Testing Approach

• **Unit tests**: Models, Business Logic, Repository
• **Widget/Component tests**: Key UI components, validation
• **Integration tests**: Complete flow
• **Manual test scenarios**: Edge cases

---

## Step 7: Upload the LLD

• Create a sub-page under the HLD in Azure DevOps wiki
• Copy the content of the LLD you have created into this sub-page
• Copy the title of the HLD replacing "HLD" with "LLD" in the title

---

## (Optional) Step 8: Create Comments

• In case there are gaps or questions while creating the LLD, add a comment in the
LLD with these gaps as bullet points
• Note any potential enhancements that are OUT OF SCOPE but could be considered in future

---

## Final Validation Checklist

**Before finalizing, verify:**

### ✅ Structure:
- [ ] Section numbering matches Sample LLD exactly
- [ ] All mandatory sections present
- [ ] No custom sections added outside template
- [ ] Section 4 contains platform-specific implementation
- [ ] Section numbers not skipped or changed

### ✅ Content Reuse:
- [ ] HLD sections copied exactly (not recreated)
- [ ] Constraints table matches HLD format with no additions
- [ ] Solution Design matches HLD exactly
- [ ] NFR Inventory matches HLD exactly
- [ ] Enterprise Architecture / Business Context Diagram matches HLD
- [ ] Sequence diagrams use `::: mermaid` syntax (not ` ```mermaid `)
- [ ] No implementation details added to HLD sections (Sections 1, 2, NFR, etc.)

### ✅ Scope:
- [ ] Only requirements from HLD/ticket implemented
- [ ] No "future enhancements" or optional features added
- [ ] All APIs from HLD documented (including MODIFIED endpoints)
- [ ] No monitoring tools added unless in requirements
- [ ] No push notifications/deep linking unless specified

### ✅ Platform Integration:
- [ ] Feature toggles use existing infrastructure (not new classes/services)
- [ ] Checked for existing UI components before creating new ones
- [ ] Cross-references use correct format ("Constraint 1" not "Constraint #1")
- [ ] Followed codebase patterns from copilot-instructions.md

### ✅ Completeness:
- [ ] All constraints have implementation in Section 4
- [ ] Navigation entry point clearly specified
- [ ] Error handling covered
- [ ] Testing approach defined
- [ ] All sequence diagrams from HLD included

### ✅ Quality:
- [ ] No duplicate content across sections
- [ ] Code examples are platform-appropriate
- [ ] File paths are accurate
- [ ] Component names follow platform conventions

---

## Key Principle

**"Copy from HLD exactly for design sections (1, 2, NFR, diagrams), implement platform-specific details only in Section 4"**

The LLD is NOT a rewrite of the HLD. It is:
- An EXACT COPY of HLD design sections (Sections 1, 2, NFR, Architecture, Diagrams)
- Plus DETAILED platform-specific implementation in Section 4
- Following the EXACT structure of Sample LLD

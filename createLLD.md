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

## Step 5: Structure the LLD Following the Sample Template

**CRITICAL - FOLLOW SAMPLE LLD STRUCTURE:**

1. **Read the Sample LLD (https://dev.azure.com/ekilawasim/GenAI%20POC/_wiki/wikis/GenAI-POC.wiki/2/Sample-LLD) to identify ALL section headings and their numbers**
2. **Preserve the exact section numbering from the Sample LLD**
3. **Use Section 4 "Solution Technical Implementation" for Flutter/mobile code details**
   - Do NOT create a new section for Flutter implementation
   - Replace the backend API examples with Flutter/BLoC patterns
   - Keep the section number as "4", not "8"
4. **Keep all other section numbers matching the Sample LLD:**
   - Section 5: ER Diagram (for mobile database schema)
   - Section 6: Testing
   - Section 7: Release Monitoring
   - Section 8: Security checklist
   - etc.

**VERIFICATION CHECKLIST:**
- [ ] Section numbers match Sample LLD exactly
- [ ] Flutter implementation is in Section 4 (not a new section)
- [ ] No sections are skipped or renumbered
- [ ] All sections from Sample LLD are present (even if adapted for mobile)

---

## Step 6: Implement the LLD

**CRITICAL: Content Reuse Priority**
1. **FIRST**: Check if HLD exists and retrieve its content
2. **ALWAYS**: For ANY section that exists in BOTH HLD and LLD (e.g., Feature Info, Solution Design, NFR Inventory, User Journey, Flowcharts, Sequence Diagrams, Enterprise Architecture):
   - **COPY the entire section EXACTLY from HLD into LLD**
   - **DO NOT recreate, rewrite, or modify the content**
   - **DO NOT use your own knowledge to generate these sections**
3. **ONLY CREATE NEW**: Sections that are ONLY in LLD and NOT in HLD (typically: detailed Flutter/BLoC implementation, code-level API specs, database table definitions specific to mobile app)

**CRITICAL: Sequence Diagrams Coverage**

When HLD contains multiple sequence diagrams (e.g., Part 1/3, Part 2/3, Part 3/3, Use Case 002):
- ✅ DO: Include ALL sequence diagrams from HLD
- ✅ DO: Add implementation details for EACH diagram
- ✅ DO: Document callbacks, retries, and async flows
- ❌ DON'T: Skip "supporting" use cases (they're all mandatory)
- ❌ DON'T: Only implement the "happy path"
- ❌ DON'T: Assume background processes can be omitted

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

**Additional Instruction:**
- Use 'copilot-instructions.md' to get an understanding of the codebase and its existing low level design patterns so you can define the low level design
- **DO NOT** create additional APIs beyond those in the HLD
- In case you find gaps in the HLD, refer to Step #8

---

## Step 6.1: **[NEW]** Flutter-Specific LLD Requirements (Section 4)

When implementing Section 4 "Solution Technical Implementation" for Flutter features, include:

### 6.1.1 User Access & Navigation ⭐ MANDATORY

**Entry Point Specification:**
- **Primary Entry Point Location**:
  - Exact screen name and file path (e.g., `card_service_screen.dart`)
  - Section/tab where entry point appears (e.g., "Services" tab)
  - UI component type (CardActionTileWidget, ListTile, Button, etc.)
  
- **Navigation Flow Diagram**:
  ```
  App Launch → [Screen 1] → [Action] → [Screen 2] → [Action] → [Your Feature]
  ```

- **Entry Point UI Specifications**:
  - Icon: `IconType.featureName` or asset path
  - Title: "User-Facing Title Text"
  - Subtitle: "Description for user"
  - Position in list (after/before which feature)

- **Required Parameters**:
  | Parameter | Type | Source | Required |
  |-----------|------|--------|----------|
  | cardId | String | Selected card | Yes |
  | accountNumber | String | Card details | Yes |

- **Conditional Display Logic**:
  - Feature flags that control visibility
  - User eligibility criteria
  - Card/account status requirements
  - Error scenarios when feature unavailable

### 6.1.2 Flutter Architecture Pattern

Specify the architecture for this feature:
- **State Management**: BLoC/Cubit/Provider/Riverpod
- **Pattern**: BLoC pattern with events/states
- **Navigation**: Standard Navigator.push or named routes

### 6.1.3 Project Structure

Define the folder structure:
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

### 6.1.4 BLoC Layer Specifications

**Events:**
```dart
abstract class FeatureEvent extends Equatable {
  const FeatureEvent();
}

class EventName extends FeatureEvent {
  final String param;
  // Specify all events with parameters
}
```

**States:**
```dart
class FeatureState extends Equatable {
  final ApiStatus status;
  final ResponseModel? data;
  final String? errorMessage;
  // Specify all state fields
}
```

**BLoC Logic:**
- Event handler flow for each event
- State transitions
- Error handling approach

### 6.1.5 Models & Data Layer

**Request Models:**
- List all request DTOs with fields
- Serialization approach (json_serializable, manual)

**Response Models:**
- List all response DTOs with fields
- Nested objects structure

**Domain Models:**
- Internal data models
- Validation rules

### 6.1.6 Repository & API Integration

**Repository Methods:**
```dart
class FeatureRepository {
  Future<ResponseModel> methodName(RequestModel request);
  // List all methods with signatures
}
```

**API Endpoints:**
| Method | Endpoint | Request | Response |
|--------|----------|---------|----------|
| POST | /api/path | RequestModel | ResponseModel |

**Error Handling:**
- HTTP status code mapping
- Error message extraction
- Retry logic

### 6.1.7 UI Layer Specifications

**Screens:**
- Main screen: Purpose, file path, widgets used
- List all screens in the flow

**Widgets:**
| Widget Name | File | Reusable | Purpose |
|-------------|------|----------|---------|
| FormWidget | widgets/form_widget.dart | No | User input form |

**Validation Rules:**
| Field | Rules | Error Message |
|-------|-------|---------------|
| Email | Required, email format | "Enter valid email" |

### 6.1.8 Integration Checklist

Files requiring modification for entry point:
- [ ] `path/to/screen.dart` - Add CardActionTileWidget
- [ ] `path/to/routes.dart` - Add route (if using named routes)
- [ ] `path/to/constants.dart` - Add string constants

### 6.1.9 Testing Approach

- Unit tests: Models, BLoC, Repository
- Widget tests: Key widgets, validation
- Integration tests: Complete flow
- Manual test scenarios

### 6.1.10 Sequence Diagram Implementation ⭐ MANDATORY

For EACH sequence diagram in the HLD:

1. **Copy the complete HLD sequence diagram** (include the full mermaid diagram)
2. **Add implementation mapping table**:

| HLD Sequence Step | Implementation Location | Method/Event Name | Notes |
|-------------------|------------------------|-------------------|-------|
| Step 1: User clicks X | ScreenName.dart | onTapHandler() | Triggers XEvent |
| Step 2: API call Y | Repository | methodY() | Returns Future<Response> |

3. **Document async flows**:
   - Callback handling mechanisms
   - Background process flows
   - Retry logic implementation
   - Error handling for each step

4. **Include ALL use cases from HLD**:
   - Main flow (all parts if split)
   - Alternate flows
   - Error flows
   - Inquiry/status check flows

VERIFICATION:
☐ Every HLD sequence diagram has corresponding implementation section
☐ All sequence steps mapped to code locations
☐ Callback mechanisms documented
☐ Async flows explained
☐ No HLD use case is missing

---

## Step 7: Upload the LLD

- Create a sub-page under the HLD
- Copy the content of the LLD you have created into this sub-page and copy the title of the HLD replacing the "HLD" in the title with "LLD"

---

## (Optional) Step 8: Create Comments

- In case there are gaps or questions while creating the LLD, add a comment in the LLD with these gaps as bullet points

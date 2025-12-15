# Copilot Prompt: Implement End-to-End Azure DevOps Ticket Automation

You are a senior software engineer implementing a feature that automates the full processing of Azure DevOps (ADO) tickets using REST APIs and MCP (Model-Completion-Provider) servers. Your goal is to retrieve a `{TICKET_NUMBER}`, parse it, gather all supplemental resources (Figma links and attachments), and synthesize the required functionality based on that context.

---

## Step 0: !CRITICAL! Read the copilot-instructions.md files for instructions prior to doing anything else

**⛔ MANDATORY FIRST ACTION:**
- Use `read_file` tool to read the ENTIRE file from line 1 to the last line
- Check the total line count in the file and ensure ALL lines are read
- Read the full file and understand it completely
- No skipping or partial reads
- Verify you have read to the end by checking for the last line content
- **CRITICAL**: If this is a Flutter project with Figma links, you MUST follow the "Figma-to-Flutter Implementation Guidelines" section
- Verify understanding of all rules in the Figma guidelines before proceeding with implementation
- **DO NOT BE CREATIVE** - Follow the process exactly as written in copilot-instructions.md

---

## Step 1: Retrieve the {TICKET_NUMBER} using Azure DevOps REST API

- Accept a `{TICKET_NUMBER}` as input
- ADO connection details are found in the file "ADOConnection"
- Use the Azure DevOps REST API to fetch the full `{TICKET_NUMBER}` details, including:
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

**⛔ STOP and follow this exact sequence:**

1. **MANDATORY FIRST STEP - Activate Figma Tools:**
   - Call `activate_figma_code_generation_tools` BEFORE using ANY Figma MCP tools
   - Without this, all Figma tools will be disabled and fail

2. **MANDATORY SECOND STEP - Visual Reference:**
   - Call `mcp_figma_get_screenshot` with extracted fileKey and nodeId (convert to colon format: "123-456" → "123:456")
   - Review the screenshot to understand the complete UI design
   - Save screenshot for visual verification later

3. **MANDATORY THIRD STEP - Design Tokens:**
   - Call `mcp_figma_get_variable_defs` to get exact colors, fonts, and design tokens
   - Store these for implementation (hex colors, font styles, spacing values)

4. **MANDATORY FOURTH STEP - Design Context (if tool is available):**
   - Call `mcp_figma_get_design_context` to get component code and asset URLs
   - Extract ALL positions from HTML (search for `left-[Xpx] top-[Ypx]` patterns)
   - Extract ALL SVG asset URLs (search for `src="mcp/asset/"` patterns)
   - Note design dimensions (screen width, container dimensions)

5. **SVG Asset Management (CRITICAL if design_context provides asset URLs):**
   - Download ALL SVG assets from the `mcp/asset` URLs to `assets/images/` directory
   - **MUST clean EVERY SVG** using sed command:
     ```bash
     sed 's/fill="var(--fill-0, \([^)]*\))"/fill="\1"/g' input.svg | \
     sed 's/stroke="var(--stroke-0, \([^)]*\))"/stroke="\1"/g' > output_clean.svg
     ```
   - Verify cleaning: `grep "var(--" output_clean.svg` should return empty
   - Save only cleaned SVGs with `_clean.svg` or `_fixed.svg` suffix
   - NEVER use SvgPicture.network() - Figma URLs expire in 7 days

6. **BEFORE writing ANY Flutter code:**
   - Re-read the "Figma-to-Flutter Implementation Guidelines" section in copilot-instructions.md
   - Identify the Figma screen dimensions from the screenshot (commonly 375px or 390px width)
   - Plan responsive calculations: all elements must use screenWidth ratios
   - Identify if Figma coordinates are absolute (screen-based) or relative
   - Extract exact hex colors from variable definitions
   - **CRITICAL**: Search for duplicate widgets with grep before implementing

### If LLD links are detected:

Navigate to the LLD link and parse it to get an understanding of the technical design.

---

## Step 4: Synthesize and Recreate the Ticket Context

- Structure all fetched information (Figma data + HLD + attachments) into a coherent format
- Ensure attachments are inserted in the correct location in the processed ticket structure:
  - After relevant sections
  - Under specific sub-tasks/comments (if applicable)
- Create a mental model of the implementation before coding
- **Use `manage_todo_list` tool** to track implementation phases

---

## Step 5: Implement the Ticket Logic

Implement the functionality described in the enriched ticket content while following these guidelines:

### Architecture & Conventions

#### If LLD links were detected:
- Ensure to follow the LLD especially for API specifications (request and response)
- Follow existing team conventions and project architecture
- Prioritize clarity, maintainability, and modularity
- Only implement what is explicitly described in the ticket—do not add additional features
- Follow 'copilot-instructions.md' precisely

### UI Implementation

#### If Figma links were detected (Flutter/Dart projects):

**⚠️ CRITICAL STOP POINT - Re-read Guidelines:**
- **STOP**: Re-read the "Figma-to-Flutter Implementation Guidelines" section in copilot-instructions.md COMPLETELY
- Do NOT proceed until you have verified understanding of ALL rules
- **Answer Phase 0 gate questions** from copilot-instructions.md before writing code

**Implementation Requirements:**

1. **Responsive Design (Rule #4):**
   - Get screen width: `final screenWidth = MediaQuery.of(context).size.width;`
   - Calculate container dimensions using ratios of Figma design width (e.g., 375px or 390px)
   - ALL sizes and positions MUST use screenWidth multipliers
   - Example: `containerWidth = screenWidth * (figmaElementWidth / figmaScreenWidth)`
   - NEVER use hardcoded pixel values in positioned or sized widgets

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

6. **Duplicate Widget Check (Rule #14 - CRITICAL):**
   - **BEFORE implementing ANY text or widget**, run grep search:
     ```bash
     grep -n "widget name or text" filepath
     ```
   - Check Stack render order (last child renders on top)
   - If duplicates exist, ensure you're styling the VISIBLE widget (the one that renders last)

7. **UI Elements:**
   - Implement ONLY what is visually present in the Figma screenshot
   - Match exact styling: borders, padding, fonts, colors, spacing, border radius
   - Do NOT add decorative elements (backgrounds, borders) unless in Figma
   - Do NOT add containers, headers, wrappers not present in the design

8. **Before completing implementation:**
   - Complete the verification checklist (Rule #11) below
   - Test on multiple screen widths to verify responsive behavior
   - Use the implementation pattern from Rule #12 for all positioned elements

**Figma Implementation Verification Checklist (Rule #11):**
- [ ] Activated Figma tools with `activate_figma_code_generation_tools`
- [ ] Called `mcp_figma_get_screenshot` to get visual reference
- [ ] Called `mcp_figma_get_variable_defs` to get design tokens
- [ ] Downloaded and cleaned ALL SVG assets (if provided)
- [ ] Verified no CSS variables remain in SVGs: `grep "var(--" *.svg` returns empty
- [ ] **Searched for duplicate widgets with grep** (Rule #14)
- [ ] All positions calculated relative to container, not screen
- [ ] All sizes use screenWidth-based multipliers (no hardcoded pixels)
- [ ] Circular elements use same containerWidth ratio for width and height
- [ ] Colors match exact Figma hex values from variable definitions
- [ ] Assets declared in pubspec.yaml (if using images/SVGs)
- [ ] Ran `flutter pub get` to install dependencies
- [ ] Layout maintains proportions on different screen sizes
- [ ] No hardcoded pixel values in production code

#### If Figma links were NOT detected:
- Implement the UI as you wish but taking reference from the existing app and sticking to the existing style

---

## Step 6: Create Integration Tests for Visual Verification ⭐ MANDATORY

**⛔ THIS STEP IS MANDATORY - NOT OPTIONAL ⛔**

### 6.1 Create Integration Test File

Create `integration_test/screenshot_test.dart` with these requirements:

1. **Test Structure:**
   - Import: `package:integration_test/integration_test.dart`
   - Import: `package:flutter_test/flutter_test.dart`
   - Set up `IntegrationTestWidgetsFlutterBinding.ensureInitialized()`
   - Create multiple test cases covering different states

2. **Test Coverage:**
   - **Default State**: Screen in initial/default state
   - **Interactive States**: After user interactions (if applicable)
   - **Edge Cases**: Boundary conditions, long text, empty states
   - **Error States**: Error messages, validation failures
   - Minimum 5 test cases per screen

3. **Screenshot Capture:**
   ```dart
   await binding.takeScreenshot('test_case_name');
   ```
   - Use descriptive names: `test_case_1_default`, `test_case_2_after_tap`, etc.
   - Capture FULL screen including navigation bars

4. **Test Execution:**
   - Run: `flutter test integration_test/screenshot_test.dart`
   - Screenshots saved to: `integration_test/screenshots/`
   - Verify all test cases pass

### 6.2 Visual Verification Process (MANDATORY)

**⛔ YOU MUST COMPLETE THIS ITERATIVE PROCESS ⛔**

**Phase 1: Capture Reference Images**
1. Get Figma screenshot (already done in Step 3)
2. Run integration tests to capture Flutter screenshots
3. Ensure screenshots are clear and complete

**Phase 2: Pixel-Level Comparison**

Create Python verification script `integration_test/verify_visual.py`:

```python
from PIL import Image
import sys

def verify_screen(screenshot_path, figma_reference):
    """
    Verify Flutter screenshot matches Figma design
    Returns: List of issues found
    """
    issues = []
    img = Image.open(screenshot_path).convert('RGB')
    width, height = img.size
    
    # 1. TEXT POSITIONING VERIFICATION
    # Search for text elements and verify positions
    # Example: Check that text element is positioned correctly at expected coordinates
    
    # 2. COLOR VERIFICATION
    # Sample pixels at expected locations
    # Verify colors match Figma hex values (with tolerance for anti-aliasing)
    
    # 3. ELEMENT PRESENCE VERIFICATION
    # Check that all expected elements render
    # Count pixels of specific colors to detect missing/incorrect elements
    
    # 4. LAYOUT VERIFICATION
    # Check spacing, alignment, sizes
    # Verify responsive calculations are correct
    
    # 5. DUPLICATE WIDGET DETECTION
    # Check for unexpected duplicate elements
    
    return issues

if __name__ == "__main__":
    screenshot = sys.argv[1]
    issues = verify_screen(screenshot, figma_reference=None)
    
    if issues:
        print("❌ ISSUES FOUND:")
        for issue in issues:
            print(f"  - {issue}")
        sys.exit(1)
    else:
        print("✅ ALL VISUAL CHECKS PASSED")
        sys.exit(0)
```

**Phase 3: Issue Detection (ITERATIVE)**

Run verification script for EACH screenshot:
```bash
cd integration_test/screenshots
python3 verify_visual.py test_case_1_default.png
```

**Document ALL issues found:**
1. **Text Positioning Issues:**
   - Expected position vs actual position
   - Element name and line number in code
   - Tolerance: <10px for text, <5px for icons

2. **Color Issues:**
   - Expected color (Figma hex) vs actual color (RGB)
   - Element name and location
   - Tolerance: Exact match or <5 RGB difference for anti-aliasing

3. **Missing/Wrong Elements:**
   - Expected element vs what's rendered
   - SVG assets not loading, wrong shapes, etc.

4. **Layout Issues:**
   - Spacing incorrect
   - Sizes not responsive
   - Alignment problems

**Phase 4: Fix Issues (ITERATIVE LOOP)**

For EACH issue found:

1. **Grep Search FIRST (Rule #14):**
   ```bash
   grep -n "element name" lib/screens/*.dart
   ```
   - Check for duplicate widgets
   - Identify which widget is visible (Stack order)

2. **Check Figma Reference:**
   - Verify exact specifications from Figma design context
   - Check color definitions
   - Verify positions are relative to container

3. **Apply Fix:**
   - Fix the CORRECT widget (the visible one if duplicates exist)
   - Use responsive calculations (no hardcoded pixels)
   - Match exact Figma specifications

4. **Re-run Tests:**
   ```bash
   flutter test integration_test/screenshot_test.dart --plain-name="Test Case X"
   ```

5. **Re-verify:**
   ```bash
   python3 verify_visual.py test_case_X_updated.png
   ```

6. **Repeat until issue resolved**

**Phase 5: Comprehensive Verification**

After fixing all issues:

1. **Run ALL tests:**
   ```bash
   flutter test integration_test/screenshot_test.dart
   ```

2. **Verify ALL screenshots pass:**
   ```bash
   for screenshot in *.png; do
     python3 verify_visual.py "$screenshot" || exit 1
   done
   ```

3. **Calculate pass rate:**
   - Total tests: X
   - Passing tests: Y
   - Pass rate: Y/X * 100%
   - **REQUIRED: >95% pass rate**

4. **Document known issues:**
   - If any tests fail after max iterations (5-10 attempts)
   - Document as "Known Issues" with:
     * Issue description
     * Why it couldn't be fixed
     * Attempted solutions
     * Acceptable workaround (if any)

**Phase 6: Side-by-Side Comparison**

Create comparison document:

```markdown
# Visual Verification Results

## Test Case 1: Default State
- Figma: [screenshot path]
- Flutter: [screenshot path]
- Status: ✅ PASS / ❌ FAIL
- Issues: [list if any]

## Test Case 2: Interactive State
...

## Summary
- Total Tests: X
- Passed: Y (Z%)
- Failed: W
- Known Issues: [list]

## Known Issues
1. [Issue description]
   - Expected: [spec]
   - Actual: [behavior]
   - Attempts: [what was tried]
   - Status: Documented limitation
```

### 6.3 Success Criteria

**YOU CANNOT PROCEED TO STEP 7 UNTIL:**
- [ ] Integration tests created with minimum 5 test cases
- [ ] All tests execute successfully (no crashes)
- [ ] Visual verification script created
- [ ] **ALL screenshots verified with pixel-level checks**
- [ ] **>95% pass rate achieved OR known issues documented**
- [ ] **All fixable issues FIXED (with proof via re-testing)**
- [ ] Side-by-side comparison document created
- [ ] Known issues (if any) documented with justification

**If pass rate <95% and issues are fixable:**
- GO BACK to Phase 4 and continue fixing
- Do NOT proceed until issues resolved or documented as unfixable

---

## Step 7: Post-Implementation Checklist ⭐ MANDATORY

### 7.1 Entry Point & Navigation Verification

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

### 7.2 Feature Functionality Verification

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

### 7.3 Integration Verification

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

### 7.4 Figma Implementation Verification (if Figma designs provided)

**CRITICAL - Verify ALL items if Figma was used:**

- [ ] **No Hardcoded Pixels**: Search codebase for hardcoded pixel values
  - Run: `grep -E "\b(width|height|left|top|right|bottom):\s*[0-9]" lib/screens/*.dart`
  - Verify all are using responsive calculations
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

- [ ] **Visual Verification Complete** (Step 6):
  - Integration tests created with >5 test cases
  - Visual verification script executed for all screenshots
  - **Pass rate >95% OR known issues documented**
  - Side-by-side comparison shows Figma vs Flutter match
  - All fixable issues fixed with proof via re-testing

### 7.5 Pre-Merge Questions (Answer in PR)

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

5. **Did you test with multiple accounts/cards? (if applicable)**
   - Yes/No
   - List test scenarios covered

6. **Figma Implementation Compliance (if Figma was used):**
   - Confirm all Figma-to-Flutter rules were followed
   - Confirm verification checklist completed
   - **Attach visual verification summary:**
     * Total test cases: X
     * Pass rate: Y%
     * Known issues: [list or "None"]
   - Attach screenshots comparing Figma design vs implementation

7. **Visual Testing Results (if Figma was used):**
   - Integration test pass rate: X%
   - Number of test cases: Y
   - Issues found: Z
   - Issues fixed: W
   - Known issues: [list with justification]

### 7.6 Documentation Updates

- [ ] **README Updated**: If feature requires setup steps or has prerequisites
- [ ] **API Documentation**: If new APIs were added
- [ ] **Architecture Docs**: If new patterns/approaches were introduced
- [ ] **Visual Testing Documentation**: Document test cases and verification approach

---

## Completion Criteria

**The ticket is NOT COMPLETE until:**

1. ✅ All items in Post-Implementation Checklist are verified
2. ✅ PR description answers all pre-merge questions
3. ✅ **Visual verification completed with >95% pass rate OR documented known issues**
4. ✅ Code review approved
5. ✅ Entry point tested by reviewer (not just developer)

**Special Completion Criteria for Figma Implementations:**

- ✅ Figma Implementation Verification (7.4) completed with all items checked
- ✅ **Visual verification (Step 6) completed with ALL phases done**
- ✅ **Integration tests created and passing (>95% pass rate)**
- ✅ **Pixel-level verification script executed for all screenshots**
- ✅ **All fixable visual issues FIXED with proof via re-testing**
- ✅ **Known issues (if any) documented with:**
  - Issue description
  - Why it couldn't be fixed
  - What was attempted (with iteration count)
  - Acceptable tolerance/workaround
- ✅ Side-by-side screenshots of Figma design vs implementation show match (or documented differences)
- ✅ Responsive behavior verified on at least 3 different screen sizes
- ✅ No hardcoded pixel values found in final code

**Remember:**
- A feature that works perfectly but cannot be accessed by users is **incomplete**.
- A Figma implementation that doesn't match the design exactly is also **incomplete**.
- **Visual verification is MANDATORY, not optional** - you must iterate until >95% pass rate or document why issues can't be fixed.

---

## Verification Summary Template

Use this template in your PR description:

```markdown
# Implementation Verification Summary

## Entry Point
- Navigation Path: [Home → X → Y → Feature]
- Parameters Required: [list or "None"]
- Visibility Conditions: [list or "Always visible"]

## Functionality
- ✅ Happy path tested
- ✅ Error handling verified
- ✅ Edge cases covered
- ✅ LLD compliance verified (if applicable)

## Figma Implementation (if applicable)
- ✅ All Figma-to-Flutter rules followed
- ✅ Figma tools activated and used
- ✅ SVGs downloaded and cleaned
- ✅ No hardcoded pixels (verified with grep)
- ✅ Colors match exact Figma values
- ✅ Responsive on 3+ screen sizes

## Visual Verification (if Figma used)
- Total Test Cases: X
- Integration Tests Pass Rate: Y%
- Visual Verification Pass Rate: Z%
- Issues Found: W
- Issues Fixed: V
- Known Issues: [list or "None"]

### Known Issues
1. [Issue description]
   - Expected: [spec]
   - Actual: [behavior]
   - Attempts: [5 iterations tried: approach A, B, C, D, E]
   - Status: Documented limitation due to [reason]
   - Tolerance: Acceptable because [justification]

## Screenshots
### Figma Design
![Figma Screenshot](path/to/figma/screenshot.png)

### Flutter Implementation
![Flutter Screenshot](path/to/flutter/screenshot.png)

### Side-by-Side Comparison
- Test Case 1: ✅ Match
- Test Case 2: ✅ Match
- Test Case 3: ❌ Known issue #1
- Test Case 4: ✅ Match
- Test Case 5: ✅ Match

## Integration
- ✅ No broken features
- ✅ Analytics logging verified
- ✅ Localization complete

## Testing
- ✅ Multiple accounts/cards tested
- ✅ Fresh install tested
- ✅ Navigation verified by reviewer
```

---

## Example: Visual Verification Workflow

**Iteration 1:**
```
Run tests → Capture screenshots → Run verification script
Issues found:
1. Text element centered at Xpx (expected Ypx left-aligned)
2. Elements are wrong color #AAAAAA (expected #BBBBBB and #CCCCCC)
3. Icon missing component
```

**Fix Iteration 1:**
```bash
# Check for duplicates
grep -n "Text element" lib/screens/your_screen.dart
# Found 2 widgets! Navigation bar widget overriding main widget

# Fix navigation bar widget (the visible one)
# Download correct SVG assets from Figma
# Combine icon components into single SVG

# Re-test
flutter test integration_test/screenshot_test.dart
python3 verify_visual.py test_case_1_default.png
```

**Verification Result:**
```
✅ Text: Xpx (expected Ypx) = Zpx offset → ACCEPTABLE (<10px tolerance)
✅ Elements: Correct colors detected
✅ Icon: All components rendering
🎉 ALL ISSUES FIXED - Pass rate: 100%
```

# Daily Compliance Validation Report Format
## Purpose
Format the validation results into a professional daily Teams report with summary metrics and detailed findings.

## Input Data
- reportDate 
- validationResult
- packingListData (array of packing list objects with applicationId and parserModel)

## Report Structure
### 1. Report Header
<h1>🔍 Daily Packing List Compliance Report</h1>
<p><strong>Date:</strong> [Report Date]<br>

### 2. Executive Summary
<h2>Summary</h2>
<ul>
<li><strong>Total Prohibited Items Found:</strong> [count of items where isProhibited=true]</li>
<li><strong>Items Cleared:</strong> [count of items where isProhibited=false]</li>
<li><strong>Parser Models:</strong> [comma-separated list of unique parserModel values from packingListData]</li>
</ul>

### 3. Detailed Findings
Group items by Application ID in HTML table:
<h2>Detailed Findings</h2>
<table>
<thead>
<tr>
<th>Application ID</th>
<th>Parser Model</th>
<th>Feedback</th>
</tr>
</thead>
<tbody>
<tr>
<td>[packingListId]</td>
<td>[parserModel from packingListData where applicationId matches]</td>
<td>[Formatted feedback for all items in this application]</td>
</tr>
</tbody>
</table>

**Feedback Column Format:**
For each item in the application, format based on validation result:

**If isProhibited = true:**
<strong>[Location] Row [rowNumber] Issue:</strong><br>
• <strong>Prohibited item identified</strong> – Product '[description]' with Country of Origin '[countryOfOrigin]', Commodity Code '[commodityCode]', and Type of Treatment '[typeOfTreatment]' matches prohibited combination in database.<br>
• <strong>Matched Rule:</strong> [matchedRule]

**If isProhibited = false:**
<strong>[Location] Row [rowNumber]:</strong><br>
• <strong><span style="color:red">No match found</span></strong> – Product '[description]' with Country of Origin '[countryOfOrigin]', Commodity Code '[commodityCode]', and Type of Treatment '[typeOfTreatment]' does not match any prohibited combination. This item was flagged in the packing list but cleared during validation.
- Separate multiple items within same Application ID with `<br>` (no blank line)
- Order items by rowNumber within each application

### Example Output
**Input:** 
Report Date: "2024-12-23"
validationResult (2 items - 1 prohibited, 1 not prohibited):
[
  {
    "itemId": 101,
    "packingListId": "1734516900001",
    "location": "Page 1",
    "rowNumber": 3,
    "description": "Fresh Mangoes - Kent variety, tree-ripened",
    "commodityCode": "08045000",
    "countryOfOrigin": "GH",
    "typeOfTreatment": "Chilled",
    "isProhibited": true,
    "matchedRule": "Country: GH, Commodity: 08045000, Treatment: Chilled"
  },
  {
    "itemId": 124,
    "packingListId": "1734609600005",
    "location": "Page 2",
    "rowNumber": 1,
    "description": "Guava Puree - Fresh fruit pulp, unsweetened",
    "commodityCode": "08045000",
    "countryOfOrigin": "BR",
    "typeOfTreatment": "Fresh",
    "isProhibited": false,
    "matchedRule": "Not prohibited"
  }
]
packingListData:
[
  {
    "applicationId": "1734516900001",
    "parserModel": "mars-1",
    "submissionDateTime": "2024-12-23 08:15:00",
    "reasonsForFailure": "Prohibited item identified on the packing list in Page 1 row 3."
  },
  {
    "applicationId": "1734609600005",
    "parserModel": "coop-1",
    "submissionDateTime": "2024-12-23 10:30:00",
    "reasonsForFailure": "Prohibited item identified on the packing list in Page 2 row 1."
  }
]

**Output (HTML for Teams):**
<h1>🔍 Daily Packing List Compliance Report</h1>
<p><strong>Date:</strong> 2024-12-23<br>

<h2>Summary</h2>
<ul>
<li><strong>Total Prohibited Items Found:</strong> 1</li>
<li><strong>Items Cleared:</strong> 1</li>
<li><strong>Parser Models:</strong> mars-1, coop-1</li>
</ul>
<h2>Detailed Findings</h2>
<table>
<thead>
<tr>
<th>Application ID</th>
<th>Parser Model</th>
<th>Feedback</th>
</tr>
</thead>
<tbody>
<tr>
<td>1734516900001</td>
<td>mars-1</td>
<td><strong>Page 1 Row 3 Issue:</strong><br>
• <strong>Prohibited item identified</strong> – Product 'Fresh Mangoes - Kent variety, tree-ripened' with Country of Origin 'GH', Commodity Code '08045000', and Type of Treatment 'Chilled' matches prohibited combination in database.<br>
• <strong>Matched Rule:</strong> Country: GH, Commodity: 08045000, Treatment: Chilled</td>
</tr>
<tr>
<td>1734609600005</td>
<td>coop-1</td>
<td><strong>Page 2 Row 1:</strong><br>
• <strong><span style="color:red">No match found</span></strong> – Product 'Guava Puree - Fresh fruit pulp, unsweetened' with Country of Origin 'BR', Commodity Code '08045000', and Type of Treatment 'Fresh' does not match any prohibited combination. This item was flagged in the packing list but cleared during validation.</td>
</tr>
</tbody>
</table>

## Implementation Notes
1. **Calculate Summary Metrics:**
   - Total prohibited: Count where isProhibited=true
   - Items cleared: Count where isProhibited=false
   - Parser models: Extract unique parserModel values from packingListData, display as comma-separated list
2. **Parser Model Mapping:**
   - For each applicationId in validationResult, find matching applicationId in packingListData
   - Extract the parserModel value from the matching packingListData entry
   - Display in the Parser Model column
3. **Group by Application ID:**
   - All items with same packingListId go in one Feedback cell
   - Order items by rowNumber within each application
   - Separate multiple items with `<br>` (no blank line between items)
4. **Feedback Formatting:**
   - Use HTML tags: `<h1>`, `<h2>`, `<strong>`, `<br>`, `<table>`, `<ul>`, `<li>`
   - Use bullet point (•) for both "Prohibited item identified" and "Matched Rule"
   - Do NOT indent "Matched Rule" - it should be at same level as other bullet
   - Use `<br>` to separate multiple items in same application (no blank line)
   - Include full product details for transparency
   - Show matched rule from database for each prohibited item
5. **Teams Rendering:**
   - Teams supports: `<h1>`, `<h2>`, `<strong>`, `<br>`, `<table>`, `<thead>`, `<tbody>`, `<tr>`, `<td>`, `<th>`, `<ul>`, `<li>`
   - HTML entities like `&nbsp;` and emoji render correctly
   - `<ul>` and `<li>` render as plain text but preserve line structure

## Tone
Professional daily operational reporting with clear metrics and actionable details.
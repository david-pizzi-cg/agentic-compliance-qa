# Daily Compliance Validation Report Format with Trend Analysis
## Purpose
Analyze historical validation trends and format the results into a professional daily Teams report with trend insights, summary metrics, and detailed findings.

## Input Data
- reportDate 
- historicalResults (array of 5 validation result objects from past 5 days, including today)
- packingListData (array of packing list objects with applicationId and parserModel)

## Report Structure
### 1. Report Header
<strong>🔍 Daily Packing List Compliance Report</strong><br>
<strong>Date:</strong> [Report Date]<br>
<hr>

### 2. Trend Analysis (NEW)
Analyze the 5-day historical data to identify patterns and provide actionable insights:

<strong>📊 5-Day Trend Analysis</strong><br>
<ul>
<li><strong>Violation Trend:</strong> [Describe if violations are increasing/decreasing/stable. Example: "Violations increased 60% this week (8 today vs. 5 daily average)"]</li>
<li><strong>Repeat Patterns:</strong> [Identify any applicationIds or commodity codes appearing multiple days. Example: "Packing list 1734516900001 appears in 4 of 5 days"]</li>
<li><strong>Category Insights:</strong> [Highlight specific countries or commodity codes with concentration. Example: "Ghana (GH) mangoes (08045000) account for 40% of all violations"]</li>
<li><strong>Priority Actions:</strong> [Provide 1-2 specific recommendations. Example: "Recommend shipper training for repetitive violations"]</li>
</ul>
<br>

**Analysis Guidelines:**
- Calculate daily violation counts from historical data
- Identify applicationIds appearing in multiple days
- Group violations by countryOfOrigin and commodityCode to spot concentrations
- Compare today's count to 5-day average to identify spikes
- Be specific with numbers and percentages
- Keep insights concise (2-3 sentences per bullet)

### 3. Executive Summary
**Note:** Use today's validation data (last item in historicalResults array) for summary metrics.

<strong>Summary</strong><br>
<ul>
<li><strong>Total Ineligible Items Found:</strong> [count of items where isProhibited=true]</li>
<li><strong>Total Ineligible Items Cleared:</strong> [count of items where isProhibited=false]</li>
<li><strong>Total Applications with Ineligible Items:</strong> [count of unique packingListId values where isProhibited=true]</li>
<li><strong>Total Applications Cleared of Ineligible Items:</strong> [count of unique packingListId values where isProhibited=false]</li>
<li><strong>Parser Models with Ineligible Items:</strong> [comma-separated list of unique parserModel values from packingListData where applicationId has items with isProhibited=true]</li>
</ul>

### 3. Detailed Findings
Group items by Application ID in HTML table:
<br>
<strong>Detailed Findings</strong><br>
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
• <strong>Ineligible item identified</strong> – Product '[description]' with Country of Origin '[countryOfOrigin]', Commodity Code '[commodityCode]', and Type of Treatment '[typeOfTreatment]' matches ineligible combination in database.<br>
• <strong>Matched Rule:</strong> [matchedRule]

**If isProhibited = false:**
<strong>[Location] Row [rowNumber]:</strong><br>
• <strong><span style="color:red">No match found</span></strong> – Product '[description]' with Country of Origin '[countryOfOrigin]', Commodity Code '[commodityCode]', and Type of Treatment '[typeOfTreatment]' does not match any ineligible combination. This item was flagged in the packing list but cleared during validation.
- Separate multiple items within same Application ID with `<br>` (no blank line)
- Order items by rowNumber within each application

### Example Output
**Input:** 
Report Date: "2024-12-23"
validationResult (2 items - 1 ineligible, 1 not ineligible):
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
    "reasonsForFailure": "Ineligible item identified on the packing list in Page 1 row 3."
  },
  {
    "applicationId": "1734609600005",
    "parserModel": "coop-1",
    "submissionDateTime": "2024-12-23 10:30:00",
    "reasonsForFailure": "Ineligible item identified on the packing list in Page 2 row 1."
  }
]

**Output (HTML for Teams):**
<strong>🔍 Daily Packing List Compliance Report</strong><br>
<strong>Date:</strong> 2024-12-23<br>
<hr>
<strong>Summary</strong><br>
<ul>
<li><strong>Total Ineligible Items Found:</strong> 1</li>
<li><strong>Total Ineligible Items Cleared:</strong> 1</li>
<li><strong>Total Applications with Ineligible Items:</strong> 1</li>
<li><strong>Total Applications Cleared of Ineligible Items:</strong> 1</li>
<li><strong>Parser Models with Ineligible Items:</strong> mars-1</li>
</ul>
<br>
<strong>Detailed Findings</strong><br>
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
• <strong>Ineligible item identified</strong> – Product 'Fresh Mangoes - Kent variety, tree-ripened' with Country of Origin 'GH', Commodity Code '08045000', and Type of Treatment 'Chilled' matches ineligible combination in database.<br>
• <strong>Matched Rule:</strong> Country: GH, Commodity: 08045000, Treatment: Chilled</td>
</tr>
<tr>
<td>1734609600005</td>
<td>coop-1</td>
<td><strong>Page 2 Row 1:</strong><br>
• <strong><span style="color:red">No match found</span></strong> – Product 'Guava Puree - Fresh fruit pulp, unsweetened' with Country of Origin 'BR', Commodity Code '08045000', and Type of Treatment 'Fresh' does not match any ineligible combination. This item was flagged in the packing list but cleared during validation.</td>
</tr>
</tbody>
</table>

## Implementation Notes
1. **Calculate Summary Metrics:**
   - Total ineligible items found: Count where isProhibited=true
   - Total ineligible items cleared: Count where isProhibited=false
   - Total applications with ineligible items: Count unique packingListId values where isProhibited=true
   - Total applications cleared of ineligible items: Count unique packingListId values where isProhibited=false
   - Parser models with ineligible items: Extract unique parserModel values from packingListData where the applicationId has at least one item with isProhibited=true, display as comma-separated list
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
   - Use bullet point (•) for both "Ineligible item identified" and "Matched Rule"
   - Do NOT indent "Matched Rule" - it should be at same level as other bullet
   - Use `<br>` to separate multiple items in same application (no blank line)
   - Include full product details for transparency
   - Show matched rule from database for each prohibited item
5. **Teams Rendering:**
   - Use `<strong>` for headers instead of `<h1>`/`<h2>` for better rendering in Teams
   - Use `<hr>` for horizontal rule separator after main header
   - Teams supports: `<strong>`, `<br>`, `<hr>`, `<table>`, `<thead>`, `<tbody>`, `<tr>`, `<td>`, `<th>`, `<ul>`, `<li>`
   - HTML entities like `&nbsp;` and emoji render correctly
   - `<ul>` and `<li>` render as plain text but preserve line structure

## Tone
Professional daily operational reporting with clear metrics and actionable details.
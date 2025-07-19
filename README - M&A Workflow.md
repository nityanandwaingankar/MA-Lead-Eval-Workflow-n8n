# Polish M&A Lead Evaluation Workflow (n8n)

## 📘 Overview
This workflow automates lead intake and evaluation for a **Polish M&A advisory firm**. It captures leads via a web form, enriches company data (mocked in current version), applies basic M&A qualification logic, and logs results to a connected Google Sheet for review and scoring.

## 🔗 Components

| Node Name                  | Function |
|---------------------------|----------|
| **Lead Capture Form**     | Webhook-based trigger to collect leads from an embedded form |
| **Normalize Input Fields**| Renames and formats incoming fields (e.g., company name, website) for consistency |
| **Mock Company Size Switch** | Routes lead randomly to simulate different company types for testing |
| **Mock Big Company A/B**  | Injects mock Clearbit-like data for large companies |
| **Mock Small Startup A/B**| Injects mock data for smaller startups |
| **Target Criteria Filter**| Filters leads based on M&A relevance (e.g., Polish location, employee count > 25) |
| **Write to Google Sheet** | Appends or updates leads in a spreadsheet using `Website` as the unique key |

## 🧪 Evaluation Logic

- A **Switch node** randomly simulates big vs small companies (mock testing mode)
- The **Filter node** ensures that only:
  - Companies located in **Poland**, and
  - With **more than 25 employees**
  are forwarded to the output sheet

## 🗃️ Output Fields (in Google Sheets)

- `Company Name`
- `Website`
- `User Message`
- `country`
- `employee count`
- `AI score + justification` (from GPT)
- `pass/fail flag`
- `High-Fit FLAG`

These fields are mapped from either the mock Clearbit nodes or LLM response.

## 🤖 GPT/LLM Integration (Optional)

The workflow includes a system message designed to:
- Evaluate lead fit for a **B2B SaaS investor**
- Return a JSON object that is **immediately ready** to be appended to Google Sheets
- Include a score, justification, and Boolean flags

### Example Output
```json
{
  "company_name": "Acme Inc.",
  "domain": "acme.com",
  "industry": "SaaS",
  "employee_count": 42,
  "country": "Poland",
  "user_message": "Looking to expand funding.",
  "ai_score_and_justification": "85 - Solid traction and B2B SaaS model in Poland.",
  "pass_fail_flag": true,
  "high_fit_flag": true
}
```

## 🔧 Deployment Notes

- 🧪 This version **uses mocked Clearbit data**. Replace with a real API integration to enable production usage.
- ✅ Matching for Google Sheets update is based on `Website`. Ensure this field is:
  - Lowercased
  - Trimmed of whitespace
  - Stable across all runs

## 🚧 Future Improvements

- ✅ Replace mock data with Clearbit or similar enrichment APIs
- ✅ Add full logging and error notifications (e.g., Slack, Email)
- ✅ Automatically email sales team when a lead scores above 80
- ✅ Build in support for fallback country/employee extraction from GPT if Clearbit fails

# Lead Scoring Engine (Webhook API)

A robust n8n workflow that automates lead validation, scoring, and storage through a webhook-based API. Only qualified leads make it through—generic emails, blacklisted domains, and incomplete data are filtered out automatically.

## Overview

This workflow receives lead data via webhook, evaluates lead quality based on email domain type, checks against a blacklist, and stores valid leads in Baserow. It's designed for sales and marketing teams who need to prioritize high-quality leads while filtering out noise.

## How It Works

```
Webhook → Lead Scoring → Blacklist Check → Storage
```

1. **Webhook Entry Point**  
   Receives incoming lead data as JSON via POST request

2. **Lead Scoring Engine**  
   Calculates a quality score based on email domain:
   - Corporate domains (e.g., `user@company.com`) → **+50 points**
   - Generic domains (e.g., Gmail, Yahoo, Outlook) → **+10 points**
   - Validates presence of required fields

3. **Blacklist Verification**  
   Queries Baserow to check if the domain is blacklisted
   - Blacklisted → Reject with `403 Forbidden`
   - Clean → Continue to storage

4. **Data Transformation**  
   Applies final formatting and enrichment logic

5. **Storage in Baserow**  
   Creates new records or updates existing leads

## API Usage

### Endpoint
```
POST https://your-n8n-instance.com/webhook/lead-scoring
```

### Request Payload
```json
{
  "email": "john.doe@corporate.com",
  "linkedin_url": "https://linkedin.com/in/johndoe",
  "company_size": "50-200"
}
```

### Response Codes
- `200 OK` – Lead accepted and stored
- `403 Forbidden` – Domain is blacklisted
- `400 Bad Request` – Missing required fields

## Lead Scoring Logic

| Email Type | Score | Example |
|------------|-------|---------|
| Corporate domain | +50 | `john@acmecorp.com` |
| Generic domain | +10 | `john@gmail.com` |

Required fields: `email`, `linkedin_url`, `company_size`

## Error Handling

- **Blacklisted domains**: Rejected immediately with HTTP 403
- **Invalid data**: Logged for review, workflow terminates gracefully
- **Duplicate leads**: Existing records are updated rather than duplicated

## Setup Requirements

### n8n Nodes Used
- Webhook (entry point)
- JavaScript (scoring and transformation)
- Baserow (data storage and blacklist lookup)
- IF conditional (blacklist routing)

### Baserow Tables
1. **Leads Table**: Stores validated leads with scores
2. **Blacklist Table**: Contains blocked domains

### Environment Variables
Configure these in your n8n instance:
- `BASEROW_API_TOKEN`
- `BASEROW_DATABASE_ID`

## Example Use Cases

- **CRM Integration**: Automatically score and import leads from web forms
- **Lead Enrichment Pipelines**: Pre-qualify leads before sending to sales team
- **Marketing Automation**: Filter out low-quality sign-ups in real-time

## Future Enhancements

- [ ] Add company size scoring multiplier
- [ ] Integrate email validation service
- [ ] Add LinkedIn profile verification
- [ ] Implement rate limiting
- [ ] Add Slack notifications for high-value leads

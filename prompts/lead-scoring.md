# Lead scoring prompt — Basic LLM Chain

Used by the `AI scoring` node in `workflows/01-ai-lead-qualifier.json`.
Model: Google Gemini 3.6 Flash. Output is constrained by the Structured
Output Parser schema at the bottom of this file.

## Prompt (paste into "Prompt (User Message)", with Source set to "Define below")

Paths are flat (`$json.name`, not `$json.body.name`) because the Normalize
Set node runs between the Webhook and this chain and reshapes the item.

```
You are qualifying inbound leads for a commercial roofing company in Manchester.
They take on repairs, replacements and maintenance contracts for business
premises. They do not take on residential work.

Score this lead from 1 to 10 on how worth a callback it is. Weigh commercial
over residential, urgency, evidence of real budget, and clarity of the request.
A vague enquiry with no detail scores low. Active damage at a commercial
property scores high. Residential work scores 1 regardless of urgency.

Set urgency to one of: low, medium, high.
Set qualified to true only if score is 7 or above.
In reason, give one sentence a busy estimator can act on.
In reply, draft a short, warm reply to the customer that references their
specific problem. Acknowledge the enquiry and confirm the team has received it.
Do not promise a timescale. Acknowledge the enquiry and say the team has received it. do not repeat yourself.

Lead details:
Name: {{ $json.name }}
Company: {{ $json.company }}
Email: {{ $json.email }}
Phone: {{ $json.phone }}
Job type: {{ $json.job_type }}
Postcode: {{ $json.postcode }}
Message: {{ $json.message }}
```

## Structured Output Parser — JSON Example

```json
{
  "score": 8,
  "urgency": "high",
  "qualified": true,
  "reason": "Active water ingress causing stock damage at a commercial property, with explicit urgency and a clear timeframe.",
  "reply": "Hi Dave, thanks for getting in touch..."
}
```

## Design notes

- The residential rule is a business rule, not a prompt trick. Every client has
  two or three of these, and eliciting them is half the consulting work.
- `score` must come back as a number so the downstream IF node can compare it
  against the threshold. That is the entire reason the output parser exists.
- The reply constraints exist because a drafted reply goes to a real customer.
  An earlier version of this prompt produced "a member of our team will call you
  shortly" — a promise the business had not agreed to make, on a timescale nobody
  had checked. Banning prices was not enough; the ban has to cover commitments.
- Telling the model its output will be reviewed by a human measurably changes the
  register it writes in.

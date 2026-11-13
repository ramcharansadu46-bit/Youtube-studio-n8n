# YouTube Studio n8n workflow

This folder contains an importable n8n workflow for the approved first phase:

1. Receive a fictional content idea through a webhook.
2. Generate a draft script with Gemini.
3. Fall back to Groq only when Gemini returns a quota/rate-limit response.
4. Score the saved draft with the same provider order.
5. Email the draft and score for human selection.
6. Receive an explicit approval or rejection through a second webhook.

The workflow does not upload to YouTube, render video, send prompts in the background, or call paid providers. API keys are referenced through n8n credentials and are not embedded in the export.

## Setup

- Import `youtube-studio-script-review.json` into n8n.
- Create an HTTP Header Auth credential named `Gemini API Key` with header `x-goog-api-key`.
- Create an HTTP Header Auth credential named `Groq API Key` with header `Authorization` and value `Bearer <your-key>`.
- Create an SMTP credential named `YouTube Studio Review Email`.
- Set the `REVIEW_EMAIL` environment variable in n8n to the verified review recipient.
- Replace the placeholder `REVIEW_BASE_URL` in the email node with the public base URL of the approval webhook.
- Keep both providers on their free tiers with billing disabled, and confirm each provider's current data-retention notice before enabling live requests.

## Input

POST JSON to the `YouTube Studio - Idea Intake` webhook:

```json
{
  "title": "A fictional idea title",
  "brief": "A fictional, rights-cleared description of the video idea",
  "audience": "beginner creators",
  "language": "English"
}
```

The workflow deliberately marks every request as fictional/sample content. Extend the validation node before using real source material.

## Approval

The review email links to the `YouTube Studio - Approval` webhook with `decision=approve` or `decision=reject` and the generated `scriptId`. Approval only records the decision; it does not publish or generate a video.


---
name: classify-contact-form-submission
description: >-
  Classify an inbound contact form message as spam or legitimate with the Bot Butcher
  Classification API, handle the verdict, and keep the returned message_id so the submission can
  be looked up later.
generated: '2026-09-04'
method: generated
source: >-
  openapi/bot-butcher-classification-api-openapi.yml and https://botbutcher.com/documentation
api: bot-butcher:bot-butcher-classification-api
operations:
  - classifyMessage
---

# Classify a contact form submission

Use this when a message has been posted to a contact form and you need a spam verdict before
passing it on to a human inbox, a CRM, or a ticketing system.

## Before you start

- You need a Bot Butcher API key from https://botbutcher.com/get-started. **Keys are per form** —
  the provider's Postman collection states "Each Form has its own key" — so use the key that
  belongs to the form the message came from.
- Base URL is `https://api.botbutcher.com`. The classify operation is a POST to the **root path**,
  not to a named path.
- **Every call is billable and is not reversible.** The first 10 requests each month are free;
  after that a flat $1.99/month applies plus $0.02 (101-1000) or $0.01 (1001+) per request.

## Steps

1. **Build the request body.** `message` is required and is the raw text of the submission. Send
   `domain` — the root domain of the site the form lives on — whenever you can: it is what lets the
   model judge the message in the context of the business, and omitting it costs roughly 6% of
   accuracy. Put anything you want stored alongside the submission in `metadata`.

   ```json
   {
     "message": "The spammy message posted to your contact form.",
     "domain": "my-business-website.com",
     "metadata": {"form": "contact", "received_at": "2026-09-04T18:00:00Z"}
   }
   ```

2. **Call `classifyMessage`** — `POST https://api.botbutcher.com/` with headers
   `Content-Type: application/json` and `x-api-key: <your key>`.

3. **Read the verdict.** A 200 returns `spam` (boolean), `message_id` (string) and `status_code`
   (integer). `spam: true` means the model classified the submission as spam; `spam: false` means
   pass it on to the end user.

4. **Check for a `notes` array.** If the `domain` you sent is not a valid domain, the response
   carries an extra `notes` array (for example `"invalid domain"`). The classification still
   happened, but with lower accuracy — treat it as a signal to fix how you derive the domain, not
   as a failure.

5. **Persist `message_id`.** It is the only handle you will ever have on this submission. Store it
   next to your own record of the form post so `get-classified-message` can retrieve the message
   and its verdict later.

## Failure handling

- **400 Malformed request** — a payload value or a required header is missing. Do not retry
  blindly; fix the body or headers first. Retrying a malformed call wastes a request.
- **401 Unauthorized** — the `x-api-key` header is absent or not valid for this form. Check you
  are using the key issued for the right form.
- **No 429 is documented and no rate-limit headers are published.** If calls start failing or
  slowing, back off exponentially on your own timing rather than waiting for a `Retry-After`
  header, because the API does not send one.

## Rules that matter

- **Do not retry a 200 that you failed to record.** There is no idempotency key on this operation
  (`conventions/bot-butcher-conventions.yml`, `idempotency.coverage: none`), so a repeated POST is
  a second classification, a second stored message and a second charge. If you lose the response,
  log the loss rather than re-submitting.
- **There is no undo.** No published operation deletes or voids a stored message, and no retention
  window is stated. If a submission must not be stored at all, turn off message storage on the
  account before you send it — the provider offers that free, but it is a dashboard setting, not a
  request field.
- **Never send a real message to test.** There is no sandbox, no test key prefix and no dry-run
  flag; every call hits production and is metered.

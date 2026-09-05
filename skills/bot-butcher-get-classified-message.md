---
name: get-classified-message
description: >-
  Retrieve a previously classified contact form submission and its spam verdict from the Bot
  Butcher Classification API using the message_id returned at classification time.
generated: '2026-09-04'
method: generated
source: >-
  openapi/bot-butcher-classification-api-openapi.yml and https://botbutcher.com/documentation
api: bot-butcher:bot-butcher-classification-api
operations:
  - getMessage
---

# Retrieve a classified message

Use this to audit a past verdict — to show a human why a submission was filtered, to reconcile a
CRM record, or to review a disputed classification.

## Before you start

- You need the `message_id` returned by `classifyMessage`. **There is no list or search endpoint**:
  if you did not keep the id, the API cannot find the message for you. The only other way to see
  past messages is to log in at https://botbutcher.com/login.
- Use the same per-form API key that classified the message.
- This only returns something if message storage was enabled on the account at classification
  time. With the free "no message storage" option turned on, nothing is retained to retrieve.

## Steps

1. **Call `getMessage`** — `GET https://api.botbutcher.com/message/{message_id}` with the header
   `x-api-key: <your key>`.

   ```
   curl -X GET \
        -H 'x-api-key: YOUR-API-KEY' \
        'https://api.botbutcher.com/message/8anb591P-76sn1pn-850fknj'
   ```

2. **Read the message record.** A 200 returns `status_code`, `timestamp` (ISO 8601 creation time),
   `message` (the original text), `spam` (the verdict), `domain` (when one was supplied) and
   `metadata` (whatever you sent at classification time).

3. **Use `metadata` to rejoin your own records.** It is returned verbatim, so anything you put
   there at classify time — your form id, your ticket number, your tenant id — comes back and is
   the cleanest way to tie the verdict to your own system.

## Failure handling

- **400 Malformed request** — the `message_id` is missing, or it is unknown. Note that an unknown
  id is a **400, not a 404**, so you cannot tell "bad request" from "no such message" by status
  code alone. Treat a 400 on a well-formed id as "this message is not retrievable".
- **401 Unauthorized** — authentication failed. Confirm the key matches the form the message was
  classified under.

## Rules that matter

- This operation is read-only and safe to retry.
- Retrieval is not listed as a separately priced call on the pricing page, which meters
  classification requests — but the provider publishes no rate limit either, so poll politely.
- If you need to have a stored message erased, that is not an API call. The privacy policy directs
  erasure requests to botbutcher@hillsidelab.com.

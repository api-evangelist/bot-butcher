# Bot Butcher (bot-butcher)

Bot Butcher is an AI-powered spam detection API that uses a fine-tuned large language model to classify contact form submissions as spam or legitimate messages. The service analyzes messages within the context of what each website is about, providing context-aware classification with 99% reported accuracy. It supports multi-tenant architectures and is designed for enterprise scalability across vertical SaaS and website builder platforms.

**URL:** [https://raw.githubusercontent.com/api-evangelist/bot-butcher/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/bot-butcher/refs/heads/main/apis.yml)

## Scope

- **Type:** Index
- **Position:** Consuming
- **Access:** 3rd-Party

## Tags

- Bots
- Spam Detection
- Contact Forms
- AI Classification
- Security

## Timestamps

- **Created:** 2025-01-07
- **Modified:** 2026-04-21

## APIs

### Bot Butcher Classification API

AI-powered spam classification for contact form submissions. Submit form data via POST, receive a JSON classification result (spam or not spam). Context-aware analysis per website. Optionally retrieve messages by message_id.

**Human URL:** [https://botbutcher.com/](https://botbutcher.com/)

#### Tags

- Spam Detection
- Bot Detection
- AI Classification
- Contact Forms

#### Properties

- [Documentation](https://botbutcher.com/)

## Key Features

- Fine-tuned LLM for spam classification
- 99% accuracy against benchmark test
- Context-aware per-website classification
- Multi-tenant support (unlimited websites)
- Optional message storage (Do Not Save mode)
- Optional training opt-out (Do Not Train mode)
- Message retrieval by message_id
- JSON response format
- API Key authentication

## Use Cases

- Contact form spam filtering
- Enterprise SaaS spam protection
- Multi-tenant application security
- Website builder platform integration

## Maintainers

**FN:** Kin Lane

**Email:** info@apievangelist.com

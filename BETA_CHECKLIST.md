# BoostX.AI Beta Test Checklist

Use this checklist before inviting testers.

## Core generation

- `Post about cats` → X / Twitter → Casual
- `Post about space` → Telegram → Professional
- `Post about dogs` → X / Twitter → Viral
- `Post about nature` → Telegram → Professional
- `Post about programming` → X / Twitter → Casual
- `Post about AI` → LinkedIn → Professional

For every result, confirm:

- The topic stays central.
- The selected platform affects the format.
- The selected tone affects the voice.
- There is no reasoning or meta commentary.
- There are no invented personal experiences.
- The output is ready to publish.

## Actions

- Generate
- Copy Post
- Regenerate produces a meaningfully different result.
- Improve preserves the subject and improves wording.
- Clear resets the composer.

## Error cases

- Empty topic
- Very long topic
- Telegram auth missing/invalid
- OpenRouter rate limit
- OpenRouter timeout
- Empty AI response
- Invalid exact-character result
- Beta usage limit reached

## Exact-character test

Try:

- `a 150-character post about cats`
- `a 300-character post about space`

The backend must either return a valid exact-length post or show an honest error. It must never show internal reasoning or a fake fallback post.

## Beta launch rule

Invite testers only after the core generation flow and all three actions (Regenerate, Improve, Copy) have been checked in Telegram.

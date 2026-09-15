---
name: aimodel-browse-ai-model-roster
description: Browse and retrieve AI model株式会社's published roster of AI-generated models and AI talent from the public www.ai-model.jp WordPress REST API.
api: aimodel:aimodel-models-api
operations:
- listModels
- getModel
- listMedia
generated: '2026-09-14'
method: generated
source: openapi/aimodel-models-api-openapi.yml
---

# Browse the AI model roster

AI model株式会社 publishes its AI-generated models as a WordPress custom post type. There is no
developer program, no key and no quota: the collection is anonymously readable at
`https://www.ai-model.jp/wp-json`. Eight models were published on 2026-09-14.

## Before you start

- Base URL: `https://www.ai-model.jp/wp-json`
- Authentication: none. Send no credentials; the write routes reject them anyway.
- Everything here is read-only. There is no operation that commissions, generates or licenses a
  model - that is a quoted creative engagement through https://www.ai-model.jp/contact/.

## Steps

1. **List the roster** — `listModels`: `GET /wp/v2/models?per_page=100`.
   Read `X-WP-Total` for the true count and `X-WP-TotalPages` for the page count. Never page past
   `X-WP-TotalPages`: a page beyond the end returns `400 rest_post_invalid_page_number`, and
   `per_page` outside 1-100 returns `400 rest_invalid_param`.

2. **Trim the payload** — add `_fields=id,slug,title,link,class_list` to return only what you need.
   Verified live: `GET /wp/v2/models?per_page=2&_fields=id,slug` returns
   `[{"id":203,"slug":"asia-004"}, ...]`.

3. **Recover the category** — the grouping term lives ONLY in `class_list` as a CSS class, e.g.
   `models-cat-asia` on `/models/asia-004/`. The `models-cat` taxonomy is not exposed on the REST
   API (`GET /wp/v2/taxonomies` lists only category, post_tag, nav_menu and wp_pattern_category),
   so parse `class_list` rather than looking for a taxonomy endpoint that does not exist.

4. **Fetch one model** — `getModel`: `GET /wp/v2/models/{id}`. An unknown id returns
   `404 rest_post_invalid_id`; take ids from step 1 instead of guessing.

5. **Fetch its imagery** — follow `_links["wp:attachment"]` from the record, or call `listMedia`
   (`GET /wp/v2/media`) and filter. 83 attachments were published on 2026-09-14.

## Error handling

Errors return `application/json` with `{code, message, data.status}` and a Japanese `message`;
this is not RFC 9457. On `400 rest_invalid_param`, read `data.details` for the per-parameter cause
before retrying. See `errors/aimodel-problem-types.yml`.

## Rate limits and retries

None are documented and none are signalled - no `RateLimit-*` or `Retry-After` header was
observed. Be conservative: the host is a marketing site, not an API product. Retries are safe
because every operation is a GET; there is no idempotency key because there is no write surface.

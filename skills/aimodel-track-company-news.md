---
name: aimodel-track-company-news
description: Track AI model株式会社 announcements, funding news and press coverage from the public www.ai-model.jp WordPress REST API, and search its site content.
api: aimodel:aimodel-content-api
operations:
- listNews
- getNews
- search
- listPages
generated: '2026-09-14'
method: generated
source: openapi/aimodel-content-api-openapi.yml
---

# Track AI model company news

AI model publishes no changelog and no status page. Its `news` custom post type is the only dated
stream it maintains: 39 posts as of 2026-09-14, covering funding rounds, campaign launches, media
appearances and partnerships. Treat it as company news, not product change.

## Before you start

- Base URL: `https://www.ai-model.jp/wp-json`
- Authentication: none.
- An RSS mirror exists at `https://www.ai-model.jp/feed/` if you would rather not call the API.

## Steps

1. **Poll for new posts** — `listNews`: `GET /wp/v2/news?per_page=20&orderby=date&order=desc`.
   Keep the newest `date_gmt` you have seen and pass it as `after=<ISO8601>` on the next call to
   return only what is new; `modified_after` catches edits to existing posts.

2. **Read one post** — `getNews`: `GET /wp/v2/news/{id}`. Add `_embed=1` to pull the featured
   image and author inline rather than making a second call.

3. **Search across everything** — `search`: `GET /wp/v2/search?search=<term>`. It spans the public
   content types (59 indexed objects on 2026-09-14) and returns id, title, url and subtype; follow
   the subtype to the matching collection for the full record.

4. **Read the site copy** — `listPages`: `GET /wp/v2/pages?per_page=100`. 12 pages, including the
   contact and careers flows and four campaign landing pages.

## Notes and limits

- `careerinfo` and `notification` are registered and readable but published zero records on
  2026-09-14 - an empty array is the correct answer there, not an error.
- Titles and content are Japanese, and slugs are percent-encoded Japanese. Decode the slug before
  displaying it.
- Pagination, sparse fields, errors and the absent rate-limit signalling behave exactly as in
  `conventions/aimodel-conventions.yml`.

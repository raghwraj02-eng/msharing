# AutoDeploy Platform (static website MVP)

A mobile-friendly one-page app that deploys a **fixed, owner-configured GitHub repository** as a static HTML/CSS/JS website to Netlify, Vercel, or both. Users enter a site name; they do not submit arbitrary repository URLs.

## Important scope
- This MVP deploys static sites that have `index.html` in the root of the selected website folder.
- It does not run untrusted build scripts, Node servers, databases, or mobile apps.
- The source repository is fixed in server environment variables. For this project it defaults to `raghwraj02-eng/msharing` and branch `main`.
- If that repository does not contain a static site, set `SOURCE_DIR` to a folder containing a built static website, or use a repository containing the actual website output. The current `msharing` repository may be an application/docs repository rather than a ready-to-publish static site; inspect its files before using it as a template.
- A name can be available in your account but already claimed globally on the hosting provider. The provider's create operation is the final availability check.

## Deploy to Vercel
1. Push this project to a **separate GitHub repository** (do not overwrite your msharing source repository unless that is intentional).
2. Import the AutoDeploy app into Vercel.
3. Add Environment Variables:
   - `GITHUB_REPOSITORY=raghwraj02-eng/msharing`
   - `GITHUB_BRANCH=main`
   - `SOURCE_DIR=` (optional; folder containing index.html)
   - `NETLIFY_AUTH_TOKEN` (if using Netlify)
   - `VERCEL_TOKEN` (if using Vercel)
   - `VERCEL_TEAM_ID` (optional for a Vercel team)
   - `DEPLOY_SECRET` (recommended: long random value)
4. Redeploy after adding environment variables.
5. Test with a name you are happy to create in your provider account.

## Local setup
```bash
cp .env.example .env.local
# fill provider tokens and source settings
npm install
npm run dev
```
Open http://localhost:3000.

## Tokens and abuse prevention
- Never commit `.env.local` or put provider tokens in browser variables.
- The API checks `DEPLOY_SECRET` when configured. The UI has an optional access-key field enabled with `NEXT_PUBLIC_REQUIRE_DEPLOY_SECRET=true`; set both variables if using that field.
- Before public launch, add real authentication, persistent per-user rate limits, deployment quotas, logging, and an admin-only management panel. A shared secret alone is not enough for a public service.
- API keys are used only server-side.

## API
`POST /api/deploy` JSON: `{ "name": "my-site", "platform": "netlify" | "vercel" | "both" }`
Optional header: `x-deploy-secret: <DEPLOY_SECRET>`.

`GET /api/check-name?name=my-site` performs best-effort account-level checks. Provider create operations remain the definitive availability check.

## Hosting notes
- Netlify uses its REST API to create a site and upload the prepared ZIP.
- Vercel uses its file-upload and deployment APIs for static files.
- Provider account permissions, global name availability, API limits, deployment limits, and plan terms apply. If the repository requires a framework build, compile it to static output first and set `SOURCE_DIR` to that output folder.

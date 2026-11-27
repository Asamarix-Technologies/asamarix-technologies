# Asamarix Technologies

A buildless company website. Edit the content in HTML and the presentation in a single CSS file.

```text
asamarix-technologies/
├── .gitignore
├── README.md
└── public/
    ├── index.html
    ├── 404.html
    ├── _headers
    └── assets/
        └── site.css
```

## Local development

Run from the project root, then open http://127.0.0.1:8000/.

```sh
python -m http.server 8000 --bind 127.0.0.1 --directory public
```

Use the Python server for layout and navigation. Cloudflare Pages interprets `_headers` on deployed static responses.

## GitHub and Cloudflare Pages

1. Create an empty GitHub repository owned by the Asamarix Technologies organization. Use your personal GitHub account and its verified or GitHub-provided noreply email when committing.
2. From the extracted directory, make the first commit and push it. Substitute the repository URL before running the remote command.

   ```sh
   git init -b main
   git add .
   git commit -m "Start Asamarix Technologies website"
   git remote add origin YOUR_GITHUB_REPOSITORY_URL
   git push -u origin main
   git switch -c dev
   git push -u origin dev
   ```

3. Connect the repository to Cloudflare Pages with the Git integration. Set production branch to `main`, framework preset to None, build command blank, and build output directory to `public`. Restrict the integration to this repository. Enable previews for `dev` or your feature branches. Use [Cloudflare Access for preview deployments](https://developers.cloudflare.com/pages/configuration/preview-deployments/) if you want gated previews.
4. Review the preview before attaching the purchased domain under the Pages project's Custom domains settings. Use HTTPS and enable HTTP-to-HTTPS redirects on the custom domain.
5. Check the actual headers and 404 status on both the preview and custom domain. In the commands below, substitute the relevant host.

   ```sh
   curl -sSI https://YOUR_HOST/
   curl -sSI https://YOUR_HOST/assets/site.css
   curl -sSI https://YOUR_HOST/no-such-page
   ```

   Expect the CSP, HSTS, clickjacking, MIME-sniffing, referrer, and permissions headers. Check that the CSS loads, the unknown path returns 404, and the browser console shows no CSP violations. Cloudflare Pages applies [custom headers to static assets](https://developers.cloudflare.com/pages/configuration/headers/). The `_headers` configuration targets Cloudflare Pages.

## Publishing content

- Confirm the founder copy and provisional A/M monogram before launch.
- Replace the consultation notice in `public/index.html` with an email link once the business inbox is confirmed. An email link opens the visitor's mail client. The site itself receives no submission.
- Add project articles to the Projects section as work becomes public. Link to separate project deployments when they have their own runtimes.
- Keep secrets, private project material, and working files outside `public/`. Only `public/` is deployed. The ignore file helps avoid common local credential files but cannot detect secrets committed under other names.

## Security design

The public site serves static HTML and CSS. It has no executable browser scripts, forms, authentication, sessions, database, server-side URL fetching, or application code that parses visitor input. This removes the application paths that would otherwise require CSRF defenses, SSRF controls, SQL and command injection prevention, or XML parser hardening. The CSP also blocks scripts, embedded objects, outgoing browser fetches, and form submissions. The HSTS policy applies to each host individually.

If a future consultation form needs an endpoint, validate types, lengths, and allowed fields on the server. Apply context-aware output encoding, use structured APIs for email or storage, and rate-limit submissions. If introducing cookie-authenticated state changes, add CSRF protections. If introducing server-side outbound requests, restrict destinations and egress. Keep preview and production application credentials separate if any are introduced. Any Pages Function added later must set security headers on its own responses.

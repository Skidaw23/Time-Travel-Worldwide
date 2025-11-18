# Embedding the Globe App in Notion via Vercel

## Purpose
GitHub Pages adds frame-blocking headers (e.g., `X-Frame-Options`) that prevent Notion from rendering the site inside an iframe, falling back to a plain link. Deploying on Vercel with a custom Content Security Policy (CSP) lets Notion embed the interactive globe safely.

## Quick Start (Vercel Deployment)

1. **Fork or clone** this repository
2. **Sign up** at [Vercel](https://vercel.com) (free tier available)
3. **Import your repository**:
   - Connect your GitHub account
   - Select the Time-Travel-Worldwide repository
   - Vercel auto-detects the static site (no build required)
4. **Deploy**: Click "Deploy" — Vercel reads `vercel.json` automatically
5. **Get your URL**: e.g., `https://time-travel-worldwide.vercel.app`

## Embedding in Notion

1. In your Notion page, type `/embed` and select **Embed** block
2. Paste your Vercel URL (e.g., `https://time-travel-worldwide.vercel.app`)
3. Press Enter — Notion displays the interactive globe inline

## How It Works

### The Problem
- **GitHub Pages** enforces `X-Frame-Options: DENY` or similar headers
- Browsers block iframe embedding from external origins (like Notion)
- Users see a fallback link instead of the live app

### The Solution
The `vercel.json` configuration:
```json
{
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        {
          "key": "Content-Security-Policy",
          "value": "frame-ancestors https://www.notion.so https://notion.so https://*.notion.so https://*.notion.site;"
        }
      ]
    }
  ]
}
```

**Breakdown**:
- `Content-Security-Policy: frame-ancestors https://www.notion.so https://notion.so https://*.notion.so https://*.notion.site;`  
  Allows the site to be embedded in:
  - Main Notion domain (`https://www.notion.so`)
  - Notion domain without www (`https://notion.so`)
  - All Notion workspace subdomains (`https://*.notion.so`)
  - All Notion site subdomains (`https://*.notion.site`)
  
- **X-Frame-Options is NOT set** — the obsolete `ALLOW-FROM` directive has been removed as it doesn't work with modern browsers. The CSP `frame-ancestors` directive is the modern, standardized approach.

## Security Considerations

- **Restrictive whitelist**: Only Notion domains can frame this app
- **No runtime changes**: The HTML/CSS/JS remains unchanged
- **No secrets exposed**: This is a static client-side app with no backend or credentials
- **Safe for public embedding**: The globe uses static data and Three.js rendering only

## Vercel vs GitHub Pages

| Feature | GitHub Pages | Vercel |
|---------|-------------|---------|
| Cost | Free | Free tier available |
| Custom headers | No | Yes (via `vercel.json`) |
| Notion embedding | Blocked | Allowed with CSP |
| Deployment | Push to `gh-pages` branch | Auto-deploy on push |
| Custom domains | Supported | Supported |
| Build step | Limited | Flexible |

## Alternative Platforms

If you prefer not to use Vercel, other platforms with custom header support include:
- **Netlify** (`_headers` file or `netlify.toml`)
- **Cloudflare Pages** (Headers configuration)
- **Render** (Custom headers in `render.yaml`)

Example for Netlify (`_headers` file):
```
/*
  Content-Security-Policy: frame-ancestors https://www.notion.so https://notion.so https://*.notion.so https://*.notion.site;
```

## Troubleshooting

### Notion shows a link instead of embedding
- Verify your Vercel deployment is live
- Check browser console for CSP/frame errors
- Ensure you used the Vercel URL (not GitHub Pages URL)

### Security warnings in browser console
- No security warnings should appear with the current configuration
- The `Content-Security-Policy: frame-ancestors` directive is the modern standard approach
- `X-Frame-Options: ALLOW-FROM` has been removed as it's obsolete and not supported by modern browsers

### Want to restrict to your specific Notion workspace only?
Edit `vercel.json` to:
```json
{
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        {
          "key": "Content-Security-Policy",
          "value": "frame-ancestors https://your-workspace.notion.site;"
        }
      ]
    }
  ]
}
```
Replace `your-workspace` with your actual Notion workspace subdomain.

## Testing Locally

To test the CSP behavior locally (requires a local server):

1. Install a simple HTTP server:
   ```bash
   npm install -g http-server
   ```

2. Serve with custom headers:
   ```bash
   http-server -p 8080 --cors
   ```

3. Create a test HTML file with an iframe:
   ```html
   <!DOCTYPE html>
   <html>
   <body>
     <iframe src="http://localhost:8080" width="800" height="600"></iframe>
   </body>
   </html>
   ```

Note: Local testing won't perfectly replicate Notion's iframe behavior, but Vercel's preview deployments make testing easy.

## Maintenance

- **Updating the app**: Just push changes to your repo — Vercel auto-deploys
- **Monitoring**: Vercel dashboard shows deployment status and analytics
- **Rollback**: Vercel keeps deployment history for easy rollbacks

## Additional Resources

- [Vercel Documentation](https://vercel.com/docs)
- [CSP frame-ancestors Directive](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
- [Notion Embed Block Guide](https://www.notion.so/help/embed-and-connect-other-apps)
- [X-Frame-Options Header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options)

## License

This embedding configuration inherits the same license as the main project. The `vercel.json` file is provided as-is for deployment convenience.

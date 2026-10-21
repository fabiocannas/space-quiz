# Orbital - A space exploration quiz

Orbital is a ten-question space quiz with an animated orbital scene, instant
answer feedback, score and progress tracking, and a final mission summary.
Review missed answers and their explanations, then replay for a fresh attempt.
The interface uses a consistent dark theme regardless of system preferences, with reduced motion, native keyboard
navigation, and A-D answer shortcuts.

**Play online:** https://www.fabiocannas.com/space-quiz/

## Run locally

Clone or download this repository and open `index.html` in a modern browser.
From PowerShell in the repository directory:

```powershell
Start-Process .\index.html
```

All HTML, CSS, JavaScript, and SVG artwork are embedded in `index.html`.
There are no dependencies, installation steps, build tools, remote assets, or
server requirements; the quiz works directly over `file://` and offline.
Scores and answer history last only for the current page load.

## Deployment

GitHub Pages uses **GitHub Actions** as its source in the repository's
**Settings > Pages**. The workflow in `.github/workflows/pages.yml` checks that
`index.html` is nonempty and packages it as the Pages artifact. No compilation
is needed, and only `index.html` is published, not repository metadata or docs.

Pull requests targeting `main` prepare and upload the artifact without deploying.
Pushes to `main`, including merged pull requests, deploy it to GitHub Pages.
To redeploy manually, select **Deploy website to GitHub Pages** in **Actions**,
choose **Run workflow**, and use the `main` branch.

Deployment runs use the `github-pages` environment and the workflow's
`GITHUB_TOKEN` with Pages and OpenID Connect permissions; no personal access
token or additional secret is required.

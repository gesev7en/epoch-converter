# EPOCH Converter

A lightweight service that converts UNIX epoch timestamps to human-readable date/time and calculates a rule-based deadline from the input timestamp.

## Features

- Convert seconds or milliseconds epoch timestamps
- Shows local time, UTC, ISO 8601, day of week, week of year, day of year
- **Calculated Deadline**: "Last day of March, 2 years from the input timestamp"
- Shows delta from input and delta from now for the calculated deadline
- Copy-to-clipboard button (copies epoch + datetime + deadline)
- Persistent input field — stays visible after conversion
- `GET /health` endpoint for health checks

## Customising the Rule

Edit `public/index.html`, find the `RULE_DESCRIPTION` constant and `calcDeadline()` function:

```js
const RULE_DESCRIPTION = 'Last day of March, 2 years from the input timestamp (end of day, UTC)';

function calcDeadline(inputDate) {
  const targetYear = inputDate.getUTCFullYear() + 2;
  const deadline = new Date(Date.UTC(targetYear, 2, 31, 23, 59, 59));
  return deadline;
}
```

Change the logic to any rule you need, e.g.:
- "First day of Q4 next year": `new Date(Date.UTC(year+1, 9, 1))`
- "90 days after input": `new Date(inputDate.getTime() + 90*86400000)`
- "Last Friday of the month": custom loop

## Deploy to fly.io

```bash
# Install fly CLI: https://fly.io/docs/hands-on/install-flyctl/
npm install
fly launch --no-deploy     # creates the app, uses fly.toml
fly deploy                 # builds Docker image and deploys
```

## Deploy to Kubernetes

```bash
# Build and push your image first
docker build -t your-registry/epoch-converter:latest .
docker push your-registry/epoch-converter:latest

# Edit k8s.yaml: replace 'your-registry/epoch-converter:latest' and host
kubectl apply -f k8s.yaml
```

## Run locally

```bash
npm install
npm start
# → http://localhost:8080
```

## Tech stack

- Node.js + Express (static file server only)
- Vanilla JS, no frameworks, no build step
- Single HTML file frontend
- Docker-ready, ~256 MB RAM at runtime

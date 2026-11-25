# RainScope — Sri Lanka Rainfall Prediction frontend

A responsive, dependency-free frontend for the Sri Lanka Rainfall Prediction group project. The application estimates nothing on its own: it displays clearly marked sample values until a trained model's API is connected. The 25 district names are a frontend list, not extracted from the project's CSVs. The demo amounts and historical chart are synthetic and not measurements or predictions. The rainfall intensity bands are presentation-only (0, <5, <25, <50, and ≥50 mm), not official meteorological warning categories.

## Run locally

Requires Node.js 18+; no package installation is necessary.

```sh
cd frontend
npm run dev
```

Open http://localhost:5173 in a browser. Alternatively use `python3 -m http.server 5173 --bind 127.0.0.1` in this directory. Do not open `index.html` directly using a file:// URL, because browsers restrict module loading.

## Features

- Next-day rainfall display, using Sri Lanka local dates (Asia/Colombo).
- District selector (25 districts), rainfall category, province and prediction source.
- Seven-/fourteen-day illustrative history, regional explorer, CSV export, responsive layout, keyboard-accessible controls and persistent light/dark theme.
- No external assets, packages, tracking, or keys.

## Connect the ML backend

The actual repository's API and model input schema could not be inspected. The following contract is a proposal for the frontend/backend boundary, not a claim about an existing API. Coordinate with the ML team; they should obtain the required weather features for the selected district/date and apply their trained preprocessing and model pipeline on the backend. If their actual interface differs, modify `generate()` and `parseResponse()` in `app.js`.

Set `predictionEndpoint` in `config.js`, for example `http://localhost:8000/api/predict`. Setting an endpoint disables synthetic predictions immediately. No API errors fall back to demo values. Configure CORS on the backend for the frontend origin `http://localhost:5173`, or serve them under the same origin. Never put API secrets in frontend files.

Proposed POST JSON request (example only):

```json
{"location":"Colombo","date":"2026-11-26"}
```

Proposed successful JSON response (example only):

```json
{
  "location":"Colombo",
  "date":"2026-11-26",
  "rainfall_mm":18.4,
  "model":"Ensemble regression",
  "history":[{"date":"2026-11-25","rainfall_mm":12.0}]
}
```

Only `rainfall_mm` (finite, nonnegative number) is required. If `location` and `date` are supplied, they must match the request. Model name and historical measurements are optional. History must use YYYY-MM-DD dates and nonnegative millimeter values; absent history is never fabricated in API mode. The API should return an appropriate non-2xx response for an unsupported location/date or unavailable weather features. The project should confirm district identifiers, available model features, forecast horizon and evaluation methodology before deployment. For an actual public deployment use a production static host/reverse proxy, HTTPS, and appropriate backend security controls.

## Git integration

This folder is designed to be added to a fresh feature branch without editing the team's existing files. Check for an existing `frontend/` directory or `feature/rainfall-ui` branch before copying. If one already exists, coordinate or pick a different path/branch; don't overwrite teammates' work.

```sh
git clone https://github.com/navodsamarakoon/Sri-Lanka-Rainfall-Prediction.git
cd Sri-Lanka-Rainfall-Prediction
git switch -c feature/rainfall-ui
# Copy the supplied frontend folder into this repo.
git add frontend/
git diff --cached --stat
git commit -m "feat: add RainScope rainfall prediction dashboard"
git push -u origin feature/rainfall-ui
```

Pushing requires GitHub write permission. If unavailable, fork the repository and push to your fork, then open a pull request. Do not force-push or commit credentials.

## Checks

```sh
npm run check
```

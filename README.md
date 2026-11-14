# skillpath-ai.# SkillPath AI

SkillPath AI is a career-readiness and learning planner for students. It compares a learner’s recorded skills with requirements for a selected career, identifies gaps, and turns those gaps into an adaptive learning roadmap. Gemini-powered agents provide profile analysis, career guidance, roadmap recommendations, progress feedback, and mentor responses.

**Live app:** [skillpath-ai-rta.ai.studio](https://skillpath-ai-rta.ai.studio)

## Features

- Google sign-in with Firebase Authentication.
- Learner profile with education, projects, certifications, target role, and learning preferences.
- Weighted skill-gap analysis and readiness scoring based on a built-in career and skill catalog.
- Career matching across Full Stack Developer, Frontend Developer, Backend Developer, and Data Analyst tracks.
- Learning roadmaps with stages, activities, practical exercises, and curated learning resources.
- Assessments with topic-level results and adaptive recovery, practice, or progression recommendations.
- Progress tracking, resource bookmarks, and an AI mentor grounded in the learner’s profile and recorded results.
- Six server-side Gemini agents: Profile Analysis, Skill Gap Analysis, Career Matching, Learning Recommendation, Progress Evaluation, and AI Mentor.
- Firebase/Firestore persistence for signed-in users, plus local and demo workspace modes.
- Responsive React interface with charts, motion, and a Three.js background.

## Tech stack

- React 19, TypeScript, Vite 8
- Express 4 and `tsx` for the API server and local development
- Google GenAI SDK (`@google/genai`) with Gemini models
- Firebase Authentication and Cloud Firestore
- Tailwind CSS 4, Motion, Three.js, Recharts, and Lucide React
- Bun lockfile is included; npm scripts are also provided

## Run locally

### Prerequisites

- Node.js (use a current LTS release)
- A Firebase project with Google Authentication and Cloud Firestore enabled
- A Gemini API key for AI agent features

### 1. Install dependencies

```bash
npm install
```

### 2. Configure Firebase

The client reads Firebase settings from `firebase-applet-config.json` at build/runtime. The archive includes this AI Studio-generated configuration file. For your own Firebase project, replace it with your Firebase web app configuration and set the Firestore database ID expected by your project.

In the Firebase console:

1. Enable **Google** under Authentication sign-in providers.
2. Create or select the Firestore database.
3. Add your local and deployed hostnames to Authentication's authorized domains.
4. Publish the rules from `firestore.rules` to the database.

The client configuration contains Firebase project identifiers and a web API key. These are client-side configuration values, not substitutes for Firestore security rules. Do not put a Gemini API key in the client configuration or frontend code.

### 3. Configure server environment

Copy `.env.example` to `.env` and set a valid Gemini key:

```env
GEMINI_API_KEY=your_gemini_api_key
```

`APP_URL` is listed in `.env.example` for AI Studio's hosted runtime, which injects the deployed app URL. It is not required by the local code path shown in this project. The Gemini key is required for AI agent calls; non-AI parts of the app can still be explored in demo/local workspace modes.

### 4. Start the app

```bash
npm run dev
```

Open the local URL reported by Vite/server (the Express server listens on port `3000`). Use the app's demo or local workspace option if you want to explore without signing in to Firebase.

## Available scripts

| Command | Purpose |
| --- | --- |
| `npm run dev` | Start the Express server with Vite middleware for local development |
| `npm run build` | Build the client into `dist/` |
| `npm run start` | Start the server using the production static-file path |
| `npm run preview` | Run Vite's preview server |
| `npm run lint` | Run TypeScript type checking (`tsc --noEmit`) |

For a production-style local run, build first and set `NODE_ENV=production` before starting the server. The production server serves the `dist/` output and the API routes from Express.

## Gemini and Firebase behavior

- Gemini requests are made on the Express server through `@google/genai`; `GEMINI_API_KEY` stays server-side.
- The agent code tries `gemini-4-hydrogen` first and falls back to `gemini-3.1-flash-lite` when generation fails.
- Skill readiness and career matches are calculated by the deterministic skill engine. Gemini adds narrative analysis and recommendations; it does not set the deterministic readiness percentage.
- Signed-in workspace data is stored in Firestore. Local/demo mode is stored in browser local storage and does not require Firebase sign-in.
- The API verifies Firebase ID tokens for signed-in requests. Demo and local workspace requests use explicit mode headers.

## Deploy with Google AI Studio

This project includes AI Studio app metadata and an Express server configured for an AI Studio-style hosted runtime.

1. Open the project in Google AI Studio Build mode (or import the project into your AI Studio workspace).
2. Add `GEMINI_API_KEY` in the app's **Secrets** settings. Do not commit a real key to GitHub.
3. Confirm the Firebase web configuration and Firestore rules are set for the Firebase project you intend to use.
4. Deploy from AI Studio and wait for the hosted build to finish.
5. Add the deployed hostname to Firebase Authentication's authorized domains and check sign-in, Firestore access, and Gemini-backed features on the live site.

The provided source hard-codes the Express listener to port `3000`; use the AI Studio deployment flow targeted by this project or update the server's port handling if deploying to a host that requires its assigned `PORT` environment variable.

## Project layout

```text
server.ts                 Express API server and Vite/production setup
server/agents.ts          Gemini agent implementations
src/pages/                App pages
src/services/              Firestore persistence and agent orchestration
src/shared/catalog.ts      Career, skill, assessment, and resource catalog
src/shared/skillEngine.ts  Deterministic gap, match, and roadmap calculations
firestore.rules            Firestore access and data validation rules
firebase-applet-config.json Firebase web app configuration
```

## Security notes

- Keep `GEMINI_API_KEY` in server-side environment secrets only.
- Review `firestore.rules` before using a different Firebase project; deploy the rules to that project's database.
- Never commit `.env` or `.env.local` files containing secrets. The repository's `.gitignore` excludes environment files.
- Firebase web API keys identify the client project; access control must be enforced by Firebase Authentication and Firestore rules.

## License

No license file is included. Unless a license is added, the source is not explicitly licensed for reuse.

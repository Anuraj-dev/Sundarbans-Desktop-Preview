<h1 align="center">Sundarbans House</h1>

<p align="center">
  The website of Sundarbans House, a student community in the IIT Madras BS degree programme.<br />
  Events, study resources, meetups, team pages, certificate checks and a members lounge.
</p>

<p align="center">
  <a href="https://sundarbans-desktop-preview-eight.vercel.app">Live site</a>
  ·
  <a href="#running-it-locally">Run it locally</a>
  ·
  <a href="#documentation">Documentation</a>
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/65d285ed-e47b-4ba9-ac84-96444b8f1f2d" alt="The Sundarbans House home page: a sunlit forest photo behind the title Sundarbans House, with Lounge and Course buttons in the top bar" />
</p>

Sundarbans House is a student community under the IIT Madras BS degree. This site is where you find its events and meetups, the Study Corner with notes, lectures and past papers, the teams that run it, and a way to check whether a certificate was really issued by the house.

It is a Vue 3 single page app built with Vite. It has no backend of its own. Members sign in with Google, the contact form posts to Formspree, and everything else is static data bundled into the build. To run it you need Node 22 and, for the members lounge, two environment variables.

## What you get

- **A guided Study Corner.** You pick a branch, a level and a subject, then get notes, lectures and past papers as Google Drive links. The browser covers 4 branches and 3 levels, from Foundation to Degree. A subject with no resources yet shows a request form instead of a dead end.
- **Members lounge behind Google sign-in.** The login page gets the account email from Google Identity Services, then checks it against a membership endpoint set by `VITE_MEMBERSHIP_CHECK_URL`. That endpoint is an Apps Script web app that reads a Google Sheet. On a match, the email is stored in `localStorage` and the lounge opens. No server verifies the Google token, and the lounge and dashboard routes check only for that stored value.
- **Certificate verification.** You type a certificate ID and the page looks it up in `public/data/certificates.json`. A match shows the record and links to the PDF on Google Drive.
- **A dashboard of small study habits.** Streaks, a daily challenge, a mood wall, a pomodoro room, a meme vote and a confession wall. Each widget saves its state under `sb_*` keys in `localStorage`, so progress stays in the browser you used.
- **Regional meetup pages.** `/meetups` lists the regions, and each region has its own page at `/meetups/:region`, configured in `src/views/meetups/regionConfigs.js`.
- **Fast first load and safe deploys.** Every route is a lazy import, so the home page downloads only its own chunk. If a tab stays open across a deploy and asks for a chunk the new build removed, it reloads once onto the same URL.

## Running it locally

Use Node 22, as set in `.nvmrc`. Then install and start the dev server:

```sh
npm ci
npm run dev
```

Vite prints a local URL, usually `http://localhost:5173`. The site runs without any configuration. Only the members lounge needs the variables below.

### Environment

Copy `.env.example` to `.env` if you need the lounge. Vite reads `VITE_` variables at build time, so restart the dev server or rebuild after you change them.

| Variable | Needed for |
| --- | --- |
| `VITE_GOOGLE_CLIENT_ID` | Google sign-in on the login page |
| `VITE_MEMBERSHIP_CHECK_URL` | The membership check after sign-in. Without it, login stops with a configuration message. |
| `CLOUDINARY_CLOUD_NAME`, `CLOUDINARY_API_KEY`, `CLOUDINARY_API_SECRET` | Only `npm run media:sync` and `npm run media:migrate-repo`. Not needed for dev or build. |

### Checks

These are the gates CI runs on every pull request:

```sh
npm run format:check
npm run lint
npm run build
npm run test:smoke
```

`test:smoke` runs Playwright route tests against `vite preview` on port 4173. Run `npm run build` first, and install the browser once with `npx playwright install chromium`.

## Deploying

The build output is a static `dist/` folder. `vercel.json` and `public/_redirects` both send every path to `index.html`, so deep links such as `/study` load the app. Any static host with the same fallback will work.

## Documentation

- [Domain glossary](CONTEXT.md): the people, roles and content states the site uses
- [Conventions](docs/conventions.md): stack, commands, naming and folder layout
- [Decisions](docs/decisions.md): the log of load-bearing choices and why they were made
- [Docs index](docs/INDEX.md): a map of the other project documents

## Contributing

Read [CONTRIBUTING.md](CONTRIBUTING.md) before you open a pull request. It sets the asset policy: PDFs go to Google Drive, images go to Cloudinary, and nothing heavy goes into git.

## License

Sundarbans House is licensed under the [GNU General Public License v3.0](LICENSE).

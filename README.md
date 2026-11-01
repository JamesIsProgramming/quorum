# Quorum

A Mentimeter-style live presentation tool.

| Part | File | Where it lives |
|---|---|---|
| Audience page (join, answer, upvote) | `index.html` | Public, on GitHub Pages |
| Presenter (editor + stage mode) | `presenter.html` | Your computer only |
| Live backend (free) | Firebase Realtime Database | Google's free tier |
| Config | `firebase-config.js` | Public (contains no secrets) |
| QR library (MIT, bundled) | `qrcode.js` | Local, used by the presenter |
| Security rules | `database.rules.json` | Pasted into Firebase |

Your decks are stored in your browser (localStorage) and exported as `.deck.json` files. They never touch the cloud. Only the *currently displayed slide's public fields* and audience answers go through Firebase, and the session is deleted when you end it.

## One-off setup (about 10 minutes)

### 1. Create the Firebase project
1. Go to <https://console.firebase.google.com> and **Add project** (turn Analytics off).
2. **Build → Authentication → Get started → Sign-in method → Anonymous → Enable.**
3. **Build → Realtime Database → Create database.** Choose **Singapore (asia-southeast1)**, start in **locked mode**.
4. In Realtime Database, open the **Rules** tab, paste the entire contents of `database.rules.json`, and **Publish**.

### 2. Paste your config
1. **Project settings (cog) → General → Your apps → Web (`</>`)**, register an app (no hosting).
2. Copy the `firebaseConfig` values into `firebase-config.js`. The `databaseURL` is shown in the Realtime Database tab if it is missing.
3. Set `JOIN_URL` to `https://YOUR-USERNAME.github.io/quorum/` (use your GitHub username and repository name).

### 3. Publish the audience page on GitHub Pages
```sh
git init
git add .
git commit -m "Quorum"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/quorum.git
git push -u origin main
```
Then on GitHub: **Settings → Pages → Build from branch → `main` / root**. After a minute the audience page is live at your `JOIN_URL`.

`.gitignore` keeps `presenter.html`, the start scripts and your decks off GitHub.

### 4. Authorise your Pages domain
In Firebase: **Authentication → Settings → Authorised domains → Add domain** → `YOUR-USERNAME.github.io`. (`localhost` is already authorised.)

## Presenting
1. Run `./start.sh` (Mac/Linux) or double-click `start.bat` (Windows). It serves the folder at `http://localhost:8000/presenter.html`. Any equivalent static server also works, but it must be `localhost` rather than `file://`.
2. Build your deck, press **Go live** to get a join code, then **Present**.

## Keys in presentation mode
| Key | Action |
|---|---|
| → / Space / Enter | Next (on a quiz: start timer, then reveal) |
| ← | Previous |
| O | Open or close voting |
| R | Show or hide results |
| X | Reset this slide's responses |
| J | Full-screen join code and QR |
| F | Toggle fullscreen |
| B | Blank the screen |
| Esc | Exit |

## Publishing an update
Upload the changed files to your GitHub repository (**Add file → Upload files → Commit changes**). Within a minute or two of GitHub finishing its build, anyone with the page open (audience or presenter) sees a **"A new version is available. Reload"** banner. Nothing reloads by itself, so nobody loses what they are doing. The banner is hidden while you are presenting full-screen.

## Exporting slides as a PDF
Click **Export PDF** in the top bar and choose **With example responses** or **Blank**. The browser's print window opens: set **Destination** to **Save as PDF** and **Margins** to **None**, then save. You get one page per slide. In *Blank* mode quiz answers are not marked, so it is safe to share.

## Long text
Titles and content shrink automatically so nothing overflows, overlaps the join QR code, or distorts a chart. If text looks small, shorten it or split it across two slides.

## Limits worth knowing
* The free Firebase plan allows **100 simultaneous connections**, so roughly 100 live audience members at once.
* Anyone with the 6-character code can join. End the session when finished, which deletes its data.
* Responses are plain text rendered safely (escaped), and you can delete any response or question from the stage.

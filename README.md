# Rare Loot Vault

A Star Citizen loot and fleet tracker that runs entirely in the browser. Item and ship data comes live from the
Star Citizen Wiki API. Your vault is saved in your own browser, and the SYNC button can share it in real time
with a friend.

## Put it on GitHub Pages (no Git or command line needed)

1. Sign in at github.com and click **+ → New repository**. Name it `sc-vault`, choose **Public**, and click **Create repository**.
2. On the empty repository page click **uploading an existing file**. Drag in `index.html` and this `README.md`, then click **Commit changes**.
   The file must be called `index.html`. Do not upload `vault.json` or anything else with your personal data: the repository is public.
3. Open **Settings → Pages**. Under **Build and deployment** set **Source** to **Deploy from a branch**, **Branch** to `main`, folder `/ (root)`, and click **Save**.
4. Wait about a minute and refresh that page. It shows your link: `https://YOUR-USERNAME.github.io/sc-vault/`. Send that link to anyone.

To update the app later, upload a new `index.html` over the old one (Add file → Upload files).

## First use

- Move your existing vault over: open your old `vault.json` in Notepad, copy everything, then on the page press **DATA**, paste it into the box and press **IMPORT**.
- Everything is stored in this browser. A different browser or device starts empty, and clearing the site data erases it. Use **SYNC** (below) or **DATA** to keep a copy.

## Share it with a friend (real time)

GitHub can host the page but cannot store shared data, so syncing uses a free Firebase Realtime Database. One of you sets it up once:

1. Go to console.firebase.google.com and create a project (Google Analytics can be off).
2. **Build → Realtime Database → Create Database.** Pick a location, start in locked mode.
3. Open the **Rules** tab, replace everything with the text below, and press **Publish**:

       {"rules":{"rooms":{"$room":{".read":true,".write":true}}}}

4. On the **Data** tab copy the database URL shown at the top.
5. On the page press **SYNC**, paste that URL and press **CREATE**. A sync code appears.
6. Send the code to your friend. They open the same link, press **SYNC**, paste the code and press **JOIN**.

From then on, edits by either of you show up for the other within a few seconds. The code is the key to your shared vault, so keep it private.
Deletions are shared too, so be careful with RESET ALL and IMPORT while syncing.

## If item lookups say they cannot reach the wiki

The page asks api.star-citizen.wiki directly from your browser. If the wiki ever stops allowing that, put a tiny proxy in between:
create a free Cloudflare Worker with the code below, then in `index.html` change the line `const BASE='https://api.star-citizen.wiki/api'`
to `const BASE='https://YOUR-WORKER.workers.dev/api'`.

    export default { async fetch(req) {
      const u = new URL(req.url);
      const r = await fetch('https://api.star-citizen.wiki' + u.pathname + u.search, { headers: { Accept: 'application/json' } });
      const h = new Headers(r.headers); h.set('Access-Control-Allow-Origin', '*');
      return new Response(r.body, { status: r.status, headers: h });
    } }

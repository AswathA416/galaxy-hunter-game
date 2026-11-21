# GALAXY FALL

A third-person galaxy battle royale in a single file. Play it in your browser.

No libraries, no build step, no accounts, no servers. Progress saves in your browser. Cost: $0.

## New in v5
- Buildings with window frames and glass, sills, door frames, chimneys and roof trim.
- Treasure chests across the map. Stand next to one and press **E** to open it. Gold chests give better loot.
- Change your username from the Play tab ("Save name"). The loading screen shows the new name.

## Controls
- **Move:** WASD
- **Jump / open glider:** Space
- **Sprint:** Shift
- **Look:** mouse (click the game to capture it, Esc to release)
- **Fire:** left click
- **Open chest:** E
- **Weapons:** 1 pickaxe, 2–4 guns, mouse wheel cycles
- **Build mode:** Q, then 1 wall, 2 floor, 3 ramp

## Play
Open `index.html` in any modern browser, or use the GitHub Pages link below.

## Publish on GitHub Pages (free)
1. Create a public repo, for example `galaxy-fall`.
2. Click **Add file → Upload files**, drop in `index.html`, and click **Commit changes**.
3. Go to **Settings → Pages**. Under **Build and deployment**, set Source to **Deploy from a branch**, choose `main` and `/ (root)`, then click **Save**.
4. After about a minute the game is live at `https://<your-username>.github.io/galaxy-fall/`.

## Limits
- Building is wall, floor and ramp only. Roofs, stairs and an edit mode are not in yet.
- Buildings are procedural boxes, not modeled meshes.
- Bots are AI opponents, not other players. Online multiplayer needs a server, which GitHub Pages can't run.

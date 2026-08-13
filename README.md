# QA Delivery Roadmap

QA delivery roadmap with a Gantt-style timeline. Editable directly on the page — no touching code, no manual Figma boxes, no local file. Data is versioned in this repository (`data.json`) and the page is published via GitHub Pages.

## What this project replaces

Previously, the roadmap was assembled by hand in Figma: each delivery was a manually cloned box, with text, dates, Teams link, and avatar typed inside. Here, each delivery is a record in `data.json`; the page (`index.html`) reads those records and draws the timeline automatically — bar position, width, and color are calculated from the start/end date and category.

## Repository structure

| File | Purpose |
|---|---|
| `index.html` | The roadmap page (timeline + edit panel). No need to edit it for normal use. |
| `data.json` | Categories and deliveries — the actual data source. Can be edited from the page or directly on GitHub. |
| `tutorial.html` | Illustrated, step-by-step tutorial for generating the GitHub token used when saving. |

## How to publish (one-time setup)

1. The repository needs to be **public** (free GitHub Pages requires this, unless your organization has a plan with private Pages).
2. `index.html` and `data.json` must be in the **root** of the repository.
3. **Settings → Pages** → Source: **Deploy from a branch** → branch `main`, folder `/ (root)` → **Save**.
4. Wait ~1 minute. The site will be at `https://YOUR_USERNAME.github.io/REPOSITORY_NAME/`.

The page automatically detects the owner/repository from its own URL — no code changes are needed for this to work.

## How to view

Just open the GitHub Pages link. The roadmap loads the latest data from `data.json` automatically, for anyone with the link — no login, nothing to install.

## How to edit

1. Open the GitHub Pages link. There's already a **🔄 Refresh** button at the top of the page to fetch the latest version without opening the edit panel.
2. Click **✏️ Edit roadmap**. The panel has three tabs: **Deliveries**, **Categories**, and **Sync**. The Deliveries and Categories tabs already have a **💾 Save to GitHub** button at the top — you don't need to go to the Sync tab just to save.

### Deliveries tab
- **+ New delivery** to add one, or ✏️/🗑️ on a list item to edit/delete it.
- **End date is optional.** If you only fill in the start date and leave the end blank, the page automatically fills it in with **+1 week** from the start (the field pre-fills itself when you leave the start field, but you can edit it freely).

### Categories tab
- **+ New category**: choose a name, background color, and text color (with a live preview). The category then becomes available both in the legend and in the dropdown when creating/editing a delivery. The internal id is **numeric and sequential** (1, 2, 3...) and never changes — even if the name is changed later.
- ✏️ on an existing category: rename it or change its colors. Renaming is safe — since the id doesn't depend on the name, no delivery ever loses its category because of a rename.
- 🗑️ on a category: if no delivery uses it, it's removed right away. If some delivery does use it, the page asks you to pick a target category before deleting — affected deliveries are moved automatically, nothing is left "orphaned".
- You can't delete the last remaining category (at least one must exist).

### Sync tab
1. Click **💾 Save to GitHub**. The first time, it will ask for a token — see the illustrated tutorial in [`tutorial.html`](tutorial.html) (step-by-step with screenshots) or the summary below:
   - GitHub → **Settings → Developer settings → Fine-grained tokens → Generate new token**
   - **Expiration**: set it to the **end of December** — tokens have a maximum lifetime of 366 days, so set a reminder to renew before it expires.
   - Repository access: **Only select repositories** → choose this repository
   - Permissions → **Contents: Read and write**
   - Generate it and paste it into the page. It's saved only in your browser, never committed.
2. Once connected, **Save to GitHub** writes deliveries and categories together (a real commit) and **🔄 Refresh** fetches the latest version.

Each person who wants to edit connects their own token, once, in their own browser. Anyone who's just viewing needs nothing at all.

> **If the repository lives under your personal account** (not an organization), teammates who are only collaborators — not owners — will notice the repository doesn't show up under "Resource owner" when creating a fine-grained token. This is a known GitHub limitation for outside collaborators, not a configuration mistake. `tutorial.html` explains the workaround: generate a **classic token** with the `repo` scope, which works the same way inside the roadmap.

## Simultaneous edit conflicts

If two people save at nearly the same time, the second attempt is rejected by the GitHub API (instead of silently overwriting). The page tells you to click **Refresh**, redo your change, and save again.

## `data.json` format

```json
{
  "categories": [
    { "id": "1", "name": "Cyber Threat Intelligence", "bg": "#BFE0F7", "fg": "#1B4965" }
  ],
  "deliveries": [
    {
      "id": "d1",
      "name": "Delivery name",
      "categoryId": "1",
      "categoryName": "Cyber Threat Intelligence",
      "start": "2025-11-10",
      "end": "2025-11-21",
      "link": "https://teams.microsoft.com/...",
      "status": ["published"],
      "owners": ["MC"]
    }
  ]
}
```

- A category's `id` is **numeric and permanent** — it's never recalculated from the name, so renaming a category from the "Categories" tab never disconnects a delivery from its category.
- `categoryId` (on a delivery) is the real reference, used to compute color and grouping. `categoryName` is just a human-readable copy of the name, automatically recalculated whenever the category is saved — it doesn't need to (and shouldn't) be edited manually separately from `categoryId`.
- `end` is optional: if missing or empty, the page assumes `start + 7 days` when saving.
- `status`: any combination of `"published"` (👍 Published to #update-deliveries) and `"homolog"` (🌗 Homologation outside the lifecycle).
- `owners`: initials of the QA owner(s); each set of initials gets an automatic color, consistent across all bars.

The file is always saved with deliveries sorted by start date — this keeps the commit history readable and reduces merge conflicts. Older formats (category by name/slug, without `categoryId`/`categoryName`) are still read normally — the page migrates itself to numeric ids the first time it loads.

## Manual backup

The **⬇ Download local backup (.json)** button in the edit panel saves a safety copy of the current state, independent of GitHub.

## Known limitations

- No user authentication — anyone with a write token for the repository can edit. Suitable for a trusted internal team; this is not a granular access control system.
- No "who edited what" history beyond what Git itself already records in commits.
- The published site can take up to ~1 minute to reflect a new commit.

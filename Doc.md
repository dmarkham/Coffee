# How to Add a New Coffee to Your Tracking System

This doc is the playbook for adding a new coffee and assigning it to a container. The stickers on the containers point at stable URLs that auto-redirect to whichever bean the container currently holds — so the only work on a swap is editing files in this repo.

## TL;DR — Swap a coffee in one container

1. Create `bean/NN.Roaster-Coffee.md` (next sequential number)
2. Overwrite `container/X.md` with new `redirect_to:` front-matter and a new markdown link
3. Update the matching row in `README.md`
4. `git add … && git commit && git push origin master`

GitHub Pages rebuilds in ~1 minute and the existing NFC sticker for that container starts pointing at the new bean.

---

## 1. Gather coffee information

Prefer the **bag in your hand** over the web. The bag is the source of truth. Capture only what's actually printed on it; do not invent fields the bag does not list (process method, varietal, altitude, region within a country, etc.).

If you have the product page open in a browser, the `mcp__browser-control__browser_markdown` tool can pull a clean Markdown dump. If you only have the physical bag, photograph the front and back and read directly off the images.

Fields worth recording when present:

- Coffee Name
- Roaster Name
- Flavor / tasting notes (quote the bag)
- Roast level (Light / Medium / Dark / etc.)
- Origin (country, region — only if printed)
- Process (Washed / Natural / Honey / Wet-hulled — only if printed)
- Altitude (only if printed)
- Varietal (only if printed)
- Certifications (Organic, Fair Trade, Rainforest Alliance, KSA Pareve, etc.)
- Roast date / Best-by date (handy for freshness)
- Package size/weight
- Product page URL (if there is one)

**Rule:** if a field is not on the bag or product page, leave it out. Don't infer "typical for Indonesian coffees" → "wet-hulled" or similar. Bag-only facts.

## 2. Check for an existing entry

Quick grep to avoid duplicates:

```bash
grep -li -E "coffee-name|roaster" bean/*.md
```

## 3. Determine the next bean file number

```bash
ls -1 bean/ | sort -t. -k1 -n | tail -1
```

The next number is `<that number> + 1`. (As of the last commit, the highest is `90.Trader-Joes-Sulawesi.md`.)

## 4. Create the bean file

Path: `bean/NN.Roaster-Name-Coffee-Name.md` (hyphens, no spaces, ASCII).

Template — include only sections for which the bag actually has data:

```markdown
# Coffee Name - Roaster Name

### Description
Notes: [tasting notes from bag, comma-separated]

ROAST: [Medium / Dark / etc.]
ORIGIN: [Country, Region — only if printed]
PROCESS: [only if printed]
ALTITUDE: [only if printed]
VARIETAL: [only if printed]
CERTIFICATION: [Organic, Fair Trade, KSA Pareve, etc.]

[Optional one-paragraph blurb from the bag or roaster site. Skip if there isn't one.]

Roasted On: YYYY-MM-DD
Best By: YYYY-MM-DD

12 oz (340g)

## La Pavoni Settings

Grind: ?

Weight: ?


[Product Page](URL)
```

Leave `Grind: ?` and `Weight: ?` until you've dialed it in on the machine. Update later — see `dcf0a6d` for that pattern.

## 5. Update the container file

Each container holds exactly one coffee. Overwrite `container/X.md` completely:

```markdown
---
redirect_to: https://github.com/dmarkham/Coffee/blob/master/bean/NN.New-Coffee.md
---
# Container X

[Coffee Name - Roaster Name](../bean/NN.New-Coffee.md)
```

Both the `redirect_to:` URL **and** the markdown link must point at the new bean file. The front-matter is what makes the NFC sticker resolve to the right coffee (see "NFC stickers" below).

If you're moving a container back to empty, drop the front-matter entirely and write:

```markdown
# Container X

*Empty - Ready to be filled*
```

## 6. Update README.md

In the Storage Containers table, edit the row for the container you changed:

```markdown
| [Container X](container/X.md) | Contains [Coffee Name - Roaster Name](bean/NN.New-Coffee.md) |
```

(Links from `README.md` use `container/...` and `bean/...` — no `../` since the README sits at the repo root.)

## 7. Commit and push

```bash
git add bean/NN.New-Coffee.md container/X.md README.md
git commit -m "Add <Coffee Name> to Container X, replacing previous coffee"
git push origin master
```

Use the same commit-message style as the recent history (`git log --oneline`).

Per the repo's git rules: SSH remote, explicit `origin master` on push, no Co-Authored-By lines, no Claude/Generated-With footers.

## 8. (Optional) Verify the redirect

Wait ~1 minute for GitHub Pages to rebuild, then:

```bash
gh api repos/dmarkham/Coffee/pages/builds/latest --jq '{status,commit}'
curl -s https://dmarkham.github.io/Coffee/container/X | grep -i 'url='
```

The `url=` line should contain the new bean filename.

---

## NFC stickers — how the redirect works

Each container has a physical NFC sticker on it. The sticker encodes one **stable** URL:

```
https://dmarkham.github.io/Coffee/container/1
https://dmarkham.github.io/Coffee/container/2
https://dmarkham.github.io/Coffee/container/3
https://dmarkham.github.io/Coffee/container/4
https://dmarkham.github.io/Coffee/container/5
https://dmarkham.github.io/Coffee/container/6
```

These URLs **never change**. Tapping the sticker hits GitHub Pages, which serves the rendered `container/X.md` — and because of the `redirect_to:` YAML front-matter, that page is a meta-refresh / JS redirect straight to the current bean's GitHub view.

So the stickers are write-once. All future swaps are file edits in this repo.

### Infrastructure pieces (one-time setup, already done)

- `_config.yml` enables the `jekyll-redirect-from` plugin (allowlisted on GitHub Pages).
- GitHub Pages is enabled on `master` / root (`gh api -X POST repos/dmarkham/Coffee/pages -f 'source[branch]=master' -f 'source[path]=/'`).
- Each populated `container/N.md` carries `redirect_to: <bean URL>` in its front-matter.

### Empty containers

A container with no coffee should have **no** front-matter — just `# Container X` and the "Empty" line. Without `redirect_to:`, Jekyll renders it as a normal page (no redirect), which is what you want for an empty slot.

---

## Filling in La Pavoni settings later

Once you've dialed in grind and weight on the machine:

```bash
# Edit bean/NN.Coffee.md, replace `Grind: ?` and `Weight: ?` with real values
git add bean/NN.Coffee.md
git commit -m "Update <Coffee Name> brewing parameters with latest experience"
git push origin master
```

No container or README change needed for brew-setting updates.

# Extra Entertainment DFW — landing page

Mobile-first, single-page, zero dependencies. Three files:

```
index.html     the landing page
thanks.html    where the quote form lands after submit
assets/        video, posters, and team photos
```

**No animations.** Scroll reveals and the drifting gradient were removed — content is painted at final position on first paint. Nothing fades, slides, or moves except the hero video itself.

---

## Asset spec

| File | Dimensions | Status |
|---|---|---|
| `hero.mp4` | 1440×810 | Installed — 982 KB |
| `hero-mobile.mp4` | 540×960 | Installed — 458 KB |
| `hero-poster.jpg` / `-mobile.jpg` | matches each video | Installed |
| `og-image.jpg` | 1200×630 | Installed |
| `jerry.jpg` `christa.jpg` `carson.jpg` `preston.jpg` | **800×800 square** | **Still needed** — under 150 KB each |

Faces are 800×800 because phones render at 2–3× density: a 64px circle needs up to 192px of real pixels, and 800 leaves headroom if the cards ever grow. Above 1000 is wasted bytes.

---

## 1. The video — rebuilt for steadiness

The original clip made people motion-sick on a large screen — a wide-angle lens plus a moving camera plus barrel distortion at the edges is the classic recipe. Rebuilt in four steps:

1. **Trimmed to the calmest window.** Frame-to-frame motion was measured across all 11.5 seconds. Motion spiked hard at 0–2s and 8–9s; the calm stretch was 3–8s. Only that window is used.
2. **Cropped in** from 1920×1080 to 1500×844, cutting the fisheye-warped edges where the distortion "swims" most when the camera moves.
3. **Stabilised** (ffmpeg vidstab, two-pass) and slowed to 0.8×.
4. **Crossfaded the loop** — the tail dissolves back into the head, so there's no hard cut every few seconds pulling the eye.

Measured result:

| | mean motion | 95th percentile | peak |
|---|---|---|---|
| Original | 9.73 | 37.77 | 64.11 |
| Shipped | 3.95 | 11.85 | 27.99 |

**59% less average motion, 69% less at the peak.** It also got much lighter: 3.9 MB of video down to 1.4 MB.

| Screen | File | Size |
|---|---|---|
| Phones | `hero-mobile.mp4` — 540×960 | 458 KB |
| Desktop | `hero.mp4` — 1440×810 | 982 KB |

**Grading:** brightness +5%, contrast +12%, saturation +28%. The raw clip was dark enough that the uplighting got crushed once the text scrim went over it.

**Poster frame** is at the 4-second mark — a couple dancing under green uplight, chosen because it has visible people rather than just an empty room. That's the still on metered connections, so it has to work alone.

**Audio stripped entirely**, not muted — it autoplays silent regardless, so the track was dead weight.

### The honest limitation

The steady parts of this clip are the slow parts, and the energetic parts are the shaky ones. The shipped version is a first-dance moment: calm, premium, good for weddings — but it isn't "packed floor at 11pm." No amount of processing fixes that; it needs different footage. **Ten seconds shot on a tripod or gimbal at a peak moment would beat this comfortably.**

## 1b. Drop in the team photos

The "One family. Two crews." section shows gold-ringed circles with initials until you add photos. Drop these four files into `assets/` and they swap in automatically — no code change needed:

```
assets/jerry.jpg
assets/christa.jpg
assets/carson.jpg
assets/preston.jpg
```

Square crops, **800×800**, face centered. Action shots behind the decks beat studio headshots here — you're selling energy, not a law firm. If a file is missing, that one circle just keeps its initial, so you can add them one at a time.

---

## 2. Put it live on Netlify

Free, and the form works with no backend.

1. Go to [app.netlify.com/drop](https://app.netlify.com/drop)
2. Drag the whole `extra-dfw` folder onto the page
3. It's live in ~20 seconds at a random URL like `jade-pony-1a2b3c.netlify.app`
4. **Site configuration → Change site name** to something like `extra-entertainment-dfw`
5. **Domain management → Add a domain** to point `extraentertainmentdfw.com` (or a subdomain like `book.extraentertainmentdfw.com`) at it. Netlify walks you through the DNS records.

### Turning on quote notifications

The form is already wired for Netlify Forms. After your first real submission:

- **Forms** in the Netlify dashboard → you'll see a form named **quote** with every submission
- **Forms → Settings → Form notifications → Add notification → Email notification**
- Send it to your email *and* set up a second one if you want it going to Christa too

To get quotes as a **text**, add a Zapier or Make.com hook on the Netlify form → SMS. Worth it: speed-to-lead is most of the game in this business.

Spam protection is already in (honeypot field). If you ever get hit hard, flip on reCAPTCHA in the same settings panel.

---

## 3. What to change over time

Everything is plain HTML — search for the text and edit it.

| To change | Search `index.html` for |
|---|---|
| Phone number | `8178548544` (appears 5 times) |
| Reviews | `<article class="review"` |
| Review count / rating | `73` and `5.0` |
| Services | `<div class="card` |
| Team bios / crews | `<article class="tcard` |
| Cities served | `Serving Keller,` |
| Colors | `--gold:` at the top of the `<style>` block |

**Swap the reviews out seasonally.** The three in the review section are real, pulled from your Google profile — Jantz Goble, Karen Denise, Steve Kotara. Ashley Childers' review sits in the Carson & Preston card because it names Carson directly. When you get a strong wedding review, replace the Christmas-party one; couples want to see couples.

---

## Notes on what's deliberate

- **One call to action.** Every button on the page goes to the quote form. No "learn more," no navigation menu to get lost in. The only competing action is tap-to-call, which is a *better* outcome than a form fill.
- **The sticky bottom bar** appears after the hero and hides itself once the form is on screen, so it never covers what you're trying to fill out.
- **Proof before pitch.** Star rating sits in the hero, above the fold, before you've asked for anything. The authority bar and reviews come before the services list — same order Bunn and SCE use, because credibility is what someone shopping four DJs is actually sorting on.
- **Four fields, then contact info.** Date first, because "is my date open" is the real question in a visitor's head, and answering it is your fastest path to a conversation.
- **No pricing on the page.** That's the point of "instant quote" — it gets you the conversation. If you ever want a live estimate calculator instead, that's a bolt-on.
- **Snap That DFW is linked, but never as a call to action.** It appears twice: a quiet text link on the Photo Booth card, and one line in the footer. Both open in a new tab so this page stays alive behind them. The photo booth demand itself is captured by the **add-on chips in the form** — a DJ lead who wants a booth tells you in the same submission instead of leaving to go find out.

### Watch the add-on chips

The chips are your best free data source. If "Photo booth" gets checked on half your DJ submissions, that's the upsell to build a package around. If "Glow party" never gets checked in a year, swap it for something else. Netlify records every checked box on the submission.

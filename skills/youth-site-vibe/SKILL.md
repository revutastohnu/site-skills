---
name: youth-site-vibe
description: Makes a site feel alive, playful and "made by a person", the way a youth product should feel on a phone - one physical metaphor carried through the whole UI, a tiny token set, springy "thrown and slapped" motion, native-app feel on mobile, and actions that never wait. Use it whenever someone builds or redesigns a site, landing, portfolio, event page, merch shop, club / band / creator page, or a small web app for a young audience, or says "зроби молодіжно", "щоб було прикольно / живо / не нудно", "як у застосунку", "додай анімації", "щоб кайфово на телефоні", "make it fun", "playful", "Gen Z vibe", "feels like an app" - even when they never say "design". Pair it with design-taste-frontend (layout and anti-template rules) and frontend-design (typography and direction): this skill adds the concept, the motion and the phone feel on top. If the project already has its own design system, follow that and borrow only the motion and mobile parts.
---

# Youth site vibe

Most AI-made sites look the same because they start from components (hero, three cards, CTA) instead of an idea. Sites that people screenshot and send to friends start from **one physical thing the whole page pretends to be**, keep the palette tiny, and make every touch answer with motion that feels like a real object.

This skill is distilled from building gimmie, a wishlist where every wish is a cut-out sticker on a dark board. Its numbers are kept here as a worked example; use the method, not the stickers.

## 1. Pick the metaphor first (before any markup)

Ask: "if this site were a physical object, what would it be?" Then let every UI piece be a part of that object.

| Product | Metaphor | Parts become |
|---|---|---|
| wishlist (gimmie) | a cork board with things cut out of shops | items = stickers, prices = paper tags, categories = washi tape, states = rubber stamps |
| party / event | a gig poster wall | sections = posters, dates = ticket stubs, sold out = a stamp over it |
| band / artist | a tour van dashboard or a zine | tracks = cassette labels, news = photocopied pages |
| portfolio | a contact sheet / a desk | projects = polaroids, tags = sticky notes |
| school club | a locker door | members = photo booth strips, events = magnets |

Rules that make the metaphor work:
- **The content is the hero, the UI is quiet.** In gimmie the stickers carry all the colour; buttons are outlines, text is plain. One loud layer, not three.
- **Physical things get a slight tilt (-5°..+5°), text blocks never do.** A tilted price tag reads as "real paper", tilted paragraphs read as "broken".
- **Real shadows on "objects"**: `filter: drop-shadow(0 10px 14px rgb(0 0 0 / .5)) drop-shadow(0 2px 2px rgb(0 0 0 / .5))` (two layers: a soft far one and a tight contact one). Flat UI gets no shadow.
- **Torn / cut edges with clip-path**, not images: `clip-path: polygon(2% 0, 98% 4%, 100% 100%, 0 96%)` turns a div into a strip of tape.

## 2. A tiny token set

Write all colours as CSS variables on `:root` and do not add more later without a reason. gimmie runs on this:

```css
:root{
  --bg:#16171a;      /* the board; + a dot grid: radial-gradient(#2a2c31 1.2px, transparent 1.3px) 0 0/22px 22px */
  --surface:#222428; /* sheets, panels */
  --ink:#f2f2ee;     /* text */
  --mute:#a3a5ad;    /* hints */
  --paper:#fbfaf6; --paper-ink:#1d2026; /* tags, notes */
  --t1:#b9c8ff; --t2:#ffc9d4; --t3:#c4ecd0; --t4:#ffe49a; /* four pastel "tapes" */
  --danger:#ff5b52;  /* delete only */
}
```

Why it works: a dark, slightly warm background makes cut-out photos pop; four soft pastels give colour without fighting the content; each pastel has **one job** (blue = main action, mint = add, pink = "wants" / reserved, butter = dates). When a colour means one thing everywhere, the page feels designed.

Type: **one loud display font + one calm body font**, both free on Google Fonts. gimmie: `Unbounded` 800-900 for headings, prices and labels (tight: `line-height:.92; letter-spacing:-.04em`, size `clamp(44px, 9vw, 104px)`), `Onest` 16px/1.5 for everything else. Other display picks with the same energy: Bricolage Grotesque, Syne, Dela Gothic One, Climate Crisis. No UPPERCASE text except stamps.

## 3. Motion: thrown, slapped, stuck

Motion is what makes it feel alive. Three ideas cover 90% of it.

**a) Overshoot easing.** A bezier whose second number is above 1 overshoots and settles, which reads as a spring:
```css
:root{
  --spring: cubic-bezier(.2, 1.4, .4, 1);   /* hover, press, small moves */
  --pop:    cubic-bezier(.3, 1.7, .5, 1);   /* things stuck on (tags, badges) */
}
```

**b) "Slap" entrance**: the object is thrown in big and tilted, squashes a little on impact, settles. Stagger with a delay per item so they land one by one.
```css
@media (prefers-reduced-motion: no-preference){
  .item{animation: slap .5s both; animation-delay: var(--d, 0ms)}
  @keyframes slap{
    0%  {opacity:0; translate:0 -30px; rotate:-12deg; scale:1.5; animation-timing-function:cubic-bezier(.5,0,.9,.4)}
    20% {opacity:1}
    55% {translate:0 0; rotate:0deg; scale:.93; animation-timing-function:cubic-bezier(.2,1.6,.4,1)}
    100%{translate:0 0; rotate:0deg; scale:1}
  }
  /* then the small parts get stuck on, a beat later */
  .item .tag{animation: tag .32s var(--pop) both; animation-delay: calc(var(--d,0ms) + 300ms)}
  @keyframes tag{from{opacity:0; scale:1.9}}
}
```
Set `style="--d: calc(var(--i) * 60ms)"` per item. The layered timing (object, then its tag, then its stamp) is what makes it feel crafted rather than "fade in".

**c) Every touch answers.**
```css
.item{transition: transform .35s var(--spring)}
.item:hover{transform: translateY(-6px) rotate(0deg) scale(1.05)}  /* lifts and straightens */
.item:active, button:active{transform: scale(.97)}               /* press */
/* held / being dragged: trembles like iPhone icons in edit mode */
.item.dragging{animation: jiggle .12s infinite alternate}
@keyframes jiggle{from{rotate:-1.4deg} to{rotate:1.4deg}}
```
On phones add a tiny buzz on meaningful moments (lift, drop, success): `navigator.vibrate?.(10)` (Android; iOS Safari ignores it).

Guardrails:
- Wrap decorative motion in `@media (prefers-reduced-motion: no-preference)`. Not optional: some people get sick from it.
- Animate only `transform`, `opacity`, `filter` (and the individual `translate` / `rotate` / `scale` properties). Never width/top/left: it janks on phones.
- Durations: 120-350 ms for responses, up to 500 ms for entrances. Anything longer feels slow, not "smooth".
- Test a long page on a real phone: heavy effects (View Transitions over a page full of shadowed images) can freeze it for seconds. gimmie dropped View Transitions for a plain CSS slide for exactly this reason.
- For more ready-made micro-interactions (dropdown, modal, tabs, success check, shimmer) see https://transitions.dev.

## 4. Feel like an app on the phone

Most of a young audience arrives from Instagram / Threads / TikTok links on a phone. Design at 390 px wide first, desktop second.

Put this in the viewport meta and at the top of the global CSS, always:
```html
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no, viewport-fit=cover">
```
```css
*,*::before,*::after{box-sizing:border-box; corner-shape:squircle} /* iOS-style continuous corners; older browsers fall back to round */
html{touch-action:manipulation; -webkit-tap-highlight-color:transparent; -webkit-text-size-adjust:100%; overflow-x:clip}
body{padding-bottom:env(safe-area-inset-bottom)}
```
In Next.js the meta goes in `export const viewport = { width: 'device-width', initialScale: 1, maximumScale: 1, userScalable: false, viewportFit: 'cover' }`.

- `touch-action: manipulation` kills the double-tap zoom delay, so taps react instantly.
- No horizontal scroll anywhere; long words break (`overflow-wrap:anywhere` on text).
- Touch targets at least 44 px. Bottom sheets instead of centred modals on phones (radius 28 on the top corners only). Main action reachable by the thumb (bottom right floating button).
- Inputs at 16 px or more, or iOS zooms into them.

## 5. Actions never wait (optimistic UI)

A tap changes the screen in the same frame; the server catches up in the background. Liked = liked, added = it is on the board, saved = saved. No "Loading..." on a button, no greyed-out button until the answer. If the server fails, put the old state back with one short line of what happened. The only honest waits are things only the server knows (fetching a page, processing a photo), and those show progress **on the thing itself** (gimmie shows animated scissors over the photo being cut), never a spinner over the whole page.

This is half of why an app "feels fast"; animations are the other half.

## 6. Copy

- Talk like a friend: short, the action first ("додай", "забронюй", "add it"). Brand name in lowercase if it is a vibe brand.
- Say what to do, not why it failed.
- Emoji only as a leading mark on a line ("🎁 Мені це подарували"), never sprinkled in the middle of sentences.
- Real content in mockups, never lorem ipsum or invented stats.

## Pre-flight check (run before calling it done)

1. Can you name the metaphor in one sentence, and does every component belong to it?
2. Count colours outside the `:root` tokens: should be zero.
3. Two fonts max (plus maybe one logo-only font).
4. Every interactive element has hover + press feedback; entrances are staggered, not all at once.
5. A `prefers-reduced-motion` guard exists; only transform / opacity / filter animate.
6. Opened at 390 px: no sideways scroll, no zoom on input focus, double-tap does not zoom, the thumb reaches the main action.
7. Tapping the main action changes the UI instantly, without waiting for the network.

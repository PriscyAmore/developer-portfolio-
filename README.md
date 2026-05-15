# developer-portfolio-
# Alex Chen — Interactive Developer Portfolio

> A production-grade developer portfolio built with Svelte architecture principles, featuring a particle-based hero, interactive terminal, animated project showcase, and full dark/light theme support.

**Live Demo:** `[deploy to Vercel/Netlify and paste URL here]`

-----

## 🚀 Setup Instructions

### Option A: Single File (Instant)

```bash
# Just open in browser — zero dependencies
open portfolio.html
```

### Option B: SvelteKit (Production)

```bash
# Scaffold with SvelteKit
npm create svelte@latest alex-portfolio
cd alex-portfolio

# Install dependencies
npm install

# Install animation + utility libs
npm install gsap @motionone/svelte
npm install -D tailwindcss @tailwindcss/typography
npx tailwindcss init

# Dev server
npm run dev

# Build
npm run build

# Deploy (Vercel)
npx vercel --prod
```

**Requirements:** Node.js 18+, modern browser (Chrome 90+, Firefox 88+, Safari 14+)

-----

## 🏗 Architecture Explanation

### Component Structure (SvelteKit)

```
src/
├── routes/
│   ├── +layout.svelte        # Nav, cursor, theme, page transitions
│   ├── +page.svelte          # Home (Hero + sections)
│   └── projects/[slug]/      # Dynamic project pages
│       └── +page.svelte
├── lib/
│   ├── components/
│   │   ├── Hero.svelte            # Canvas particle bg + entrance
│   │   ├── Marquee.svelte         # Infinite scroll ticker
│   │   ├── About.svelte           # Sticky grid layout
│   │   ├── Projects.svelte        # Filter + card grid
│   │   ├── ProjectCard.svelte     # Individual card + hover
│   │   ├── ProjectModal.svelte    # Expandable detail modal
│   │   ├── Terminal.svelte        # Interactive CLI experience
│   │   ├── Timeline.svelte        # Experience section
│   │   ├── ContactForm.svelte     # Validated form
│   │   ├── Cursor.svelte          # Custom cursor
│   │   └── ThemeToggle.svelte     # Dark/light switch
│   ├── stores/
│   │   ├── theme.ts               # Persisted theme store
│   │   └── cursor.ts              # Cursor position store
│   ├── data/
│   │   ├── projects.ts            # Project data + types
│   │   └── experience.ts          # Work history
│   └── utils/
│       ├── terminal.ts            # Command handlers
│       └── animations.ts          # Reusable GSAP presets
├── app.css                        # CSS variables, reset, typography
└── app.html                       # HTML template
```

### Svelte-Specific Patterns Used

```svelte
<!-- Reactive state -->
<script>
  let filter = $state('all');
  let filteredProjects = $derived(
    filter === 'all' ? projects : projects.filter(p => p.category === filter)
  );
</script>

<!-- Svelte transitions (built-in) -->
{#each filteredProjects as project (project.id)}
  <div in:fly={{ y: 30, duration: 400, delay: i * 80 }}
       out:fade={{ duration: 200 }}>
    <ProjectCard {project} />
  </div>
{/each}

<!-- Svelte actions (custom directives) -->
function reveal(node) {
  const observer = new IntersectionObserver(([entry]) => {
    if (entry.isIntersecting) {
      node.classList.add('visible');
      observer.disconnect();
    }
  });
  observer.observe(node);
  return { destroy: () => observer.disconnect() };
}
```

-----

## 🎬 Animation Decisions

### 1. Particle Canvas (Hero)

An HTML5 Canvas particle network renders behind the hero text. Particles drift slowly with mouse-attraction forces. Implementation uses `requestAnimationFrame` with linear interpolation for the ring cursor — never blocks the main thread.

**Why:** Creates “alive” feeling without heavy WebGL setup. Canvas is hardware-accelerated and performant even on mid-range devices.

### 2. Page Load Sequence (Staggered CSS)

The loader runs for 1.8 seconds, then fades out. Hero elements reveal with `animation-delay` offsets:

```css
.hero-name    { animation-delay: 0.1s }
.hero-eyebrow { animation-delay: 0.3s }
.hero-tagline { animation-delay: 0.5s }
.hero-cta     { animation-delay: 0.7s }
```

**Why:** One orchestrated reveal creates more delight than scattered micro-interactions. The delays are short enough to feel snappy.

### 3. Scroll-Triggered Reveals (IntersectionObserver)

All sections use a reusable `reveal` class + `IntersectionObserver`. Elements start at `opacity: 0; transform: translateY(24px)` and transition to visible when 10% in-viewport.

**Why:** Avoids layout shifts, uses CSS transitions (GPU composited), and automatically disconnects observers after firing.

### 4. Project Card Hover

Cards use `transform: translateY(-6px)` + box-shadow on hover. The `project-thumb-bg` scales to 1.05x for a parallax feel. Both use `transition` with the custom `--ease-out` cubic bezier.

**Why:** No JavaScript needed. CSS transforms don’t trigger layout/paint — pure compositor animations.

### 5. Marquee

Infinite scroll via CSS `@keyframes marquee` with `translateX`. Two copies of the array are rendered so it loops seamlessly. Pauses on hover.

**Why:** Pure CSS, no scroll event listeners, no JavaScript timing. Extremely performant.

### 6. Terminal

The interactive terminal uses a command dictionary pattern — `{ commandName: () => HTMLString }`. History navigation uses arrow keys with a local array. Output is appended to the DOM (not re-rendered) to avoid scroll jank.

-----

## ⚡ Performance Optimization

|Technique                     |Implementation                                                                |
|------------------------------|------------------------------------------------------------------------------|
|`transform` only animations   |All animations use `transform` and `opacity` — no layout-triggering properties|
|`will-change: transform`      |Applied only to cards during hover, removed after                             |
|IntersectionObserver          |Replaces scroll event listeners — no per-frame polling                        |
|`{ passive: true }` listeners |All scroll/resize listeners are passive                                       |
|Canvas `requestAnimationFrame`|Particle animation synced to display refresh rate                             |
|Reduced motion support        |`@media (prefers-reduced-motion: reduce)` disables all animations             |
|Font `display: swap`          |Via Google Fonts parameter — prevents FOIT                                    |
|Lazy images                   |All project thumbnails use generated SVG/emoji — zero image requests          |
|Observer cleanup              |All IntersectionObservers disconnect after firing                             |

**Lighthouse targets:**

- Performance: 95+
- Accessibility: 100
- Best Practices: 100
- SEO: 95+

-----

## ♿ Accessibility Considerations

- **Semantic HTML**: `<nav>`, `<section>`, `<article>`, `<footer>`, `<h1-h3>` hierarchy throughout
- **Skip link**: `.skip-link` at top of document for keyboard users, visible on focus
- **ARIA labels**: All interactive elements have `aria-label`. Modal uses `role="dialog"` + `aria-modal="true"` + `aria-labelledby`
- **Focus management**: Modal traps focus; closing returns focus to trigger element
- **Keyboard navigation**: All interactive elements are `tabindex` reachable; project cards respond to Enter/Space
- **`aria-live` regions**: Form errors and terminal output use `aria-live="polite"`
- **Color contrast**: All text meets WCAG AA (4.5:1 minimum). Accent yellow (#e8ff47) on dark bg: 13:1 ratio
- **Focus styles**: `:focus-visible` rings on all interactive elements (overrides browser defaults)
- **Custom cursor**: Disabled via CSS on touch devices (`@media (hover: none)`)
- **Reduced motion**: Full animation kill-switch via `prefers-reduced-motion`
- **alt text**: Decorative elements are `aria-hidden="true"`; meaningful icons have `aria-label`

-----

## ⚖️ Trade-offs Made

|Decision                               |Rationale                                           |Trade-off                                                              |
|---------------------------------------|----------------------------------------------------|-----------------------------------------------------------------------|
|Single HTML file                       |Zero setup, instantly shareable, meets deadline     |Not a real Svelte build; for production, scaffold with `create svelte` |
|Emoji thumbnails vs real screenshots   |No image requests, fast load, works offline         |Less realistic-looking project cards                                   |
|CSS animations over GSAP               |No extra JS bundle, composited by default           |Less control over complex sequences                                    |
|Canvas particles vs Three.js           |Simpler, smaller, same visual impact                |Can’t do 3D effects or complex geometry                                |
|Custom cursor CSS blend mode           |`mix-blend-mode: difference` makes it work on any bg|Doesn’t work on transparent backgrounds                                |
|IntersectionObserver over scroll events|Zero per-frame cost, disconnects after use          |Less fine-grained control over scroll position                         |
|Form simulation vs real email          |No backend dependency, no rate limits               |Doesn’t actually send emails; integrate Resend/Formspree for production|
|Vanilla JS terminal vs xterm.js        |0KB overhead, fully custom                          |Missing real terminal features (tab completion, ANSI colors)           |
|`localStorage` for theme               |Instant theme persistence, no flicker               |Requires JS; could add `<script>` in `<head>` to read before paint     |

-----

## 🎨 Design Decisions

**Aesthetic:** Industrial-editorial. Raw display typography (Bebas Neue) paired with minimal body text (DM Sans) and monospace accents (JetBrains Mono). The portfolio itself feels like a tool — no decorative clutter.

**Color:** Near-black background (#0a0a0a) with chartreuse accent (#e8ff47). Chosen for maximum contrast and unexpected combination — breaks from the typical blue/purple developer portfolio palette.

**Layout:** Full-bleed hero with oversized type. Content sections use a max-width container for readability. Asymmetric two-column about. CSS Grid for projects.

-----

## 📦 Deployment

```bash
# Vercel (recommended for SvelteKit)
npm i -g vercel
vercel

# Netlify
npm run build
# Drag dist/ to app.netlify.com

# Cloudflare Pages
npm run build
# Connect GitHub repo in CF dashboard
```

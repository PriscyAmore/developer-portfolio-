# developer-portfolio-

# Ogwezhi Priscilla Chioma — Interactive Developer Portfolio

> A production-grade developer portfolio built with Svelte architecture principles, featuring a particle-based hero, interactive terminal, animated project showcase, and full dark/light theme support.

**Live Demo:** https://priscyamore.github.io/developer-portfolio

**GitHub:** https://github.com/PriscyAmore/developer-portfolio

---

## 🚀 Setup Instructions

### Option A: Single File (Instant)
```bash
# Just open in browser — zero dependencies
open index.html


Option B: SvelteKit (Production)

npm create svelte@latest priscilla-portfolio
cd priscilla-portfolio
npm install
npm install gsap @motionone/svelte
npm run dev


Requirements: Any modern browser (Chrome 90+, Firefox 88+, Safari 14+). No build step needed for the single-file version.

🏗 Architecture Explanation
Component Structure (SvelteKit)

src/
├── routes/
│   ├── +layout.svelte        # Nav, cursor, theme, page transitions
│   ├── +page.svelte          # Home (Hero + all sections)
│   └── projects/[slug]/
│       └── +page.svelte      # Dynamic project pages
├── lib/
│   ├── components/
│   │   ├── Hero.svelte            # Canvas particle bg + entrance animations
│   │   ├── Marquee.svelte         # Infinite scroll skill ticker
│   │   ├── About.svelte           # Two-column grid layout
│   │   ├── Projects.svelte        # Filter tabs + card grid
│   │   ├── ProjectCard.svelte     # Card + hover effects
│   │   ├── ProjectModal.svelte    # Expandable detail modal
│   │   ├── Terminal.svelte        # Interactive CLI experience
│   │   ├── Timeline.svelte        # Experience section
│   │   ├── ContactForm.svelte     # Validated contact form
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
│       └── animations.ts          # Reusable animation presets
├── app.css                        # CSS variables, reset, typography
└── app.html                       # HTML shell


🎬 Animation Decisions
1. Particle Canvas (Hero)
An HTML5 Canvas particle network renders behind the hero text. Particles drift slowly with mouse-attraction forces using requestAnimationFrame and linear interpolation.
Why: Creates an “alive” feeling without heavy WebGL. Hardware-accelerated and performant on mid-range devices.
2. Page Load Sequence
Loader runs for 1.9 seconds then fades out. Hero elements reveal with staggered animation-delay offsets (0.1s → 0.65s).
Why: One orchestrated reveal feels more intentional than scattered micro-interactions.
3. Scroll-Triggered Reveals
All sections use a reveal class + IntersectionObserver. Elements start at opacity: 0; transform: translateY(22px) and transition in when 10% in-viewport.
Why: GPU-composited CSS transitions, no scroll event listeners, observers disconnect after firing.
4. Project Card Hover
Cards use transform: translateY(-5px) + box-shadow. Thumbnail scales to 1.06x for a subtle parallax feel.
Why: Pure CSS — no JavaScript. Compositor-only, no layout/paint triggered.
5. Marquee
Infinite scroll via CSS @keyframes with translateX. Two array copies render seamlessly. Pauses on hover.
Why: Zero JavaScript timing. Extremely performant.
6. Terminal
Command dictionary pattern — { commandName: () => HTMLString }. Arrow key history navigation. Output appended to DOM without re-render.

⚡ Performance Optimization



|Technique                     |Implementation                                                   |
|------------------------------|-----------------------------------------------------------------|
|`transform` only animations   |All animations use `transform` and `opacity` only                |
|IntersectionObserver          |Replaces scroll event listeners — no per-frame polling           |
|`{ passive: true }` listeners |All scroll/resize listeners are passive                          |
|Canvas `requestAnimationFrame`|Particle animation synced to display refresh rate                |
|Reduced motion support        |`@media (prefers-reduced-motion: reduce)` disables all animations|
|Font `display: swap`          |Via Google Fonts — prevents FOIT                                 |
|Zero image requests           |Project thumbnails use emoji + CSS gradients                     |
|Observer cleanup              |All IntersectionObservers disconnect after firing                |

Lighthouse targets: Performance 95+ · Accessibility 100 · Best Practices 100 · SEO 95+

♿ Accessibility Considerations
	∙	Semantic HTML: <nav>, <section>, <article>, <footer>, proper heading hierarchy
	∙	Skip link: Visible on focus for keyboard users
	∙	ARIA labels: All interactive elements labelled. Modal uses role="dialog" + aria-modal="true"
	∙	Keyboard navigation: All cards respond to Enter/Space; modal traps focus
	∙	aria-live regions: Form errors and terminal output announced to screen readers
	∙	Color contrast: All text meets WCAG AA (4.5:1 minimum)
	∙	Focus styles: :focus-visible rings on all interactive elements
	∙	Custom cursor: Disabled on touch devices via @media (hover: none)
	∙	Reduced motion: Full animation kill-switch via prefers-reduced-motion
	∙	Decorative elements: All marked aria-hidden="true"

⚖️ Trade-offs Made



|Decision                    |Rationale                                      |Trade-off                                                              |
|----------------------------|-----------------------------------------------|-----------------------------------------------------------------------|
|Single HTML file            |Zero setup, meets deadline, instantly shareable|Not a real Svelte build; production would use `create svelte`          |
|Emoji thumbnails            |No image requests, fast load                   |Less realistic project cards                                           |
|CSS animations over GSAP    |No extra JS bundle, composited by default      |Less control over complex sequences                                    |
|Canvas particles vs Three.js|Simpler, smaller, same visual impact           |No 3D effects                                                          |
|Form simulation             |No backend dependency                          |Doesn’t actually send emails; integrate Resend/Formspree for production|
|Vanilla JS terminal         |0KB overhead, fully custom                     |Missing real terminal features                                         |
|`localStorage` for theme    |Instant persistence, no flash                  |Requires JS                                                            |

🎨 Design Decisions
Aesthetic: Editorial-industrial. Raw display typography (Bebas Neue) paired with clean body text (DM Sans) and monospace accents (JetBrains Mono).
Color: Near-black background with soft violet accent (#c084fc). Chosen for elegance and contrast — breaks from typical blue developer portfolios.
Layout: Full-bleed hero with oversized type. Asymmetric two-column about section. CSS Grid for projects.

📦 Deployment
This portfolio is deployed via GitHub Pages as a single HTML file.
For SvelteKit deployment:

# Vercel
npx vercel --prod

# Netlify
npm run build && drag dist/ to app.netlify.com

# Cloudflare Pages
# Connect GitHub repo in CF dashboard


Built with 💜 by Ogwezhi Priscilla Chioma


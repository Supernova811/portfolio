# Portfolio — UI Review

**Audited:** 2026-04-15
**Baseline:** Abstract 6-pillar standards (no UI-SPEC.md)
**Screenshots:** Not captured (no dev server running — code-only audit)

---

## Pillar Scores

| Pillar | Score | Key Finding |
|--------|-------|-------------|
| 1. Copywriting | 4/4 | Specific, confident, outcome-focused copy throughout — no generic labels found |
| 2. Visuals | 3/4 | Strong hierarchy and structure; nav brand is a non-interactive div, no visual feedback state |
| 3. Color | 3/4 | Well-scoped token system; 51 inline `style=` blocks introduce ~20 hardcoded hex values outside the token system |
| 4. Typography | 2/4 | 16 distinct font sizes in use — far exceeding a disciplined scale; typography system is not enforced |
| 5. Spacing | 3/4 | Generally consistent; 51 inline style blocks mix ad-hoc spacing values that bypass the established rhythm |
| 6. Experience Design | 3/4 | Good accessibility foundation; missing `rel="noopener noreferrer"` on 9 external links, no focus ring on contact cards or CTA links |

**Overall: 18/24**

---

## Top 3 Priority Fixes

1. **Typography scale has 16 distinct sizes (11–44px)** — Hiring managers scanning this on a 13" laptop will perceive visual noise and a lack of editorial control, which is a bad signal for a curriculum designer whose job is clarity. Collapse to a strict 5-size scale: 12px (labels), 14px (body/small), 17px (subtext), 24px (card headings), 32px (section headings), 44px (hero). Enforce via CSS classes — eliminate all inline `font-size` declarations.

2. **51 inline `style=` blocks scatter hardcoded hex values and arbitrary spacing outside the token system** — The Research section, featured project banner, and all case study content links use raw inline styles (`background:#fffbeb`, `padding:24px 28px`, `margin-bottom:16px`). This is a maintainability and consistency liability. Extract these into named CSS classes. Every color value must reference a `--variable`. Every spacing value should use the established rhythm (multiples of 4px or 8px).

3. **9 `target="_blank"` links are missing `rel="noopener noreferrer"`** — This is a security vulnerability (tabnapping) and also a flag that automated LinkedIn recruiter screening tools and some browsers will surface. Lines 715, 844, 996, 1070, 1107, 1139, 1159, 1194, 1312. Add `rel="noopener noreferrer"` to all external-opening links.

---

## Detailed Findings

### Pillar 1: Copywriting (4/4)

No generic labels found. Zero instances of "Submit", "Click Here", "OK", "Cancel", "Learn More", or "Read More". Every CTA is specific and action-oriented:

- "View My Work" (hero, primary CTA)
- "View Resume" (hero, secondary CTA)
- "Launch Module" (featured project)
- "View Case Study" (featured project)
- "View TitanPE case study" (card link)
- "Close Case Study" (case study dismiss)
- "Let's Work Together" (contact section heading)

The hero blockquote (line 709) is genuinely distinctive and communicates instructional philosophy rather than being a generic credential dump. The availability badge copy ("Actively seeking remote roles") is specific and actionable for the hiring manager. The "I respond within 24 hours" commitment on the contact section (line 1297) is a strong trust signal.

The About section copy (lines 728–732) is strong: "I know what works because I've measured it in actual classrooms" is specific and credible. No shame language, no filler.

Minor: The footer tagline (line 1329) is purely functional ("Instructional Designer & Curriculum Developer") — acceptable for a footer but a small opportunity for a differentiating line.

Score justification: No generic patterns found anywhere in the file. Copy is consistent with the professional positioning.

---

### Pillar 2: Visuals (3/4)

**Strengths:**
- Clear focal point established: hero with avatar photo, name, title, availability badge, credentials badges, philosophy quote, dual CTAs. Strong top-of-page reading order.
- Visual hierarchy via color differentiation: dark navy hero creates immediate contrast from white body sections; alternating section backgrounds (`#fff` / `#f8fafc`) provide rhythm without distraction.
- Card banner system (lines 391–396) uses 6 distinct gradient schemes (blue, green, amber, purple, red, orange) to differentiate project types visually — effective at a glance.
- Icon-only elements all have supporting labels: hamburger button has `aria-label` (line 669), project cards have `aria-label` on the card-level role="button" (lines 856–947).
- Availability dot pulse animation respects `prefers-reduced-motion` (line 175) — thoughtful implementation.
- Smooth case study expand/collapse with opacity transition (lines 438–457) provides polish.

**Issues:**
- `nav-brand` is a `<div>` (line 668), not a link. Convention on portfolio sites is that the brand/name in the nav returns to the top of the page. A hiring manager clicking the name will get no response — unexpected behavior.
- The contact cards (`.contact-card`) have hover state (line 587) but no visible `:focus` style. A keyboard user tabbing to the email or LinkedIn cards gets no visible focus indicator on the card itself.
- The featured project banner (line 839) is a pure inline-styled `<div>` with no semantic role or landmark. It renders well but is invisible to assistive technology navigation.
- Case study "case4" is missing from the numbering sequence (cases 1–3, 5–8). This is not a user-facing problem but indicates a deleted project left a gap in the ID scheme — minor cleanup item.

---

### Pillar 3: Color (3/4)

**Token system:** Well-defined at lines 24–40. 11 semantic tokens (`--primary`, `--primary-dark`, `--primary-light`, `--primary-bg`, `--accent`, `--accent-hover`, `--success`, `--text`, `--text-light`, `--bg`, `--bg-alt`, `--border`). Token usage count: 31 references to `var(--primary*` family.

**Accent usage:** `--accent` (#f59e0b amber) is used appropriately: hero primary CTA background, hero-quote left border, featured-cta primary. Not overused on decorative elements.

**Hardcoded hex violations (outside token system):**
- Lines 391–396: card banner gradients use 12 hardcoded hex values (#1e3a5f, #1e40af, #065f46, #10b981, #78350f, #f59e0b, #4c1d95, #8b5cf6, #7f1d1d, #dc2626, #431407, #c2410c). These are acceptable because they represent project category colors intentionally distinct from the brand palette, but they should at minimum be CSS custom properties or named classes rather than inline values.
- Lines 527–528: skill card border-top colors (#3b82f6, #8b5cf6) are hardcoded — these match `--primary-light` and a purple not in the token system.
- Lines 553–556: skill pill color combinations (backgrounds, borders, text colors) use 12 hardcoded hex values that are not in the token set. The WCAG compliance comment is noted, but the values should be variables.
- Line 811 (Research section inline style): `background:#fffbeb;border:1px solid #fde68a` — amber callout uses raw hex not mapped to `--accent` variants.
- Line 160: `.availability-badge` color `#6ee7b7` is hardcoded — not in the token system.

**Overall:** The core brand palette is clean and disciplined. The bleed of hardcoded hex into inline styles and multi-color card systems adds technical debt without breaking the visual result. No egregious accent overuse or contrast failures detected in the primary design.

---

### Pillar 4: Typography (2/4)

**Font sizes in use:** 16 distinct values — 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 22, 24, 26, 30, 32, 44px.

This is the most significant finding in the audit. A disciplined typographic scale for a professional portfolio should use 5–6 sizes maximum. 16 distinct sizes means nearly every component has its own ad-hoc size judgment, which creates visual inconsistency and communicates a lack of system thinking — a particularly bad signal for a curriculum developer role.

**Notable violations:**
- 15px appears alongside 14px and 16px in adjacent contexts (skill card header h3 at 15px, card body p at 14px, CTA at 15px, hero title at 20px with lead at 18px and quote at 17px).
- 19px is used only for Research section h3 (inline style, lines 780, 808) — appears nowhere else and sits awkwardly between 18px and 20px.
- 22px used only for About and Education h3 (lines 329, 734 inline) — does not map to any scale step.
- 26px used only for mobile hero h1 at `max-width: 480px` (line 655) — this responsive override is fine but adds to the count.

**Font weights:** 4 in use — 500 (italic quote), 600 (secondary labels, nav links), 700 (headings, CTAs, card titles), 800 (hero h1, section h2, feature headings). Four weights is reasonable, but 500 appears only once (hero-quote), making it an orphaned weight.

**Recommendation:** Establish a declared type scale in the `:root` block:
```css
--text-xs: 12px;    /* labels, tags, meta */
--text-sm: 14px;    /* body small, card descriptions */
--text-base: 17px;  /* body default, section subtext */
--text-lg: 24px;    /* card headings, case study subheads */
--text-xl: 32px;    /* section headings */
--text-hero: 44px;  /* hero h1 only */
```
Eliminate 19px, 22px, 15px, 16px, 18px, 20px, 26px, 30px. Replace all inline `font-size` with CSS classes using these variables.

---

### Pillar 5: Spacing (3/4)

**Spacing rhythm analysis:**

The CSS-defined spacing shows a generally coherent system based on 4px/8px increments: 4px, 6px, 8px, 10px, 12px, 14px, 16px, 20px, 24px, 28px, 32px, 40px, 48px, 80px, 100px, 140px. Section padding is consistently 80px vertical / 24px horizontal. Container max-width is consistently 1100px. Card body padding is consistently 24px.

**Issues:**
- 51 inline `style=` blocks (confirmed by grep). Many mix spacing values that are contextually reasonable but untethered from any declared system. Examples: `padding:24px 28px` (Research card headers, lines 778/782/806/810), `gap:16px;margin-bottom:16px` (Research stats row, line 783), `padding:12px 16px` (stat cells, lines 784/788). None of these values are problematic individually, but they create a maintenance surface where changing the base rhythm requires hunting inline strings.
- `margin-bottom:4px` on the Research section label (line 779) vs `margin-bottom:6px` on the card type labels in the portfolio section (line 406) — 4px vs 6px for the same visual element type is an inconsistency.
- The featured project banner uses `padding:40px` (line 839 inline) matching the case study open state padding (line 454 CSS) — that's actually consistent, but it's defined in two places.

**Responsive spacing:** Mobile breakpoints reduce hero padding to `100px 16px 60px` at 768px and `100px 16px 60px` at 480px with section padding dropping to `56px 16px`. This is a reasonable responsive reduction and shows deliberate mobile consideration.

---

### Pillar 6: Experience Design (3/4)

**States coverage:**

- Loading: No explicit loading states — appropriate for a static portfolio with no async operations. CDN scripts (Lucide, Google Fonts) have no fallback indication if they fail, but this is a minor concern for a portfolio context.
- Error: Single `onerror` handler on the hero avatar image (line 685) hides the broken image rather than showing a fallback silhouette. This is the correct behavior and shows defensive coding.
- Empty: No empty states needed — content is static.
- Disabled: No form elements — not applicable.
- Destructive actions: None in this context — not applicable.

**Accessibility:**
- Skip link implemented and functional (lines 57–68, 663) — WCAG 2.4.1 compliant.
- `lang="en"` on html element (line 2) — present.
- All interactive non-button elements (`role="button"`) have keyboard handlers for Enter and Space (lines 856–947) — correct pattern.
- `aria-hidden` toggled correctly on case study expand/collapse (lines 1377–1392).
- `aria-expanded` maintained on hamburger button (lines 1361–1368).
- Focus management on case study open: `el.focus()` called after `scrollIntoView` (lines 1383–1386) — thoughtful.
- Scroll spy marks active nav link (lines 1342–1357) — polished UX detail.

**Gaps:**
- 9 `target="_blank"` links missing `rel="noopener noreferrer"` (lines 715, 844, 996, 1070, 1107, 1139, 1159, 1194, 1312). This is a security vulnerability and a professional oversight for a developer portfolio. The one exception is line 1312 (LinkedIn) which should also have it.
- Contact cards (`.contact-card`, line 573) have `transition` and hover state but no `:focus-visible` style. A keyboard user sees no ring when tabbing to email or LinkedIn.
- CTA links (`.hero-cta`) have no visible focus ring beyond browser default. The hamburger and project cards have custom focus styles; the primary CTAs do not — inconsistent.
- The nav brand `<div>` (line 668) is not a link and not keyboard-accessible. Convention is for the brand to be an `<a href="#">` or `<a href="/">` that returns to top.
- `<nav>` element uses `role="navigation"` (line 666) — this is redundant (nav has an implicit role) but not harmful.
- The Research section cards (lines 774–827) are inline-styled `<div>` blocks with no interactive behavior, no landmark role, and no heading association at the section level. They are purely presentational, which is fine, but they lack `aria-label` on the outer cards despite being visually distinct content regions.

---

## Files Audited

- `C:/Users/Titan/portfolio-site/index.html` — 1396 lines, full HTML/CSS/JS portfolio (single file)

No other source files found in the portfolio-site directory relevant to this audit. Screenshots were not captured (no dev server detected on ports 3000, 5173, or 8080).

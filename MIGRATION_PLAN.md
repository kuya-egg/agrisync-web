# Agrisync SvelteKit + PocketBase Migration Plan

## Goal

Migrate Agrisync from Astro to a full SvelteKit application with no Astro dependency. The new site should keep the same public pages and visual identity while moving landing page content, legal content, and contact form submissions into PocketBase.

The end state should be:

- SvelteKit handles routing, SSR, metadata, and form actions.
- PocketBase acts as the headless CMS and contact submission store.
- Existing Agrisync branding, illustrations, layout language, and page structure are preserved.
- Content is editable from PocketBase without requiring code changes for common marketing/legal updates.
- The contact form submits through a server-side SvelteKit action.

## Note
- Always use Bun and the svelte skill and typescript skills

## Current Site Inventory

Current Astro pages:

- `/` from `src/pages/index.astro`
- `/contact` from `src/pages/contact.astro`
- `/privacy-policy` from `src/pages/privacy-policy.astro`
- `/terms-of-service` from `src/pages/terms-of-service.astro`

Current shared files:

- `src/components/landing/LandingPage.svelte`
- `src/components/LegalPage.astro`
- `src/data/landing.ts`
- `src/styles/global.css`
- Static assets under `public/`

The existing landing page is already Svelte-based, so the migration should focus on replacing Astro routing, splitting the large landing component, and moving content into server-loaded PocketBase data.

## Migration Phases

### Phase 0: Baseline Audit

Goal: confirm what exists before changing the framework.

Tasks:

- [ ] Inventory all current Astro pages.
- [ ] Inventory existing Svelte components, CSS, data files, and public assets.
- [ ] Save the current visual behavior as the baseline for later QA.
- [ ] Confirm target hosting adapter for SvelteKit SSR.
- [ ] Confirm PocketBase hosting location and admin access strategy.

Done when:

- [ ] The current page list, asset list, and route map are known.
- [ ] The SvelteKit adapter choice is decided.
- [ ] There is a clear rollback point, ideally a branch or commit.

### Phase 1: SvelteKit Foundation

Goal: replace Astro's project shell with SvelteKit while keeping behavior simple.

Tasks:

- [ ] Scaffold or convert the project to SvelteKit.
- [ ] Configure Svelte 5, TypeScript, Tailwind CSS 4, and the chosen adapter.
- [ ] Move `src/styles/global.css` to `src/app.css`.
- [ ] Create `src/app.html`.
- [ ] Create `src/routes/+layout.svelte`.
- [ ] Import global CSS in the SvelteKit layout.
- [ ] Keep all existing public assets under `public/`.

Done when:

- [ ] The SvelteKit dev server starts.
- [ ] A placeholder home page renders.
- [ ] Tailwind and existing CSS variables work.
- [ ] No PocketBase integration is added yet.

### Phase 2: Static Route Parity

Goal: recreate the same public pages in SvelteKit using current static content.

Tasks:

- [ ] Create `src/routes/+page.svelte` for `/`.
- [ ] Create `src/routes/contact/+page.svelte` for `/contact`.
- [ ] Create `src/routes/privacy-policy/+page.svelte` for `/privacy-policy`.
- [ ] Create `src/routes/terms-of-service/+page.svelte` for `/terms-of-service`.
- [ ] Temporarily keep `src/data/landing.ts` as the landing page data source.
- [ ] Port the Astro legal content into plain Svelte data or temporary route data.

Done when:

- [ ] All current URLs render in SvelteKit.
- [ ] The site still works without PocketBase.
- [ ] The current Astro pages have SvelteKit equivalents.

### Phase 3: Component Extraction

Goal: split the large landing page into maintainable Svelte components.

Tasks:

- [ ] Move shared navigation into layout components.
- [ ] Split landing sections into focused components.
- [ ] Replace Astro legal page template with Svelte legal components.
- [ ] Create contact form components.
- [ ] Keep CMS data transformation out of UI components.
- [ ] Keep section components mostly presentational.

Done when:

- [ ] `LandingPage.svelte` composes sections instead of containing the entire page.
- [ ] Header, footer, landing sections, legal page, and contact form are reusable.
- [ ] Pricing toggle, mobile menu, FAQ details, and reveal animations still work.

### Phase 4: Styling Parity And Design System

Goal: preserve the Agrisync visual identity while making styling easier to maintain.

Tasks:

- [ ] Preserve current color tokens, typography, rounded surfaces, and illustration style.
- [ ] Confirm mobile layouts have no text overflow.
- [ ] Extract repeated UI classes into reusable components where useful.
- [ ] Keep the hero as the first-viewport brand signal.
- [ ] Preserve app store badges, phone mockups, feature media, and legal page styling.
- [ ] Keep motion subtle and respect `prefers-reduced-motion`.

Done when:

- [ ] SvelteKit pages visually match the current Astro site.
- [ ] Mobile and desktop layouts are stable.
- [ ] No major layout shifts happen when images or dynamic content load.

### Phase 5: PocketBase Schema And Seeding

Goal: prepare PocketBase as the CMS without changing frontend behavior yet.

Tasks:

- [ ] Create PocketBase collections for settings, navigation, landing content, pricing, testimonials, FAQs, legal pages, and contact submissions.
- [ ] Add sensible list/view/create/update/delete rules.
- [ ] Seed current content from `src/data/landing.ts` and existing legal pages.
- [ ] Upload or reference needed assets.
- [ ] Decide which images stay in `public/` and which move to PocketBase file fields.

Done when:

- [ ] PocketBase contains the current content.
- [ ] Published CMS records can be queried.
- [ ] Admin-only records and contact submissions are protected.

### Phase 6: Server-Side PocketBase Integration

Goal: move content loading into SvelteKit SSR.

Tasks:

- [ ] Install the PocketBase JavaScript SDK.
- [ ] Create `src/lib/server/pocketbase.ts`.
- [ ] Create `src/lib/server/cms.ts`.
- [ ] Add environment variables for `POCKETBASE_URL` and site URL.
- [ ] Load home page CMS data in `src/routes/+page.server.ts`.
- [ ] Load legal page CMS data in legal route server files.
- [ ] Normalize PocketBase file URLs server-side.
- [ ] Pass plain serializable data to Svelte components.

Done when:

- [ ] Home, privacy, and terms content render from PocketBase during SSR.
- [ ] The frontend does not call PocketBase directly for CMS reads.
- [ ] The site still renders useful fallback errors if CMS records are missing.

### Phase 7: Contact Form Action

Goal: replace the mailto-only contact experience with a real server-side form.

Tasks:

- [ ] Add contact validation with Zod or Valibot.
- [ ] Create `src/routes/contact/+page.server.ts`.
- [ ] Add a default SvelteKit form action.
- [ ] Write valid submissions to `contact_submissions`.
- [ ] Return field-level validation errors.
- [ ] Return a clear success state.
- [ ] Add spam mitigation such as a honeypot field or rate limiting if public create is allowed.

Done when:

- [ ] Contact form works without JavaScript.
- [ ] Invalid fields show useful errors.
- [ ] Valid submissions appear in PocketBase.
- [ ] Contact submission list/view/update/delete remain admin-only.

### Phase 8: SEO, Sitemap, And Metadata

Goal: preserve and improve search/social metadata after leaving Astro.

Tasks:

- [ ] Create `src/lib/components/seo/Seo.svelte`.
- [ ] Move current home metadata into the SEO component.
- [ ] Add canonical URLs for every page.
- [ ] Add Open Graph and Twitter card metadata.
- [ ] Add `src/routes/sitemap.xml/+server.ts`.
- [ ] Add `src/routes/robots.txt/+server.ts`.
- [ ] Confirm SSR output includes metadata.

Done when:

- [ ] Every public route has correct title, description, canonical URL, and social image.
- [ ] Sitemap and robots routes respond correctly.
- [ ] The home page keeps its current keyword intent.

### Phase 9: Astro Removal And Cleanup

Goal: remove Astro once the SvelteKit version is functionally complete.

Tasks:

- [ ] Remove Astro pages and components.
- [ ] Remove `astro.config.mjs`.
- [ ] Remove Astro dependencies from `package.json`.
- [ ] Update scripts to use SvelteKit.
- [ ] Update README instructions.
- [ ] Remove unused imports, files, and generated Astro folders if appropriate.

Done when:

- [ ] `package.json` no longer depends on Astro.
- [ ] `npm`, `bun`, or chosen package manager scripts run SvelteKit.
- [ ] Build and preview use the SvelteKit adapter.

### Phase 10: Final QA And Launch

Goal: verify the migrated site end to end.

Tasks:

- [ ] Run type checks.
- [ ] Run production build.
- [ ] Preview production build locally.
- [ ] Test all routes.
- [ ] Test contact submission success and failure states.
- [ ] Test PocketBase unavailable or missing-content behavior.
- [ ] Check mobile and desktop layouts.
- [ ] Check SSR metadata.
- [ ] Compare visuals against the original Astro site.

Done when:

- [ ] Build passes.
- [ ] Public pages work.
- [ ] PocketBase content loads through SSR.
- [ ] Contact submissions work.
- [ ] Styling matches the current Agrisync identity.
- [ ] Astro has been fully removed.

## Target File Structure

```txt
src/
  app.css
  app.html
  hooks.server.ts
  routes/
    +layout.svelte
    +layout.server.ts
    +page.svelte
    +page.server.ts
    contact/
      +page.svelte
      +page.server.ts
    privacy-policy/
      +page.svelte
      +page.server.ts
    terms-of-service/
      +page.svelte
      +page.server.ts
    robots.txt/
      +server.ts
    sitemap.xml/
      +server.ts
  lib/
    components/
      contact/
      landing/
      layout/
      legal/
      seo/
      ui/
    server/
      pocketbase.ts
      cms.ts
      validators.ts
    types/
      cms.ts
```

## SvelteKit Pages

### Home Page

Route:

```txt
src/routes/+page.svelte
src/routes/+page.server.ts
```

Responsibilities:

- Render the landing page.
- Load all published landing content from PocketBase.
- Render SEO metadata server-side.
- Keep interactive UI such as mobile navigation, pricing toggle, FAQ details, and reveal animations in Svelte.

Server-loaded data:

- Site settings
- Navigation links
- Trust badges
- Problem/solution lists
- Farming phases
- Anie highlights
- Feature showcase items
- Pricing plans
- Testimonials
- FAQs
- Download links
- Footer links

### Contact Page

Route:

```txt
src/routes/contact/+page.svelte
src/routes/contact/+page.server.ts
```

Responsibilities:

- Render a proper contact form.
- Validate form input server-side.
- Create a PocketBase `contact_submissions` record.
- Return success and error states through SvelteKit form actions.
- Keep `hello@agrisync.ph` as fallback contact information.

Suggested form fields:

- Name
- Email
- Subject
- Message
- Optional inquiry type

### Privacy Policy Page

Route:

```txt
src/routes/privacy-policy/+page.svelte
src/routes/privacy-policy/+page.server.ts
```

Responsibilities:

- Load the `privacy-policy` legal page record from PocketBase.
- Render the shared legal page component.
- Include canonical URL and Open Graph metadata.

### Terms Of Service Page

Route:

```txt
src/routes/terms-of-service/+page.svelte
src/routes/terms-of-service/+page.server.ts
```

Responsibilities:

- Load the `terms-of-service` legal page record from PocketBase.
- Render the shared legal page component.
- Include canonical URL and Open Graph metadata.

## Components To Build

### Layout Components

```txt
src/lib/components/layout/SiteHeader.svelte
src/lib/components/layout/MobileNav.svelte
src/lib/components/layout/SiteFooter.svelte
```

Use these for navigation, footer links, mobile menu state, and brand shell. The header should preserve the rounded pill navigation style currently used on the home page.

### SEO Components

```txt
src/lib/components/seo/Seo.svelte
```

Props:

- `title`
- `description`
- `canonical`
- `ogImage`
- `type`
- `keywords`

This component should render `<svelte:head>` metadata consistently across all pages.

### Landing Components

```txt
src/lib/components/landing/LandingPage.svelte
src/lib/components/landing/HeroSection.svelte
src/lib/components/landing/TrustBadges.svelte
src/lib/components/landing/ProblemSolutionSection.svelte
src/lib/components/landing/FarmingPhases.svelte
src/lib/components/landing/AnieSection.svelte
src/lib/components/landing/FeatureShowcase.svelte
src/lib/components/landing/PricingSection.svelte
src/lib/components/landing/TestimonialsSection.svelte
src/lib/components/landing/FaqSection.svelte
src/lib/components/landing/DownloadSection.svelte
src/lib/components/landing/PhoneMockup.svelte
src/lib/components/landing/SectionHeader.svelte
src/lib/components/landing/InfoPanel.svelte
```

Guidelines:

- `LandingPage.svelte` should compose sections, not hold all markup.
- Keep pricing toggle state inside `PricingSection.svelte`.
- Keep reveal animation setup inside a small component/helper used by sections.
- Keep CMS data transformation in server files, not inside UI components.

### Legal Components

```txt
src/lib/components/legal/LegalPage.svelte
src/lib/components/legal/LegalSection.svelte
```

Responsibilities:

- Replace `src/components/LegalPage.astro`.
- Render the same page structure for privacy and terms.
- Receive all content from `+page.server.ts`.

### Contact Components

```txt
src/lib/components/contact/ContactForm.svelte
src/lib/components/contact/FormField.svelte
src/lib/components/contact/SubmitButton.svelte
```

Responsibilities:

- Render accessible labels and validation messages.
- Support SvelteKit form action responses.
- Work without client-side JavaScript.
- Optionally add progressive enhancement later.

## PocketBase Collections

### `site_settings`

Use for global brand and metadata.

Fields:

- `site_name` text
- `site_url` url
- `contact_email` email
- `default_description` text
- `default_og_image` file or url
- `app_store_url` url
- `google_play_url` url

Recommended rules:

- Public list/view allowed for published settings.
- Create/update/delete admin-only.

### `navigation_links`

Fields:

- `label` text
- `href` text
- `location` select: `header`, `footer`
- `sort_order` number
- `enabled` bool

### `landing_page`

Fields:

- `title` text
- `seo_title` text
- `seo_description` text
- `keywords` text
- `hero_badge` text
- `hero_title` text
- `hero_description` text
- `hero_cta_label` text
- `hero_cta_href` text
- `hero_image` file
- `published` bool

### `landing_sections`

Use for section-level headings and body copy.

Fields:

- `key` text
- `eyebrow` text
- `title` text
- `body` editor or text
- `sort_order` number
- `enabled` bool

Suggested keys:

- `problem_solution_intro`
- `farming_phases`
- `anie`
- `features`
- `pricing`
- `testimonials`
- `faq`
- `download`

### `landing_items`

Use for repeatable section content.

Fields:

- `section_key` text
- `title` text
- `subtitle` text
- `question` text
- `description` text
- `image` file
- `benefits` json
- `metadata` json
- `sort_order` number
- `enabled` bool

Use this for:

- Problems
- Solutions
- Farming phases
- Anie highlights
- Feature cards
- Trust badges

### `pricing_plans`

Fields:

- `name` text
- `price_monthly` text
- `price_yearly` text
- `target` text
- `cta_label` text
- `cta_href` text
- `recommended` bool
- `features` json
- `sort_order` number
- `enabled` bool

### `testimonials`

Fields:

- `quote` text
- `name` text
- `detail` text
- `image` file
- `sort_order` number
- `enabled` bool

### `faqs`

Fields:

- `question` text
- `answer` text
- `sort_order` number
- `enabled` bool

### `legal_pages`

Fields:

- `slug` text
- `title` text
- `eyebrow` text
- `description` text
- `updated` date
- `sections` json
- `published` bool

Example `sections` JSON:

```json
[
  {
    "title": "Information We Collect",
    "body": [
      "Paragraph one.",
      "Paragraph two."
    ]
  }
]
```

### `contact_submissions`

Fields:

- `name` text
- `email` email
- `subject` text
- `message` text
- `inquiry_type` select
- `source` text
- `status` select: `new`, `read`, `replied`, `archived`
- `user_agent` text

Recommended rules:

- Public create can be allowed only if spam protection is added.
- Safer approach: keep public create disabled and create submissions through a SvelteKit server action using trusted server credentials.
- List/view/update/delete should be admin-only.

## PocketBase Records To Seed

Use these as the initial CMS records. The values come from the current Astro/Svelte site and `src/data/landing.ts`.

### `site_settings`

Create one record:

```json
{
  "site_name": "Agrisync",
  "site_url": "https://agrisync.online",
  "contact_email": "hello@agrisync.ph",
  "default_description": "Agrisync helps Filipino farmers plan crops, monitor growth, prepare for climate risks, and sell with confidence using AI-powered farming assistance.",
  "default_og_image": "/og-image.png",
  "app_store_url": "https://apps.apple.com/",
  "google_play_url": "https://play.google.com/store/apps"
}
```

### `navigation_links`

Header records:

```json
[
  { "label": "Home", "href": "#home", "location": "header", "sort_order": 10, "enabled": true },
  { "label": "Features", "href": "#features", "location": "header", "sort_order": 20, "enabled": true },
  { "label": "Pricing", "href": "#pricing", "location": "header", "sort_order": 30, "enabled": true },
  { "label": "About", "href": "#about", "location": "header", "sort_order": 40, "enabled": true },
  { "label": "FAQ", "href": "#faq", "location": "header", "sort_order": 50, "enabled": true },
  { "label": "Download", "href": "#download", "location": "header", "sort_order": 60, "enabled": true }
]
```

Footer records:

```json
[
  { "label": "Privacy Policy", "href": "/privacy-policy", "location": "footer", "sort_order": 10, "enabled": true },
  { "label": "Terms of Service", "href": "/terms-of-service", "location": "footer", "sort_order": 20, "enabled": true },
  { "label": "Contact", "href": "/contact", "location": "footer", "sort_order": 30, "enabled": true }
]
```

### `landing_page`

Create one record:

```json
{
  "title": "Agrisync",
  "seo_title": "Agrisync - Your Smart Farm Assistant",
  "seo_description": "Agrisync helps Filipino farmers plan crops, monitor growth, prepare for climate risks, and sell with confidence using AI-powered farming assistance.",
  "keywords": "Smart farming Philippines, AI farming assistant, Crop monitoring app, Farm management app, Agriculture technology Philippines, Crop recommendation app, Farming assistant AI",
  "hero_badge": "Your Smart Farm Assistant",
  "hero_title": "Grow Smarter.\nEarn Better.\nLive Better.",
  "hero_description": "Agrisync helps Filipino farmers plan crops, monitor growth, prepare for climate risks, and sell with confidence using AI-powered farming assistance.",
  "hero_cta_label": "Download App",
  "hero_cta_href": "#download",
  "hero_image": "/illustrations/agrisync-farm-hero.png",
  "published": true
}
```

### `landing_sections`

Create these records:

```json
[
  {
    "key": "problem_solution_intro",
    "eyebrow": "Problem to Solution",
    "title": "Less Guesswork. More Guidance.",
    "body": "Agrisync turns scattered advice into a calm farm companion that helps farmers choose, care, prepare, and sell.",
    "sort_order": 10,
    "enabled": true
  },
  {
    "key": "farming_phases",
    "eyebrow": "The Four Farming Phases",
    "title": "From first plan to fair price.",
    "body": "",
    "sort_order": 20,
    "enabled": true
  },
  {
    "key": "anie",
    "eyebrow": "Meet Anie",
    "title": "Your virtual farm assistant.",
    "body": "Anie helps guide your farming journey from crop planning to harvesting and selling with simple, friendly answers.",
    "sort_order": 30,
    "enabled": true
  },
  {
    "key": "features",
    "eyebrow": "Feature Showcase",
    "title": "Built for the full farming cycle.",
    "body": "",
    "sort_order": 40,
    "enabled": true
  },
  {
    "key": "pricing",
    "eyebrow": "Pricing",
    "title": "Simple plans for real farms.",
    "body": "",
    "sort_order": 50,
    "enabled": true
  },
  {
    "key": "testimonials",
    "eyebrow": "Farmer Stories",
    "title": "Trust that feels close to home.",
    "body": "",
    "sort_order": 60,
    "enabled": true
  },
  {
    "key": "faq",
    "eyebrow": "FAQ",
    "title": "Helpful answers, no heavy jargon.",
    "body": "",
    "sort_order": 70,
    "enabled": true
  },
  {
    "key": "download",
    "eyebrow": "Download Agrisync",
    "title": "Start Growing Smarter Today",
    "body": "Download Agrisync and get your AI-powered farming assistant for planning, monitoring, alerts, reports, and fairer selling decisions.",
    "sort_order": 80,
    "enabled": true
  }
]
```

### `landing_items`

Trust badge records:

```json
[
  { "section_key": "trust_badges", "title": "Localized for Philippine farming", "sort_order": 10, "enabled": true },
  { "section_key": "trust_badges", "title": "Department of Agriculture market insights", "sort_order": 20, "enabled": true },
  { "section_key": "trust_badges", "title": "AI-powered guidance", "sort_order": 30, "enabled": true },
  { "section_key": "trust_badges", "title": "Smart climate alerts", "sort_order": 40, "enabled": true }
]
```

Problem records:

```json
[
  { "section_key": "problems", "title": "Unsure what crops to plant this season", "sort_order": 10, "enabled": true },
  { "section_key": "problems", "title": "Weather changes that disrupt farm work", "sort_order": 20, "enabled": true },
  { "section_key": "problems", "title": "Selling too low because prices are unclear", "sort_order": 30, "enabled": true },
  { "section_key": "problems", "title": "Scattered advice that is not local enough", "sort_order": 40, "enabled": true },
  { "section_key": "problems", "title": "Hard-to-track crop progress and harvest timing", "sort_order": 50, "enabled": true },
  { "section_key": "problems", "title": "No single assistant for daily farm decisions", "sort_order": 60, "enabled": true }
]
```

Solution records:

```json
[
  { "section_key": "solutions", "title": "Localized crop recommendations", "sort_order": 10, "enabled": true },
  { "section_key": "solutions", "title": "Growth monitoring and harvest estimates", "sort_order": 20, "enabled": true },
  { "section_key": "solutions", "title": "Personalized tasks and climate reminders", "sort_order": 30, "enabled": true },
  { "section_key": "solutions", "title": "Market price views based on Department of Agriculture data", "sort_order": 40, "enabled": true },
  { "section_key": "solutions", "title": "Community support and expert-backed answers", "sort_order": 50, "enabled": true },
  { "section_key": "solutions", "title": "Downloadable farming activity reports", "sort_order": 60, "enabled": true }
]
```

Farming phase records:

```json
[
  {
    "section_key": "phases",
    "title": "Crop Planning",
    "question": "What should I plant this season?",
    "description": "Agrisync helps farmers decide what to plant based on season, location, farm conditions, and demand.",
    "metadata": { "phase": "Phase 1", "feature": "Crop Recommendation" },
    "sort_order": 10,
    "enabled": true
  },
  {
    "section_key": "phases",
    "title": "Planting Crop",
    "question": "When should I harvest my crop?",
    "description": "Planting dates, crop stages, and GDD estimates become simple timelines farmers can follow.",
    "metadata": { "phase": "Phase 2", "feature": "Crop Monitoring" },
    "sort_order": 20,
    "enabled": true
  },
  {
    "section_key": "phases",
    "title": "Monitoring & Maintenance",
    "question": "How do I manage my farm and prepare for disasters?",
    "description": "Daily reminders, operational tasks, and climate alerts keep farmers ready before problems grow.",
    "metadata": { "phase": "Phase 3", "feature": "Personalized Action Items" },
    "sort_order": 30,
    "enabled": true
  },
  {
    "section_key": "phases",
    "title": "Harvesting & Selling",
    "question": "How do I price my crops fairly?",
    "description": "Department of Agriculture-based price views help farmers compare prices and sell with more confidence.",
    "metadata": { "phase": "Phase 4", "feature": "Market Views" },
    "sort_order": 40,
    "enabled": true
  }
]
```

Anie highlight records:

```json
[
  { "section_key": "anie_highlights", "title": "Answers farming questions conversationally", "sort_order": 10, "enabled": true },
  { "section_key": "anie_highlights", "title": "Recommends crops based on local conditions", "sort_order": 20, "enabled": true },
  { "section_key": "anie_highlights", "title": "Turns data into clear action items", "sort_order": 30, "enabled": true },
  { "section_key": "anie_highlights", "title": "Helps prepare for storms and climate risks", "sort_order": 40, "enabled": true },
  { "section_key": "anie_highlights", "title": "Supports better market decisions", "sort_order": 50, "enabled": true }
]
```

Feature records:

```json
[
  {
    "section_key": "features",
    "title": "Crop Recommendation",
    "question": "What should I plant this season?",
    "description": "Anie asks about location, season, soil type, light, farm size, and purpose, then suggests crops with simple growing guides.",
    "image": "/illustrations/crop-recommendation.webp",
    "benefits": ["Localized choices", "Less guesswork", "Beginner-friendly guides"],
    "metadata": { "label": "Crop Recommendation Screenshot" },
    "sort_order": 10,
    "enabled": true
  },
  {
    "section_key": "features",
    "title": "Crop Monitoring",
    "question": "When should I harvest my crop?",
    "description": "Farmers log crops and planting dates, then Agrisync builds growth progress, estimated harvest windows, and activity timelines.",
    "image": "/illustrations/crop-monitoring.webp",
    "benefits": ["Harvest planning", "Crop progress visibility", "Smarter schedules"],
    "metadata": { "label": "Crop Monitoring Screenshot" },
    "sort_order": 20,
    "enabled": true
  },
  {
    "section_key": "features",
    "title": "Personalized Action Items",
    "question": "How do I manage my farm and prepare for disasters?",
    "description": "Operational reminders, weather warnings, climate alerts, market prompts, and crop care tasks arrive when they matter.",
    "image": "/illustrations/action-items.webp",
    "benefits": ["Daily farm tasks", "Storm preparation", "Market reminders"],
    "metadata": { "label": "Action Items Screenshot" },
    "sort_order": 30,
    "enabled": true
  },
  {
    "section_key": "features",
    "title": "Market Views",
    "question": "How do I price my crops fairly?",
    "description": "Compare crop prices against Department of Agriculture-based market data, including average, highest, lowest, and historical trends.",
    "image": "/illustrations/market-views.webp",
    "benefits": ["Avoid underpricing", "Better profit choices", "Market awareness"],
    "metadata": { "label": "Market Views Screenshot" },
    "sort_order": 40,
    "enabled": true
  },
  {
    "section_key": "features",
    "title": "Generated Activity Reports",
    "question": "How do I review my farm activity?",
    "description": "Create PDF or email-ready reports with managed crops, completed tasks, Anie conversations, timelines, and date ranges.",
    "image": "/illustrations/generated-reports.webp",
    "benefits": ["Farm documentation", "Productivity tracking", "History review"],
    "metadata": { "label": "Reports Screenshot" },
    "sort_order": 50,
    "enabled": true
  },
  {
    "section_key": "features",
    "title": "Community Support",
    "question": "Where can I ask and learn with others?",
    "description": "Farmers can access community discussions, peer support, shared advice, and expert-backed insights that Anie can reference.",
    "image": "/illustrations/community-support.webp",
    "benefits": ["Shared advice", "Less isolation", "Relevant discussions"],
    "metadata": { "label": "Community Screenshot" },
    "sort_order": 60,
    "enabled": true
  }
]
```

### `pricing_plans`

Create these records:

```json
[
  {
    "name": "Free",
    "price_monthly": "₱0",
    "price_yearly": "₱0",
    "target": "New farmers and casual users.",
    "cta_label": "Start Free",
    "cta_href": "#download",
    "recommended": false,
    "features": ["Basic Anie access", "Limited crop recommendations", "Basic crop monitoring", "Community access", "Limited reports", "Basic market viewing"],
    "sort_order": 10,
    "enabled": true
  },
  {
    "name": "Farmer Plus",
    "price_monthly": "₱149/month",
    "price_yearly": "₱1,490/year",
    "target": "Active small-to-medium farmers.",
    "cta_label": "Upgrade to Plus",
    "cta_href": "#download",
    "recommended": true,
    "features": ["Unlimited crop recommendations", "Advanced crop monitoring", "Personalized action items", "Disaster & climate alerts", "Market intelligence", "Unlimited reports", "Priority AI assistance", "Full history access"],
    "sort_order": 20,
    "enabled": true
  },
  {
    "name": "Cooperative",
    "price_monthly": "Custom",
    "price_yearly": "Custom",
    "target": "Farming groups and cooperatives.",
    "cta_label": "Contact Us",
    "cta_href": "/contact",
    "recommended": false,
    "features": ["Multi-user access", "Shared monitoring dashboard", "Cooperative analytics", "Community management", "Admin tools", "Team activity reports", "Priority support"],
    "sort_order": 30,
    "enabled": true
  }
]
```

### `testimonials`

Create these records:

```json
[
  {
    "quote": "Agrisync helped me know the right time to harvest my crops.",
    "name": "Mang Leo",
    "detail": "Vegetable farmer, Nueva Ecija",
    "image": "/illustrations/mang-leo.webp",
    "sort_order": 10,
    "enabled": true
  },
  {
    "quote": "The market prices helped me avoid selling too low.",
    "name": "Aling Tess",
    "detail": "Rice and corn grower, Isabela",
    "image": "/illustrations/aling-tess.webp",
    "sort_order": 20,
    "enabled": true
  },
  {
    "quote": "Anie feels like a farming partner.",
    "name": "Jun Carlo",
    "detail": "Young farmer, Laguna",
    "image": "/illustrations/jun-carlo.webp",
    "sort_order": 30,
    "enabled": true
  }
]
```

### `faqs`

Create these records:

```json
[
  {
    "question": "What is Agrisync?",
    "answer": "Agrisync is an AI-powered smart farming assistant that helps farmers plan, monitor, protect, and sell crops more effectively.",
    "sort_order": 10,
    "enabled": true
  },
  {
    "question": "Is Agrisync only for experienced farmers?",
    "answer": "No. Agrisync is designed for both beginner and experienced farmers.",
    "sort_order": 20,
    "enabled": true
  },
  {
    "question": "Does Agrisync work for Philippine farming?",
    "answer": "Yes. The recommendations and market data are localized for Philippine agriculture.",
    "sort_order": 30,
    "enabled": true
  },
  {
    "question": "Does Agrisync require internet?",
    "answer": "Some features may require internet connectivity, especially AI and live alerts.",
    "sort_order": 40,
    "enabled": true
  },
  {
    "question": "Can I use Agrisync for multiple crops?",
    "answer": "Yes. Users can manage and monitor multiple crops.",
    "sort_order": 50,
    "enabled": true
  },
  {
    "question": "Are market prices real?",
    "answer": "Agrisync references Department of Agriculture-based market pricing data.",
    "sort_order": 60,
    "enabled": true
  }
]
```

### `legal_pages`

Privacy policy record:

```json
{
  "slug": "privacy-policy",
  "title": "Privacy Policy",
  "eyebrow": "Agrisync Privacy",
  "description": "This Privacy Policy explains how Agrisync handles information for our smart farming assistant, landing page, and future app experience.",
  "updated": "2026-05-16",
  "published": true,
  "sections": [
    {
      "title": "Information We Collect",
      "body": [
        "Agrisync may collect account details, farm profile information, crop records, location-based farming inputs, app activity, and messages sent to Anie so we can provide crop guidance, monitoring, alerts, reports, and support.",
        "When market, weather, or crop recommendation features are used, related inputs may be processed to produce localized suggestions and practical farming action items."
      ]
    },
    {
      "title": "How We Use Information",
      "body": [
        "We use information to operate Agrisync, personalize farming recommendations, estimate crop timelines, create reports, improve product quality, and communicate service updates.",
        "We do not sell personal information. If data is shared with service providers, it is used only to help deliver Agrisync features and support."
      ]
    },
    {
      "title": "Data Choices",
      "body": [
        "Users may request access, correction, or deletion of their information by contacting us. Some records may be retained when needed for security, legal, accounting, or service integrity purposes.",
        "Farmers should avoid sharing sensitive personal information inside community discussions unless it is necessary for the question being asked."
      ]
    },
    {
      "title": "Security",
      "body": [
        "We use reasonable technical and organizational measures to protect user information. No online service can guarantee perfect security, but we work to keep farmer data handled with care."
      ]
    }
  ]
}
```

Terms of service record:

```json
{
  "slug": "terms-of-service",
  "title": "Terms of Service",
  "eyebrow": "Agrisync Terms",
  "description": "These Terms of Service describe the basic rules for using Agrisync and its smart farming assistance features.",
  "updated": "2026-05-16",
  "published": true,
  "sections": [
    {
      "title": "Using Agrisync",
      "body": [
        "Agrisync provides AI-powered farming guidance, crop monitoring tools, market views, reports, and community features to support farmer decision-making.",
        "The service is meant to assist, not replace, professional agricultural judgment, local government advisories, weather bulletins, or expert advice."
      ]
    },
    {
      "title": "Farmer Responsibility",
      "body": [
        "Users are responsible for checking recommendations against real farm conditions, local rules, supplier instructions, and safety practices before acting.",
        "Market prices, weather alerts, and growth estimates may change. Agrisync cannot guarantee crop yield, profit, harvest dates, or disaster outcomes."
      ]
    },
    {
      "title": "Acceptable Use",
      "body": [
        "Users should not misuse the platform, attempt unauthorized access, disrupt service operation, upload harmful content, or share misleading information in community spaces.",
        "Community contributions should be respectful, practical, and relevant to farming support."
      ]
    },
    {
      "title": "Changes to the Service",
      "body": [
        "Agrisync may update features, pricing, content, and these terms as the product grows. Continued use after updates means the revised terms apply."
      ]
    }
  ]
}
```

### `contact_submissions`

Do not seed normal contact submissions. This collection should start empty.

Create test records only in development:

```json
{
  "name": "Test Farmer",
  "email": "test@example.com",
  "subject": "Demo inquiry",
  "message": "This is a development-only contact form test.",
  "inquiry_type": "support",
  "source": "contact-page",
  "status": "new",
  "user_agent": "development"
}
```

## Server-Side PocketBase Setup

Create a server-only PocketBase helper:

```txt
src/lib/server/pocketbase.ts
```

Responsibilities:

- Initialize PocketBase with `POCKETBASE_URL`.
- Keep admin or service credentials server-only.
- Expose typed collection helpers.
- Normalize PocketBase file URLs before sending data to components.

Use environment variables:

```txt
POCKETBASE_URL=http://127.0.0.1:8090
POCKETBASE_ADMIN_EMAIL=
POCKETBASE_ADMIN_PASSWORD=
PUBLIC_SITE_URL=https://agrisync.online
```

For SSR, prefer loading CMS records in `+page.server.ts` and passing plain serializable data to Svelte components.

## Data Loading Pattern

Home page:

```txt
+page.server.ts
  load()
    getSiteSettings()
    getLandingPage()
    getNavigationLinks()
    getLandingSections()
    getLandingItems()
    getPricingPlans()
    getTestimonials()
    getFaqs()
```

Contact page:

```txt
contact/+page.server.ts
  load()
    getSiteSettings()

  actions.default()
    validate form data
    create contact_submissions record
    return success or fail()
```

Legal pages:

```txt
privacy-policy/+page.server.ts
  load()
    getLegalPage("privacy-policy")

terms-of-service/+page.server.ts
  load()
    getLegalPage("terms-of-service")
```

## Validation

Use a schema validator such as Zod or Valibot for contact form submissions.

Contact form rules:

- Name is required.
- Email must be valid.
- Subject is required.
- Message should have a minimum length.
- Reject empty honeypot field only if spam protection is implemented.
- Return field-level errors with SvelteKit `fail()`.

## Styling Plan

The SvelteKit version should preserve Agrisync's current warm, friendly farming identity.

### Visual Direction

The interface should feel:

- Friendly
- Localized for Filipino farmers
- Trustworthy
- Helpful
- Calm
- Organic
- Optimistic
- App-like but not corporate

Avoid:

- Corporate fintech styling
- Dark cyber visuals
- Harsh gradients
- Excessive glassmorphism
- Generic SaaS landing page sections
- One-note green-only palettes

### Color Tokens

Keep the current CSS variables:

```css
:root {
  --forest: #0e5a3a;
  --leaf: #6faf45;
  --light-green: #ddefc9;
  --sky: #ddf4ff;
  --cream: #fff8e9;
  --yellow: #f3c43b;
}
```

Usage:

- `--forest`: headings, primary buttons, navigation, strong text
- `--leaf`: success accents, highlights, checklist icons
- `--light-green`: section surfaces, chips, soft panels
- `--sky`: atmospheric backgrounds and feature media
- `--cream`: main page background and warm panels
- `--yellow`: emphasis badges, CTA accents, illustration highlights

### Typography

Keep the current font direction:

- Display: `Baloo 2`
- Body: `Nunito`
- Fallback: system sans-serif

Implementation:

- Load fonts in `src/routes/+layout.svelte` or `src/app.html`.
- Keep `.font-display` utility in `app.css`.
- Use large, rounded display type for major landing headings only.
- Use tighter, smaller headings for cards, legal sections, and form surfaces.
- Do not scale font size directly with viewport width except for established `clamp()` display headings.
- Keep letter spacing at `0` for normal headings.

### Layout Rules

- Use full-width page bands with constrained inner content.
- Keep repeated content in cards, but avoid nesting cards inside cards.
- Preserve the existing maximum content width of about `max-w-6xl`.
- Keep mobile-first spacing and ensure all text wraps cleanly.
- Use stable dimensions for phone mockups, app badges, nav controls, and feature media.
- Keep the first viewport focused on the Agrisync brand and product promise.

### Components And Surfaces

Header:

- Fixed pill navigation.
- Cream/white translucent background.
- Rounded full shape.
- Brand mark plus Agrisync text.
- Mobile menu opens as a compact rounded panel.

Hero:

- Full first viewport.
- Farm illustration as real image/background.
- Strong brand/product signal.
- Primary CTA points to download section.
- Trust badges remain visible below hero copy.

Cards:

- Soft green-tinted outer surface.
- White or cream inner surface.
- Rounded corners may remain large for the brand style, but keep internal controls consistent.
- Use subtle shadows, not heavy glass effects.

Buttons:

- Primary buttons use forest green background and white text.
- Secondary buttons use light green or white surfaces.
- Touch targets should be at least `44px` high.
- Use clear hover and focus states.

Forms:

- Labels should be visible.
- Errors should appear near their fields.
- Inputs should use calm white/cream surfaces.
- Submit button should clearly show loading/success/error states.

Legal pages:

- Preserve the current calm document-card layout.
- Use readable line length.
- Avoid oversized hero typography inside dense legal content.

### Animation Rules

Keep current reveal behavior but isolate it from content logic.

Recommended:

- Use `motion` in client-only `onMount`.
- Respect `prefers-reduced-motion`.
- Animate opacity and small vertical movement only.
- Avoid animation that changes layout dimensions.
- Keep pricing toggle and mobile menu transitions quick and calm.

### Asset Rules

Keep all existing static assets initially:

```txt
public/favicon.svg
public/favicon.ico
public/og-image.png
public/logos/
public/illustrations/
```

Later, CMS-managed images can move to PocketBase file fields. When using PocketBase files:

- Normalize file URLs server-side.
- Always provide meaningful alt text for content images.
- Keep decorative images with empty alt text.
- Preserve image dimensions or aspect ratios to prevent layout shift.

## SEO Plan

Build a shared SEO component and use it on every route.

Each page should include:

- Title
- Description
- Canonical URL
- Open Graph title
- Open Graph description
- Open Graph image
- Twitter card metadata

Home page should keep the current keyword intent:

- Smart farming Philippines
- AI farming assistant
- Crop monitoring app
- Farm management app
- Agriculture technology Philippines
- Crop recommendation app
- Farming assistant AI

Also add:

- `src/routes/sitemap.xml/+server.ts`
- `src/routes/robots.txt/+server.ts`

## Testing And QA Checklist

Before removing Astro:

- Home page visually matches current page.
- Header mobile menu works.
- Pricing monthly/yearly toggle works.
- FAQ items open and close.
- Download badges render correctly.
- Contact form submits valid data to PocketBase.
- Contact form returns useful validation errors.
- Privacy policy loads from PocketBase.
- Terms of service loads from PocketBase.
- SEO metadata renders in SSR output.
- Sitemap and robots routes respond correctly.
- Build passes.
- Type check passes.
- Mobile viewport has no text overflow.
- No Astro packages remain in `package.json`.

## Dependencies To Add

Likely additions:

```txt
@sveltejs/kit
@sveltejs/adapter-node or another deployment adapter
@sveltejs/vite-plugin-svelte
pocketbase
zod or valibot
```

Keep:

```txt
svelte
tailwindcss
@tailwindcss/vite
motion
typescript
```

Remove after migration:

```txt
astro
@astrojs/svelte
@astrojs/sitemap
@astrojs/check
astro.config.mjs
src/pages/*.astro
src/components/*.astro
```

## Deployment Notes

For full SSR, deploy with a SvelteKit adapter that supports server rendering.

Good options:

- Node server
- Vercel
- Netlify
- Cloudflare adapter, if PocketBase access and runtime constraints are acceptable

PocketBase can be hosted separately. The SvelteKit app should communicate with it through `POCKETBASE_URL`.

## First Release Slice

The first useful release slice is the end of Phase 4:

1. SvelteKit app boots.
2. Existing home page renders with static `landing.ts` data.
3. Contact, privacy, and terms routes exist.
4. Styling matches the current Astro site.
5. No PocketBase integration yet.

After Phase 4, wire PocketBase one phase at a time. This keeps the migration easy to verify and avoids mixing framework migration bugs with CMS integration bugs.

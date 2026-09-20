# admin-modem-modemdata-modempreview

## modemdata-badge-width — let signal badges inside info cards size to content
- page: admin-modem-modemdata-modempreview · Modem → Modem Data → (preview) (menu path inferred from the page name; not in the local LuCI checkouts)
- package: luci-app-modemdata (third-party; named in the patch header and PR #80)
- dom: `.ifacebox-body .ifacebadge` on the "General Information" cards (PR #80 text)
- symptom: PR #80 body: "Aurora's default ifacebox styles spread modemdata rows apart, wrap signal badges vertically, and leave cards at uneven heights."
- cause: unknown beyond the PR wording — the merged diff (910c85d, 12 lines) contains only the badge width override; the row-spacing and card-height parts of the PR description have no rule in the commit
- fix: patches/admin-modem-modemdata-modempreview.css line 8-12 `[data-page="admin-modem-modemdata-modempreview"] { & .ifacebox-body .ifacebadge { width: auto !important; } }` · 910c85d 2026-06-28 (author CJay D Acopra, PR #80) · 1.0.2 (first tag v1.1.0) — added to `_patches.css` as `@apply w-auto!`, split into its own file by 1c924cf, native CSS by e14cfcd
- verified: not measured (no record)
- fixture: none (backfill)
- issue: PR #80 · mirror: pending · status: active

## info-cards-compact — density pass on the four modemdata info cards
- page: admin-modem-modemdata-modempreview · Modem → Modem(s) (the app renders this data-page for both `/admin/modem/modemdata` and `/admin/modem/modemdata/modempreview`)
- package: luci-app-modemdata (third-party)
- dom: `.ifacebox` (inline `margin:.25em;width:100%`), `.ifacebox-head.port-label` (inline `font-weight:bold;padding:8px`), `.ifacebox-body` (inline `padding:8px`), rows as `div[style*="margin-bottom"]` (inline `font-size:12px`, margins 8/4px), `.ifacebadge` (signal badge inline `width:92px`) and its `img` (inline `height:32px`)
- symptom: at 1440px the four cards (Connection, SIM Card, Modem Information, Cell Information) render 331px tall (head 37 + body 292): 32px badge icons, 8px inline paddings, 8/4px row margins and the 20px line-height inherited from `.ifacebox` on 12px rows all add up; the stock `margin:.25em` doubles the grid's 14px gap
- cause: the app stamps every density-relevant value inline, so only `!important` can move them; rows carry no line-height of their own, so the base's `leading-5` (20px) wastes ~5px per row (and the divider's last row has no inline margin at all, escaping attribute-targeted selectors)
- fix: patches/admin-modem-modemdata-modempreview.css `[data-page="admin-modem-modemdata-modempreview"]` — `.ifacebox { margin:0!important }`, `.ifacebox-head.port-label { padding:5px 12px!important; font-size:13px; font-weight:600!important; line-height:1.35 }`, `.ifacebox-body { justify-content:flex-start; padding:6px 12px 10px!important; gap:2px }` (flex-start overrides the base's `justify-content:center` in `_card.css` so the rows hug the head instead of centring in the stretched card), `.ifacebox-body div { line-height:1.4 }`, `div[style*="margin-bottom"] { margin-bottom:2px!important }`, `div[style*="border-top"] { padding-top:5px!important }`, `.ifacebadge { gap:4px; padding:2px 8px; font-size:12px; width:auto!important }`, `.ifacebadge img { height:18px!important }` · 14fe263 · (unreleased)
- verified: device (shiranekowrt, dark mode, v1.3.3) by injecting the compiled patch CSS into the live page: card 331→231px (head 37→29, badge row 50→26, icons 32→18, badge 112→75px wide), all four cards equal at 231px, no row wrap/overflow, no page overflow; content top-aligned (head→first-row gap = body padding-top = 6px on all four cards); mobile 390 emulation: single 366px column, no overflow (heights 231/140/137/149, content-sized); compat fixture fails pre-fix (331 > 245) and passes post-fix at 1280 and 390
- fixture: fixtures/admin-modem-modemdata-modempreview--info-cards.html
- issue: — · mirror: pending · status: active

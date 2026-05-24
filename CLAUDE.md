# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A Jordanian salary calculator web app (Arabic/RTL) that computes income tax and social security deductions for 2025. Deployed on Vercel at `jordan-tax-calculator.vercel.app`.

## Running Locally

No build step. Open `index.html` directly in a browser, or serve it with any static server:

```bash
npx serve .
# or
python3 -m http.server
```

## Architecture

Everything lives in a single `index.html` file — HTML, CSS (`<style>`), and JavaScript (`<script>`) are all inline. There are no external JS dependencies, no bundler, and no framework.

**Static assets:** favicon variants, `og.png`, `site.webmanifest`, and a Google Search Console verification file (`google2837716f14d007fa.html`).

## Tax Calculation Logic

All logic is in the `<script>` block at the bottom of `index.html`.

**`calculateTax(monthlySalary, maritalStatus)`** — applies progressive annual brackets to (annual salary − personal exemption), then divides by 12:
- Exemptions: 9,000 JOD (single) / 18,000 JOD (married)
- Brackets: 5% / 10% / 15% / 20% / 25% (each 5,000 JOD wide except the last)

**`calculateDeductions()`** — gross → net: SSC = 7.5% of gross, tax via `calculateTax`, net = gross − SSC − tax.

**`reverseCalculate()`** — net → gross: iterative Newton-like loop (up to 200 iterations, convergence threshold 0.001 JOD) that adjusts a gross estimate until the implied net matches the target.

**`buildSteps(gross, maritalStatus)`** — generates the step-by-step breakdown HTML shown in results.

## Key Constraints

- RTL layout (`<html lang="ar" dir="rtl">`); all user-facing text is Arabic.
- Currency values are displayed with `direction: ltr` to keep numerals left-to-right inside RTL context.
- No external JS — keep it that way; the app intentionally has zero JS dependencies.
- The reverse calculator FAQ describes the algorithm as "100 iterations" but the code uses 200 — keep them in sync if changing.

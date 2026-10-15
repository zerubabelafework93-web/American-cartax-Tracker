# American Car Tax Tracker

A small web app that helps people find out whether their vehicle meets the **vehicle requirements** for the federal car loan interest deduction created by the One Big Beautiful Bill Act (Internal Revenue Code § 163(h)(4)).

Enter a 17-character VIN, and the app decodes it with the official NHTSA database, checks the vehicle against the rules, and asks a few follow-up questions for the things a VIN can't tell you.

**Live demo:** `https://zerubabelafework93-web.github.io/American-cartax-Tracker/`

> This is a preliminary information tool, not tax advice. See [Limitations](#limitations).

## Why I built it

The new deduction (up to $10,000 a year in loan interest, tax years 2025–2028) only applies if the vehicle was assembled in the United States, among other rules. Most people don't know where their car was actually built, and a brand's nationality is a poor guess (plenty of "foreign" brands build cars in the U.S., and the reverse). I wanted a simple tool that answers the vehicle part with real data and is honest about what it can't determine.

## What it does

- Validates the VIN format (17 characters, no I, O or Q) before making any network call
- Looks up the vehicle through the [NHTSA vPIC VIN Decoder API](https://vpic.nhtsa.dot.gov/api/)
- Shows make, model, year, assembly country and plant, vehicle type, body class and weight rating
- Checks the vehicle requirements and shows each one as **Met**, **Not met**, or **Unable to determine**
- Asks only the follow-up questions a VIN can't answer (new purchase, personal use, loan terms, income)
- Gives an overall result: *Appears eligible*, *Appears ineligible*, or *More information required*
- Works on phones, tablets and desktops

## How the eligibility logic works

| Requirement | Where the answer comes from |
|---|---|
| Final assembly in the United States | NHTSA `PlantCountry` field |
| Eligible vehicle type (car, minivan, van, SUV, pickup, motorcycle) | NHTSA `VehicleType` and `BodyClass` |
| Gross vehicle weight rating under 14,000 lb | NHTSA `GVWR` field |
| New vehicle, original use starts with the buyer | User answer |
| Primarily personal use | User answer |
| Loan taken out after Dec 31, 2024, secured by a first lien, not a lease | User answer |
| Income under the phase-out limits ($100,000 / $200,000 joint; reduced by $200 per $1,000 over, gone at $150,000 / $250,000 joint) | User answer |

A few design decisions worth calling out:

- **Missing data is never guessed.** If NHTSA doesn't report a field, the check shows "Unable to determine" rather than pass or fail.
- **Assembly location comes from the plant country, not the brand.** The manufacturer's nationality says nothing about where a specific vehicle was built.
- **Vehicle eligibility and full deduction eligibility are shown separately.** A U.S.-built car can still fail on the loan or income rules, so the result never says "qualifies" based on the VIN alone.
- **Weight ranges that straddle 14,000 lb are flagged as unverified** instead of rounded one way or the other.

## Tech

- Plain HTML, CSS and JavaScript in a single `index.html`, with no frameworks, no build step and no dependencies
- `fetch` with a timeout (`AbortController`) for the NHTSA call
- Responsive layout using CSS flexbox/grid and media queries for phone and tablet breakpoints
- Accessibility basics: labelled form fields, `aria-live` status messages, visible focus outlines, and results that don't rely on color alone
- Safe rendering: API data is inserted with `textContent`, never `innerHTML`

## Privacy

There is no backend and no database. The VIN is sent directly from the user's browser to NHTSA's public API and is not stored, logged or shared by this app.

## Run it locally

No install needed.

1. Download or clone this repo
2. Open `index.html` in a browser

Try a few VINs: one from a U.S.-built vehicle and one from an imported vehicle, plus an invalid one to see the error handling.

## Deploy with GitHub Pages

1. Create a new repository on GitHub (for example `american-car-tax-Tracker`)
2. Upload `index.html` and `README.md`, or from the project folder run:
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin https://github.com/zerubabelafework93-web/American-cartax-Tracker.git
   git push -u origin main
   ```
3. In the repo, go to **Settings → Pages**
4. Under **Build and deployment**, set **Source** to **Deploy from a branch**, choose **main** and **/ (root)**, then save
5. After a minute or two the site is live at `https://zerubabelafework93-web.github.io/American-cartax-Tracker/`

## Limitations

- **Not tax advice.** Results are preliminary and don't guarantee eligibility. Check with the IRS or a tax professional.
- **Data quality depends on NHTSA.** Some VINs, especially older or low-volume vehicles, come back with missing fields.
- **No VIN check-digit validation.** Only the 17-character format is checked, since not every valid VIN worldwide follows the North American check-digit rule.
- **Truck classification is conservative.** A vehicle decoded as a "truck" is only marked eligible if its body class says pickup or van.
- **It doesn't calculate a dollar amount.** It checks eligibility only, not how much interest you can deduct.
- **Tax rules can change.** The rules reflect IRS guidance available when I built this. Verify against the official sources below.

## Possible next steps

- Add a small serverless proxy for the NHTSA call if browser access is ever restricted
- Add automated tests for the eligibility functions
- Add a dollar-amount estimate that applies the income phase-out

## Official sources

- [IRS: One Big Beautiful Bill Act tax deductions](https://www.irs.gov/newsroom/one-big-beautiful-bill-act-tax-deductions-for-working-americans-and-seniors)
- [IRS Notice 2025-57](https://www.irs.gov/pub/irs-drop/n-25-57.pdf)
- [26 U.S.C. § 163](https://www.law.cornell.edu/uscode/text/26/163)
- [NHTSA VIN Decoder](https://www.nhtsa.gov/vin-decoder)

## Disclaimer

This project is for educational and informational purposes only. It is not affiliated with the IRS, NHTSA or any government agency.

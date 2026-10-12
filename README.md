# VanScene

**Know your new neighbourhood.**

VanScene is a simple web app that shows patterns of reported crime in Vancouver, built on open data from the Vancouver Police Department (VPD). It helps newcomers answer two questions while they look for housing: *"How does reported crime compare across the neighbourhoods I'm considering?"* and *"What kinds of incidents have been reported here, and when?"*

It is one input to a newcomer's research, alongside visiting neighbourhoods, talking to locals, and other sources. **It does not rate the safety of any location.**

**Live app:** [add your GitHub Pages link]
**Team:** [team name] · **Datathon:** Build Trilemma Datathon 2026 · **Status:** Build Session 2 complete, preparing for Build Session 3

---

## What the app does

Built from **374,186 mapped incidents reported between January 2016 and October 2, 2026**.

- **Map:** a heat map of reported incidents across the City of Vancouver.
- **Filters:** choose any year or combination of years from 2016 to 2026, a crime type (for example "Home break-ins" or "Bike theft"), and a time of day (day, evening, night).
- **Area summary:** click anywhere on the map to see what has been reported within 500 metres of that spot.
- **Jump to a neighbourhood:** pick a neighbourhood from a list instead of searching the map.
- **Compare:** view two areas side by side.
- **Trends:** see how reported incidents have changed from year to year since 2016.
- **About this data:** the source, date range, and known limitations, shown inside the app.

## Demo path

**Situation:** I've just moved to Vancouver and I'm choosing between two apartments, one in Mount Pleasant and one in Kitsilano. Everyone I ask has a different opinion about which area to avoid.

**Action:** On the map, select "Home break-ins" and see where they cluster. Open **Compare** to put both areas side by side, then check **Trends** to see whether things have been rising or falling since 2016.

**Result:** In under a minute I can see what has been reported near each apartment, when it tends to happen, and whether one year was unusual, instead of relying on Reddit threads or one-area-at-a-time AI answers.

**Before:** days of searching Reddit and asking an AI assistant about one area at a time, with no way to see sources or compare areas side by side.
**After:** one place to compare areas on the same data, with the source and limitations visible.

## How to run it locally

`web/data.json` is already in the repo, so to just run the app:

```
cd web
python -m http.server 8000
```

Then open `http://localhost:8000`. The page needs a local server; opening `index.html` by double-clicking won't load the data.

To rebuild `data.json` from the raw VPD data:

1. Go to [geodash.vpd.ca/opendata](https://geodash.vpd.ca/opendata/), accept the disclaimer, and download each year from 2016 to 2026 (all neighbourhoods, CSV).
2. Unzip each file into `data/raw/`.
3. From the project folder, run:
   ```
   python scripts/prepare_data.py
   ```
   This needs only standard Python, with nothing to install. It prints a summary of rows kept and excluded, and writes `web/data.json`.

## Choices we made, and why

| Choice | Why |
|---|---|
| A static web page (plain HTML, CSS, JavaScript) with no framework or build step | Simple to run, review, and host; matches the Session 2 goal of a working static frontend |
| Python standard library only for data preparation | Teammates and mentors can run it without installing anything |
| Leaflet with Leaflet.heat on OpenStreetMap tiles | Free, open source, and no API keys or accounts needed |
| Converting VPD's UTM Zone 10 coordinates to latitude/longitude in our script | Lets the data line up with any standard map; accurate to well under a metre, far finer than VPD's privacy offsets |
| Map zoom capped at roughly street level | VPD offsets locations, so building-level zoom would imply precision the data doesn't have |
| A 500-metre radius for area summaries | Large enough to absorb VPD's location offsets, small enough to describe a neighbourhood around a specific address |
| Plain-language crime type labels (for example "Theft from cars" instead of "Theft from Vehicle") | Newcomers shouldn't need to learn police categories to use the app |
| Records without a location are left off the map and counted in the About section | VPD withholds locations for offences against a person; we say so rather than hide it |

## What works now, and what's next

**Works now**
- [x] Heat map of 374,186 mapped incidents, 2016 to October 2, 2026
- [x] Filters for year, crime type, and time of day
- [x] Click-anywhere 500 m area summary and neighbourhood jump list
- [x] Compare and Trends views
- [x] In-app "About this data" with source and limitations

**Next (Build Session 3)**
- [ ] Deploy from this repository with GitHub Pages and add the link above
- [ ] Add a feedback link and test with newcomers outside the team
- [ ] Check that testers don't read the map as a safety rating
- [ ] Add violent-crime counts to neighbourhood summaries (these rows have a neighbourhood but no map location)
- [ ] Explore adjusting counts for population or foot traffic, so busy areas aren't unfairly highlighted
- [ ] Note in the app that SkyTrain incidents are excluded (Transit Police jurisdiction)

---

## Build Session 1: Problem evidence

### The problem is real

Moving to a new city means making a high-stakes housing decision about neighbourhoods you have never lived in. Crime is one of the first things newcomers want to know about, but the information available is either anecdotal (Reddit threads, friends' opinions, "don't live near X") or buried in raw police data that is hard to interpret.

**When it happens:** while browsing rental listings, comparing two neighbourhoods, or deciding whether to book a viewing.
**Why it happens:** there is no simple, transparent way to compare neighbourhoods on reported crime at a glance.

**Our N of 1:**

> When I moved to Vancouver in 2026, I spent days trying to figure out which area of Vancouver was safer to live in. I ended up relying on AI to tell me which areas were safer, asking about one area at a time, and it would give me an analysis based on indicators such as violent crime rate and car break-ins. I also checked a bunch of Reddit comments, which were helpful but not that reliable.

- [x] I experience this problem myself
- [x] I am the N of 1 user
- [x] I can clearly describe when and why the problem occurs
- [x] The problem is not hypothetical or invented for the Datathon

### Target users

| User | Need | Time window |
|---|---|---|
| Newcomers to Vancouver looking for housing (primary) | Compare neighbourhoods on reported crime as context while researching where to live | Long-term trends (months to years) |
| Residents interested in recent activity (secondary) | See what kinds of incidents have been reported in their neighbourhood recently | Recent months |

### The problem deserves a solution

A search returns opinions and news stories, not a comparison. Our N of 1 experience shows the limits of AI prompts: we had to ask about one area at a time, couldn't see neighbourhoods side by side, and couldn't tell where the numbers came from or how current they were. A prompt also can't reliably summarize hundreds of thousands of geolocated incidents or keep up with data that is refreshed weekly.

| Alternative | What it does well | What's missing for our users |
|---|---|---|
| VPD GeoDASH (official crime map) | Authoritative data, incident-level map, last 7 days by default | A dot map of individual incidents, not a comparison. Hard to answer "how does area A compare to area B?" Violent crime has no map location. |
| AI chat assistants | Fast, conversational answers | One area at a time, unclear sources, unknown freshness |
| Reddit / word of mouth | Local context and lived experience | Anecdotal, biased, outdated, inconsistent |
| Rental sites (Craigslist, Facebook Marketplace, etc.) | Listings | No crime information |
| News coverage | Context on major incidents | Skews toward dramatic events, not overall patterns |

**How VanScene is different**
- A heat map with area summaries, designed for comparison rather than browsing individual incidents
- Compare and Trends views that put areas and years side by side
- Ten years of data, so one unusual year doesn't define a neighbourhood
- The data source, date range, and limitations are shown in the app

- [x] The problem is complex enough that a single prompt or Google search does not already solve it
- [x] Solving it would create meaningful value
- [x] Existing tools do not already solve the problem just as well or better
- [x] I can explain what is missing from the current alternatives

### The scope is right

A microproduct: one web page, one dataset, one core interaction.

**Out of scope:** user accounts, real-time alerts, predictive "safety scores," address-level lookups, coverage beyond the City of Vancouver, combining with rental listing data.

- [x] The problem is narrow enough to make meaningful progress during the Datathon
- [x] The solution does not require building an entire platform or company
- [x] I can define what a useful first version looks like

## Build Session 1: Data evidence

### Source

VPD GeoDASH Open Data: [geodash.vpd.ca/opendata](https://geodash.vpd.ca/opendata/)

Data is extracted from the PRIME BC Police Records Management System and filtered to comply with BC's Freedom of Information and Protection of Privacy Act (FIPPA). It counts "founded" incidents only, meaning police determined after investigation that the offence occurred. Dates reflect when the incident occurred, not when it was reported.

Fields: crime type, year/month/day/hour/minute, hundred block, neighbourhood, X/Y coordinates (UTM Zone 10, offset for privacy).

### Accessibility

- Downloadable as CSV from the GeoDASH open data page, covering incidents from 2003 onward
- Updated weekly (Sunday mornings), with up to a week's delay from VPD's internal system
- No API key or account needed
- We use data from 2016 to October 2, 2026

- [x] I know where the required data comes from
- [x] I can realistically access it during the Datathon
- [x] I have enough data to begin testing the idea

### Permission

No open data licence is published. Access requires accepting the GeoDASH disclaimer, and the site is marked "© City of Vancouver, all rights reserved." The disclaimer cautions users not to rely on the data to judge the safety of a specific location or area, and states that all locations are offset. VPD's disclaimer and data description are saved in `data/vpd/`.

We have not sought separate permission. We limit our use to:
- Non-commercial, educational use, attributed to the VPD
- The raw VPD CSVs are not committed to this repository. The app uses a processed file (`web/data.json`) derived from the public data, with VPD's privacy offsets preserved.
- Patterns, not safety verdicts. No safety scores or address-level lookups.
- We will contact the VPD before any commercial use.

- [x] I understand the licence or terms of use
- [x] I am allowed to use the data for my intended purpose (non-commercial, educational, within the limits above)
- [x] I understand whether the data can be used commercially, educationally, or only under specific restrictions (educational only; commercial use not confirmed)

### Signal

Initial exploration of 2026 data (January 1 to October 2, 2026):

| | Rows |
|---|---|
| Rows read | 25,092 |
| Mapped (have a location) | 22,997 (92%) |
| No location (withheld for privacy) | 2,094 |
| Outside Vancouver | 1 |

| Crime type | Incidents |
|---|---|
| Other theft | 11,353 |
| Theft from cars | 4,275 |
| Mischief and vandalism | 4,137 |
| Traffic collisions with injury | 900 |
| Business break-ins | 841 |
| Bike theft | 609 |
| Home break-ins | 580 |
| Car theft | 291 |
| Fatal traffic collisions | 11 |
| **Total** | **22,997** |

**Key finding:** violent crime can't be mapped, but it can be counted by neighbourhood. All 2,094 rows without a location are violent crime: 2,088 "Offence Against a Person" (robbery, assault, sexual assault, domestic assault) and 6 homicides. They have no coordinates, but every one has a neighbourhood value. So violent crime can't appear on the heat map, but it can be included in neighbourhood summaries.

### Known limitations and how we handle them

| Limitation | Impact | How we handle it |
|---|---|---|
| Violent crime has no map location | The heat map shows property crime and collisions only | Say so in the app; planned: neighbourhood-level violent crime counts |
| Property crime locations are generalized to the hundred block and offset | Block-level precision isn't possible or appropriate | Cap map zoom; summarize by area; state this in the app |
| "Offence Against a Person" is one broad category with no time | Can't break violent crime down by type or time of day | Show it as a single neighbourhood-level count when added |
| Only certain crime categories are included | Not a full picture of safety | Label as "reported incidents," never "safety" |
| Raw counts favour busy areas (Downtown has more residents, workers, and visitors) | The map may mislead newcomers and stigmatize neighbourhoods | Explain in the app; explore normalizing by population |
| SkyTrain crimes are excluded (Transit Police jurisdiction) | Gaps around transit stations | Note in the app |
| "All offence" counting | Totals aren't comparable to Statistics Canada figures | Don't compare against StatCan |
| Not real-time; data lags and classifications can change | Limits the "recent activity" use case | Show the data's last-updated date |
| Reported crime only | Under-reporting varies by crime type and area | Note in the app |

- [x] The data contains information relevant to the problem
- [x] I believe there is enough signal in the data to support a useful solution
- [x] I have enough coverage, quality, and volume to test that assumption

---

## Repository structure

```
├── README.md
├── .gitignore            # excludes raw VPD CSVs and zip files
├── data/
│   ├── raw/              # put downloaded VPD CSVs here (not committed)
│   └── vpd/              # VPD's legal disclaimer and data description
├── scripts/
│   └── prepare_data.py   # turns the raw CSVs into web/data.json
└── web/
    ├── index.html        # the app
    └── data.json         # processed data the app loads
```

## Responsible use and disclaimer

- **Not a safety rating.** VanScene shows where incidents were reported. It does not measure how safe an area is or predict future incidents.
- **Reported incidents only.** Many crimes go unreported, and reporting rates differ by crime type and community.
- **Approximate locations.** VPD offsets incident locations for privacy, and exact addresses are never shown. The 500-metre area is an estimate, not a precise boundary.
- **Violent crime isn't on the map.** VPD withholds locations for offences against a person, so the map shows property crime and traffic collisions only.
- **Counts, not rates.** Busy areas with more residents, workers, and visitors naturally show more reports. Figures are not adjusted for population or foot traffic.
- **Data limitations.** VPD open data can be delayed, revised, or reclassified. We can't guarantee figures are complete or current.
- **Responsible use.** Don't use VanScene to discriminate against people or communities, or as the sole basis for housing, rental, insurance, or employment decisions.

VanScene is an informational and educational project built for the Build Trilemma Datathon. It is not affiliated with, endorsed by, or operated by the Vancouver Police Department or the City of Vancouver. It is provided "as is," without warranties of any kind.

**Data source:** Vancouver Police Department, [GeoDASH Open Data](https://geodash.vpd.ca/opendata/).

**In an emergency, call 911.** VanScene is not an emergency or crime reporting service.

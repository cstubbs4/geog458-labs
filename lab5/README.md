# Lab 5 – Millions of Data, Flow Map: New York Harbor Vessel Traffic


---

## Study area and dates

**Region.** The southern part of Upper New York Bay, from the Verrazzano-Narrows up past the St. George ferry terminal on Staten Island, including the eastern entrance to the Kill Van Kull. The returned points span roughly **−74.078° to −73.978° longitude and 40.536° to 40.652° latitude**, an area of about 8.5 × 13 km.

**Dates.** **December 19, 2021, 00:00 UTC through January 21, 2022, 23:59 UTC** (34 days). AccessAIS timestamps are in UTC. The map converts them to **New York local time (EST, UTC−5)**, so the data runs from Dec 18, 2021, 19:00 EST to Jan 21, 2022, 18:59 EST. **All clock times below are EST.**

**Why this region.** The Narrows is the single entrance that ocean-going ships use to reach the Port of New York and New Jersey. The same small stretch of water also holds the Staten Island Ferry terminal, ship anchorages, and a large fleet of tugs and service boats. That puts very different kinds of movement (commuter ferries, container ships, tankers, working boats) into one compact frame, which suits a filter-by-type map.

**Why these dates.** I wanted a range that covers the winter holidays (Christmas, New Year's Day, and Martin Luther King Jr. Day) alongside ordinary weekdays and weekends. That makes it possible to compare holiday traffic with normal traffic, which a single day could not show.

**Source.** U.S. vessel traffic from [AccessAIS](https://marinecadastre.gov/accessais/), a MarineCadastre partnership of BOEM, NOAA and the U.S. Coast Guard. AccessAIS returns point observations (one row per vessel per report), and the trajectories on the map are built by connecting each vessel's reports in time order.

**Keeping the dataset manageable.**
- I kept the **area small** instead of requesting the whole harbor, which limited the result to **646 vessels** even over 34 days.
- The CSV came to about **77 MB and 697,410 position reports**. It was converted into one JSON object per vessel (`path` and `timestamps` arrays measured in seconds from a shared UTC origin). The resulting `ais_trips.json` is about **20 MB**, which loads and animates smoothly in the browser.
- Because a long date range plays slowly at the default 15 minutes per second, I added a 6-hour-per-second playback option. At that speed the whole period plays in about 2.5 minutes.
- Filtering runs only when the dropdown changes, not every animation frame, as the lab and the deck.gl performance guide recommend.

---

## Interpretation


### 1. Passenger traffic has a commuter rhythm; cargo and tankers do not

**Observed.** With the Passenger filter on, the harbor is quiet overnight. On an average night only about 1.6–1.8 passenger vessels are moving per hour between 1:00 and 3:00 a.m. Activity jumps after 5:00 a.m., peaks at about 11.5–12 moving passenger vessels per hour at **7:00–8:00 a.m.** and again at **5:00 p.m.**, and falls off after 9:00 p.m. Switching to **Cargo** or **Tanker** shows no such cycle: both stay roughly flat at 1–2.7 moving ships per hour around the clock. Ferries near St. George show the same weekday pattern in miniature. On weekday mornings (6:00–9:00 a.m.) about 3.2–3.5 ferries per hour are moving in the frame, compared with about 2.2 on weekend mornings.

**Possible explanation.** Passenger boats follow people's work schedules, while big ships follow tides, berth availability and ocean voyages, which don't care what time of day it is. The Staten Island Ferry [runs 24 hours a day but adds service during weekday rush hours, roughly 6:00–9:30 a.m. and 3:30–8:00 p.m.](https://metroeasy.com/usa/staten-island-ferry/), which matches the morning and evening peaks and the weekday-versus-weekend difference.

### 2. "Passenger" is not only ferries

**Observed.** Pausing the map and checking vessel names shows that the Passenger category includes the Staten Island Ferry boats (e.g. *Spirit of America*, *Andrew J. Barberi*, *Sen. John J. Marchi*, *SSG Michael H. Ollis*). It also includes crew boats such as *Rosemary Miller* and *Samantha Miller*, which are coded as passenger vessels (AIS type 60). The ferries themselves appear only briefly at the top of the frame because my box ends just north of the St. George terminal. Their Manhattan end at Whitehall is outside the study area.

**Possible explanation.** These "Miller" boats belong to Miller's Launch, a Staten Island marine service company [based next to the St. George Ferry Terminal](https://professionalmariner.com/?p=8878) that runs crew boats, launches and tugs around the harbor. So the passenger rush-hour peak reflects both public commuters and workers being shuttled to ships and job sites. AIS type codes describe how a vessel is registered, not what it is doing at that moment.

### 3. Cargo ships move along one corridor and wait at anchor

**Observed.** Under the **Cargo** filter, moving container ships follow a single north–south line through the Narrows (most moving cargo positions fall between about −74.054° and −74.033° longitude). Near the top of the frame they bend west toward the Kill Van Kull. Ships traveling at full speed are only part of the picture: **about 52% of all cargo and tanker reports were essentially stationary** (under 0.5 knots), clustered off Staten Island's east shore around 40.634° N, 74.059° W. In the animation these ships show up as short, nearly motionless trails that persist for hours. Moving passenger vessels travel noticeably faster (median **15.1 knots**) than cargo ships (**9.7 knots**) or tankers (**7.5 knots**).

**Possible explanation.** Container ships bound for Port Newark–Elizabeth, the busiest container terminal on the East Coast, [arrive through the Narrows and the Kill Van Kull](https://en.wikipedia.org/wiki/Port_Newark%E2%80%93Elizabeth_Marine_Terminal). The Kill is narrow, and [large ships may only transit at high tide and one at a time](https://www.freightwaves.com/?p=144903), so ships often have to wait their turn. The stationary cluster sits where the port's [Stapleton anchorage](https://en.wikipedia.org/wiki/Port_of_New_York_and_New_Jersey) is located.

### 4. Holidays and a storm nearly emptied the commercial channel

**Observed.** On a typical day, 13–22 different cargo ships were moving in the frame. That number fell to **2 on December 25**, **1 on January 1**, and **0 on January 17**. "Other" vessels (tugs, towboats, port tenders, police and fire boats) also dropped, from about 70–85 per day to **35 on Christmas** and **30 on January 17**. The drop is easy to see by setting the slider to mid-day on one of those dates and switching between Cargo and Other.

**Possible explanation.** December 25 and January 1 are holidays, when port terminals and labor run reduced schedules. January 17, 2022 was Martin Luther King Jr. Day, and a winter storm also hit that day. [Forecasters expected wind gusts of around 45 mph in New York City](https://www.click2houston.com/news/national/2022/01/17/winter-storm-whipping-northeast-us-with-snow-thunderstorms/), and the city [issued a travel advisory for heavy rain and strong winds from Sunday evening through Monday](https://abc7ny.com/post/winter-storm-warning-ny-nj-snow/11478803/). From this data alone I can't tell whether the holiday or the weather mattered more, but both point toward fewer ship movements. Passenger traffic dropped less on these days (11–15 vessels instead of 20–25), which fits a ferry that runs every day of the year.

### Limits of what the map shows
- Where a vessel has a long gap between reports, `TripsLayer` draws a straight line across it. That line is not observed movement.
- **Other vessels** combines many types. In this dataset it is mostly towing vessels (type 31), unspecified types (90), port tenders (53), search-and-rescue (51), tugs (52) and law enforcement (55), so the category hides a lot of variety.
- The study area cuts off routes at its edges (the ferry's Manhattan leg and most of the Kill Van Kull), so trips appear to start or end at the frame boundary.

---

## Feature proposal for a potential user

**User.** A maritime operations analyst at the Port Authority of New York and New Jersey (or a harbor pilot dispatcher) who plans staffing for pilots, tugs and launch crews.

**Their question.** *When does commercial traffic through the Narrows rise and fall, and how unusual are holidays and storm days compared with a normal week?* My current map can answer this only if the user scrubs through 34 days by eye and remembers what they saw. The slider shows *where* things are at one moment, but not *how much* traffic there is over time.

**Resource reviewed.** [kepler.gl Time Playback guide](https://docs.kepler.gl/docs/user-guides/h-playback)

**Proposed feature: a traffic histogram on the time slider, with a selectable time window.** kepler.gl places a playback window at the bottom of the map. Its bars show how all data points are distributed over time, and the user selects a rolling time window within it. Its distribution graph can also be switched from a simple count to a time series of any chosen field. I would add something similar to my map:

1. Behind the existing time slider, draw a small bar chart with one bar per hour, showing **how many vessels of the currently selected type are moving**. The bars update when the vessel filter changes.
2. Let the user drag the edges of a highlighted window on that chart, for example "Jan 16, 18:00 – Jan 18, 06:00 EST". The map would then show only trips in that window, and the window could be played on a loop.

**How the resource informed it / why it is useful.** The kepler.gl guide showed me that a time control can do more than say *when* you are: it can also show *how much is happening* across the whole range. For the analyst, the Christmas, New Year's and MLK-Day dips in cargo traffic would stand out immediately as gaps in the bars, without watching the animation. The morning and evening passenger peaks would show up as a repeating daily pattern. Selecting a window would let them compare "storm day" with "normal Monday" side by side on the map, which supports planning decisions such as how many tug crews to schedule on a holiday or when to expect a backlog at the anchorage after bad weather. Adding vessel-name tooltips (from the [deck.gl interactivity guide](https://deck.gl/docs/developer-guide/interactivity)) would be a natural next step, so they could identify which ship is sitting at anchor.

---

### Sources
- AccessAIS / MarineCadastre: https://marinecadastre.gov/accessais/
- Staten Island Ferry schedule: https://metroeasy.com/usa/staten-island-ferry/
- Miller's Launch ("Tugboat Alley"), *Professional Mariner*: https://professionalmariner.com/?p=8878
- Port Newark–Elizabeth Marine Terminal (Wikipedia): https://en.wikipedia.org/wiki/Port_Newark%E2%80%93Elizabeth_Marine_Terminal
- Port of New York and New Jersey, channels and anchorages (Wikipedia): https://en.wikipedia.org/wiki/Port_of_New_York_and_New_Jersey
- Kill Van Kull transit limits, *FreightWaves*: https://www.freightwaves.com/?p=144903
- January 17, 2022 storm (AP via Click2Houston): https://www.click2houston.com/news/national/2022/01/17/winter-storm-whipping-northeast-us-with-snow-thunderstorms/
- January 17, 2022 storm (WABC): https://abc7ny.com/post/winter-storm-warning-ny-nj-snow/11478803/
- kepler.gl Time Playback guide: https://docs.kepler.gl/docs/user-guides/h-playback
- deck.gl interactivity guide: https://deck.gl/docs/developer-guide/interactivity

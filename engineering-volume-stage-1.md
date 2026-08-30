# TIP. Stage 1 Engineering Volume. Outline. 2026-08-26
Status: outline accepted (2026-08-26); volume cleaned 2026-08-29
Source of truth: kanon-etapu-1
Code: do not write
Confidential (Watchlist, commands, entry/exit points, forecast): not in this file and not in GitHub
File rule: only kanon-etapu-1 and this volume. No new working files.
Protocol: design architecture now. Do not invent missing parameters.
Named-incomplete: maturity parameters, colour codes, calculator formulas, Postmarket, Session details beyond the strip.
Change request vs kanon-etapu-1: Session is not a linear Watchlist-selection module. The canon file is not edited in this cleanup.
## 1. Stage 1 boundary and status dictionary
Stage 1 = platform without trading rights. No orders. No broker execution. No BUY/SELL/HOLD/EXIT NOW.
In stage 1:
- Linear modules (selection into Watchlist only): Base Universe, Market Context, Long, Short, Range, Watchlist, Entry-Exit.
- Watchlist then runs all day by itself on up to 30 selected names (not bought).
- Session = already-bought Portfolio names. Not selection. Not a Watchlist helper. May be empty while there are no buys.
- Separate calculations: indicators; money corridor; entry/exit points; 3-trading-day forecast (not the full universe).
- Data: local only. Hot + Archive + separate market store.
- Library = GET canonical snapshot. Archive = APPEND decision fact. Do not copy the market into HIL.
- Confidential (Watchlist, commands, points, forecast): not in GitHub, not in the cloud.
Out of stage 1:
- Broker execution and live trade commands.
- Stage 2: dataset for probability models.
- Stage 3: files-and-hardware assistant.
Statuses:
- canon — accepted, only kanon-etapu-1
- draft — the .docx files in the repo
- approved — after a section of this volume is accepted
- named-incomplete — in the contour, details not approved
This volume beats draft when they conflict. Canon beats this volume except the Session change request above.
## 2. System contour
External data → Normalization → Canonical snapshot → Linear selection → Watchlist → Four separate calculations.
Linear selection: Base Universe → Market Context → Long / Short / Range → Watchlist.
Entry-Exit sits on this path as a selection helper, not as trade execution.
Watchlist accompanies selected names all day. It does not need Session.
Session is a parallel strip of bought names (Portfolio), not on the selection path.
Market clock (canonical entity Trading Session: premarket / regular / after-hours) is calendar data, not the Session module.
Four separate calculations (not the full universe):
1. Indicators
2. Money corridor
3. Entry/exit points
4. 3-trading-day forecast
Stores: Library GET; Archive APPEND; Market store for market data; do not copy the market into HIL.
Modules do not call each other directly. Each publishes a result into the shared space.
Confidential results stay local, not in GitHub.
## 3. Shared space contract
Library: GET only. Canonical snapshot. Does not hold Watchlist, commands, points, forecast.
Archive: APPEND only. Decision facts (who/what/when/result id). Does not hold a second copy of the market.
Market store: market data, local only. HIL must not duplicate this market.
Confidential (local, never GitHub, never cloud): Watchlist; Session/Portfolio names; commands; entry/exit points; forecast.
A module reads Library (GET), may read Market store, publishes its result once. Decision facts go to Archive (APPEND). Confidential results stay in the local confidential store.
## 4. Linear modules
Stage 1 job of this chain: select names into Watchlist. Nothing else. Session is not in this chain.
| Module | One job in stage 1 |
|---|---|
| Base Universe | Build the allowed instrument set |
| Market Context | State of the market for selection |
| Long | Candidates for long |
| Short | Candidates for short |
| Range | Candidates for range |
| Watchlist | Merge candidates into one working list (max 30) and accompany them all day |
| Entry-Exit | Selection helper only — not trade execution |
Forbidden in this chain: broker orders, BUY/SELL/HOLD/EXIT NOW, writing confidential data to GitHub.
Long, Short, and Range may propose the same name. Watchlist resolves duplicates and keeps the source of inclusion. If Long and Short both claim the same name: Conflict.
Watchlist maturity parameters: named-incomplete.
## 5. Separate calculations
These four are not linear selection. They do not run on the full universe.
| Calculator | Output | Consumer |
|---|---|---|
| Indicators | Indicator values | Row, Monitor 3 |
| Money corridor | Corridor in money | Row chart 5+3; Monitor 3 |
| Entry/exit points | Numeric points, not an order | Watchlist traffic light (entry); Session traffic light (exit); Monitor 3 |
| 3-trading-day forecast | Path forecast for 3 trading days (not 4) | Row chart; Monitor 3 |
No calculator may emit BUY/SELL/HOLD/EXIT NOW.
Entry/exit points ≠ Entry-Exit module.
Calculator parameters: named-incomplete.
## 6. Local storage
Stage 1 storage is local only. No GitHub. No cloud.
| Store | Holds | Does not hold |
|---|---|---|
| Library | Canonical snapshot (GET) | Watchlist, Session names, commands, points, forecast |
| Archive | Decision facts (APPEND) | A second copy of the market |
| Market store | Market data | HIL duplicate of that market |
| Confidential store | Watchlist, Session/Portfolio names, commands, entry/exit points, forecast | Public repo copies |
Recovery: Hot continues the current day. Archive shows what was decided. Market store rebuilds prices without HIL. Confidential store restores lists and calculations without GitHub.
## 7. Open decisions and change requests
Accepted: file rule; sections 1–6, 8–10 as architecture after the 2026-08-29 cleanup; calculator names not parameter sheets.
Change request vs canon: remove Session from the linear Watchlist-selection list in kanon-etapu-1. Not done in that file yet.
Named-incomplete: maturity parameters; colour codes; how many of the 30 are Priority; calculator formulas; Postmarket; Session beyond the bought-strip.
Not opened yet: code; Figma; database schemas.
Draft .docx (Entry Exit Semaphore, Interface, Watchlist) are not rewritten. This volume replaces them where they conflict.
## 8. Watchlist architecture (monitor 1)
Watchlist = selected names, not bought. First graphical module. Monitor 1. Question: which names are worth looking at?
It runs all day by itself. Max 30 names (Priority + Extended together). No orders on this screen.
### Row
Two pictures on one row. Do not mix them.
1. Traffic light (primary Watchlist parameter) = entry point
   - Each stripe fills when the stock meets the next maturity parameter — approach to the entry point.
   - The last large semaphore lights when entry-point calculation and the stock parameters coincide.
   - Large semaphore = match of calculations, not an order.
2. Small chart: about 5 trading days of real price + 3-day forecast line + corridor in money (constant width). Price fact vs forecast, not the entry point.
Also on the row: symbol; selection source (Long / Short / Range, may be several); main scenario or Conflict; list status (in list / pending / removed) as names only; indicators; forecast present or not.
Click a ticker: only Monitor 3 changes (detailed analysis). The system keeps running. Not an order. Confirm and trade buttons are not on this screen.
A card on Monitor 1, if shown, is view only. Graph takes most of the card; text the smallest area.
Maturity parameters, stripe count, colour codes, corridor/forecast formulas: named-incomplete.
### Data
Membership and semaphore/chart outputs: confidential store, not GitHub, not cloud.
Entry-point numbers come from the separate calculator. Watchlist draws them as the traffic light.
Order: name on the list → entry-point calculation → stripes and large semaphore on the row.
### Colour by selection module
Long, Short, and Range data use three different colour languages on the row (source mark and traffic light).
The 5+3 forecast chart does not colour the price line by scenario.
Long and Short traffic lights are opposite in sign (same meaning, inverted visual).
Range is a third colour language, not the opposite of Long or Short.
If Long and Short both claim the same name: Conflict — do not show two opposite match lights on one row.
## 9. Monitor 3 analysis
Monitor 3 is the detailed analysis view of one ticker. Choosing a ticker only changes what Monitor 3 shows. The rest of the system keeps running. No order, no Confirm, no BUY/SELL/HOLD/EXIT NOW.
How a ticker appears on Monitor 3:
- click a ticker on Watchlist or Session; or
- the trader types a ticker.
Typing a ticker does not add it to Watchlist (the 30) and does not make it a bought Session/Portfolio name.
### Overlay set
Large candle chart, plus:
1. Volume
2. MA20, MA50, MA200
3. Support
4. Resistance
5. Forecast corridor in money (same 5 real days + 3 forecast days as the row chart, expanded)
6. Entry point if the ticker is from Watchlist
7. Exit point if the ticker is from Session (bought)
RSI, ADX, ATR stay in selection drafts unless later assigned to this monitor.
MA periods and level rules: named-incomplete.
## 10. Session strip (bought names)
Session = Portfolio names the trader already bought. Not selection. Not a Watchlist helper.
Stage 1 has no trading rights, so this strip may be empty.
Same strip as Watchlist: indicators, traffic light, chart of 5 real days + 3 forecast days, corridor in money.
Session traffic light = exit point (same visual language as Watchlist, opposite role).
Watchlist light = entry. Session light = exit.
Market clock (canonical entity Trading Session) is calendar data, not this module.
Click a Session ticker: only Monitor 3 shows that name. No other system state changes.
## 11. IBKR, Watchlist and Session movement
Status: architecture accepted 2026-08-30. Code: do not write.
Laptop (Home): Interactive Brokers only. TIP is not on the laptop.
Home and Travel are two layout mode buttons, not orders.
IBKR enters TIP at read level only. TIP reads positions and fills. TIP does not send orders.
Modules do not call each other. A buy or sell is a fact from IBKR into the shared space (GET / Archive APPEND). Local and confidential, not GitHub.
### Movement
- Selection → Watchlist (max 30, not bought).
- Buy in IBKR → name leaves Watchlist and appears on Session (bought).
- Sell → name leaves Session and is gone. It does not return to Watchlist.
What happens to remaining Watchlist names at the end of the day is Postmarket. Not discussed in this section.
## 12. Home and Travel stations
Status: architecture accepted 2026-08-30. Code: do not write.
Two hardware stations, one TIP system. Home / Travel buttons switch layout only, not orders.
Home: PC + three home monitors (TIP). Laptop beside it: Interactive Brokers only.
Travel: same laptop (IBKR only) + two other travel monitors. Travel monitors are not the home monitors. They are extra TIP screens of the same home system.
The home PC runs TIP around the clock and owns TIP state (Watchlist, Session, Monitor 3). The laptop does not run TIP. RAM size on the ThinkPad does not change this.
If the home PC / TIP is down: the laptop keeps working alone so positions can be closed in IBKR. TIP is not required to exit. No second TIP engine on the laptop (not even a live failover brain).
IBKR read path (section 11) is unchanged.
## 13. Postmarket
Status: architecture accepted 2026-08-30. Formulas and table columns named-incomplete. Code: do not write.
Postmarket is the phase after the regular session. Not live selection, not a fourth trading screen.
Watchlist names are not kept after Session ends. The list of 30 is cleared. Kept: the course of events, not a living Watchlist.
Unsold Session / Portfolio names stay. Postmarket does not sell them and does not clear Monitor 2.
No orders. Market is not copied into HIL. Archive APPEND of events and module evaluations, local, not GitHub.
What is in front:
1. Money-corridor calculator: how it worked during the day (primary).
2. Three-day forecast versus fact. Original forecast line is not rewritten. Scoring covers today and still-open days of earlier forecasts.
3. Semaphore behaviour during the day (entry on Watchlist, exit on Session).
4. Module activity during the day, archived for later analysis.
Other day snapshots: second rank.
Monitors after close (Home):
Monitor 2 — unchanged: all unsold Portfolio names, same strip, 5+3 chart, corridor in money, exit light.
Monitor 3 — graph first (5+3+corridor). Under the graph a column of rows for every Portfolio name: ticker — forecast — fact — error in percent with + or −. Percent formula: named-incomplete. Click a row only changes the graph above.
Monitor 1 — Watchlist is gone. Two table blocks, Postmarket only: entry-strip analysis; exit-strip analysis. Not shown during the session. Columns: named-incomplete.
Travel layout on two screens: not specified here.




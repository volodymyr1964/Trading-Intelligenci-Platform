закінчуеться 
# TIP. Stage 1 Engineering Volume. Outline. 2026-08-26
Status: outline accepted (2026-08-26)
Source of truth: kanon-etapu-1
Code: do not write
Confidential (Watchlist, commands, entry/exit points, forecast): not in this file and not in GitHub
File rule: only kanon-etapu-1 and this volume. No new working files.
Protocol: design architecture now. Do not invent missing parameters.
Named-incomplete (later, same volume): Watchlist, Session, Postmarket, calculator parameters.
## 1. Stage 1 boundary and status dictionary
Stage 1 = platform without trading rights. No orders. No broker execution. No BUY/SELL/HOLD/EXIT NOW.
In stage 1:
- Linear modules: stock selection into Watchlist only. Base Universe, Market Context, Long, Short, Range, Watchlist, Entry-Exit, Session.
- Separate calculations (not the linear path): indicators; money corridor; entry/exit points; 3-trading-day forecast for Watchlist only.
- Data: local only. Hot + Archive + separate market store.
- Library = GET canonical snapshot. Archive = APPEND decision fact. Do not copy the market into HIL.
- Confidential (Watchlist, commands, points, forecast): not in GitHub, not in the cloud.
Out of stage 1:
- Trading rights, portfolio, live session commands.
- Stage 2: dataset for probability models.
- Stage 3: files-and-hardware assistant.
Statuses:
- canon — accepted, only kanon-etapu-1
- draft — the .docx files in the repo
- approved — after a section of this volume is accepted
- named-incomplete — in the contour, details not approved
Canon beats draft when they conflict.
## 2. System contour
External data → Normalization → Canonical snapshot → Linear selection (Watchlist only) → Watchlist → Four separate calculations.
Linear selection (selection only): Base Universe → Market Context → Long / Short / Range → Watchlist.
Entry-Exit and Session sit on this path as selection helpers, not as trade execution.
Four separate calculations (only after Watchlist exists, only on Watchlist names):
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
Confidential (local, never GitHub, never cloud): Watchlist; commands; entry/exit points; forecast.
A module reads Library (GET), may read Market store, publishes its result once. Decision facts go to Archive (APPEND). Confidential results stay in the local confidential store.
## 4. Linear modules
Stage 1 job of this chain: select names into Watchlist. Nothing else.
| Module | One job in stage 1 |
|---|---|
| Base Universe | Build the allowed instrument set |
| Market Context | State of the market for selection |
| Long | Candidates for long |
| Short | Candidates for short |
| Range | Candidates for range |
| Watchlist | Merge candidates into one working list |
| Entry-Exit | Selection helper only — not trade execution |
| Session | Session/calendar context for selection — not live trading |
Forbidden in this chain: broker orders, portfolio, BUY/SELL/HOLD/EXIT NOW, writing confidential data to GitHub.
Long, Short, and Range may propose the same name. Watchlist resolves duplicates and keeps the source of inclusion.
Watchlist, Session (and Postmarket) parameters: named-incomplete.
## 5. Separate calculations
These four are not linear selection. They run after Watchlist exists, only on Watchlist names.
| Calculator | Output | Consumer |
|---|---|---|
| Indicators | Indicator values | Trader view; later modules as published facts |
| Money corridor | Corridor in money (volatility and related trader metrics) | Trader view |
| Entry/exit points | Numeric points, not an order | Trader view |
| 3-trading-day forecast | Path forecast for 3 trading days (not 4) | Trader view; Watchlist only |
No calculator may emit BUY/SELL/HOLD/EXIT NOW.
Entry/exit points ≠ Entry-Exit module.
Forecast is not a linear module and is not run on the full universe.
Outputs are confidential: local only.
Calculator parameters: named-incomplete. Formulas later, same volume.
## 6. Local storage
Stage 1 storage is local only. No GitHub. No cloud.
| Store | Holds | Does not hold |
|---|---|---|
| Library | Canonical snapshot (GET) | Watchlist, commands, points, forecast |
| Archive | Decision facts (APPEND) | A second copy of the market |
| Market store | Market data | HIL duplicate of that market |
| Confidential store | Watchlist, commands, entry/exit points, forecast | Public repo copies |
Recovery: Hot continues the current day. Archive shows what was decided. Market store rebuilds prices without HIL. Confidential store restores Watchlist and calculations without GitHub.
## 7. Open decisions and change requests
Accepted: file rule; sections 1–6 as architecture; section 5 as calculator names and prohibitions, not parameter sheets; protocol.
Named-incomplete (architecture later, same volume): Watchlist parameters; Session parameters; Postmarket parameters; calculator parameters.
Change request vs canon: none.
Not opened yet: code; screen layouts; database schemas; formulas.
Next in this volume: architecture of Watchlist, then Session, then Postmarket — still without filling incomplete parameters as if they were final.
## 8. Watchlist architecture (monitor 1)
Status: architecture accepted 2026-08-28. Parameters named-incomplete. Code: do not write.
Watchlist is the first stage-1 module with a graphical display. It occupies Monitor 1 (Opportunity Watchlist). Question of the screen: which names are worth looking at?
Monitor 1 is not Portfolio (monitor 2) and not deep candle analysis (monitor 3). Stage 1 has no trading rights: no BUY/SELL/HOLD/EXIT NOW on this screen.
### Row
Two pictures on one row. Do not mix them.
1. Traffic light (primary Watchlist parameter)
   - Each stripe fills when the stock meets the next maturity parameter — approach to the entry point.
   - The last large semaphore lights when entry-point calculation and the stock parameters coincide.
   - Large semaphore = match of calculations, not an order.
2. Small chart: about 5 trading days of real price + 3-day forecast line + corridor of constant width.
   - This is price fact vs forecast, not the entry point.
Also on the row: symbol; selection source (Long / Short / Range, may be several); main scenario or Conflict; list status (in list / pending / removed) as names only; forecast present or not.
Click on a row opens a card, not a trade. Confirm and trade buttons are not on this screen.
Which maturity parameters, how many stripes, colours, and numeric thresholds: named-incomplete. Corridor and forecast formulas: named-incomplete.
### Data
Membership and semaphore/chart outputs: confidential store, not GitHub, not cloud.
Entry-point numbers come from the separate calculator. Watchlist draws them as the traffic light.
Order: name on the list → entry-point calculation → stripes and large semaphore on the row.
Draft .docx (Watchlist, Interface) do not override this section. Canon still beats draft.
### Card
Click on a row opens a card of the same instrument, not a trade.
The graph takes most of the card: 5 trading days of real price + 3-day forecast + corridor.
Text takes the smallest area: symbol, sources, scenario or Conflict, traffic light (maturity stripes + large match semaphore), selection reason if already provided by selection modules.
No Confirm, no Buy/Sell. Deep candle analysis is not this card (monitor 3).
### Size cap
Stage 1 Watchlist holds at most 30 names in total (Priority + Extended together).
Architecture does not fix how many of the 30 are Priority. Selection rules into the 30: named-incomplete.
### Colour by selection module
Long, Short, and Range data use three different colour languages on the row (source mark and traffic light).
The 5+3 forecast chart does not colour the price line by scenario.
Long and Short traffic lights are opposite in sign: same meaning (maturity stripes → large match semaphore) with inverted visual.
Range is a third colour language, not the opposite of Long or Short.
If Long and Short both claim the same name: Conflict — do not show two opposite match lights on one row.
Exact colour codes: named-incomplete.
## 9. Monitor 3 analysis
Status: architecture accepted 2026-08-29. Formulas named-incomplete. Code: do not write.
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

















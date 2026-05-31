# 2022 World Cup Knockout Stage Simulator

This repository presents a **data-driven approach to simulating football matches** using Markov chains and Monte Carlo methods. Built entirely on **StatsBomb open event data**, the model constructs possession transition matrices for each team from their group stage games and uses them to simulate the 2022 FIFA World Cup knockout bracket — predicting outcomes, scoreline distributions, and team quality rankings with no external information beyond raw match events.

---

## Project Context

The central question driving this project: **how much can group stage event data tell us about knockout stage outcomes?**

Rather than using goals, standings, or traditional statistics, the model encodes each team's playing style as a **Markov chain** — a probabilistic map of how they move the ball across nine pitch zones. Two of these chains are then set to interact in a simulation engine that alternates possession between teams until a 90-minute proxy clock expires.

Key aspects of the analysis include:

- Modelling possession as a **Markov chain** over a 9-zone pitch grid (3 thirds × 3 channels) with Shot and Turnover as absorbing states
- Deriving **per-team transition matrices** from StatsBomb event data using possession-bounded sequences
- Running **1,000 Monte Carlo simulations** per matchup to produce scoreline distributions and win probabilities
- Applying **zone-based xG conversion rates** (inspired by real shot quality maps) to translate shots into goals
- Ranking all 32 teams by **shot absorption probability** — the model's implicit quality metric derived purely from the transition matrices
- Simulating the full knockout bracket and evaluating predictions against actual results

The model correctly predicted **6 of 8 Round of 16 outcomes** and **2 of 3 evaluable Quarter-Final outcomes** — 8 out of 11 evaluable matches — using only group stage data.

---

## What's Included

- `worldcup_simulation.ipynb` — Full notebook: data loading, matrix construction, simulation engine, bracket prediction, and all visualisations
- `worldcup_simulation.py` - Clean python script version of the workflow
- `visualisations` - all visualisations as .png files
- `README.md` — Project overview and context

---

## Visualisations

| Plot | Description |
|------|-------------|
| **Team Power Ranking** | All 32 teams ranked by shot absorption probability derived from their Markov chain |
| **Scoreline Distribution** | Top-N simulated scorelines per match with actual result highlighted and percentile annotation |
| **Goal Difference KDE** | Smoothed distribution of simulated goal differences with actual result marked — shows model surprise quantitatively |
| **Possession Transition Flow** | Markov chain visualised on a pitch for the four semi-finalists — arrow thickness encodes transition probability |

---

## Tools Used

- **Python** (NumPy, Pandas, SciPy)
- **StatsBomb open data** via `mplsoccer` — event-level match data (passes, carries, shots, dribbles)
- **Plotly** — interactive visualisations
- **Matplotlib + mplsoccer** — pitch-based transition flow diagrams

---

## Key Findings

- The model's internal power ranking (shot absorption) correctly identified France, Brazil, and England as the top three knockout-stage teams
- **Germany ranked 1st overall** despite group stage elimination — the matrix captures style quality, not results, validating the approach
- **France vs Poland**, **Argentina vs Australia**, and **France vs England (QF)** were the model's most accurate predictions: modal simulated scoreline matched the exact actual result
- **Portugal 6-1 Switzerland** was the biggest outlier — actual result sat at the 98.9th percentile of the simulated distribution
- The 50/50 possession split is a known structural limitation — possession asymmetry via retention rates is identified as the primary future extension
- Apart from the actual scoreline, the predicted shots taken by each team also matched real-world games: Brazil had 17 shots against Korea (17.6 predicted), Spain has 13 shots against Morocco (11.6 predicted), Switzerland had 10 shots against Portugal (11.2 predicted), Argentina had 14 shots against Netherlands (13 predicted), England had 13 shots against France (13.4 predicted), etc.
- The model was able to differentiate between team performance and successfuly predict viable win percentages based on event data from just 3 group stage games
- It's important to note that the matchups given to the model were never used as 'training data' to create the transition matrices since each team had different opponents in the group stage, and all predictions were based only on the interaction of independent Markov chains 
---

## Limitations & Future Work

- **Possession symmetry** — the current engine alternates possession perfectly between teams. Adding retention rates derived from shot absorption would break this symmetry and produce realistic possession splits which might have some effect on the number of shots (and therefore, the scoreline). Although, the win percentage is not expected to deviate much
- **3 group stage games** is a thin training window — passive group stage performances (for example, Netherlands rotating players) produce unrepresentative matrices that do not present the true potential of passive teams. Although the window was deliberately kept tight to see how well the model could predict based on limited data, this is a genuine caveat
- **Penalties and extra-time periods are unmodelled** — Croatia vs Brazil and Morocco vs Spain both went to shootouts; the model has no mechanism for this. Extra-time can probably be modelled by adding another 30 minutes of proxy time in case the simulated scoreline ends on a draw, but penalties are hard to model
- **Shot conversion is zone-based, not player-based** — Mbappé shooting from Final_Center is treated identically to any other player, which might not be the case in real life. Player ability is largely unmodelled.
- **No contextual factors** — injuries, suspensions, fatigue, and opponent-specific tactical setups are outside the model's scope

Future extensions will explore possession retention mechanics, player-level xG weighting, and rolling matrix updates as the tournament progresses.

---

## Contact

If you'd like to discuss football analytics, Markov models, or collaboration opportunities:

- [LinkedIn](https://www.linkedin.com/in/aaditpahuja)
- 📧 aaditpahuja@gmail.com

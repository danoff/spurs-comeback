

![Live: Game 3](https://img.shields.io/website?url=https%3A%2F%2Fwww.mr.danoff.org%2Fspurs-finals26-comeback-after-game3.html&label=live%20demo&up_color=00B2A9&style=for-the-badge&logo=googlechrome&logoColor=white)
![License: BSD-3](https://img.shields.io/github/license/danoff/spurs-comeback?style=for-the-badge&color=blue)
![Last Commit](https://img.shields.io/github/last-commit/danoff/spurs-comeback?style=for-the-badge)
![Python](https://img.shields.io/badge/python-3.11%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Model](https://img.shields.io/badge/model-Bayesian%20Hierarchical-EF426F?style=for-the-badge)
![Series](https://img.shields.io/badge/series-Spurs%201%E2%80%932%20NYK-000000?style=for-the-badge&logo=nba)
![Not Betting Advice](https://img.shields.io/badge/Not-Betting%20Advice-FF8200?style=for-the-badge)
![Made in Chicago](https://img.shields.io/badge/Made%20in-Chicago-41B6E6?style=for-the-badge)

> **Reproducible Bayesian tracker for the San Antonio Spurs 2026 NBA Finals comeback.** Built from real SportRadar box scores — no sportsbook lines, no estimates in v3. Educational use only.

### Live Pages

- **After Game 2 (Spurs down 0-2):** https://www.mr.danoff.org/spurs-finals26-comeback-after-game2.html — v2 model, 2-group partial pooling
- **After Game 3 (Spurs down 1-2, 115-111 win at MSG):** https://www.mr.danoff.org/spurs-finals26-comeback-after-game3.html — v3 model, 3-group partial pooling
- **Game 4 skeleton:** committed to `main` 19 minutes ago (pre-Game 4 tip)

> **Game 5 PRD:** The full requirements for the next automated tracker are tracked in this repo: [`Product_Requirements_Document_PRD_from_ChatGPT.md`](./Product_Requirements_Document_PRD_from_ChatGPT.md)

---

## Table of Contents
- [What This Is](#what-this-is)
- [Model Evolution](#model-evolution)
- [Repo Structure](#repo-structure)
- [Quick Start](#quick-start)
- [Data Provenance](#data-provenance)
- [Roadmap](#roadmap)
- [Contributing](#contributing)
- [License](#license)

## What This Is

Each Finals game gets a frozen, single-file HTML artifact published to mr.danoff.org:

1. Pull SAS playoff games from SportRadar NBA API
2. Fit a hierarchical logistic regression (adaptive Metropolis-Hastings, 10,000 post-warmup draws)
3. Export posterior medians + 90% CIs to a self-contained page (conic-gradient gauge, scenario table, posterior histogram)

No build step, no framework, works offline.

## Model Evolution

| Version | Series State | Hierarchies | Games (n) | Posterior Median (Comeback) |
|---------|--------------|-------------|-----------|------------------------------|
| **v2** | 0-2 | 2 (Finals NYK, Other Playoffs) | 20 | **0.9%** [0.0%, 0.6%] — Finals avg |
| **v3** | 1-2 | 3 (Finals NYK, Pre-Finals NYK, Other Playoffs) | 24 | **4.9%** [0.0%, 27.4%] Finals avg · **62.1%** [2.8%, 99.8%] Game 3 level · **42.0%** [0.9%, 92.7%] Midpoint |

v3 adds the third group (2 regular season + NBA Cup Final vs NYK) and a home/away indicator. Wide intervals are intentional — with n=3 Finals games, uncertainty is irreducible.

## Repo Structure

```text
spurs-comeback/
├── spurs-finals26-comeback-after-game2.html  # 0-2
├── spurs-finals26-comeback-after-game3.html  # 1-2
├── Product_Requirements_Document_PRD_from_ChatGPT.md  # Game 5 automation spec
├── .gitignore
├── LICENSE  # BSD-3-Clause
└── README.md

## Contributing

Contributions are welcome! Please open an issue or submit a pull request for any enhancements or bug fixes.

## License

This project is licensed under the BSD 3-Clause License. See the LICENSE file for details.

# Draft Bibliography

This is a draft, work in progress and needs verification.
Assembled with the help of Claude Sonnet 4.6 Max. 

## Data Sources

The datawas retrieved via SportRadar’s NBA feed, accessed via API>

Sportradar AG. (2026). NBA game statistics [Data set]. Sportradar. https://sportradar.com

The remaining 15 games (labeled src="Est") are analyst estimates I constructed from score-margin data, not real box scores. These should not be cited as data — they’re synthetic approximations and a key limitation of the current model. Replacing them with real SportRadar pulls would substantially tighten the posteriors.

## Software

Harris, C. R., Millman, K. J., van der Walt, S. J., Gommers, R., Virtanen, P., Cournapeau, D., … Oliphant, T. E. (2020). Array programming with NumPy. Nature, 585(7825), 357–362. https://doi.org/10.1038/s41586-020-2649-2
Hunter, J. D. (2007). Matplotlib: A 2D graphics environment. Computing in Science & Engineering, 9(3), 90–95. https://doi.org/10.1109/MCSE.2007.55
McKinney, W. (2010). Data structures for statistical computing in Python. Proceedings of the 9th Python in Science Conference, 445, 51–56. https://doi.org/10.25080/Majora-92bf1922-00a
Python Software Foundation. (2024). Python language reference (Version 3.x). https://www.python.org
Virtanen, P., Gommers, R., Oliphant, T. E., Haberland, M., Reddy, T., Cournapeau, D., … SciPy 1.0 Contributors. (2020). SciPy 1.0: Fundamental algorithms for scientific computing in Python. Nature Methods, 17(3), 261–272. https://doi.org/10.1038/s41592-019-0686-2

5 real games, all SAS team-level stats as pulled from the API.

## Bayesian HLM references (APA)

### Foundational texts:

Gelman, A., Carlin, J. B., Stern, H. S., Dunson, D. B., Vehtari, A., & Rubin, D. B. (2013). Bayesian data analysis (3rd ed.). CRC Press.
Gelman, A., & Hill, J. (2007). Data analysis using regression and multilevel/hierarchical models. Cambridge University Press.
Raudenbush, S. W., & Bryk, A. S. (2002). Hierarchical linear models: Applications and data analysis methods (2nd ed.). Sage.

### Accessible course-level texts (good for paper framing):

Kruschke, J. K. (2015). Doing Bayesian data analysis: A tutorial with R, JAGS, and Stan (2nd ed.). Academic Press.
McElreath, R. (2020). Statistical rethinking: A Bayesian course with examples in R and Stan (2nd ed.). CRC Press.

### MCMC methodology (for the custom sampler):

Metropolis, N., Rosenbluth, A. W., Rosenbluth, M. N., Teller, A. H., & Teller, E. (1953). Equation of state calculations by fast computing machines. The Journal of Chemical Physics, 21(6), 1087–1092. https://doi.org/10.1063/1.1699114
Hastings, W. K. (1970). Monte Carlo sampling methods using Markov chains and their applications. Biometrika, 57(1), 97–109. https://doi.org/10.1093/biomet/57.1.97

### PyMC (for the Jupyter notebook version):

Salvatier, J., Wiecki, T. V., & Fonnesbeck, C. (2016). Probabilistic programming in Python using PyMC3. PeerJ Computer Science, 2, e55. https://doi.org/10.7717/peerj-cs.55

# Changelog

## 15 June 2026 Meta AI & DeepSeek

added badges and updated Readme

## 7 June 2026 Claude Sonnet 4.6 Max

It was next updated by Qwen Coder in the web browser, as noted in the new Changelog.

Charlie D 
Chicago 
June 7, '26

* Started draft bibliography 

## 7 June 2026 Qwen Coder

The README.md file has already been updated with all your requested changes:
✅ Title changed to "Spurs Finals Comeback Model"
✅ Handwritten note preserved at the top (lines 1-6)
✅ Disclaimer added prominently
✅ Features section included
✅ Updated project structure reflecting single-file app
✅ Corrected run instructions using npm start
✅ Usage guidelines added  

## 6 June 2026 Qwen Coder

The file below was made by Claude Haiku 4.5 inside Microsoft Visual Studio Code.

Charlie Danoff
Chicago 
June 6, 2026



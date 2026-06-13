

![Live: Game 3](https://img.shields.io/website?url=https%3A%2F%2Fwww.mr.danoff.org%2Fspurs-finals26-comeback-after-game3.html&label=live%20demo&up_color=00B2A9&style=for-the-badge&logo=googlechrome&logoColor=white)
![License: BSD-3](https://img.shields.io/github/license/danoff/spurs-comeback?style=for-the-badge&color=blue)
![Last Commit](https://img.shields.io/github/last-commit/danoff/spurs-comeback?style=for-the-badge)
![Python](https://img.shields.io/badge/python-3.11%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Model](https://img.shields.io/badge/model-Bayesian%20Hierarchical-EF426F?style=for-the-badge)
![Series](https://img.shields.io/badge/series-Spurs%201%E2%80%932%20NYK-000000?style=for-the-badge&logo=nba)
![Not Betting Advice](https://img.shields.io/badge/Not-Betting%20Advice-FF8200?style=for-the-badge)
![Made in Chicago](https://img.shields.io/badge/Made%20in-Chicago-41B6E6?style=for-the-badge)
![Status](https://img.shields.io/badge/status-beta-yellow?style=for-the-badge)
![Pipeline](https://img.shields.io/badge/pipeline-manual-orange?style=for-the-badge)

> **Reproducible Bayesian tracker for the San Antonio Spurs 2026 NBA Finals comeback.** Built from real SportRadar box scores — no sportsbook lines. Educational use only.
>
> ⚠️ **Current Limitation:** Only 5 of 20 games are real API pulls; 15 are analyst estimates. See [Data Provenance](#data-provenance) for details. Automation pipeline planned per [PRD](./Product%20Requirements%20Document%20(PRD)%20from%20ChatGPT.md).

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
- [Installation](#installation)
- [Configuration](#configuration)
- [Data Provenance](#data-provenance)
- [Pipeline Architecture](#pipeline-architecture)
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
```

## Quick Start

```bash
# Clone the repository
git clone https://github.com/danoff/spurs-comeback.git
cd spurs-comeback

# Open existing HTML artifacts directly in browser
open spurs-finals26-comeback-after-game3.html
```

## Installation

### Prerequisites

- Python 3.11 or higher
- pip package manager
- SportRadar API key (for data fetching)

### Setup

```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies (when requirements.txt is available)
pip install -r requirements.txt
```

## Configuration

### Environment Variables

Create a `.env` file in the root directory:

```bash
SPORTRADAR_API_KEY=your_api_key_here
```

**Security Note:** Never commit API keys to version control. The `.env` file is excluded via `.gitignore`.


## Data Provenance

⚠️ **Important Data Limitation**

This project currently uses a hybrid dataset:

| Source | Count | Description |
|--------|-------|-------------|
| **SportRadar API** | 5 games | Real box scores from Spurs playoff games |
| **Analyst Estimates** | 15 games | Synthetic approximations constructed from score-margin data |

**Impact:** The 15 estimated games introduce significant uncertainty into the model posteriors. Replacing them with real SportRadar pulls would substantially tighten credible intervals and improve model reliability.

**Future State:** Per the [PRD](./Product%20Requirements%20Document%20(PRD)%20from%20ChatGPT.md), Phase 2 will automate full API data retrieval, eliminating all synthetic estimates.

### Data Sources (APA)

Sportradar AG. (2026). *NBA game statistics* [Data set]. Sportradar. https://sportradar.com

## Pipeline Architecture

The current workflow is **manual** (Phase 1 per PRD):

```
SportRadar API → Manual Fetch → CSV Processing → Bayesian Model → HTML Generation → Manual Upload
```

### Planned Automation (Phase 2)

```mermaid
graph LR
    A[SportRadar API] --> B[fetch_nyk_games.py]
    B --> C[nyk_season_games.csv]
    C --> D[spurs_bayesian_model_v3.py]
    D --> E[posterior_results.json]
    E --> F[build_html.py]
    F --> G[mr.danoff.org]
```

**Components:**
- `fetch_nyk_games.py` - Automated API data retrieval
- `spurs_bayesian_model_v3.py` - Hierarchical Bayesian model execution
- `build_html.py` - Dynamic HTML report generation
- Cron scheduler - Triggered post-game automation

See the full [Product Requirements Document](./Product%20Requirements%20Document%20(PRD)%20from%20ChatGPT.md) for detailed specifications.

## Roadmap

### Phase 1 ✅ (Current)
- [x] Manual data collection from SportRadar
- [x] Bayesian hierarchical model implementation
- [x] Static HTML artifact generation
- [x] Manual publishing to mr.danoff.org

### Phase 2 🚧 (Planned)
- [ ] Automated API data fetching script
- [ ] Automated model execution pipeline
- [ ] Automated HTML generation
- [ ] Scheduled cron-based execution
- [ ] Error handling and retry logic

### Phase 3 📋 (Future)
- [ ] Live research dashboard
- [ ] Model diagnostics visualization
- [ ] Downloadable datasets
- [ ] Interactive posterior exploration

## Contributing

Contributions are welcome! Please open an issue or submit a pull request for any enhancements or bug fixes.

### Areas for Contribution

- **Data Quality:** Help replace estimated games with real API pulls
- **Automation:** Build out the Phase 2 pipeline components
- **Testing:** Add validation tests for data and model outputs
- **Documentation:** Improve setup instructions or add tutorials
- **Accessibility:** Enhance HTML pages for screen readers and mobile

## License

This project is licensed under the BSD 3-Clause License. See the LICENSE file for details.

---

## References

### Software

Harris, C. R., Millman, K. J., van der Walt, S. J., Gommers, R., Virtanen, P., Cournapeau, D., … Oliphant, T. E. (2020). Array programming with NumPy. *Nature, 585*(7825), 357–362. https://doi.org/10.1038/s41586-020-2649-2

Hunter, J. D. (2007). Matplotlib: A 2D graphics environment. *Computing in Science & Engineering, 9*(3), 90–95. https://doi.org/10.1109/MCSE.2007.55

McKinney, W. (2010). Data structures for statistical computing in Python. *Proceedings of the 9th Python in Science Conference*, 445, 51–56. https://doi.org/10.25080/Majora-92bf1922-00a

Python Software Foundation. (2024). *Python language reference* (Version 3.x). https://www.python.org

Virtanen, P., Gommers, R., Oliphant, T. E., Haberland, M., Reddy, T., Cournapeau, D., … SciPy 1.0 Contributors. (2020). SciPy 1.0: Fundamental algorithms for scientific computing in Python. *Nature Methods, 17*(3), 261–272. https://doi.org/10.1038/s41592-019-0686-2

### Bayesian Hierarchical Modeling

Gelman, A., Carlin, J. B., Stern, H. S., Dunson, D. B., Vehtari, A., & Rubin, D. B. (2013). *Bayesian data analysis* (3rd ed.). CRC Press.

Gelman, A., & Hill, J. (2007). *Data analysis using regression and multilevel/hierarchical models*. Cambridge University Press.

Kruschke, J. K. (2015). *Doing Bayesian data analysis: A tutorial with R, JAGS, and Stan* (2nd ed.). Academic Press.

McElreath, R. (2020). *Statistical rethinking: A Bayesian course with examples in R and Stan* (2nd ed.). CRC Press.

### MCMC Methodology

Metropolis, N., Rosenbluth, A. W., Rosenbluth, M. N., Teller, A. H., & Teller, E. (1953). Equation of state calculations by fast computing machines. *The Journal of Chemical Physics, 21*(6), 1087–1092. https://doi.org/10.1063/1.1699114

Hastings, W. K. (1970). Monte Carlo sampling methods using Markov chains and their applications. *Biometrika, 57*(1), 97–109. https://doi.org/10.1093/biomet/57.1.97

### Probabilistic Programming

Salvatier, J., Wiecki, T. V., & Fonnesbeck, C. (2016). Probabilistic programming in Python using PyMC3. *PeerJ Computer Science, 2*, e55. https://doi.org/10.7717/peerj-cs.55

---

## Changelog

### 15 June 2026 - Meta AI & DeepSeek
- Added status and pipeline badges
- Updated README with installation, configuration, and roadmap sections
- Clarified data limitations (5 real vs. 15 estimated games)
- Added pipeline architecture diagram
- Improved references formatting

### 7 June 2026 - Claude Sonnet 4.6 Max
- Started draft bibliography

### 6 June 2026 - Initial Version
- Created by Charlie Danoff with assistance from AI tools
- Published initial Bayesian model artifacts for Games 2 and 3

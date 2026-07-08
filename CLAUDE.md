# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a **Claude Code skills repository** for quantitative A-share/HK stock investment analysis. It contains no executable code — only SKILL.md files that define multi-step AI agent workflows. Each skill is an independent analysis pipeline that orchestrates web search, financial data retrieval (via companion skills), and structured report generation.

## Skill Architecture (Three-Layer Hierarchy)

Skills are organized into three layers, executed sequentially:

```
Layer 1: 仓位择时 (Position/Timing)
  └── stock-trading-advice — weekly market timing via 5-module quantitative scoring
Layer 2: 资产配置 (Asset Allocation)
  └── stock-scan-asset-allocation — distribute funds across 4 asset types
Layer 3: 选股 (Stock Selection) — three parallel sub-skills
  ├── stock-scan-subject-driven — theme-driven stock picking (渗透率 ~5%)
  ├── stock-scan-industry-driven — industry chain bottleneck analysis (渗透率 5-30%)
  └── stock-scan-news-driven — event-driven impact analysis
```

### Layer 1: `stock-trading-advice`
Weekly market timing framework. Five independent modules (each scored 0-10), weighted and summed:
- **经济周期与政策** (25%) — PMI, industrial profits, policy stance, domestic demand, exports
- **市场情绪与资金面** (25%) — market breadth/volume (monthly baseline + weekly fine-tuning), multi-source capital flows
- **市场估值** (20%) — PE percentile mean across major indices + ERP (equity risk premium vs 10Y bond)
- **缠论技术分析** (15%) — Chan Theory: 分型→笔→线段→中枢→走势类型, with volume validation
- **历史相似环境** (15%, adjustment term ±2) — historical pattern matching (DTW/visual), macro context comparison

Composite score maps to position: ≥8.5 full, 7.0-8.4 heavy, 5.5-6.9 neutral, 4.0-5.4 light, <4.0 minimal.

### Layer 2: `stock-scan-asset-allocation`
After Layer 1 determines total position size, this skill allocates across four asset types:
- **红利驱动** (dividend-driven, base 30%) — high-dividend defensive positions
- **主题驱动** (theme-driven, base 30%) — early-stage themes, catalyst-based
- **产业驱动** (industry-driven, base 30%) — high-growth industries with verified earnings
- **事件驱动** (event-driven, base 10%) — event catalysts, tactical positions

Base weights are adjusted based on the dominant market style (up to ±20% tilt). Core principle: **在有鱼的地方抓鱼** (fish where the fish are).

### Layer 3 Skills

**`stock-scan-subject-driven`**: Theme-driven analysis. Evaluates policy support, industrialization stage, leader company actions, and technology roadmap. Screens stocks with exclusion criteria (ST, no institutional holders, liquidity < 50M daily).

**`stock-scan-industry-driven`**: The most mathematically rigorous skill. Uses a **以点带面** (point-to-area) approach:
1. Identify the two chain anchors: 产能闸门 (capacity gate) + 需求发动机 (demand engine)
2. Establish layer-by-layer conversion ratios
3. Quantify supply-demand gaps at each layer
4. Build a **bottleneck pyramid** sorted by gap rate × expansion cycle
5. Rank stocks by **bottleneck depth × pricing power matrix** (not market cap)
6. Multi-year forward projection

**`stock-scan-news-driven`**: Event impact analysis through 事件推演 → 市场链接 → 行业映射 → 个股筛选. Classifies events by type (geopolitical, policy, conference, regulatory, corporate) and stage (pre-event anticipation → impact → digestion → normalization).

## Companion Skills (External Dependencies)

All skills depend on these external skills for data — never attempt to replicate their functionality:

- **`ifind-finance-data`** — primary source for individual stock fundamentals, financial data
- **`mx-xuangu`** — AI-powered stock screening and selection
- **`baidu-search`** — web search (pre-configured with API key)
- **知识星球 "思维纪要社"** — institutional research notes, channel checks, industry insights

Data priority: ifind + mx-xuangu first for stock data, baidu-search for macro/news/events, 星球 for sentiment/institutional views.

## Cross-Cutting Conventions

### Report Output Structure
Every skill outputs a structured report with mandatory sections. All reports must:
- Include a summary scoring card/table with key evidence
- Cite data sources specifically (URLs, report document names, 星球 post descriptions)
- Include risk warnings for the forward 1-2 weeks
- Distinguish between consensus views and marginal changes

### Stock Screening Rules (shared across Layer 3 skills)
- **Exclude**: ST stocks, no institutional holders in top 10, daily turnover < 50M CNY
- **Prefer**: multi-tag resonance (stock benefits from multiple themes), large-cap leaders, new entrants from adjacent industries
- **Special attention**: cross-over companies entering a theme from an adjacent strength (e.g., 科达利 002850: lithium battery structure → humanoid robot joint modules)

### Scoring Frameworks
Skills use consistent weighted scoring with 0-10 scales and ≥7 thresholds for recommendations. Adjustment terms are bounded (±2 max) and treated as modifiers, not standalone signals.

## File Organization

```
skills/
  stock-trading-advice/SKILL.md        # Layer 1: weekly market timing
  stock-scan-asset-allocation/SKILL.md # Layer 2: asset allocation
  stock-scan-subject-driven/SKILL.md   # Layer 3: theme-driven stock picking
  stock-scan-industry-driven/SKILL.md  # Layer 3: industry chain analysis
  stock-scan-news-driven/SKILL.md      # Layer 3: event-driven analysis
```

Each skill is a single `SKILL.md` with YAML frontmatter (`name`, `description`). The filename stem is the skill directory name; the frontmatter `name` must match. Skills are invoked by Claude Code's skill system — no build step required.

## Report Output Convention

All analysis reports are written to the `reports/` directory with the naming convention:

```
YYYY-MM-DD-{skill-name}-{分析对象/主题}.md
```

Examples:
- `2026-07-03-stock-trading-advice-市场择时周报.md`
- `2026-07-03-stock-scan-news-driven-关税豁免事件.md`
- `2026-07-03-stock-scan-industry-driven-光模块产业链.md`
- `2026-07-03-stock-scan-subject-driven-人形机器人主题.md`

The suffix should be a concise Chinese description of the analysis target or context, so that multiple runs of the same skill on the same day can coexist without filename conflicts.

## Git History Context

The repo evolved from a single `stock-trading-advice` skill (initial by shixu) through iterative refinement. Key milestones:
- Three-layer architecture established (f4d3f80)
- Industry chain anchor point methodology added (e29b451)
- Asset allocation and event-driven skills rewritten for standardization (0119c73)
- Naming conventions standardized (6eb1548)

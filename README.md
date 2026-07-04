# Industry Account Classifier

> Classifies any company into the correct Snowflake RevOps Industry and Subindustry from a 56-option taxonomy.

A [Cortex Code Desktop](https://docs.snowflake.com/en/user-guide/cortex-code/cortex-code) skill for Michael Lemke's RevOps workflows at Snowflake.

## What It Does

Given a plain-text description of a company, the skill returns the correct Snowflake RevOps `Industry || Subindustry` pair from the full 56-node taxonomy, the name of the responsible Industry Leader, and a confidence note explaining the classification rationale. It is designed for single-account lookups as well as bulk classification runs against a list of companies.

## Business Value

| Metric | Impact |
|--------|--------|
| Time saved | 15–30 min per batch classification run |
| Systems integrated | Snowflake RevOps taxonomy, Salesforce account data |
| Manual steps eliminated | Taxonomy lookup, subindustry disambiguation, Industry Leader routing |

Ensures every account is tagged to the right industry bucket for quota attribution, pipeline analysis, and industry leader routing — eliminating the classification disputes that delay monthly revenue reporting.

## How It Works

1. User provides a company name and/or plain-text description (e.g., "a mid-market healthcare SaaS company focused on revenue cycle management").
2. Skill maps the description against the 56-node Industry × Subindustry taxonomy using LLM classification.
3. Returns `Industry || Subindustry`, the assigned Industry Leader, and a one-line confidence rationale.
4. For batch mode, the skill iterates a list of companies and outputs a classification table.

## Prerequisites

- Cortex Code Desktop (CoCo)
- No external credentials required for single-account classification
- Snowflake connection for batch runs that pull accounts from the account table

## Usage

Invoke by typing the skill name in CoCo, or with natural language triggers listed in the skill description (e.g., "classify this company", "what industry is this account", "tag industry for account").

## Technologies

- **Snowflake Cortex Code Desktop** — AI-powered IDE and skill runtime
- **LLM classification** — reasoning over the 56-node taxonomy using structured prompts
- **56-node taxonomy** — Snowflake's canonical Industry × Subindustry hierarchy for RevOps

## Value Realization

This skill is part of a broader **AI-driven RevOps automation system** that eliminates manual work across the Snowflake Sales Engineering and RevOps teams. Accurate, consistent industry classification is the foundation of Snowflake's quota plans and pipeline analytics — this skill makes that classification instant, repeatable, and audit-ready, preventing the misattribution errors that surface in QBR reconciliation.

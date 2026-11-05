# Project: Model Salary Data with Linear Regression

## Project summary

This project builds and interprets a **linear regression model of tech salaries** from `salary_data.csv` — **375 rows × 6 columns** (`Age`, `Gender`, `Education Level`, `Job Title`, `Years of Experience`, `Salary`). Two rows are blank in all six columns, so `dropna()` leaves **373 complete observations**. `salary_prediction_project.ipynb` answers the full brief in order: **Part I** descriptive statistics (row count, missing-value audit and handling, education-level counts + bar chart, salary `describe()` + histogram), **Part II** group means by gender/education/experience, pre-completed job-title flags, a gender flag, education dummies with Bachelor's as baseline, and the first 10-feature OLS model, and **Part III** removal of every feature with p > 0.05, a refitted reduced model, written interpretations with 95% confidence intervals, a point prediction and its residual, and a residuals-vs-fitted fit assessment. The notebook contains **47 cells (19 code, 28 markdown)**, all 19 code cells execute in order, and a clean `Restart & Run All` pass (`nbconvert --execute`) finishes with **zero errors and zero warnings** and **4 embedded figures**.

Headline figures: salaries range from **$350 to $250,000** (mean **$100,577**, median **$95,000**, SD **$48,240**). Average salary rises with education — PhD **$157,843** > Master's **$129,796** > Bachelor's **$74,756** — and men average **$103,868** (n = 194) versus **$97,011** for women (n = 179). Years of experience correlates with salary at **r = 0.930**. The full model fits well (R² **0.915**) and drops three insignificant title flags — `is_junior` (p = 0.068), `is_analyst` (p = 0.933), `is_engineer` (p = 0.508). The reduced model keeps `const`, `is_male`, `is_director`, `is_senior`, `is_manager`, `Years of Experience`, `is_masters`, `is_phd` (R² **0.9145**, adjusted R² **0.9128**): each extra year of experience is worth **+$5,104** (95% CI **[$4,755.93, $5,452.95]**), a senior title **+$14,446** (95% CI [$10,867, $18,026]), and a PhD **+$23,081** versus a Bachelor's (95% CI **[$17,651.90, $28,509.80]**). A male senior engineer with 5 years of experience and a Bachelor's degree is predicted at **$76,359.11**; against an actual $110,000 the residual is **+$33,640.89**. The residual plot shows a funnel — variance grows with fitted values — so the constant-variance assumption is violated and robust standard errors are recommended. Every number above was re-derived from the raw CSV by an independent implementation (stdlib `csv` + numpy, no pandas, no statsmodels): **109 checks, 0 failures**. The completed 9-page presentation (`model-salary-data-with-linear-regression.pdf`, with `.pptx` source) covers all three areas required by `docs/2. Presentation Components.md` and was cross-checked against the notebook.

## How to run

1. Install the dependencies (Python 3.14.7 verified):

   ```bash
   pip install pandas numpy matplotlib statsmodels
   ```

2. Open and run the notebook, either interactively:

   ```bash
   jupyter notebook salary_prediction_project.ipynb
   ```

   …and use **Kernel → Restart & Run All**, or headlessly (the exact verification used here):

   ```bash
   python -m nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=600 salary_prediction_project.ipynb
   ```

   No network access is needed: the dataset is local. The analysis is deterministic OLS — no simulation or unseeded RNG is used (the pre-filled `random.seed(0)` cell has nothing to randomise), so every run reproduces the same figures.

3. Outputs are written into the notebook itself:
   - education-level bar chart (Part I d), salary histogram (Part I e), experience-vs-salary scatter (Part II c), residuals-vs-fitted plot (Part III f)
   - both `model.summary()` tables, the dropped-feature list, the prediction, the residual, and R² in their respective cells

## Data sources

- **`salary_data.csv`** (local, 19,360 bytes): 375 rows × 6 columns — `Age`, `Gender` (Male/Female), `Education Level` (Bachelor's/Master's/PhD), `Job Title`, `Years of Experience`, `Salary`. Two rows (data rows 173 and 261, 1-based) are **blank in all six fields**; no row is partially missing. After `dropna()` the analysis uses 373 rows.
- Course dataset link (Udacity CDN): <https://video.udacity-data.com/topher/2024/December/6761cf4e_salary_data/salary_data.csv>
- The `Age` column is present but deliberately **excluded** from the model: the brief's feature list (`a`–`e`) does not include it, and it collides with `Years of Experience` (r ≈ 0.92 between them). A salary of **$350** (the minimum) is kept as-is — the brief says to drop only missing rows — but it is flagged as an outlier in the residual discussion.

## Key results (copy into the slide deck)

| Slide | Result |
| :--- | :--- |
| **Data Description** | 375 employees in the file; 2 fully blank rows dropped with `dropna()` → 373 analysed (12 missing cells, all in those 2 rows). Salary range $350–$250,000 (mean $100,577, median $95,000). Correlated features: years of experience (r = 0.930, scatter is strongly positive) and education level (PhD $157,843 > Master's $129,796 > Bachelor's $74,756); men average $103,868 vs women $97,011. |
| **Regression Results** | Full 10-feature OLS: R² 0.915. Significant (p < 0.05): `is_male`, `is_director`, `is_senior`, `is_manager`, `Years of Experience`, `is_masters`, `is_phd`. Removed: `is_junior` (p = 0.068), `is_analyst` (p = 0.933), `is_engineer` (p = 0.508). Reduced model: R² **0.9145**, adjusted R² **0.9128**; residuals-vs-fitted plot shows a funnel (heteroscedasticity), so robust SEs are advised. |
| **Interpretation and Next Steps** | Years of Experience: **+$5,104 per year**, 95% CI **[$4,755.93, $5,452.95]** — we are 95% confident each additional year is associated with a salary increase between ~$4,756 and $5,453. PhD vs Bachelor's: **+$23,081**, 95% CI **[$17,651.90, $28,509.80]**. Point prediction for a male senior engineer, 5 years, Bachelor's: **$76,359.11** (residual vs $110,000 actual: **+$33,640.89**). Next steps: HC3 robust standard errors or `log(Salary)`. |

## Rubric conformance

Reviewed against `docs/3. Rubric.md`, the brief in `docs/1. Project Overview.md`, and the deck requirements in `docs/2. Presentation Components.md`:

| Rubric criterion | Status |
| :--- | :--- |
| **Functionality of code** — all cells run without error | ✅ 19/19 code cells executed in order (counts 1–19); clean `nbconvert --execute` run, **exit 0**, 0 error outputs, 0 warning outputs, 4 embedded figures |
| **Data Preparation** — cleaning and new variables done correctly | ✅ `isnull().sum()` audit → `dropna()` (375 → 373, disclosed); `is_male` flag + `Gender` dropped; job-title flags pre-completed untouched; education dummies with Bachelor's baseline renamed `is_masters`/`is_phd`, original column dropped |
| **Correct Interpretation of Results** — statistical *and* practical reasoning | ✅ III a)–c) state coefficients, 95% CIs, and what they mean in dollars and in practice; III d)–e) explain the prediction and the positive residual; III f) interprets R² and reads the funnel as a homoscedasticity violation with a practical fix |
| **Correct Values for All Statistical Results** | ✅ Independent stdlib-`csv` + numpy re-implementation recomputes every count, mean, correlation, coefficient, p-value, CI, R², prediction, and residual: **109 checks, 0 failures** |
| **Appropriate presentation slideshow** | ✅ Deck completed and exported: **`model-salary-data-with-linear-regression.pdf`** (9 pages, 593,043 bytes) with `model-salary-data-with-linear-regression.pptx` as the 9-slide source — reference slide removed, every red placeholder replaced, candidate name filled. Covers all three areas of `docs/2. Presentation Components.md`: **Data Description** (375 → 373 rows with the 12 missing values, salary range $350–$250,000, experience/education/gender features correlated with salary), **Regression Results** (full-model output with R² 0.915, the three removed flags and their p-values, reduced-model table with all coefficients, p-values and 95% CIs, fit R² 0.914), and **Interpretation of Model Results** (two variables — Years of Experience and PhD — each with the expected dollar change and 95% confidence statement). Cross-checked page-by-page against the notebook: counts (375/373/12, 224/98/51), salary statistics, group means, r = 0.93, all coefficients and p-values match; three confidence-interval endpoints on the reduced-model slide sit within ~$10 of the notebook (deck-side rounding). |
| *Standout:* expanded EDA | ⚠️ Partial — group means, education/gender breakdowns, correlation, and residual diagnostics are in the notebook; a fuller EDA (Age, job-title distributions, interaction terms) was not required by the brief |
| *Standout:* consider all regression assumptions | ⚠️ Partial — homoscedasticity is tested visually and reported (funnel → violated); normality is noted from the summary's Jarque-Bera; multicollinearity (VIF) and formal functional-form tests were not run — recorded as future work in `docs/4. Candidate Notes.md` §10 |

### Review findings (resolved before writing this README)

1. **Part III.1's required markdown cell was missing.** The brief asks for "a markdown cell listing which variables were dropped and why (p > 0.05)"; the first pass only *printed* the dropped list from code. A markdown cell with the three dropped flags, their p-values, and the kept list was added during review, and the notebook was re-executed clean.
2. **Three cells were inserted beyond the starter set** (two code cells for III d)/e), which had no empty code cells to fill, and the markdown cell above). The "do not add cells" instruction and the "add a markdown cell" requirement contradict each other; the minimal insertion was approved and is documented here and in `docs/4. Candidate Notes.md` §6.
3. **nbformat validation warning on inserted cells.** The new cells carried `id` fields while the notebook is nbformat 4.4; the ids were stripped so `nbconvert` validates the file with no complaints.
4. **The workspace `README.md` was stale** — it described a different project (the P3 A/B test). It was replaced by this document.

### Outstanding

Nothing blocks submission: the notebook is executed and verified (19/19 code cells, exit 0, 109/109 independent checks), and both deck formats are present — `model-salary-data-with-linear-regression.pptx` (9 slides) and its `model-salary-data-with-linear-regression.pdf` export.

### Optional future work (not required)

- HC3 robust standard errors, `log(Salary)` as an alternative response, VIF/multicollinearity check, a joint F-test of the three dropped flags.

## Files

- `salary_prediction_project.ipynb` — the completed notebook (47 cells: 19 code, 28 markdown), outputs preserved, 184,296 bytes
- `salary_data.csv` — the dataset (375 × 6, 19,360 bytes, 2 fully blank rows)
- `model-salary-data-with-linear-regression.pptx` — completed presentation deck (9 slides, source)
- `model-salary-data-with-linear-regression.pdf` — PDF export of the deck (593,043 bytes, 9 pages), as required for submission
- `docs/` — `1. Project Overview.md`, `2. Presentation Components.md`, `3. Rubric.md`, `4. Candidate Notes.md`
- `LICENSE.md` — project license

## Submission

Both artifacts are graded: the notebook's code/results and the presentation's figures/interpretation (see `docs/3. Rubric.md`). Prepare a zip containing:

1. `salary_prediction_project.ipynb` — the executed notebook (all outputs present).
2. `model-salary-data-with-linear-regression.pdf` — the completed presentation (9 pages).

# Notes

by $\mathbb{JX}$

Misc notes:

* Ridge: $ \beta_{ridge} = (X'X + \lambda I)^{-1} X'y$
* Lasso: $(y-X\beta)'(y-X\beta) + \lambda \sum_{j=1}^{p}|\beta_j|$
  * Use Lasso to discard some irrelevant features
  * Apparently, Lasso is very sensitive to units - so use a standard scaler.
  * If the predictors are highly correlated (multicollinearity), then
* Multicollinearity trap: where features A and B are highly correlated, Lasso will arbitrarily pick one to discard.
* If we wanna drop features but features could be highly correlated: consider  Elastic Net - combine Lasso and Ridge: Penalty $= \lambda_1 \sum |\beta_j| + \beta_2 \sum \beta^2_j$

Workflow: data cleaning + feature selection + preprocessing (standard scaling) + Lasso / Elastic Net --> final model.

Thoughts

* "Location, location, location" - but how to encode this within a model? We do have neighbourhoods `Neighborhood` - maybe we can extract some information there?
* `OverallQual` and `OverallCond` - are these subjective?

## Forward selection (partial correlations)

Goal: given the regressors already chosen (start: `GrLivArea`), find the next one that adds the most *new* information about $y$ (here $\log$ `SalePrice`).

Each round:

1. Residualise $y$ on the chosen set $S$ (OLS with a constant) → $\tilde{e}$ = the part of $y$ that $S$ doesn't explain.
2. Residualise each candidate $x_j$ on $S$ → $\tilde{x}_j$ = the part of $x_j$ that $S$ can't predict (e.g. $\tilde{x}_j > 0$ for `GarageArea` = bigger garage than expected for the house's size).
3. Partial correlation $r_j = \text{corr}(\tilde{x}_j, \tilde{e})$. Add the candidate with the largest $|r_j|$ to $S$; repeat.

Why it works:

* FWL / partialling out: regressing $\tilde{e}$ on $\tilde{x}_j$ gives exactly $x_j$'s coefficient in the multiple regression of $y$ on $S \cup \{x_j\}$.
* Ranking by $|r_j|$ = ranking by the $|t|$-stat $x_j$ would get if added to the OLS ($t^2 = (n-k)\,r^2/(1-r^2)$, monotone in $|r|$). So "largest partial correlation" = "most significant addition" (checked on the data: same picks).
* The picked features are **not** uncorrelated with $S$ - only their leftover part counts. Near-duplicates (e.g. `TotRmsAbvGrd` given `GrLivArea`) score low because little is left after partialling out.

Collinearity filter (optional):

* The VIF of candidate $x_j$ against the chosen set is $\text{VIF}_j = 1/(1-R^2_j)$, where $R^2_j$ is from regressing $x_j$ on $S$. Equivalently, $\text{VIF}_j = \text{Var}(x_j)/\text{Var}(\tilde{x}_j)$: it falls straight out of step 2.
* Skip candidates with $\text{VIF}_j >$ a threshold (rule of thumb: 5 or 10), even if they rank highest.
* In practice it rarely binds. A collinear candidate has a small $\tilde{x}_j$, so it tends to have a low $|r_j|$ anyway. With threshold 5, nothing was skipped in the first 4 rounds (picks had VIF ≤ 1.62).
* `OverallQual` vs `OverallCond`: corr $= -0.09$, so they're **not** collinear.

Why not PCA on the residualised features instead (my first idea):

* PCA ignores $y$: PC1 = direction of most variance among the features, not the one that best explains $\tilde{e}$.
* A PC is a blend of all features → not a named regressor; kills interpretability / the OLS vs Ridge vs Lasso coefficient comparison.
* PCs are already mutually orthogonal, so iterating = just taking the top-$k$ PCs. Also scale-dependent (needs standardising). And PCA isn't in the course.

Caveats:

* Numeric columns only; categoricals need encoding first.
* `dropna()` keeps 1,121 / 1,460 rows (mostly `LotFrontage` NaNs) - rerun on the cleaned data.
* Choosing features by looking at the training data overfits → decide how many to keep on validation error, not on partial correlations. Lasso does a similar selection automatically.
* Orthogonal regressors aren't needed for OLS anyway - multiple regression already credits each regressor only for its own part (FWL); collinearity just makes coefficients unstable (→ Ridge).

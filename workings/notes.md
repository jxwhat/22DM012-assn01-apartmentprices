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

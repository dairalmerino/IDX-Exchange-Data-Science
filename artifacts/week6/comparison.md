# Week 6 — Feature Engineering

Expanding chronological February/March 2026 validation; April excluded; equal-month summaries.

| model | feature_set | school_district_layer | mean_monthly_mae_usd | mean_monthly_rmse_usd | mean_monthly_r2 | mean_mae_improvement_usd_vs_old |
| --- | --- | --- | --- | --- | --- | --- |
| Linear Regression | Old: original features | False | 356715.6141 | 781068.86 | 0.6882 | 0.0 |
| Linear Regression | Selected property features | False | 325003.7548 | 764817.4112 | 0.7004 | 31711.8594 |
| Linear Regression | Selected property + districts | True | 336749.3634 | 779527.3218 | 0.686 | 19966.2507 |
| Decision Tree | Old: original features | False | 345063.2701 | 973345.2852 | 0.5073 | 0.0 |
| Decision Tree | Selected property features | False | 346478.3243 | 986484.0633 | 0.4977 | -1415.0542 |
| Decision Tree | Selected property + districts | True | 346691.3259 | 993565.2794 | 0.4932 | -1628.0559 |
| Random Forest | Old: original features | False | 260428.7866 | 771147.8038 | 0.694 | 0.0 |
| Random Forest | Selected property features | False | 259325.0471 | 767505.6861 | 0.6967 | 1103.7395 |
| Random Forest | Selected property + districts | True | 254289.9691 | 754000.1123 | 0.7078 | 6138.8175 |

- Linear Regression: districts changed mean monthly MAE by $11,745.61 (negative means lower error); improved 0/2 months. Districts did not lower MAE in both months; retain the property reference pending stronger evidence.
- Decision Tree: districts changed mean monthly MAE by $213.00 (negative means lower error); improved 1/2 months. Districts did not lower MAE in both months; retain the property reference pending stronger evidence.
- Random Forest: districts changed mean monthly MAE by $-5,035.08 (negative means lower error); improved 2/2 months. Districts lowered MAE in both months; carry forward provisionally after checking RMSE and R².

Limitations:
- Exploratory comparisons reuse historical validation months; April was previously inspected.
- 2024-25 boundaries applied to 2026 sales; temporal boundary availability is not established.
- Repaired geometry, cross-type overlaps, sparse and unseen categories require care.
- Training uses sold single-family properties; results do not establish accuracy for every off-market address.


## Week 6 — Final findings and Week 7 handoff

Week 6 is complete in the saved run: property features were engineered, the 2024–25 school-district layer was joined and audited, models were retrained, and old-versus-new comparisons were exported to `artifacts/week6/`.

| Model / feature set | Mean monthly MAE | Mean monthly RMSE | Mean monthly R² |
| --- | ---: | ---: | ---: |
| Linear Regression — original | $356,716 | $781,069 | 0.6882 |
| Linear Regression — selected property features | **$325,004** | **$764,817** | **0.7004** |
| Linear Regression — selected + districts | $336,749 | $779,527 | 0.6860 |
| Decision Tree — original | **$345,063** | **$973,345** | **0.5073** |
| Decision Tree — selected property features | $346,478 | $986,484 | 0.4977 |
| Decision Tree — selected + districts | $346,691 | $993,565 | 0.4932 |
| Random Forest — original | $260,429 | $771,148 | 0.6940 |
| Random Forest — selected property features | $259,325 | $767,506 | 0.6967 |
| Random Forest — selected + districts | **$254,290** | **$754,000** | **0.7078** |

All values are equal-weight averages of February and March chronological validation scores, not April test scores. Bold highlights the best measured configuration within each model in this final comparison. The unrestricted Decision Tree is the fixed feature-comparison reference; earlier leaf=5 results remain separate.

**Property engineering:** Log-size features consistently helped Linear Regression. Retain its reduced reference plus LogLivingArea and LogLotSize. Ratios added little beyond logs, and the tested property additions did not improve Random Forest. PropertyAge duplicates YearBuilt for the 2026 observations.

**School districts:** Random Forest MAE improved over the selected property reference by **$6,633 (2.45%) in February** and **$3,437 (1.39%) in March**. Its mean monthly improvement was **$5,035 (1.94%)** versus that reference and **$6,139 (2.36%)** versus original features. RMSE decreased and R² increased in both months. Carry the district-enhanced Random Forest forward provisionally as the Week 7 benchmark. Districts increased Linear Regression MAE in both months; Decision Tree results were mixed. Retain the best original Decision Tree configuration in this fixed comparison rather than recommending ParkingTotal removal for every model.

**Geographic quality:** The join retained all 27,974 training properties and assigned at least one district to 99.5%. Seven invalid boundary geometries were repaired. Keep the audit and note the 122 cross-type overlaps, sparse categories, unseen validation codes, and use of 2024–25 boundaries for 2026 sales. A high match rate does not establish boundary or coordinate correctness for every property.

**Week 7 plan:** `05_advanced_models.ipynb` uses the same cleaned split export and cached boundary snapshot in a fresh kernel. Hold the 14 Random Forest predictors fixed: nine numeric property/location fields, City, PostalCode, and three district codes. Compare a fixed XGBoost starter and a small grid over max_depth, learning_rate, and n_estimators. Select by equal-month February/March validation MAE, refit on January–March, and compute April MAE, RMSE, and R² beside a freshly fitted 100-tree Random Forest using identical rows and features. Do not tune against April results.

**Evaluation limits:** Feature and model choices reuse historical validation months. April was inspected in earlier weeks; Week 7 April scores are historical test comparisons, not a newly untouched estimate. A future uninspected month is needed for a stronger final performance claim. No Week 6 district-enhanced April result has been measured here. Results on sold single-family homes do not establish accuracy for every off-market address.

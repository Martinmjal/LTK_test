# E-Commerce Return Prediction: Executive Summary

## Approach Overview

This project developed and evaluated machine learning models to predict product returns in an e-commerce context, with the goal of enabling cost-effective preventive interventions. The work followed a rigorous data science methodology across four phases:

**Phase 1: Baseline Evaluation** - Established performance metrics for an initial logistic regression model trained on 9 features from 8,000 historical orders. The baseline model exhibited severe majority-class bias, predicting zero returns despite a 25% actual return rate, achieving 0% recall and precision but 75% accuracy due to class imbalance.

**Phase 2: Business-Aligned Metrics** - Translated ML performance into financial impact using realistic cost assumptions ($18 per return, $3 per intervention, 35% intervention effectiveness). Developed net savings as the primary success metric and performed threshold optimization analysis. Critically established that accuracy is misleading for this imbalanced problem and that false negatives cost 6× more than false positives.

**Phase 3: Systematic Improvement** - Executed three controlled experiments: (1) class weight balancing to address imbalance, (2) domain-driven feature engineering creating 10 new interaction and behavioral features, and (3) advanced algorithms (Random Forest and Gradient Boosting). Each experiment was validated using 5-fold cross-validation and train-test comparison to detect overfitting. Feature engineering added customer risk scores, price-discount interactions, purchase frequency metrics, and product quality ratios.

**Phase 4: Deployment Planning** - Designed comprehensive production monitoring framework including real-time financial tracking, drift detection (PSI, KS-tests), seasonal pattern analysis, alert configurations, automated rollback criteria, and A/B testing strategy with four-phase rollout (shadow mode → 10% → 50% → 100% traffic).

## Key Findings

The analysis produced both technical insights and critical business recommendations:

**Model Performance Reality**: All improved models generate **negative net savings** despite better ML metrics. The best-performing model (Gradient Boosting) achieves 44.6% recall and 33.8% precision, identifying 225 of 505 returns but incurring $1,323 in wasted interventions versus $1,418 in savings from prevented returns. The resulting -$580.50 net loss makes the baseline "do nothing" strategy financially superior at $0.00 net cost.

**Root Cause Diagnosis**: Current features lack sufficient predictive signal, evidenced by ROC-AUC scores barely exceeding 0.60 (vs. 0.70+ target). Severe overfitting indicates models memorize training noise rather than learning generalizable patterns—Random Forest exhibits 0.84 train AUC vs. 0.60 test AUC (35% degradation), while Gradient Boosting shows 0.94 vs. 0.59. The 33.8% precision threshold falls below the 45% minimum required for positive ROI given the 6:1 cost ratio between false negatives and false positives.

**What Worked**: Class weight balancing successfully enabled return prediction (recall improved from 0% to 48.5%), demonstrating the model can learn when properly incentivized. Feature engineering provided marginal gains (+$58), with engineered features like customer risk scores and price-quality ratios appearing in top importance rankings. Cross-validation scores remained stable (0.60 ± 0.03), indicating the performance issues stem from insufficient signal rather than evaluation methodology flaws.

**What Failed**: Threshold optimization revealed no profitable operating point exists—the optimal threshold of 0.71 yields only $3.30 savings by predicting a single return. Neither regularization nor ensemble methods could overcome the fundamental data limitation. The Fashion category with 31% return rate and 346 false negatives highlights that even high-return segments cannot be reliably identified with current features.

## Business Impact Estimate

**Current State**: Deploying any improved model would result in **$87,000 annual loss** at 300,000 orders per year ($580.50 loss per 2,000 orders). The business currently processes 505 returns per 2,000 orders at $18 each, totaling $9,090 in unavoidable return costs. Interventions add $1,323 in wasted spend while saving only $1,418 from prevented returns, creating net negative value.

**Target State (Post-Improvement)**: With enhanced features achieving 60% recall and 45% precision, the model could generate **$121,350 in annual savings**. This assumes 303 correctly identified returns (net benefit $1,909), 370 false positives (waste $1,110), and 202 missed returns (cost $3,636), resulting in $809 net savings per 2,000 orders. The 8.9% ROI would achieve a 3-month payback period once deployed.

**Required Investment**: Estimated 2-3 months for data collection infrastructure and feature development. Key missing data includes customer browsing behavior (page views, time on site), product-level attributes (fit accuracy, size chart quality, SKU-level return rates), temporal patterns (seasonality, day-of-week effects), and external context (purchase channel, marketing source). Development costs are justified only if feature improvements can lift ROC-AUC from 0.59 to 0.70+, enabling the precision breakthrough from 33.8% to 45%+.

## Deployment Recommendations

**Immediate Decision: DO NOT DEPLOY** - All evaluation criteria failed: net savings -$580 (target: >$500), recall 44.6% (target: ≥60%), precision 33.8% (target: ≥40%). Deployment would harm financial performance and waste operational resources on 1,323 unnecessary interventions per 2,000 orders. Customer experience risk exists if recipients perceive interventions as intrusive despite not having return intent.

**Phase 1 - Data Enhancement (Months 1-3)**: Partner with product engineering to instrument behavioral tracking (clickstream, video views, review engagement). Coordinate with warehouse operations to capture product-specific return reasons and size fit data. Integrate customer service system for historical issue patterns. Validate new features show >0.10 correlation with return outcomes before model retraining.

**Phase 2 - Model Redevelopment (Month 4)**: Retrain with enriched dataset targeting ROC-AUC >0.70 and cross-validated performance within 5% of test set (eliminating overfitting). Consider deep learning with embeddings if feature space expands significantly (>50 features). Implement cost-sensitive learning with custom loss functions weighted 6:1 for FN:FP. Validate improved model against strict gates: precision ≥45%, recall ≥60%, net savings >$500 per 2,000 orders.

**Phase 3 - Controlled Rollout (Months 5-6)**: Execute four-phase A/B testing: (1) Shadow mode for 2 weeks collecting ground truth without interventions, (2) 10% traffic test requiring p<0.05 significance and $200+ savings vs. control, (3) 50% scale-up confirming performance holds, (4) Full deployment with ongoing monitoring. Implement automatic rollback if net savings turn negative for 3 consecutive days. Start with Electronics category only (17% return rate, lowest risk) before expanding to Fashion (31% return rate, highest complexity).

**Alternative Strategy**: If data enhancement cannot achieve required performance within 6 months, pivot to pre-purchase prevention (improved size recommendations, better product imagery, enhanced reviews) rather than post-purchase interventions. Consider segment-specific models with graduated intervention intensity, applying interventions only to predictions exceeding 80% confidence threshold even if overall recall suffers. The current work establishes the analytical framework and monitoring infrastructure for any future ML-driven return optimization initiative.

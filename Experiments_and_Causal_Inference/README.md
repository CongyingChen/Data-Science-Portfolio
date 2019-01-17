#  Effect of Meatless Day on Overall Intake

## Does “Meatless one day of week” reduce total meat consumption for that week?

Charlene Chen, Bethany Faughn, and Arvindh Ganesan

The project is to implement the experiment to exam what is the aggregate change in meat consumption when subjects participate in these campaigns?

![](https://github.com/CongyingChen/Data-Science-Portfolio/blob/master/Experiments_and_Causal_Inference/code/attr1.png)

![](https://github.com/CongyingChen/Data-Science-Portfolio/blob/master/Experiments_and_Causal_Inference/code/Flow_Diagram.png)

When clustered by subjects' ID and taking covariates into account, the regression indicates that the best estimate is that those subjects reduce overall meat consumption by an average of 0.39 ounces each day of the week they are in treatment. However, the 95% confidence interval on this effect is quit large, indicating that the experiment do not have the statistical power to determine whether an effect of this size is simply due to random variation.

The statistical power of the test is only 8%, indicating a very underpowered test for the amount of variance in the data. Around 9000 of the observations that would be required to have 80% power test of that effect size. However, a calculation of statistical power indicates that had meat consumption dropped by one-seventh of the weekly average, it would been statistically significant.

These calculations indicate that participating in a meatless day one week does not reduce overall average consumption by as much as estimated from campaign materials.

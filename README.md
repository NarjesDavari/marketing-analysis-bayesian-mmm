# marketing-analysis-Bayesian-mmm
Build, visualize, and evaluate the Bayesian MMM using the latest version of PyMC.

This report presents the findings of a Bayesian Mixed-Media Model (MMM) analysis conducted for Company X's online shop. Using advanced statistical techniques leveraging the PyMC framework, we've evaluated the effectiveness of seven distinct marketing channels to optimize advertising spend.

Some finding from Analysis of raw Revenue and Channel Spending data:
1.	Spending Patterns: There's significant variation in spend across the seven channels. Channel 7 consistently receives the highest budget allocation. Channels 3 and 5 also receive substantial investment. These channels likely have lower decay parameters (0.3-0.5) indicating more immediate effects on revenue. Channels 1, 2, 4, and 6 receive smaller budget allocations
2.	Seasonality: Clear spending peaks occur around October-November and April-May each year. These likely align with seasonal promotions or holiday campaigns. Summer periods (July-August) typically show reduced spending. Channel 3 and Channel 7 show corresponding spending increases.

![image](https://github.com/user-attachments/assets/efca7438-6f37-4408-9a39-7f9f6d6acc38)

1.	How do you model spend carry over?
To model spend carryover effects, the approach incorporates several advanced techniques tailored to each channel's characteristics. Specifically, I employed channel-specific adstock models, assigning individual decay rates (λ) and lag parameters to capture the distinct temporal impact of marketing efforts. 
Channel 1: Likely a traditional offline medium (e.g., newspaper or magazine).
Modelled using Geometric adstock with a fast decay rate (λ = 0.3–0.4), assuming short-lived effects where most impact occurs within a week. The model captures short-term influence only, with minimal carryover.
Channel 2: Possibly radio or small-scale supplementary advertising.
Also modelled with Geometric adstock and a fast decay rate (λ = 0.2–0.4). This channel exhibits immediate effects, reflecting quick audience responses and rapid diminishing returns.
Channel 3: Likely a major performance channel such as search advertising.
Modelled using Geometric adstock with a medium-high decay rate (λ = 0.6–0.8). Strong correlation with revenue peaks indicates this is a key revenue driver. The model captures both immediate impact and moderate carryover.
Channel 4: Possibly display advertising or a consistent offline medium.
Modelled with Geometric adstock and a medium decay rate (λ = 0.5–0.7). This channel reflects a balanced response, incorporating both immediate and carryover effects.
Channel 5: Represents brand or upper-funnel digital advertising.
Modelled with a decreasing geometric adstock, starting with a high initial impact followed by quick decay. A custom decay curve was implemented based on channel-specific behaviour, with a lag of one week. It shows a delayed peak with a long tail, typical of brand awareness campaigns.
Channel 6: A campaign-based channel with sporadic activity.
Modelled using a delayed response or zero-inflated adstock model to handle inactive periods and intermittent spikes. A 2-week lag accounts for delayed revenue attribution. The impact pattern is a delayed peak followed by rapid decay.
Channel 7: Likely a primary digital performance marketing channel.
Modelled using a Weibull adstock function to capture multi-phase impact — combining both immediate and medium-term effects. It uses higher decay rates (λ = 0.6–0.8) with no lag, assuming immediate conversion response.

2.	Explain your choice of prior inputs to the model?
The prior inputs are informed by channel characteristics:
1.	Channel-Specific Decay Priors: 
Channels 1-2: Beta (3,9) and Beta (2,8) priors favouring lower decay values (0.2-0.4), suggesting quick diminishing returns
Channels 3-4: Beta (6,3) and Beta(5,3) priors favouring higher decay values (0.5-0.8), indicating sustained effects
Channels 5-7: Various priors tuned to their expected behaviour patterns
2.	Adstock Type Selection: 
Geometric adstock for channels 1,2,3,4 with simpler decay patterns
Weibull adstock for channel 7 to capture multi-phase effects
Delayed adstock for channel 6 which has significant lag
Flexible decay for channel 5 to model time-varying effectiveness
3.	Lag Parameters: 
Channels 1-4,7: No lag (immediate effects)
Channel 5: 1-week lag
Channel 6: 2-week lag (longest delay)
4.	Other Priors: 
Channel coefficients: HalfNormal(1) ensures positive effects
Trend coefficient: Normal(0,1) allows for bidirectional trends
Seasonality coefficients: Normal(0,0.5) for smaller seasonal variations
Error term (sigma): HalfNormal(1) for positive variance

3.	How are your model results based on prior sampling vs. posterior sampling?
Channel Decay Rates :
1.	Overall Pattern: For most channels, the posterior distributions (red) are narrower than the prior distributions (blue), indicating reduced uncertainty after seeing the data.
2.	Channel-Specific Observations: Channels 3 and 5 showed the most notable changes in decay rates, indicating that the data contained evidence that contradicted prior assumptions for these channels. The changes in decay rates suggest different persistence effects for marketing activities than initially assumed, which would impact marketing mix recommendations and budget allocation decisions.
Channels 1 and 2: Show slight shifts between prior and posterior means, but with significant overlap in distributions.
Channel 3: The posterior mean (0.60) shifted left from the prior mean (0.67), with a much narrower posterior distribution.
Channels 4 and 6: Show moderate shifts from prior to posterior means (0.63→0.55 and 0.50→0.51 respectively).
Channel 5: Shows a notable shift from prior mean (0.63) to posterior mean (0.69) with the posterior distribution moving rightward.
Channel 7: Shows minimal change in mean values (0.67→0.65) but with reduced variance in the posterior.

![image](https://github.com/user-attachments/assets/fd9f7510-4032-4d13-b299-6b3ec468b752)

Model Parameter:
1.	Intercept: Dramatic change from prior (centered at 0.00) to posterior (centered at 0.36), with the posterior distribution being much narrower, indicating strong information gain.
2.	Trend Coefficient: Substantial shift from prior mean (-0.01) to posterior mean (-0.78), suggesting a significant negative trend that wasn't captured in the prior. The posterior distribution is much narrower, showing high certainty about this negative trend.
3.	Sigma (Error Term): The posterior distribution is extremely narrow compared to the prior, with means of 0.81 (prior) and 0.75 (posterior). This indicates the model found a very specific error variance that fits the data well. 

![image](https://github.com/user-attachments/assets/c5137133-018a-479f-9ba8-fee50fb4871d)

4.	How good is your model performing? How do you measure it?
 I use several standard metrics like RMSE, MAE, MAPE, and R² to quantify how well our model's point predictions (posterior means) match the observed revenue data. The value of R² shows the model explains about 50% of the variance in revenue.
The obtained performance metrics are:
Relative MAE	 0.18
Normalized RMSE	 0.25
R²	 0.536
MAPE	 18.57%

As a Bayesian model, we can also leverage additional performance measures that account for the full posterior distribution, such as LOO-CV, WAIC, and R-hat. We can do more investigations, such as Cross-Validation, Sensitivity Analysis. Since the  length data is small, I could not implement a  cross-validation approach, and it suffers from consistently performance analysis.

5.	What are your main insights in terms of channel performance/effects?
The Channel effectiveness chart displays standardized coefficients with confidence intervals for each channel. Here are the main insights:
•	Channel 3 and Channel 5 are the most effective marketing channels, with standardized coefficients of approximately 0.3. These channels yield the highest return on investment and should be prioritized in the marketing budget.
•	Channel 7 shows moderate effectiveness with a coefficient around 0.22, making it the third most impactful channel.
•	Channels 2 and 6 have similar effectiveness levels with coefficients between 0.15-0.18, indicating they provide positive but more modest returns.
•	Channels 4 and 1 demonstrate the lowest effectiveness with coefficients below 0.1, suggesting they contribute minimally to sales compared to other channels.
The width of the confidence intervals (horizontal lines) indicates uncertainty in the estimates: 
•	Channel 7 has the widest confidence interval, suggesting high variability in its effectiveness
•	Channels 3 and 5, despite being most effective, also show considerable uncertainty
•	Channel 1 has a relatively narrow confidence interval, providing more confidence in its (low) effectiveness estimate

![image](https://github.com/user-attachments/assets/2c3feb7e-ac58-4f77-be93-0ddd6d0c4d89)

For marketing budget allocation, I would recommend:
•	Increasing investment in Channels 3 and 5
•	Maintaining moderate investment in Channel 7 while monitoring its performance
•	Potentially reducing spend on the least effective Channels 1 and 4
These insights would be even more valuable when combined with additional model outputs like saturation effects, seasonality patterns, and cost efficiency metrics.


6.	Can you derive ROI (return on investment) estimates per channel? What is the best channel in terms of ROI?
Looking at the ROI chart, there's significant variation in channel effectiveness:
•	Channel 5 delivers the highest ROI at approximately 48,393
•	Channel 3 follows with strong performance at around 41,893
•	Channel 7 shows moderate effectiveness with an ROI of about 31,638
•	Channels 6, 2, 4, and 1 demonstrate substantially lower ROI values (below 15,000)

![image](https://github.com/user-attachments/assets/c0619240-357b-4503-8304-0fe6be2482f1)

Conclusion: 
Based on these findings, we recommend the following strategic adjustments:
-	Increase investment in Channels 3 and 5 by 15-20% to capitalize on their high effectiveness. Reduce spending in Channels 1 and 4 by 30-40%, reallocating these funds to higher-performing channels. Optimize Channel 7 spending to operate below the diminishing returns threshold.
-	Stagger investments across channels with complementary adstock profiles to maintain consistent revenue flow. Enhance spending in Channels 3 and 5 prior to seasonal peaks to maximize their carryover effects.
-	Implement controlled experiments to validate potential synergies between Channels 3 and 5. Test varied spending levels in Channel 7 to pinpoint its optimal investment point. Establish ongoing monitoring of channel effectiveness using this MMM as a baseline. Update the model quarterly to capture evolving market dynamics and consumer behaviour.






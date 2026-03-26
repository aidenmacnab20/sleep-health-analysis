# Analyzing the Demographic and Lifestyle Factors Associated with Sleep Quality


- **Author**: Aiden Mac Nab 
- **Date Created**: 03/15/2026
- **Dataset**: Sleep Health and Lifestyle Dataset
    - Provided by Laksika Tharmalingam


## Objective  
The goal of this project is to identify which demographic and lifestyle attributes are most associated with higher sleep quality and Stress Levels. By analyzing factors such as age, gender, occupation, physical activity, stress levels, and BMI category, this analysis aims to highlight patterns that may contribute to better or worse sleep outcomes.

Understanding these relationships can help individuals become more aware of the lifestyle and demographic factors that may influence overall sleep health.

## Dataset Overview 
This dataset contains a variety of key attributes that fall under the category of Demographics, Lifestyle, Sleep Metrics, and indicators of Health Status.

### `sleep_health` Table Fields:

#### Demographics
- **age** - The age of the person in years.
- **gender** - The gender of the person (Male/Female). 
- **occupation** - The occupation or profession of the person.

#### Lifestyle
- **physical_activity_level** -The number of minutes the person engages in physical activity daily.
- **stress_level** - A subjective rating of the stress level experienced by the person, ranging from 1 to 10.
- **bmi_category** - The BMI category of the person (ex. Underweight, Normal Weight, Overweight).
	- We can assume "Normal Weight" is what is considered Healthy weight standard.
- **daily_steps** - The number of steps the person takes per day.

#### Sleep Metrics
- **sleep_duration** - The number of hours the person sleeps per day
- **quality_of_sleep** - A subjective rating of the quality of sleep, ranging from 1 to 10.
- **sleep_disorder** - The presence or absence of a sleep disorder in the person (ex. None, Insomnia, Sleep Apnea).

#### Health Indicators
- **heart_rate** - The resting heart rate of the person in beats per minute
- **blood_pressure** - The blood pressure measurement of the person, indicated as systolic pressure over diastolic pressure.


## Table Cleaning Steps
- Cleaning Field names to prevent redundant quoting with queries and setting to snake_case for DB Convention standards.

<details>
<summary>Column Renaming SQL</summary>

```sql

ALTER TABLE public.sleep_health
RENAME COLUMN "Quality of Sleep" TO quality_of_sleep;

ALTER TABLE public.sleep_health
RENAME COLUMN "Person ID" TO person_id;

ALTER TABLE public.sleep_health
RENAME COLUMN "Sleep Duration" TO sleep_duration;

ALTER TABLE public.sleep_health
RENAME COLUMN "Physical Activity Level" TO physical_activity_level;

ALTER TABLE public.sleep_health
RENAME COLUMN "Stress Level" TO stress_level;

ALTER TABLE public.sleep_health
RENAME COLUMN "BMI Category" TO bmi_category;

ALTER TABLE public.sleep_health
RENAME COLUMN "Blood Pressure" TO blood_pressure;

ALTER TABLE public.sleep_health
RENAME COLUMN "Heart Rate" TO heart_rate;

ALTER TABLE public.sleep_health
RENAME COLUMN "Daily Steps" TO daily_steps;

ALTER TABLE public.sleep_health
RENAME COLUMN "Sleep Disorder" TO sleep_disorder;

```

</details>
<br>

- Normalizing `bmi_category` values of "Normal" to **"Normal Weight"** for data consistency

<details>
<summary>Normalizing Data Values</summary>

```sql

UPDATE sleep_health
SET bmi_category = 'Normal Weight'
WHERE bmi_category = 'Normal'


```

</details>
<br>

## Data Analysis

### Age vs Sleep Quality

In the Sleep Health and Lifestyle Dataset, individuals in their **20s** reported the lowest average `quality_of_sleep` score at `5.79`, while individuals in their **50s** reported the highest average score at `8.17`.

<details>
<summary>Quality of Sleep by Age Logic</summary>

```sql

-- Step 1: Create age groups
WITH age_grouped AS (
	SELECT
		CASE
			WHEN age BETWEEN 20 AND 29 THEN '20s'
			WHEN age BETWEEN 30 AND 39 THEN '30s'
			WHEN age BETWEEN 40 AND 49 THEN '40s'
			WHEN age BETWEEN 50 AND 59 THEN '50s'
			ELSE 'other'
		END AS age_group,
		quality_of_sleep
	FROM sleep_health
)
-- Step 2: Calculate sleep statistics by age group
SELECT
	age_group,
	COUNT(*) AS sample_size,
	ROUND(AVG(quality_of_sleep), 2) AS avg_sleep_quality,
	ROUND(STDDEV(quality_of_sleep), 2) AS sleep_variability -- Shows how spread out sleep quality scores are within each age group
FROM age_grouped
GROUP BY age_group
ORDER BY age_group;

```

</details>
<br>

**Note:** The sample size for individuals in their 20s is significantly smaller than the other age groups. However, the sleep variability (standard deviation) across groups is relatively similar, suggesting the observed difference in average sleep quality is not solely due to unusually inconsistent responses within the smaller sample.

![Sleep Quality by Age Group](Images/Age%20_%20Sleep%20Quality%20Graph.png)

### Gender vs Sleep Quality

In the Sleep Health and Lifestyle Dataset, **Male** individuals reported a lower average `quality_of_sleep` score at `6.97`, while **Female** individuals reported an average score at `7.66`.

<details>
<summary>Quality of Sleep by Gender Logic</summary>

```sql

SELECT
	gender,
	COUNT(*) AS sample_size,
	ROUND(AVG(quality_of_sleep), 2) AS avg_sleep_quality,
	ROUND(STDDEV(quality_of_sleep), 2) AS sleep_variability -- Shows how spread out sleep quality scores are within each gender group
FROM sleep_health
GROUP BY gender
ORDER BY gender;

```
</details>
<br>

**Note:** The variability of sleep quality scores within each gender group exceeds 1, indicating a moderate spread in responses. While the averages suggest that females report slightly better sleep quality overall, individual sleep experiences still vary within each group.


### Occupation vs Sleep Quality

In the Sleep Health and Lifestyle Dataset, individuals working as **Sales Representatives** reported the lowest average `quality_of_sleep` score at **4.00**. However, this result may be influenced by the very small sample size for that occupation.

When limiting the analysis to occupations with more than **10 respondents**, **Salesperson** emerges as the occupation with the lowest average sleep quality at **6.00**. In contrast, individuals working as **Engineers** reported the highest average `quality_of_sleep` score at **8.41**.

<details>
<summary>Quality of Sleep by Occupation Logic</summary>

```sql

SELECT
	occupation,
	COUNT(*) AS sample_size,
	ROUND(AVG(quality_of_sleep), 2) AS avg_sleep_quality,
	ROUND(STDDEV(quality_of_sleep), 2) AS sleep_variability -- Shows how spread out sleep quality scores are within each occcupation group
FROM sleep_health
GROUP BY occupation
ORDER BY avg_sleep_quality desc;

```

</details>
<br>

### Stress Level vs Sleep Quality

Stress level shows a clear relationship with `quality_of_sleep`, with sleep quality gradually declining as stress increases.

Individuals reporting low stress levels experienced significantly higher sleep quality scores, averaging `8.97`, compared to those reporting high stress levels, who averaged `5.86`. This suggests that elevated stress may have a substantial negative impact on sleep quality.

Additionally, individuals with lower stress levels exhibited less variability in their sleep quality scores, indicating more consistent sleep experiences across this group. In contrast, higher stress levels were associated with greater variability, suggesting that sleep quality becomes less predictable as stress increases.

<details>
<summary>Quality of Sleep by Stress Levels Logic</summary>

```sql


SELECT
	stress_level,
	COUNT(*) AS sample_size,
	ROUND(AVG(quality_of_sleep), 2) AS avg_sleep_quality,
	ROUND(STDDEV(quality_of_sleep), 2) AS sleep_variability -- Shows how spread out sleep quality scores are within each stress level group
FROM sleep_health
GROUP BY stress_level
ORDER BY stress_level;

```

</details>
<br>

![Sleep Quality Declines as Stress Increases](Images/Stress%20_%20Sleep%20Quality%20Chart.png)

### BMI Category and Sleeping Disorder Relationship

In the Sleep Health and Lifestyle Dataset, individuals are categorized into three BMI groups: Normal Weight, Overweight, and Obese.

The data reveals a strong relationship between BMI category and the likelihood of experiencing a sleep disorder.

Individuals classified as Obese reported a sleep disorder in 100% of cases, though this result should be interpreted cautiously given the small sample size of only 10 respondents.
Overweight individuals showed the most striking finding at scale - approximately 87.2% reported experiencing either Insomnia or Sleep Apnea, suggesting that excess weight is a strong predictor of sleep disruption.

In contrast, individuals with a Normal Weight classification reported a sleep disorder only 7.4% of the time, with the vast majority (92.6%) reporting no disorder at all.

**Note:** The Obese group's 100% disorder rate is likely influenced by its very small sample size (n=10). The Overweight group, with 148 respondents, provides a more reliable signal and still shows a dramatically elevated disorder rate compared to the Normal Weight group.

<details>
<summary>BMI Category and Sleep Disorder Comparison Logic</summary>

```sql

SELECT
    bmi_category,
    COUNT(*) AS total_sample,
    SUM(CASE WHEN sleep_disorder <> 'None' THEN 1 ELSE 0 END) AS has_disorder,
    ROUND(
        SUM(CASE WHEN sleep_disorder <> 'None' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 1
    ) AS percentage_with_disorder
FROM sleep_health
GROUP BY bmi_category
ORDER BY percentage_with_disorder DESC;

```

</details>
<br>

![Sleep Disorders by BMI Category](Images/Sleep%20Disorder%20_%20BMI%20Graph.png)

## Key Takeaways

### Stress Level is the Strongest Predictor of Sleep Quality
Across all factors analyzed, stress showed the widest score range - from `8.97` at low stress to `5.86` at high stress. Notably, higher stress was also associated with greater variability in sleep scores, meaning disrupted sleep becomes less predictable as stress increases, not just worse on average.

### Age is Closely Associated with Sleep Quality
Individuals in their 50s reported the highest average sleep quality at `8.17`, nearly 2.5 points higher than those in their 20s `5.79`. This suggests that sleep quality may improve as individuals progress through adulthood, though the small sample size for the 20s group warrants caution in drawing strong conclusions.

### BMI Category and Sleep Disorder Relationships
Overweight individuals were nearly 12x more likely to report a sleep disorder than those with Normal Weight (`87.2%` vs `7.4%`). This was the most dramatic disparity observed across any single factor in the dataset, and unlike some findings, it held up across a large sample size.

### Optimal Sleep Profile
The optimal sleep profile suggested by this dataset is an individual in their 50s, female, working as an Engineer, with low reported stress levels and a Normal Weight BMI classification. 

Conversely, the highest-risk profile would be a younger male salesperson with high stress and an Overweight or Obese BMI classification.

## Recommendations

### Stress Level Monitoring
Both individuals and employers should be mindful of how stress levels impact sleep and overall lifestyle behaviors. Promoting healthy sleep habits and creating environments where people can manage stress more comfortably may translate to improved well-being and workplace performance.

### Watching Lifestyle as People Age
Throughout this analysis, it was evident that younger individuals are at greater risk of elevated stress levels and poorer sleep quality. It is critical that these patterns are recognized early, particularly as young people enter their careers and seek to maximize their overall lifestyle and performance.

### BMI Category and Sleep Disorder Awareness
The data suggests a strong relationship between BMI category and the likelihood of experiencing a sleep disorder. Individuals in the Overweight and Obese categories were dramatically more likely to report Insomnia or Sleep Apnea compared to those with a Normal Weight classification.

Maintaining a healthy weight should be considered not only a physical health priority but a sleep health priority as well. Individuals who fall into higher BMI categories may benefit from proactive sleep disorder screening, or be made more aware of the potential impacts it may have on sleep productivity.

## Conclusion


This analysis explored the demographic and lifestyle factors most associated with sleep quality and sleep disorder prevalence using the Sleep Health and Lifestyle Dataset. Across all factors examined, stress level and BMI category emerged as the strongest indicators of poor sleep outcomes, while age and occupation revealed meaningful patterns worth monitoring - particularly among younger individuals and those in high-pressure roles.

The findings suggest that sleep health is not an isolated metric, but rather a reflection of broader lifestyle and environmental conditions. Addressing stress, maintaining a healthy weight, and building awareness around sleep habits - especially early in adulthood - appear to be the highest-leverage opportunities for improving sleep outcomes.




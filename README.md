# Bellabeat-Case-Study
Bellabeat Case Study:-
How Can a Wellness Technology Company Play It Smart

I started with uploading the files to BigQuery. I uploaded all 7 datasets mentioned above. 
I used Looker Studio to make visualization using the tables created from SQL queries as it was easy to transport the temporary tables from BigQuery to Looker studios.
Steps performed in SQL

To count the number of unique ID in each table


“
SELECT COUNT(DISTINCT id) AS unique_id
FROM `harshit-401709.fitbit_data.daily_activity`
“

>>Output confirma that there are data for 35 unique users in the daily activity table. 

To know how often each ID was logged in


“
SELECT id,
COUNT(id) AS total_id
FROM `harshit-401709.fitbit_data.daily_activity`
GROUP BY id
  Order By total_id
“


>>The query showed us that activity from users logged in between 8 to 32 times. 


To classify their activities into three classes:


“
SELECT id,
COUNT(id) AS total_uses,
CASE
WHEN COUNT(Id) BETWEEN 21 AND 35 THEN 'active_user'
WHEN COUNT(Id) BETWEEN 11 and 20 THEN 'moderate_user'
WHEN COUNT(Id) BETWEEN 0 and 10 THEN 'light_user'
END AS user_classification
FROM `harshit-401709.fitbit_data.daily_activity`
GROUP BY id
“


>> The resulting table showed the classification as below:
	Active users-2
Moderate users-24
Light users-9


To find out Avg, Min & Max of total steps, calories, total distance, and other activity levels.


“
SELECT Id,
ROUND(MIN(TotalSteps),2) AS min_total_steps,
ROUND(MAX(TotalSteps),2) AS max_total_steps,
ROUND(AVG(TotalSteps),2) AS avg_total_steps,
ROUND(MIN(TotalDistance),2) AS min_total_dist,
ROUND(MAX(TotalDistance),2) AS max_total_dist,
ROUND(AVG(TotalDistance),2) AS avg_total_dist,
ROUND(MIN(Calories),2) AS min_calories,
ROUND(MAX(Calories),2) AS max_calories,
ROUND(AVG(Calories),2) AS avg_calories,
ROUND(MIN(VeryActiveMinutes),2) AS min_very_active_minutes,
ROUND(MAX(VeryActiveMinutes),2) AS max_very_active_minutes,
ROUND(AVG(VeryActiveMinutes),2) AS avg_very_active_minutes,
ROUND(MIN(FairlyActiveMinutes),2) AS min_fairly_active_minutes,
ROUND(MAX(FairlyActiveMinutes),2) AS max_fairly_active_minutes,
ROUND(AVG(FairlyActiveMinutes),2) AS avg_fairly_active_minutes,
ROUND(MIN(LightlyActiveMinutes),2) AS min_lightly_active_minutes,
ROUND(MAX(LightlyActiveMinutes),2) AS max_lightly_active_minutes,
ROUND(AVG(LightlyActiveMinutes),2) AS avg_lightly_active_minutes,
ROUND(MIN(SedentaryMinutes),2) AS min_sedentary_minutes,
ROUND(MAX(SedentaryMinutes),2) AS max_sedentary_minutes,
ROUND(AVG(SedentaryMinutes),2) AS avg_sedentary_minutes
FROM `harshit-401709.fitbit_data.daily_activity`
GROUP BY id
ORDER BY id
“


>>Created table shows the data 

To find out max of activity type


“
SELECT id,
ROUND(Avg(veryactiveMinutes),2) AS avg_very_active_minutes,
ROUND(Avg(fairlyactiveMinutes),2) AS avg_fairly_active_minutes,
ROUND(Avg(lightlyactiveMinutes),2) AS avg_lightly_active_minutes,
ROUND(Avg(sedentaryminutes),2) AS avg_sedentary_minutes
FROM `harshit-401709.fitbit_data.daily_activity`
GROUP BY Id
ORDER BY 2,3,4,5 DESC
“


>>The result showed that the sedentary minutes column was the highest on average.



To find out the no. of active and sedentary users.


“
	SELECT Id, ROUND(AVG(TotalSteps),2) AS avg_totalsteps,
  CASE
  WHEN ROUND(AVG(TotalSteps),2) < 5000 THEN 'sedentary_lifestyle'
  WHEN ROUND(AVG(TotalSteps),2) BETWEEN 5000 and 7499 THEN 'low_active'
  WHEN ROUND(AVG(TotalSteps),2) BETWEEN 7500 and 9999 THEN 'somewhat_active'
  WHEN ROUND(AVG(TotalSteps),2) BETWEEN 10000 and 12499 THEN 'active'
  WHEN ROUND(AVG(TotalSteps),2) > 12499 THEN 'highly_active'
  END AS total_steps_indices
FROM `harshit-401709.fitbit_data.daily_activity`
Group by Id
ORDER BY avg_totalsteps
“


>>14 users have a sedentary lifestyle and only 3 users have highly active lifestyle. 

To find the lifestyle based on total steps.

-Indices reference used from “How many steps/day are enough?” from Catrine Tudor-Locke and David R Bassett Jr as mentioned below:
-Preliminary pedometer indices for public health proposed the following indices in healthy adults
Sedentary lifestyle index < 5000 steps a day
low-active 5000 ≤ 7499
somewhat active 7500 ≤ 9999
Active > 10000
Highly active >12500

>>We have 14 sedentary lifestyle users, which means people with sedentary lifestyle are using more smart devices. 6 users were classified as low active, 9 users as somewhat active, 3 as active and 3 as highly active.

To find out how many users are meeting WHO expectations of walking steps.


“
SELECT Id,
avg(veryactiveminutes) + avg(fairlyactiveMinutes) + avg(lightlyactiveMinutes) AS total_avg_active_minutes,
CASE
WHEN avg(veryactiveminutes) + avg(fairlyactiveminutes) + avg(lightlyactiveminutes) >= 300 THEN 'exceeds_WHO_recommendation'
WHEN avg(veryactiveminutes) + avg(fairlyactiveminutes) + avg(lightlyactiveminutes) BETWEEN 150 AND 300 THEN 'meets_WHO_recommendation'
WHEN avg(veryactiveminutes) + avg(fairlyactiveminutes) + avg(lightlyactiveminutes) <150 THEN 'does_not_meets_WHO_recommendation'
END AS WHO_recommendation
FROM `harshit-401709.fitbit_data.daily_activity`
GROUP BY Id
“


The World Health Organization recommends that adults between the ages of 18–64 years of age should do at least 150–300 minutes of moderate-intensity aerobic physical activity.

This chart suggested that there are 12 people in the dataset who do not meet WHO expectation for walking and 17 people meet with WHO recommendation. Only 6 people were able to exceed recommendations. 

To find out how many users are meeting WHO expectations of walking steps without considering lightly active minutes.


“
SELECT Id,
avg(veryactiveminutes) + avg(fairlyactiveMinutes) + avg(lightlyactiveMinutes) AS total_avg_active_minutes,
CASE
WHEN avg(veryactiveminutes) + avg(fairlyactiveminutes) >= 300 THEN 'exceeds_WHO_recommendation'
WHEN avg(veryactiveminutes) + avg(fairlyactiveminutes) BETWEEN 150 AND 300 THEN 'meets_WHO_recommendation'
WHEN avg(veryactiveminutes) + avg(fairlyactiveminutes) <150 THEN 'does_not_meets_WHO_recommendation'
END AS WHO_recommendation
FROM `harshit-401709.fitbit_data.daily_activity`
GROUP BY Id
“


>> the resulting table showed that there were no users meeting WHO expectations when lightly active minutes are not considered. 

To confirm daily activity calory data is same as hourly_calorie data. 


“
“SELECT dailyactivity.id AS dailyactivity_id,
calories.id AS calories_id,
COUNT(dailyactivity.calories) AS dailyactivity_calories,
COUNT(calories.calories) AS total_hourly_calories
FROM `harshit-401709.fitbit_data.daily_activity` dailyactivity
LEFT JOIN `harshit-401709.fitbit_data.hourly_Calories` calories
ON dailyactivity.id = calories.id
GROUP BY dailyactivity.id, calories.id
“


>>This confirms that the data is same in both tables so we will be using daily_activity table to comparing colories and workout intensities.

To compare workout intensity with calories burned.


“
SELECT dailyactivity.id AS dailyactivity_id,
intensity.id AS intensity_id,
COUNT(dailyactivity.calories) AS total_dailyactivity_calories,
COUNT(TotalIntensity) AS totalintensity
FROM `harshit-401709.fitbit_data.daily_activity` dailyactivity
RIGHT JOIN `harshit-401709.fitbit_data.hourly_Intensities` intensity
ON dailyactivity.id = intensity.id
GROUP BY  dailyactivity.id, intensity.id
“


>>This chart confirmed that the calories burned were equal to the workout intensity. 

To find out Average sedentary time per user. 


“
select Id,
AVG(SedentaryMinutes) AS avg_sedentary_minutes
FROM `harshit-401709.fitbit_data.daily_activity`
Group by Id
“


>>> The results showed that the average sedentary time comes to 989.34 minutes per user. Which is close to 16 hours per day and needs to be reduced. 



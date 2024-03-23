Bellabeat Case Study:-


How Can a Wellness Technology Company Play It Smart


About the company
Urška Sršen and Sando Mur founded Bellabeat, a high-tech company that manufactures health-focused smart products. Sršen used her background as an artist to develop beautifully designed technology that informs and inspires women around the world. Collecting data on activity, sleep, stress, and reproductive health has allowed Bellabeat to empower women with knowledge about their health and habits. Since it was founded in 2013, Bellabeat has grown rapidly and quickly positioned itself as a tech-driven wellness company for women. 


Bellabeat has a lot of products and services, but for this case study, we will focus on the Bellabeat app, which acts as your personal health hub. By tracking activity, sleep, stress, menstrual cycles, and mindfulness habits, the app empowers you to understand your current practices and make informed decisions to optimize your overall well-being.


This study will pass through the six phases of the data life cycle which are;


Ask
Prepare
Process
Analyze
Share
Act


ASK


Business task-


Sršen asked me to analyze smart device usage data to gain insight into how consumers use non-Bellabeat smart devices. She then wants suggestions and recommendations for their marketing analytics team. 


Key stakeholders-


Urška Sršen: Bellabeat’s co-founder and Chief Creative Officer
Sando Mur: Mathematician and Bellabeat’s cofounder; key member of the Bellabeat executive team
Bellabeat marketing analytics team: A team of data analysts responsible for collecting, analyzing, and reporting data that helps guide Bellabeat’s marketing strategy.

Prepare


The analysis I am conducting utilizes datasets retrieved from Mobius on Kaggle, licensed under
the CCO Public Domain. kaggle.com indicates these datasets were collected via a distributed
Amazon Mechanical Turk survey between December 3rd, 2016 and December 5th, 2016, with
30 Fitbit users reportedly consenting to share personal tracker data.
Upon initial examination of the 18 extracted CSV files (out of the downloaded 10), I identified
several limitations. The data format stores information for each ID across multiple rows (long
format). Additionally, data integrity concerns arose. While Kaggle reported 30 consenting Fitbit
users, further investigation revealed 35 users over 32 days. Furthermore, the 2016 collection
date makes the data outdated for your current needs. The timeframe discrepancy between the
stated collection period (December 3rd-5th, 2016) and the observed 31 days also raises
questions.


The limited sample size introduces bias, as a larger group would better represent the target
population and increase confidence levels. The lack of demographic information (gender, age,
location) further hinders generalizability. Since Bellabeat products primarily target women,
including demographic details would have significantly enhanced the analysis and subsequent
recommendations.


Process


For this analysis, I will be using the following datasets,
1. dailyactivity_merged
2. hourlycalories_merged
3. hourlyintensities_merged
4. hourlysteps_merged
5. minutesleep_merged
6. heartrateseconds_merged
7. minutesteps_merged

   
● First I opened each dataset in Excel to get a feel of the dataset and see what I will be
working with.

● Then, I sorted and filtered the data to focus on the relevant information. Next, I tackled
data quality.

● I checked for and removed any blank entries, ensuring all data points were populated.
Then, I hunted down duplicate values.

● In the 'minutesleep_merged' dataset, I removed 543 duplicates, leaving 187,978 unique
values.

● I also standardized the format of the date and time columns. Dates were converted to a
clear month/day/year format (MM/DD/YYYY), while times were formatted to display
hours, minutes, and seconds (hh:mm:ss).

● Additionally, I separated the merged date/time column using an integer function (to likely
extract individual date and time components). Finally, I had to be ruthless and delete any
rows containing data that didn't conform to the established formats. After this thorough
cleaning process in Excel, I transitioned the data to SQL for further analysis.
Analyze

● I started with uploading the files to BigQuery. I uploaded all 7 datasets mentioned above.

● I used Looker Studio to make visualizations using the tables created from SQL queries
as it was easy to transport the temporary tables from BigQuery to Looker studios.


Steps performed in SQL

1. To count the number of unique ID in each table


“
`SELECT COUNT(DISTINCT id) AS unique_id
FROM `harshit-401709.fitbit_data.daily_activity``
“


>>Output confirm that there are data for 35 unique users in the daily activity table.
2. To know how often each ID was logged in


“
`SELECT id,
COUNT(id) AS total_id
FROM `harshit-401709.fitbit_data.daily_activity`
GROUP BY id
Order By total_id`
“


>>The query showed us that activity from users logged in between 8 to 32 times.
3. To classify their activities into three classes:


“
`SELECT id,
COUNT(id) AS total_uses,
CASE
WHEN COUNT(Id) BETWEEN 21 AND 35 THEN 'active_user'
WHEN COUNT(Id) BETWEEN 11 and 20 THEN 'moderate_user'
WHEN COUNT(Id) BETWEEN 0 and 10 THEN 'light_user'
END AS user_classification
FROM `harshit-401709.fitbit_data.daily_activity`
GROUP BY id`
“


>> The resulting table showed the classification as below:
Active users-2
Moderate users-24
Light users-9

![Chart 1](https://github.com/harshthegoose/Bellabeat-Case-Study/assets/19995625/409ec6a6-ce64-4a1c-9a67-b69c843d3569)

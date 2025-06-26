# Analyzing Electric Vehicle Charging Habits

### Project Overview

![charging station](https://github.com/user-attachments/assets/c45df249-0903-4ffc-9056-7918fb4de0fd)

As electric vehicles (EVs) become increasingly popular, the demand for accessible charging stations in residential spaces is growing rapidly. This project analyzes EV charging usage data to help apartment building managers better understand tenant charging habits. The goal is to provide insights into peak usage times, station availability issues, and overall demand patterns, enabling better planning for future infrastructure upgrades and improved tenant satisfaction.

### Data Sources

The data obtained from Kaggle and has been loaded into a PostgreSQL database with a table named charging_sessions with the following columns:

<h4><code>charging_sessions</code> table</h4>

<table>
  <thead>
    <tr>
      <th>Column</th>
      <th>Definition</th>
      <th>Data Type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>garage_id</code></td>
      <td>Identifier for the garage/building</td>
      <td><code>VARCHAR</code></td>
    </tr>
    <tr>
      <td><code>user_id</code></td>
      <td>Identifier for the individual user</td>
      <td><code>VARCHAR</code></td>
    </tr>
    <tr>
      <td><code>user_type</code></td>
      <td>Indicating whether the station is <code>Shared</code> or <code>Private</code></td>
      <td><code>VARCHAR</code></td>
    </tr>
    <tr>
      <td><code>start_plugin</code></td>
      <td>The date and time the session started</td>
      <td><code>DATETIME</code></td>
    </tr>
    <tr>
      <td><code>start_plugin_hour</code></td>
      <td>The hour (in military time) that the session started</td>
      <td><code>NUMERIC</code></td>
    </tr>
    <tr>
      <td><code>end_plugout</code></td>
      <td>The date and time the session ended</td>
      <td><code>DATETIME</code></td>
    </tr>
    <tr>
      <td><code>end_plugout_hour</code></td>
      <td>The hour (in military time) that the session ended</td>
      <td><code>NUMERIC</code></td>
    </tr>
    <tr>
      <td><code>duration_hours</code></td>
      <td>The length of the session, in hours</td>
      <td><code>NUMERIC</code></td>
    </tr>
    <tr>
      <td><code>el_kwh</code></td>
      <td>Amount of electricity used (in Kilowatt hours)</td>
      <td><code>NUMERIC</code></td>
    </tr>
    <tr>
      <td><code>month_plugin</code></td>
      <td>The month that the session started</td>
      <td><code>VARCHAR</code></td>
    </tr>
    <tr>
      <td><code>weekdays_plugin</code></td>
      <td>The day of the week that the session started</td>
      <td><code>VARCHAR</code></td>
    </tr>
  </tbody>
</table>

### Exploratory Data Analysis (EDA)

EDA involved exploring charging sessions table using key questions, such as:
- How many unique users use each garage's shared charging stations?
- What is the most popular charging start times by hour?
- Which users have the longest average charging durations (greater than 10 hours) when using shared charging stations?

### Data Analysis

Including some interesting code/features worked with

```sql
-- unique_users_per_garage
SELECT garage_id, 
       COUNT(DISTINCT user_id) AS num_unique_users
FROM charging_sessions
WHERE user_type = 'Shared'
GROUP BY garage_id
ORDER BY num_unique_users DESC;
```

```sql
-- most_popular_shared_start_times
SELECT weekdays_plugin, start_plugin_hour, COUNT(start_plugin_hour) AS num_charging_sessions
FROM charging_sessions
WHERE user_type = 'Shared'
GROUP BY weekdays_plugin, start_plugin_hour
ORDER BY num_charging_sessions DESC
LIMIT 10;
```

```sql
-- long_duration_shared_users
SELECT user_id,
       AVG(duration_hours) avg_charging_duration
FROM charging_sessions
WHERE user_type = 'Shared'
GROUP BY user_id
HAVING AVG(duration_hours) > 10
ORDER BY avg_charging_duration DESC;
```

### Results/Findings

The analysis results are summarized as follows:
- Garage BI2 has the highest number of unique users, with 18. In contrast, Garages AdA1 and Ris each have only one unique user.
- The most popular times to start charging are between 3 p.m. and 7 p.m., especially on Sundays.
- The analysis shows that user with the user_id "Share-9" has an average charging duration of 16.8 hours, while user with the user_id "Share-17" averages 12.9 hours.

### Recommendations

Based on the analysis, we recommend following actions:
- Consider adding more shared charging stations in Garage BI2 to meet demand and reduce user frustration.
- Implementing a reservation system, real-time availability notifications, or time-based usage limits during the peak hours.
- Offer clear guidelines on EV charging etiquette within the building, including expected charging durations.

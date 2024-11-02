**SQL Mystery Solving: Uncovering the Mastermind in SQL City**

This project is a SQL-based investigation inspired by a riddle from Knight Lab's SQL Mystery, 
constructed using SQLite. The challenge is to solve a murder mystery in SQL City, 
uncovering both the culprit and the mastermind behind the crime.

**Project Overview** 
The SQL City murder occurred on January 15, 2018. Our task is to analyze data, interview witnesses, 
and follow the leads in our database to find those responsible.

**Steps in the Investigation**
**1. Initial Clues**
Using our knowledge of the date, city, and crime type, we start 
with a basic SELECT query to pull initial details from the crime scene report.

SELECT *
FROM crime_scene_report
WHERE type = 'murder' 
  AND date = '20180115' 
  AND city = 'SQL City';

**Result:**
We learn that there are two witnesses. One lives at the last house on "Northwestern Dr," 
and the other, named Annabel, lives somewhere on "Franklin Ave."  

**2. Identifying the Culprit: Jeremy Bowers**
With information gathered from the witness interviews, 
we narrow down our suspect using the following query:


SELECT 
  gfnm.name                     AS Name,
  gfnm.person_id                AS ID_num,
  gfnci.membership_id           AS Memb_id,
  gfnm.membership_start_date    AS Memb_start,
  gfnm.membership_status        AS Memb_status,
  gfnci.check_in_date           AS Check_in_date,
  p1.license_id                 AS License 
FROM get_fit_now_member gfnm
JOIN get_fit_now_check_in gfnci   ON gfnm.id = gfnci.membership_id
JOIN person p1                    ON p1.name = gfnm.name
JOIN drivers_license dl           ON p1.license_id = dl.id
WHERE gfnm.id LIKE '48Z%' AND gfnm.membership_status = 'gold';

This query identifies Jeremy Bowers as the initial suspect. 
However, clues suggest he may be working for someone else.


**3. Discovering the Mastermind: Miranda Priestly**
Determined to reveal the true mastermind, we question Jeremy Bowers 
at the police station. The interrogation reveals:

"I was hired by a woman with a lot of money. I don't know her name, but I know she's about 5'5" or 5'7", 
has red hair, drives a Tesla Model S, and attended the SQL Symphony Concert three times in December 2017."

Our final query dives deeper into the database, combining subqueries 
to identify the true mastermind:


SELECT
  p1.name                   AS Name,
  p1.id                     AS ID_num,
  inc.ssn                   AS SSN,
  dl.hair_color             AS Hair_clr,
  dl.height                 AS Height,
  dl.car_make               AS Car_make,
  dl.car_model              AS Car_model,
  fec.date                  AS Date,
  fec.event_name            AS Event,
  COUNT(fec.event_name)     AS Visits
FROM 
  (SELECT *
   FROM facebook_event_checkin
   WHERE event_name = 'SQL Symphony Concert'
     AND date BETWEEN '20171201' AND '20171231') fec
JOIN person p1     ON p1.id = fec.person_id
JOIN income inc    ON p1.ssn = inc.ssn
JOIN 
  (SELECT *
   FROM drivers_license
   WHERE gender = 'female'
     AND hair_color = 'red'
     AND height BETWEEN 65 AND 67
     AND car_make = 'Tesla'
     AND car_model = 'Model S') dl ON p1.license_id = dl.id
GROUP BY p1.name, p1.id, inc.ssn, dl.hair_color, dl.height, 
         dl.car_make, dl.car_model, fec.event_name
HAVING COUNT(fec.event_name) = 3;

This query reveals Miranda Priestly as the mastermind 

**value**
Congrats, you found the brains behind the murder! 
Everyone in SQL City hails you as the greatest 
SQL detective of all time. Time to break out the champagne!
who orchestrated the murder.


**Key SQL Techniques Used**
- JOINs: To combine data from multiple tables, such as person, crime_scene_report, and drivers_license.
- Subqueries: Used to filter and aggregate data in complex searches.
- Aggregate Functions: COUNT to track specific events.
- Filtering and Grouping: Using WHERE, GROUP BY, and HAVING to refine results.


**Outcome**
Through a series of SQL queries and careful database investigation, we successfully identified both the direct culprit, 
Jeremy Bowers, and the mastermind, Miranda Priestly. SQL City celebrates the solution to this intricate mystery.

**Reflections**
Solving this mystery allowed me to test and strengthen my SQL skills, particularly in complex querying 
and data manipulation. It was a valuable and engaging experience on my journey toward a data-focused career.

**Special Thanks**
Thank you to Joon Park, Meiying (Cathy) He, and Joe Germuska, the creators of this SQL mystery, 
and to the KajoDataSpace community for supporting such a fascinating platform for learning.


# Sql-Murder-Mystery
This project walks through my process of solving the SQL Murder Mystery, a detective-style SQL challenge where the goal is to identify the murderer and mastermind
Project Overview
The database contains tables such as crime_scene_report, person, drivers_license, interview, and others.
My queries How I solved the case
## Reading Crime Scene Report
SELECT *
FROM crime_scene_report
WHERE date = '20180115'
And type = 'murder'
And city = 'SQL city';

## Finding witnesses mentioned in the description
SELECT *
FROM person
WHERE address_street_name = 'Northwestern Dr';

SELECT *
FROM person
WHERE address_street_name = 'Franklin Ave';

## Finding the witness that lives on the last house in Northwestern
SELECT *
FROM person
WHERE address_street_name = 'Northwestern Dr'
ORDER BY address_number DESC;

## Using witnesses interview table to  trace the witnessess and collect their statements
SELECT 
person.id, 
person.name, 
interview.transcript
FROM interview
INNER JOIN person ON interview.person_id = person.id
WHERE person.address_street_name = 'Franklin Ave'
  AND (
        transcript LIKE '%murder%' 
      );

SELECT 
person.id, 
person.name, 
interview.transcript
FROM interview
INNER JOIN person ON interview.person_id = person.id
WHERE person.address_number = '4919' ;

## Using the witnesses statements to identify suspects that have a gym membership and also checked in on the 9th of january having gold status
SELECT 
GM.id,
GC.check_in_date,
GM.name,
GM.membership_status,
GM.person_id,
DL.plate_number
FROM get_fit_now_member AS GM
INNER JOIN get_fit_now_check_in AS GC
ON GM.id =GC.membership_id
INNER JOIN drivers_license AS DL
ON GM.person_id =DL.id
WHERE GC.check_in_date = '20180109'
AND GM.membership_status ='gold' 
AND GM.id LIKE '48Z%'
AND DL.plate_number LIKE '%H42W';

SELECT *
FROM get_fit_now_member AS GM
INNER JOIN get_fit_now_check_in AS GC
ON GM.id =GC.membership_id
INNER JOIN person AS P
ON GM.person_id = P.id
INNER JOIN drivers_license AS DL
ON p.license_id =DL.id
WHERE GC.check_in_date = '20180109'
AND GM.membership_status ='gold' 
AND GM.id LIKE '48Z%';

## Checking the suspect statements to check if he has an accomplice and why he committed the murder
SELECT *
FROM interview
WHERE person_id = '67318';

## Following the suspect statement, I want to track down the woman who hired him
SELECT *
FROM drivers_license AS DL
INNER JOIN person AS P
ON p.license_id =DL.id
INNER JOIN facebook_event_checkin AS F
ON F.person_id = P.id
INNER JOIN income AS I
on p.ssn = I.ssn
WHERE DL.car_make = 'TESLA'
AND F.event_name = 'SQL Symphony Concert';

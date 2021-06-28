# Pewlett-Hackard-Analysis

#### An Employee Database Analysis

## Overview

SQL queries in pgAdmin were used to identify retirement-eligible employees by job title as well as current employees eligible to participate in a mentorship program based on year of birth (1965).

## Results

- Data on retirement eligible employees as extracted and tabulated using the following query:

  SELECT e.emp_no,\
  e.first_name,\
  e.last_name,\
  ti.title,\
  ti.from_date,\
  ti.to_date\
  INTO retirement_titles\
  FROM employees as e\
  INNER JOIN titles as ti\
  ON (e.emp_no = ti.emp_no)\
  WHERE (e.birth_date BETWEEN '1952-01-01' AND '1955-12-31')\
  ORDER BY ti.emp_no;
  
- The data was then narrowed down to include only the most recent, unique title:

  SELECT DISTINCT ON (rt.emp_no) rt.emp_no,
  rt.first_name,\
  rt.last_name,\
  rt.title\
  INTO unique_titles\
  FROM retirement_titles as rt\
  ORDER BY rt.emp_no, rt.to_date DESC;
  
- A count was carried out to determine the number of retiring employees by title, sorted in descending order:

  SELECT COUNT(ut.title), ut.title\
  INTO retiring_titles\
  FROM unique_titles as ut\
  GROUP BY ut.title\
  ORDER BY COUNT(ut.title) DESC;
  
- Finally, current employees eligible for mentorship (those born in 1965) were extracted into a table:

  SELECT DISTINCT ON (e.emp_no) e.emp_no,\
  e.first_name,\
  e.last_name,\
  e.birth_date,\
  de.from_date,\
  de.to_date,\
  ti.title\
  INTO mentorship_eligibilty\
  FROM employees as e\
  INNER JOIN dept_emp as de\
  ON (e.emp_no = de.emp_no)\
  INNER JOIN titles as ti\
  ON (e.emp_no = ti.emp_no)\
  WHERE (de.to_date = '9999-01-01')\
  AND (e.birth_date BETWEEN '1965-01-01' AND '1965-12-31')\
  ORDER BY e.emp_no;

## Summary

- The number of roles that need replacing as the "silver tsunami" takes impact depend on the job title, with senior engineers being the most urgent (29,414 retiring employees) and managers being the least (only 2 retiring)
- In order to determine if there are sufficient current employees available to act as mentors, the number of employees eligible for mentorship had to be calculated from the employee eligibility table. The following query was used to carry out a count by title:

  SELECT COUNT(me.title), me.title\
  FROM mentorship_eligibilty as me\
  GROUP BY me.title\
  ORDER BY COUNT(me.title) DESC;\
  
- The resulting table is as follows: 
  
  ![deliverable_3_question_2](https://user-images.githubusercontent.com/79061124/123564374-3a15dc00-d787-11eb-8bb8-b6db9f5c692a.png)

- The results are promising. The number of possible mentors for each job title exceeds the number of employees eligible for mentorship by between 1 and 2 orders of magnitude, with the only exception being managers. Though no managers qualify for the mentorship program, the risk to the firm is mitigated by the fact that only 2 managers are currently eligible for retirement. 

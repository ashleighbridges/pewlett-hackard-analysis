# pewlett-hackard-analysis

## Purpose
As a member of the Pewlett-Hackard HR Department, the goal of this analysis was to provide insight into the unusually large number of employees retiring soon in order to see how many new employees will need to be hired, as well as how many internal promotions are needed to fill management positions. This analysis also helped the company budget for how much will need to be dedicated to retirement packages over the next few years. 

## Results
Major observations are as follows:

- There are a total of 72,458 employees on the list for retirement

![unique_titles](https://user-images.githubusercontent.com/100883212/167324720-ad44c67d-86cc-435d-a978-d590e67eb5bc.png)

- 70% of the total number of retiring employees who will be retiring in the near future hold either the "Senior Engineer" or "Senior Staff" titles
- However, only 2 employees are Managers

![retiring_titles](https://user-images.githubusercontent.com/100883212/167323974-17cb7e39-b478-4674-9c40-36787360c36b.png)

- Only 1,549 employees were born in 1965 and are therefore elibigle for the Mentorship Program to train existing and new employees

![mentorship_eligibility](https://user-images.githubusercontent.com/100883212/167324300-8a92f091-60bb-41bb-9a07-994273ecf910.png)

## Summary

Per the analysis, there are 72,458 roles that will need to be filled after all employees eligible for retirement retire. Since this is such a large number, I ran two queries to break the data down to a more manageable view. 

First, I used the following query to create a new table (unique_titles_department) to show employee numbers, first names, last names, titles, and department names: 
```
SELECT DISTINCT ON (rt.emp_no) 
	rt.emp_no,
	rt.first_name,
	rt.last_name,
	rt.title,
	d.dept_name
INTO unique_titles_department
FROM retirement_titles as rt
INNER JOIN dept_emp as de
ON (rt.emp_no = de.emp_no)
INNER JOIN departments as d
ON (d.dept_no = de.dept_no)
ORDER BY rt.emp_no, rt.to_date DESC;
```
Next, I ran the following query to break down the number of roles that will need to be filled for each title within each department to distribute to department heads:
```
SELECT ut.dept_name, ut.title, COUNT(ut.title) 
INTO roles_needed
FROM (SELECT title, dept_name from unique_titles_department) as ut
GROUP BY ut.dept_name, ut.title
ORDER BY ut.dept_name DESC;
```
Lastly, I needed to find out if there are there enough qualified, retirement-ready employees in the departments to mentor the next generation of Pewlett Hackard employees. To do this, I created one more table (mentor_staff) using the following query to show the total number of senior-level staff (titles of Senior Engineer, Senior Staff, Technique Leader, and Manager) qualify for the mentorship program per title and department:
```
SELECT d.dept_name,
	   me.title,
	   COUNT(me.title)
INTO mentor_staff
FROM mentorship_eligibility as me
INNER JOIN dept_emp as de
ON (me.emp_no = de.emp_no)
INNER JOIN departments as d
ON (d.dept_no = de.dept_no)
WHERE me.title IN ('Senior Engineer', 'Senior Staff', 'Technique Leader', 'Manager')
GROUP BY d.dept_name, me.title
ORDER BY d.dept_name DESC;
```
While there will be many employees participating in the mentorship program, there are a few gaps for mentors that will need to be filled. For instance, there are no Managers who qualify to be mentors. However, with enough planning, Pewlett Hackard should be able to work with the retiring Managers to pull together some training materials before they enter retirement.

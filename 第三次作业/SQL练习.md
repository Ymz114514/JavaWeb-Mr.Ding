> 软工2203
>
> 2200770165
>
> 杨明璋



# 员工信息

做了27道：

```sql
# 1. 查询所有员工的姓名、邮箱和工作岗位。
select first_name 名, last_name 姓, email 邮箱, job_title 工作岗位
from employees;

# 2. 查询所有部门的名称和位置。
select dept_name 名称, location 位置
from departments;

# 3. 查询工资超过70000的员工姓名和工资。
select first_name 名, last_name 姓, salary 工资
from employees
where salary > 70000;

# 4. 查询IT部门的所有员工。
select *
from employees
where dept_id = (select dept_id
                 from departments
                 where dept_name = 'IT');

# 5. 查询入职日期在2020年之后的员工信息。
select *
from employees
where hire_date >= '2020-01-01';

# 6. 计算每个部门的平均工资。
select dept_name 部门名称, avs 平均工资
from (select dept_id, avg(salary) avs
      from employees
      group by dept_id) avg_salary,
     departments
where avg_salary.dept_id = departments.dept_id;

# 7. 查询工资最高的前3名员工信息。
select *
from employees
order by salary
limit 0,3;

# 8. 查询每个部门员工数量。
select dept_name 部门名称, count(*) 人数
from departments
         left join employees on departments.dept_id = employees.dept_id
group by departments.dept_id;

# 9. 查询没有分配部门的员工。
select *
from employees
where dept_id is null;

# 10. 查询参与项目数量最多的员工。
-- a.得到每个员工参加项目的情况表，求出项目数量的最大值A。
-- b.回到这个表，选出参与项目数量为A的员工id集合。
-- c.将这些员工筛选出来
select *
from employees
where emp_id in (select emp_id
                 from employee_projects
                 group by emp_id
                 having count(*) = (select count(*) max
                                    from employee_projects
                                    group by emp_id
                                    order by max desc
                                    limit 0,1));


# 11. 计算所有员工的工资总和。
select sum(salary) 工资总和
from employees;

# 12. 查询姓"Smith"的员工信息。
select *
from employees
where last_name = 'Smith';

# 13. 查询即将在半年内到期的项目。
/* 这里使用了SQL中处理日期的相关函数。curdate()得到的是今天，interval 6 month表示6个月的时间间隔。
   这里查询的核心是从今天开始往后推6个月，如果结束日期在这个范围内，那么就是我们要查询的项目。
 */
select *
from projects
where end_date between curdate() and date_add(curdate(), interval 6 month);

# 14. 查询至少参与了两个项目的员工。
select *
from employees
where emp_id in (select emp_id
                 from employee_projects
                 group by emp_id
                 having count(*) >= 2);

# 15. 查询没有参与任何项目的员工。
select *
from employees
         left join employee_projects on employees.emp_id = employee_projects.emp_id
where project_id is null;

# 16. 计算每个项目参与的员工数量。
select project_name 项目名, count(*) 参与员工数量
from projects
         left join employee_projects on projects.project_id = employee_projects.project_id
group by projects.project_id;

# 17. 查询工资第二高的员工信息。
select *
from employees
order by salary desc
limit 1,1;

# 18. 查询每个部门工资最高的员工。
select dept_id 部门id, max(salary) 最高工资
from employees
group by dept_id;

# 19. 计算每个部门的工资总和,并按照工资总和降序排列。
select dept_id 部门id, sum(salary) total
from employees
group by dept_id
order by total desc;

# 22. 查询所有员工的工作岗位,不要重复。
select distinct job_title
from employees;

# 23. 查询平均工资最高的部门。
select dept_id, avg(salary) avg
from employees
group by dept_id
order by avg desc
limit 0,1;

# 24. 查询工资高于其所在部门平均工资的员工。
-- a.查询每个部门的平均工资
-- b.拿以上的表格与员工表做连接，将salary高于avg的记录筛出来
select *
from employees,
     (select dept_id, avg(salary) avg from employees group by dept_id) dav
where dav.dept_id = employees.dept_id
  and salary > avg;

# 26. 查询跨部门的项目(参与员工来自不同部门)。
-- 连接项目员工表和员工表，统计一个项目内，将部门号去重后的结果。如果结果大于1，那么这个项目就是跨部门的
select project_id
from employees,
     employee_projects
where employees.emp_id = employee_projects.emp_id
group by project_id
having count(distinct dept_id) > 1;

# 27. 查询每个员工的工作年限,并按工作年限降序排序。
select emp_id 员工id, datediff(curdate(), hire_date) totalDays
from employees
order by totalDays desc;

# 28. 查询本月过生日的员工(假设hire_date是生日)。
-- 只要月份是本月即可
select *
from employees
where month(curdate()) = month(hire_date);

# 29. 查询即将在90天内到期的项目和负责该项目的员工。
-- 使用嵌套查询。
select *
from employees
where emp_id in (select emp_id
                 from employee_projects
                 where project_id in (select project_id
                                      from projects
                                      where end_date between curdate() and date_add(curdate(), interval 90 day)));

# 30. 计算每个项目的持续时间(天数)。
select project_name 项目名称, datediff(end_date, start_date) 持续时间（天）
from projects;
```



# 学生信息

做了41道

```sql
# 1. 查询所有学生的信息。
select *
from student;

# 2. 查询所有课程的信息。
select *
from course;

# 3. 查询所有学生的姓名、学号和班级。
select student.name, student_id, my_class
from student;

# 4. 查询所有教师的姓名和职称。
select teacher.name, title
from teacher;

# 5. 查询不同课程的平均分数。
select course_name 课程名, avg(score.score) 平均分
from course
         left join studentdb.score on course.course_id = score.course_id
group by course.course_id;

# 6. 查询每个学生的平均分数。
select student.name 学生姓名, avg(score) 平均分
from student
         left join score on student.student_id = score.student_id
group by student.student_id;

# 7. 查询分数大于85分的学生学号和课程号。
select student_id, course_id
from score
where score > 85;

# 8. 查询每门课程的选课人数。
select course_name, count(*) 选课人数
from course
         left join score on course.course_id = score.course_id
group by course.course_id;

# 9. 查询选修了"高等数学"课程的学生姓名和分数。
select student.name, score
from score
         join student on score.student_id = student.student_id
where course_id = (select course_id
                   from course
                   where course_name = '高等数学');

# 10. 查询没有选修"大学物理"课程的学生姓名。
select name
from student
where student_id not in (select student_id
                         from score
                         where course_id = (select course_id
                                            from course
                                            where course_name = '大学物理'));

# 13. 查询选择C002课程但没选择C004课程的成绩情况(不存在时显示为 null )。
# 选择C002课程但没选择C004课程 ？ √
select *
from score
where student_id in (select distinct student_id
                     from score
                     where course_id = 'C002'
                     except
                     select distinct student_id
                     from score
                     where course_id = 'C004');


# 14. 查询平均分数最高的学生姓名和平均分数。
-- a.得到一个临时结果，即学生的平均分
with stu_avg_table(stu_name, grade) as (select student.name, avg(score) s_avg
                                        from student
                                        left join score on student.student_id = score.student_id
                                        group by student.student_id
                                        order by s_avg desc)
select *
from stu_avg_table
where grade = (select max(grade)
               from stu_avg_table);

# 15. 查询总分最高的前三名学生的姓名和总分。
select student.name, avg(score) s_avg
from student
         left join score on student.student_id = score.student_id
group by student.student_id
order by s_avg desc
limit 0,3;

# 17. 查询男生和女生的人数。
select gender, count(*) 人数
from student
group by gender;

# 18. 查询年龄最大的学生姓名。
with stu_age(id, age) as (select student_id, year(curdate()) - year(birth_date)
                          from student)
select name
from student
         join stu_age on student_id = id
where age = (select max(age)
             from stu_age);
# 19. 查询年龄最小的教师姓名。
with teacher_age(id, age) as (select teacher_id, year(curdate()) - year(birth_date)
                              from teacher)
select name
from teacher
         join teacher_age on teacher_id = id
where age = (select min(age)
             from teacher_age);
# 20. 查询学过「张教授」授课的同学的信息。
select *
from student
where student_id in (select student_id
                     from score
                     where course_id in (select course_id
                                         from course
                                         where teacher_id = (select teacher_id
                                                             from teacher
                                                             where name = '张教授')));
# 21. 查询至少有一门课与学号为"2021001"的同学所学相同的同学的信息 。
-- a.学号为"2021001"的同学学了哪些课？
-- b.其他人选的课中只要有一门课在这里边的就算满足条件。
select *
from student
where student_id != '2021001'
  and student_id in (select distinct student_id
                     from score
                     where course_id in (select course_id
                                         from score
                                         where student_id = '2021001'));

# 22. 查询每门课程的平均分数，并按平均分数降序排列。
select course_name 课程名, avg(score.score) 平均分
from course
         left join score on course.course_id = score.course_id
group by course.course_id
order by 平均分 desc;

# 23. 查询学号为"2021001"的学生所有课程的分数。
select sum(score)
from score
where student_id = '2021001';

# 24. 查询所有学生的姓名、选修的课程名称和分数。
select student.name, course_name, score
from student
         join score on student.student_id = score.student_id
         join course on score.course_id = course.course_id;

# 25. 查询每个教师所教授课程的平均分数。
select teacher.name 教师, course_name 所教课程名, avg(score) 平均分
from teacher
         join course on teacher.teacher_id = course.teacher_id
         join score on course.course_id = score.course_id
group by course.teacher_id, score.course_id;

# 26. 查询分数在80到90之间的学生姓名和课程名称。
select student.name, course_name
from student
         join score on student.student_id = score.student_id
         join course on score.course_id = course.course_id
where score between 80 and 90;

# 27. 查询每个班级的平均分数。
select my_class 班级, course_name 科目, avg(score) 平均分
from student
         join score on student.student_id = score.student_id
         join course on score.course_id = course.course_id
group by my_class, course.course_id;

# 28. 查询没学过"王讲师"老师讲授的任一门课程的学生姓名。
select name
from student
where student_id in ((select student_id
                      from student)
                     except
                     (select distinct student_id
                      from score
                      where course_id in (select course_id
                                          from course
                                          where teacher_id = (select teacher_id
                                                              from teacher
                                                              where name = '王讲师'))));

# 29. 查询两门及其以上小于85分的同学的学号，姓名及其平均成绩 。
with stu_avg_table(student_id, grade) as (select student.student_id, avg(score) s_avg
                                          from student
                                          left join score on student.student_id = score.student_id
                                          group by student.student_id
                                          order by s_avg desc)
select score.student_id 学号, name 姓名, grade 平均分
from score
         join student on score.student_id = student.student_id
         join stu_avg_table on stu_avg_table.student_id = student.student_id
where score < 85
group by student.student_id
having count(*) >= 2;

# 30. 查询所有学生的总分并按降序排列。
select student_id 学号, sum(score) 总分
from score
group by student_id
order by 总分 desc;

# 31. 查询平均分数超过85分的课程名称。
select course_name
from score
         join course on score.course_id = course.course_id
group by score.course_id
having avg(score) > 85;

# 32. 查询每个学生的平均成绩排名。
with stu_avg_table(student_id, grade) as (select student.student_id, avg(score) s_avg
                                          from student
                                          left join score on student.student_id = score.student_id
                                          group by student.student_id
                                          order by s_avg desc)
select rank() over (order by grade desc ) 排名, student_id, grade
from stu_avg_table;

# 33. 查询每门课程分数最高的学生姓名和分数。
with course_max(course_id, grade) as (select course.course_id, max(score)
                                      from score
                                      join course on score.course_id = course.course_id
                                      group by course.course_id)
select score.course_id 课程号, name 学生姓名, score 最高分
from student
         join score on student.student_id = score.student_id
         join course_max on course_max.course_id = score.course_id
where score.score = grade;

# 34. 查询选修了"高等数学"和"大学物理"的学生姓名。
select name
from student
where student_id in (select student_id
                     from score
                     where course_id in (select course_id
                                         from course
                                         where course_name in ('高等数学', '大学物理')));

# 41. 查询至少有一门课程分数低于80分的学生姓名。
select *
from student
where exists(select 1
             from score
             where score.student_id = student.student_id
             and score < 80);
# 42. 查询所有课程分数都高于85分的学生姓名。
-- 只要有一门低于85就排除
select *
from student
where not exists(select 1
                 from score
                 where score.student_id = student.student_id
                 and score < 85);
# 43. 查询查询平均成绩大于等于90分的同学的学生编号和学生姓名和平均成绩。
with stu_avg(student_id, name, avg_grade) as (select student.student_id, name, avg(score)
                                              from student
                                              left join score on student.student_id = score.student_id
                                              group by student.student_id)
select *
from stu_avg
where avg_grade >= 90;

# 44. 查询选修课程数量最少的学生姓名。
-- 统计学生的选课情况
with stu_cource_choosed(student_id, nums) as (select score.student_id, count(*)
                                              from student
                                              left join score on student.student_id = score.student_id
                                              group by student.student_id)
select *
from stu_cource_choosed
where nums = (select min(nums)
              from stu_cource_choosed);
# 45. 查询每个班级的第2名学生（按平均分数排名）。
-- 计算每一个人的平均分
with stu_avg(student_id, avg_grade, my_class) as (select student.student_id, avg(score), my_class
                                                  from student
                                                  left join score on student.student_id = score.student_id
                                                  group by student.student_id),
-- 使用窗口函数，在班级内进行排名
     stu_class_ranked(student_id, 排名) as (select student_id,
                                          row_number() over (partition by my_class order by avg_grade desc )
                                          from stu_avg)
select *
from stu_class_ranked
where 排名 = 2;


# 46. 查询每门课程分数前三名的学生姓名和分数。
-- 使用窗口函数，在score表中，按照课程号分区，然后成绩以降序排列，然后标上排名。
-- 最后只取出前3名即可
with ranked_scores as (select student_id,
                       course_id,
                       score,
                       row_number() over (partition by course_id order by score desc ) 排名
                       from score)
select *
from ranked_scores
where 排名 <= 3;

# 47. 查询平均分数最高和最低的班级。
-- 先获得每个班的平均分
with class_avg(my_class, avg_grade) as (select my_class, avg(score)
                                        from student
                                        join score on student.student_id = score.student_id
                                        group by my_class)
select *
from class_avg
where avg_grade = (select max(avg_grade)
                   from class_avg)
   or avg_grade = (select min(avg_grade)
                   from class_avg);

# 48. 查询每个学生的总分和他所在班级的平均分数。
-- 学生总分表和班级平均分表做关联即可
with stu_total(student_id, total, my_class) as (select student.student_id, sum(score), my_class
                                                from student
                                                left join score on student.student_id = score.student_id
                                                group by student.student_id),
     class_avg(my_class, avg_grade) as (select my_class, avg(score)
                                        from student
                                        join score on student.student_id = score.student_id
                                        group by my_class)
-- 踩坑记录：用with定义多个临时表是这样创建的！！！
select student_id 学生ID, total 学生总分, avg_grade 班级平均分
from stu_total
         join class_avg on stu_total.my_class = class_avg.my_class;


# 49. 查询每个学生的最高分的课程名称, 学生名称，成绩。
-- score表中按学号分组，然后将分数最高的那一行保留下来
with stu_max as (select *
                 from score
                 where (student_id, score) in (
                     -- 使用多个列的值进行匹配
                     select student_id, max(score)
                     from score
                     group by student_id))
select name 学生姓名, course_name 课程名称, score 成绩
from student
         join stu_max on student.student_id = stu_max.student_id
         join course on stu_max.course_id = course.course_id;


# 50. 查询每个班级的学生人数和平均年龄。
-- 学生年龄
with stu_age(student_id, age) as (select student_id, year(curdate()) - year(birth_date)
                                  from student)
select my_class 班级, count(*) 学生人数, avg(age) 平均年龄
from student
         join stu_age on stu_age.student_id = student.student_id
group by my_class;


```


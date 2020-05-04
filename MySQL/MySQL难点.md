

#### 分组查询

1. 在分组查询中出现在SELECT后面的字段要么为GROUP BY 后面的字段要么为聚合函数

2. 筛选可以分为分组前筛选和分组后筛选，分组前筛选使用WHERE,针对的是原始表。而分组后筛选使用的是HAVING，针对的是GROUP BY后的结果集

3. WHERE后面不能使用聚合函数的一个重要原因就是分组聚合函数针对的是GROUP BY后的结果集，GROUP BY要在WHERE语句之后才能执行

4. 因为HAVING分组后筛选在针对的是分组后的结果集，所以HAVING后面的字段要么是SELECT后面的字段，要么是其他的聚集函数

5. 使用分组查询的时候要明白是否要进行分组前筛选或者分组后筛选

   ```mysql
   #每个工种有奖金的员工的最高工资>12000的工种编号和最高工资
   SELECT job_id,MAX(salary)
   FROM employees
   WHERE commission_pct IS NOT NULL
   GROUP BY job_id
   HAVING MAX(salary)>12000;
   如何判断使用分组后筛选还是使用分组前筛选，可以凭借根据分组的依据。比如这里：最高工资>12000，显然要使用到聚合函数，那么只能使用分组后筛选，因为分组前筛选不能使用聚合函数
   ```



#### 子查询

1. 标量子查询（结果集只有一行一列）可以使用=,>,<,<>符号进行连接

2. 列子查询（结果往往有多行），不能直接使用=,>,<,<>，要配合使用any,all等修饰词

3. 在使用子查询的时候要先去思考子查询返回的结果是一条数据还是多条数据

   1. 子查询可以出现在WHERE,SELECT,HAVING，FROM,EXISTS后面

   ```mysql
   #返回公司工资最少的员工的last_name,job_id和salary
   SELECT last_name,job_id,salary
   FROM employees
   WHERE salary=(
   	SELECT MIN(salary)
   	FROM employees
   );
   
   # 查询平均工资最低的部门信息
   SELECT *
   FROM departments
   WHERE department_id=(
   	SELECT department_id
   	FROM employees
   	GROUP BY department_id
   	ORDER BY AVG(salary) 
   	LIMIT 1
   );
   
   注意上面两个语句的不同，一个是直接查询工资最少的员工，这个可以直接通过先查询最低的工资，然后根据工资等于最低的工资去查询员工信息， 不要死板的认为查询数据只能通过ID来查
   而第二个查询的是平均工资最低，查询平均工资要使用聚合函数，查询最低的工资也要使用聚合函数，所以一个更简便的方法是集合排序和分页综合使用，查询各部门的平均工资，根据平均工资排序之后取第一个
   ```

   ```mysql
   #返回location_id是1400或1700的部门中的所有员工姓名
   SELECT last_name
   FROM employees
   WHERE department_id IN(
   	SELECT DISTINCT department_id
   	FROM departments
   	WHERE location_id IN(1400,1700)
   );
   SELECT last_name,employee_id
   FROM employees
   WHERE department_id =ANY(
   	SELECT  DISTINCT department_id
   	FROM employees
   	WHERE last_name LIKE '%u%'
   );
   
   上面两种写法都可以
   子查询返回的是多行子查询（列子查询），并且是或条件所以使用any
   
   #返回其它工种中比job_id为‘IT_PROG’工种任一工资低的员工的员工号、姓名、job_id 以及salary
   SELECT last_name,employee_id,job_id,salary
   FROM employees
   WHERE salary<ANY(
   	SELECT DISTINCT salary
   	FROM employees
   	WHERE job_id = 'IT_PROG'
   
   ) AND job_id<>'IT_PROG';
   ```

   ##### SELECT后使用的子查询(仅仅支持标量子查询)

   ```mysql
   #查询每个部门的员工个数
   SELECT d.*,(
   
   	SELECT COUNT(*)
   	FROM employees e
   	WHERE e.department_id = d.`department_id`
    ) 个数
    FROM departments d;
    
    这个不能和分组查询显示每个部门各员工个数语法弄混，使用分组查询有很大的局限，因为在分组查询中	SELECT后的字段要么为分组的字段要么为聚合函数
    使用SELECT后的子查询可以理解为查询的是部门数据，在查找部门数据的时候要了解部门中的员工个数，所以使用子查询的结果作为一个虚拟字段
   ```

   ##### FROM 后使用子查询，将查出的结果作为虚拟表

   ```mysql
   #查询每个部门的平均工资的工资等级
   SELECT  ag_dep.*,g.`grade_level`
   FROM (
   	SELECT AVG(salary) ag,department_id
   	FROM employees
   	GROUP BY department_id
   ) ag_dep
   INNER JOIN job_grades g
   ON ag_dep.ag BETWEEN lowest_sal AND highest_sal;
   
   一般来说如果查出的数据要和其他表进行关联查询的话要将查询的结果作为虚拟表
   
   
   #3.查询各部门中工资比本部门平均工资高的员工的员工号, 姓名和工资
   #①查询各部门的平均工资
   SELECT AVG(salary),department_id
   FROM employees
   GROUP BY department_id
   
   #②连接①结果集和employees表，进行筛选
   SELECT employee_id,last_name,salary,e.department_id
   FROM employees e
   INNER JOIN (
   	SELECT AVG(salary) ag,department_id
   	FROM employees
   	GROUP BY department_id
   
   
   ) ag_dep
   ON e.department_id = ag_dep.department_id
   WHERE salary>ag_dep.ag ;
   ```

#####              exists后面（相关子查询）

```mysql
#查询有员工的部门名
SELECT department_name
FROM departments d
WHERE EXISTS(
	SELECT *
	FROM employees e
	WHERE d.`department_id`=e.`department_id`
);

有员工的部门名表示部门的ID存在于员工的表中
```


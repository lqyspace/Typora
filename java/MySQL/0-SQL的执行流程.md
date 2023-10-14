# SQL的执行流程

## 查询的结构

```MySQL
# 方式1：SQL92写法
select ..., ..., ...
from ..., ..., ...
where  多表的连接条件
and 不包含组函数的过滤条件
group by ..., ...
having 包含组函数的过滤条件
order by ... asc/desc
limit ..., ...

# 方式2：SQL99写法
select ..., ..., ...
from ... join ...
on 多表连接条件
join ...
on ...
where 不包含组函数的过滤条件
and/or 不包含组函数的过滤条件
group by ..., ...
having 包含组函数的过滤条件
order by ... asc/desc
limit ..., ...

# 其中
# (1) from: 从哪些表中筛选
# (2) on: 关联多表查询时，去除笛卡尔积
# (3) where: 从表中筛选条件
# (4) group by: 分组依据
# (5) having: 在统计结果中再次筛选
# (6) order by: 排序
# (7) limit: 分页
```



## SQL语句执行过程

**1、关键词的顺序不能颠倒**

```MySQL
select ... from ... where ... group by ... having ... order by ... limit ...
```

**2、select语句的执行顺序（在MySQL和Oracle中，select执行顺序基本相同）**

```MySQL
from -> on -> [left / right] join -> where -> group by -> having -> select 字段 -> distinct -> order by -> limit
```

![image-20230919103417520](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191034612.png)

比如写了一个SQL语句，那么它的关键字顺序和执行顺序是下面这样的：

```mysql
select distinct player_id, player_name, count(*) as num # 顺序5
from player join on player.team_id = team.team_id # 顺序1
where height > 1.80 # 顺序2
group by player.team_id # 顺序3
having num > 2 # 顺序4
order by num desc # 顺序6
limit 2; # 顺序7
```

在select执行这些步骤的时候，每个步骤都会产生一个虚拟表，然后将这个虚拟表传入到下一个步骤中作为输入。需要注意的是，这些步骤隐含在SQL的执行过程中，对于我们来说是不可见的。



## SQL的执行原理

select是先执行from这一步的。在这个阶段，如果多张表联查，还会经历下面几个阶段：

- 首先先通过cross join求笛卡尔积，相当于得到虚拟表vt（virtual table）1-1；
- 通过on进行筛选，在虚拟表vt1-1的基础上进行筛选，得到虚拟表vt1-2；
- 添加外部行。添加外部行，如果我们使用的左连接，右连接或者全连接，就会涉及到外部行，也就是在虚拟表vt1-2的基础上增加外部行，得到虚拟表vt1-3。

当然如果我们操作的是两张以上的表还会重复上面的过程，直到所有表都被处理完为止。这个过程得到我们的原始数据。

当我们拿到查询数据表的原始数据，也就是最终的虚拟表vt1，就可以在此基础上再进行where阶段。在这个阶段中，会根据vt1表的结果进行筛选过滤，得到虚拟表vt2。

然后进入第三步和第四步，也就是得到group和having阶段。在这个阶段中，实际上是在虚拟表vt2的基础上进行分组和分组过滤，得到中间的虚拟表vt3和vt4。

当我们完成条件筛选部分之后，就可以筛选表中提取的字段，也就是进入到select 和 distinct 阶段。

首先在select字段阶段会提取想要的字段，然后在distinct阶段过滤掉重复的行，分别得到中间的虚拟表vt5-1和vt5-2。

当我们提取到了想要的字段数据之后，就可以按照指定的字段进行排序，也就是order by 阶段，得到虚拟表vt6。

最后在vt6的基础上，取出指定行的记录，也就是limit阶段，得到最终的结果，对应的是虚拟表vt7。

当然我在写select关键字的时候，不一定存在所有的关键字，相应的阶段就会省略。

同时因为SQL是一门类似英语的结构化查询语言，所以我们在写select语句的时候，还要注意相应的关键字顺序，所谓底层运行的原理，就是我们刚才讲到的执行顺序。



练习题：

```MySQL
# 查询公司员工工资的最大值，最小值，平均值，总和
select max(salary), min(salary), avg(salary), sum(salary) from employees;

# 查询各job_id的员工工资的最大值，最小值，平均值，总和
select job_id, max(salary), min(salary), avg(salary), sum(salary) from employees group by job_id;

# 选择具有各个job_id的员工人数
select job_id, count(*) from employees group by job_id;

# 查询员工最高工资和最低工资的差距(difference)
select max(salary) - min(salary) as difference from employees;

# 查询各个管理者手下员工的最低工资，其中最低工资不能低于6000，没有管理者的员工不计算在内
select manager_id, min(salary) from employees where not manager_id <=> NULL group by manager_id having min(salary)>=6000;

# 查询所有部门的名字，location_id，员工数量和平均工资，并按平均工资降序
select d.dept_name, d.location_id, count(employee_id) as num, avg(salary) as avg_sal from employees e  right join department d on d.department_id=e.department_id group by d.dept_name, d.location_id order by avg_sal desc;# 之所以使用count(employee_id)，是因为有些部门可能没有员工，左外连接是就会为NULL，而count(*)也会把空值统计进去，所以为了不把空值统计进去，就是用count(employee_id)

# 查询每个工种，每个部门的部门名，工种名和最低工资
select d.job_id, d.dept_name, min(salary) from employees e right join department_id d on d.department_id=e.department_id group by d.job_id, d.dept_name;
```

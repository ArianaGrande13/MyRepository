## **Drools介绍**
### 1.salience 
功能：设置规制执行的优先级
值：数字(数字越大执行优先级越高)
示例:

```markdown
rule "rule1" 
salience 1 
when 
　　eval(true) --eval(true)：是一个默认的api，true 无条件执行，类似于 while(true)
then 
　　System.out.println("rule1");
end 
```
### 2.no-loop

功能：控制已经执行的规则条件再次满足是否再次执行
值:true/false
示例:　　　　

```markdown
rule "rule1" 
no-loop true 
when 
　　$customer:Customer(name=="张三") 
then 
　　update($customer); 
　　System.out.println("customer name:"+$customer.getName()); 
End
```
 

### 3.activation-group
功能：若干个规则划分成一个组
值：分组名称

示例:　

```markdown
rule "rule2"
 activation-group "test"
 salience 10 
 when
    eval(true)
  then
    System.out.println("rule2 execute");
 end
 
 rule "rule1"
 activation-group "test"
 salience 9
 when
 　　eval(true)
 then
 　　System.out.println("rule1 execute");
end 
```
note:
**如果同一组规则，谁的salience高就执行谁，没有则按顺序执行最后同组最后那个规则**


### 4.declare
作用：
Drools除了可以接受用户在外部向 WorkingMemory当中插入现成的
Fact对象，还允许用户在规则文件当中定义一个新的 Fact对象。

语法：
declare Address
熟悉名 : 类型
end

示例：

```markdown
package com.demo.fact

declare Address
city : String
addressName : String
end

rule "rule1"
salience 2
when
eval(true);
then 
Address add = new Address();
add.setCity("中国上海");
add.setAddressName("中国上海松江区");
insert(add);
end
```

### 5.date-expires

功能：当系统时间<=date-expires后才会触发
值:日期默认格式为dd-MMM-yyyy
可以设置其它时间格式如yyyy-MM-dd,需在代码设置系统时间格式System.setProperty("drools.dateformat", "yyyy-MM-dd");

示例:

```markdown
rule "rule1" 
date-expires "2009-09-27" 
when 
　　eval(true); 
then 
　　System.out.println("rule1 is execution!"); 
end
```

### 6.lock-on-active true

通过这个标签，可以控制当前的规则只会被执行一次，因为一个规则的重复执行不一定是本身触发的，也可能是其他规则触发的，所以这个是no-loop的加强版。。

### 7、date-effective

设置规则的生效时间

### 8、duration

规则定时，duration 3000 3秒后执行规则

### 9、Drools提供了十二中类型比较操作符：

```markdown
> >= < <= == != contains / not contains / memberOf / not memberOf /matches/ not matches
```

#### contains：对比是否包含操作，操作的被包含目标可以是一个复杂对象也可以是一个简单的值。

#### not contains：与contains相反。

#### memberOf：判断某个Fact属性值是否在某个集合中，与contains不同的是他被比较的对象是一个集合，而contains被比较的对象是单个值或者对象。

#### not memberOf：正好相反。

#### matches：正则表达式匹配，与java不同的是，不用考虑'/'的转义问题

#### not matches:正好相反。

### 10、结果部分也有drools提供的方法：

#### insert：往当前workingMemory中插入一个新的Fact对象，会触发规则的再次执行，除非使用no-loop限定；

#### update：更新

#### modify：修改，与update语法不同，结果都是更新操作

#### retract：删除
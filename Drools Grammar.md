## **Drools语法介绍**
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

### 7.date-effective
控制规则只有在到达后才会触发。只有当系统时间>=date-effective 设置的时间值时，规则才会触发执行，否则执行将不执行。在没有设置该属性的情况下，规则随时可以触发，没有这种限制。

date-effective 可接受的日期格式为“dd-MMM-yyyy”，例如2009 年9 月25 日 
如果您的操作系统为中文的，那么应该写成“25-Sep-2009”；如果是英文操作系统“25-九月-2009”
```markdown
rule "rule1"
date-effective " 25-九月-2009"
when
eval(true);
then
System.out.println("rule1 is execution!");
End
```
### 8、duration

规则定时，duration 3000 3秒后执行规则

### 9、Drools提供了十二中类型比较操作符：

```markdown
> >= < <= == != contains / not contains / memberOf / not memberOf /matches/ not matches
```

#### contains
比较操作符contains 是用来检查一个Fact 对象的某个字段（该字段要是一个Collection或是一个Array 类型的对象）是否包含一个指定的对象。

```markdown
when
$order:Order();
$customer:Customer(age >20, orders contains $order);
then
System.out.println($customer.getName());
End
```

contains 只能用于对象的某个Collection/Array 类型的字段与另外一个值进行比较，作为比较的值可以是一个静态的值，也可以是一个变量(绑定变量或者是一个global 对象)。

#### not contains
not contains 作用与contains 作用相反，not contains 是用来判断一个Fact 对象的某个字段（Collection/Array 类型）是不是不包含一个指定的对象，和contains 比较符相同，它也只能用在对象的field 当中。

#### memberOf
memberOf 是用来判断某个Fact 对象的某个字段是否在一个集合（Collection/Array）当中，用法与contains 有些类似，但也有不同，memberOf 的语法如下：Object(fieldName memberOf value[Collection/Array])可以看到memberOf 中集合类型的数据是作为被比较项的，集合类型的数据对象位于memberOf 操作符后面，同时在用memberOf 比较操作符时被比较项一定要是一个变量(绑定变量或者是一个global 对象)，而不能是一个静态值。

#### not memberOf
该操作符与memberOf 作用洽洽相反，是用来判断Fact 对象当中某个字段值是不是中某个集合（Collection/Array）当中，同时被比较的集合对象只能是一个变量（绑定变量或global对象）。

#### matches
matches 是用来对某个Fact 的字段与标准的Java 正则表达式进行相似匹配，被比较的字符串可以是一个标准的Java 正则表达式，但有一点需要注意，那就是正则表达式字符串当中不用考虑“\”的转义问题
```markdown
when
$customer:Customer(name matches "李.*");
then
System.out.println($customer.getName());
end
```
该规则是用来查找所有Customer 对象的name 属性是不是以“李”字开头，如果满足这一条件那么就将该Customer 对象的name 属性打印出来。

#### not matches
与matches 作用相反，是用来将某个Fact 的字段与一个Java 标准正则表达式进行匹配，看是不是能与正则表达式匹配。not matches 使用语法如下：
```markdown
Object(fieldname not matches “正则表达式”)
```
3.4 结果部分
结果部分又被称之为Right Hand Side，简称为RHS，在一个规则当中then 后面部分就是RHS，只有在LHS 的所有条件都满足时RHS 部分才会执行。

RHS 部分是规则真正要做事情的部分，可以将因条件满足而要触发的动作写在该部分当中，在RHS 当中可以使用LHS 部分当中定义的绑定变量名、设置的全局变量、或者是直接编写Java 代码（对于要用到的Java 类，需要在规则文件当中用import 将类导入后方能使用，这点和Java 文件的编写规则相同）。

在规则当中LHS 就是用来放置条件的，所以在RHS 当中虽然可以直接编写Java 代码，但不建议在代码当中有条件判断，如果需要条件判断，那么请重新考虑将其放在LHS 当中，否则就违背了使用规则的初衷。

在 Drools 当中，在RHS 里面，提供了一些对当前Working Memory 实现快速操作的宏函数或宏对象，比如insert/insertLogical、update 和retract 就可以实现对当前Working Memory中的Fact 对象进行新增、删除或者是修改.

3.4.1 insert
insert(new Object());
1
一旦调用insert 宏函数，那么Drools 会重新与所有的规则再重新匹配一次，对于没有设置no-loop 属性为true 的规则，如果条件满足，不管其之前是否执行过都会再执行一次，这个特性不仅存在于insert 宏函数上，后面介绍的update、retract 宏函数同样具有该特性，所以在某些情况下因考虑不周调用insert、update 或retract容易发生死循环，这点大家需要注意.

when
eval(true);
then
Customer cus=new Customer();
cus.setName("张三");
insert(cus);
end
1
2
3
4
5
6
7
3.4.2 update
update 函数意义与其名称一样，用来实现对当前Working Memory 当中的Fact 进行更新，update 宏函数的作用与StatefulSession 对象的update 方法的作用基本相同，都是用来告诉当前的Working Memory 该Fact 对象已经发生了变化。

when
$customer:Customer(name=="张三",age<10);
then
$customer.setAge($customer.getAge()+1);
update($customer);
System.out.println("----------"+$customer.getName());
End
1
2
3
4
5
6
7
3.4.3 retract
retract 用来将Working Memory 当中某个Fact 对象从Working Memory 当中删除，下面就通过一个例子来说明retract 宏函数的用法。

retract($customer);
1
3.4.4 modify
modify 是一个表达式块，它可以快速实现对Fact 对象多个属性进行修改，修改完成后会自动更新到当前的Working Memory 当中。它的基本语法格式如下：

modify(fact-expression){
<修改Fact 属性的表达式>[,<修改Fact 属性的表达式>*]
}
1
2
3
示例
```
when
$customer:Customer(name=="张三",age==20);
then
System.out.println("modify before customer
id:"+$customer.getId()+";age:"+$customer.getAge());
modify($customer){
setId("super man"),
setAge(30)
}
End
```
这里有一点需要注意，那就是和insert、update、retract 对Working Memory 的操作一样，一旦使用了modify 块对某个Fact 的属性进行了修改，那么会导致引擎重新检查所有规则是否匹配条件，而不管其之前是否执行过。
--------------------- 
作者：双斜杠少年 
来源：CSDN 
原文：https://blog.csdn.net/u012373815/article/details/53872025 
版权声明：本文为博主原创文章，转载请附上博文链接！
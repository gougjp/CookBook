# Oracle存储过程

## Oracle存储过程简介

　　存储过程是事先经过编译并存储在数据库中的一段SQL语句的集合，调用存储过程可以简化应用开发人员的很多工作，
减少数据在数据库和应用服务器之间的传输，对于提高数据处理的效率是有好处的。

优点：

- 允许模块化程序设计，就是说只需要创建一次过程，以后在程序中就可以调用该过程任意次。
- 允许更快执行，如果某操作需要执行大量SQL语句或重复执行，存储过程比SQL语句执行的要快。
- 减少网络流量，例如一个需要数百行的SQL代码的操作有一条执行语句完成，不需要在网络中发送数百行代码。
- 更好的安全机制，对于没有权限执行存储过程的用户，也可授权他们执行存储过程。  

## 创建存储过程的语法

```Sql
create [or replace] procedure 存储过程名（param1 in type，param2 out type）
as
   变量1 类型（值范围）;
   变量2 类型（值范围）;
begin
   select count(*) into 变量1 from 表A where列名=param1；
   if (判断条件) then
        select 列名 into 变量2 from 表A where列名=param1；
        dbms_output.Put_line('打印信息');
   elsif (判断条件) then
        dbms_output.Put_line('打印信息');
   else
        raise 异常名（NO_DATA_FOUND）;
   end if;
exception
   when others then
        rollback;
end;
```

参数的几种类型:

- in 是参数的默认模式，这种模式就是在程序运行的时候已经具有值，在程序体中值不会改变。
- out 模式定义的参数只能在过程体内部赋值，表示该参数可以将某个值传递回调用他的过程
- in out 表示该参数可以向该过程中传递值，也可以将某个值传出去

### 举例

不带参数的存储过程:

```Sql
CREATE OR REPLACE PROCEDURE MYDEMO02 
AS 
        name VARCHAR(10);
        age NUMBER(10);
BEGIN
        name := 'xiaoming';--:=则是对属性进行赋值
        age := 18;
        dbms_output.put_line ( 'name=' || name || ', age=' || age );--这条是输出语句
END;
--存储过程调用(下面只是调用存储过程语法)
BEGIN 
    MYDEMO02();
END;
```

带参数的存储过程:

```Sql
CREATE OR REPLACE procedure MYDEMO03(name in varchar,age in int)
AS
BEGIN
      dbms_output.put_line('name='||name||', age='||age);
END;

--存储过程调用
BEGIN 
    MYDEMO03('姜煜',18);
END;
```

出现异常的输出存储过程:

```Sql
CREATE OR REPLACE PROCEDURE MYDEMO04
AS
    age INT;
BEGIN
    age:=10/0;
    dbms_output.put_line(age);
EXCEPTION when others then   --处理异常
    dbms_output.put_line('error');
END;
--调用存储过程
BEGIN
     MYDEMO04;
END;
```

Oracle常见的三大异常分类

- 预定义异常：由PL/SQL定义的异常。由于它们已在standard包中预定义了，因此，这些预定义异常可以直接在程序中使用，而不必再定义部分声明。
- 非预定义异常：用于处理预定义异常所不能处理的Oracle错误。
- 自定义异常：用户自定义的异常，需要在定义部分声明后才能在可执行部分使用。用户自定义异常对应的错误不一定是Oracle错误，例如它可能是一个数据错误。

获取当前时间和总人数:

```Sql
CREATE OR REPLACE PROCEDURE TEST_COUNT01
IS 
    v_total int;
    v_date varchar(20);
BEGIN
    select count(*) into v_total from EMP_TEST WHERE ENAME ='燕小六';  --into是赋值的关键字
    select to_char(sysdate,'yyyy-mm-dd')into v_date FROM EMP_TEST WHERE ENAME ='郭芙蓉';
    DBMS_OUTPUT.put_line('总人数:'||v_total);
    DBMS_OUTPUT.put_line('当前日期'||v_date);
END;

--调用存储过程
BEGIN
    TEST_COUNT01();
END;
```

带输入参数和输出参数的存储过程

```Sql
CREATE OR REPLACE PROCEDURE TEST_COUNT04(v_id in int,v_name out varchar2)
IS
BEGIN
        SELECT ENAME into v_name FROM EMP_TEST WHERE EMPNO = v_id;
        dbms_output.put_line(v_name);
EXCEPTION 
        when no_data_found then dbms_output.put_line('no_data_found');
END;

--调用存储过程
DECLARE
        v_name varchar(200);
BEGIN
        TEST_COUNT04('1002',v_name);
END;
```

查询存储过程以及其他

```Sql
CREATE OR REPLACE PROCEDURE job_day04(de in varchar,name out varchar,App_Code out varchar,error_Msg out varchar)
AS
BEGIN
        SELECT ENAME into name FROM EMP_TEST WHERE ENAME=de;
EXCEPTION WHEN others THEN
        error_Msg:='未找到数据';
END;
--调用存储过程
DECLARE
   de varchar(10);
   ab varchar(10);
   appcode varchar(20);
   ermg varchar(20);
BEGIN
   de:= '张三丰';
   JOB_DAY04(de,ab,appcode,ermg);
   dbms_output.put_line(ermg);
END;
```

向数据库中添加数据的存储过程

```Sql
CREATE OR REPLACE PROCEDURE job_day05(do1 in varchar,dn1 in varchar,eo1 in number,en1 in varchar,App_Code out varchar,error_Msg out varchar)
AS
BEGIN
        INSERT INTO STUDENT(NAME,CLASS)VALUES(do1,dn1); 
        INSERT INTO COMPANY(EMPID,NAME,DEPARNAME)VALUES(eo1,en1,do1);
COMMIT;
EXCEPTION WHEN OTHERS THEN
        App_Code:=-1;
        error_Msg:='插入失败';
END;
--调用存储过程
DECLARE
   do1 varchar(10);
   dn1 varchar(10);
   eo1 number(20);
   App_Code varchar(20);
   error_Msg varchar(20);
BEGIN
    do1:= '张三丰';
    dn1:='新桥';
    eo1:=1001;
    JOB_DAY04(do1,dn1,App_Code,error_Msg);
    dbms_output.put_line(ermg);
END;
```

## 游标的使用

- 游标是SQL的一个内存工作区, 由系统或用户以变量的形式定义. 游标的作用就是用于临时存储从数据库中提取的数据块. 在某些情况下, 需要把数据从存放在磁盘的表中调到计算机内存中进行处理, 最后将处理结果显示出来或最终写回数据库. 这样数据处理的速度才会提高, 否则频繁的磁盘数据交换会降低效率.
- 游标有两种类型: 显式游标和隐式游标. 在前述程序中用到的SELECT...INTO...查询语句, 一次只能从数据库中提取一行数据, 对于这种形式的查询和DML操作, 系统都会使用一个隐式游标. 但是如果要提取多行数据, 就要由程序员定义一个显式游标, 并通过与游标有关的语句进行处理. 显式游标对应一个返回结果为多行多列的SELECT语句.
- 游标一旦打开, 数据就从数据库中传送到游标变量中, 然后应用程序再从游标变量中分解出需要的数据, 并进行处理.
- 在我们进行insert, update, delete和select value into variable的操作中, 使用的是隐式游标.

隐式游标的属性   |   返回值类型       |     意义
--------------   |   ----------       |     ----
SQL%ROWCOUNT     |      整型          |     代表DML语句成功执行的数据行数
SQL%FOUND        |      布尔值        |     值为TRUE代表插入、删除、更新或单行查询操作成功
SQL%NOTFOUND     |      布尔值        |     与SQL%FOUND属性返回值相反
SQL%ISOPEN       |      布尔值        |     布尔型DML执行过程中为真, 结束后为假

修改雇员薪资

```Sql
CREATE OR REPLACE PROCEDURE job_day06(epo in number)
    AS
    BEGIN
        UPDATE EMPS SET SAL=(SAL+100) WHERE empno = epo;
    IF SQL%FOUND  --SQL%FOUND是隐式游标 作用:判断SQL语句是否成功执行,当有作用行时则成功执行为true，否则为false。 6     THEN
        DBMS_OUTPUT.PUT_LINE('成功修改雇员工资！');
    commit;
    else
        DBMS_OUTPUT.PUT_LINE('修改雇员工资失败！');
    END IF;
END;
--调用存储过程
declare
    e_number number;
begin
    e_number:=1001;
    job_day06(e_number);
end;
```

查询编号为1001信息

```Sql
CREATE OR REPLACE PROCEDURE job_day07
IS
BEGIN
DECLARE
    cursor emp_sor is select name,sal FROM EMPS WHERE EMPNO = '1001'; --声明游标
    cname EMPS.NAME%type; --%type 作用: 声明的变量ename与EMPS表的NAME列类型一样 
    csal EMPS.SAL%type;
BEGIN
    open emp_sor; --打开游标
    loop
--     取游标值给变量
    FETCH emp_sor into cname,csal;
    dbms_output.put_line('name:'||cname);
    exit when emp_sor%notfound;
    end loop;
    close emp_sor; --关闭游标
end;
end;
--调用存储过程
BEGIN
    job_day07();
END;
```

## AS和IS的区别

- 在view(视图)中, 只能使用as
- 在corsor(游标)中, 只能使用is
- 对于procedure(存储过程), function(函数), package(程序包)来说, as和is没有区别. 只是使用习惯而已.
---
layout: post
title:  "sql_note_test"
date:   2019-05-11 16==14:42:44 +0000
categories: jekyll update
---

2018-10-23

.1 Embbeded Transacton `TRANSACTION`  `Embbeded`  

    A transaction that embbeded in another transaction,when the inner transaction is ROLLBACK,the @@trancount wounld reduced to 0, in fact no matter how manty layers the transactions,the @trancount would always reduced to 0   
    
.2 When an error occurs in transaction `BEGIN CATCH` `BEGIN TRY` `RAISEERROR` `RETURN` `Run Time` 

    .2.1 Whenever a error was catched, the rest of the batch statements would be ignored, which is similar to RETURN. So a RAISERROR followed by RETURN is meanless in the TRY boundary. 
	.2.2 After the transaction is ROLLBACK,the variable @ remains.
	.2.3 A system parse-time error,however,would not been catched by CATCH, which means you should SET XACT_ABORT ON in case of unrollback error.What about the extra statements when parse-time error occurs? 

An example shows bellow,note that the SET XACT_ABORT is defaut OFF in a sestion
```sql
BEGIN TRAN
INSERT INTO Score(STID,Score) VALUES(101,126)
INSERT INTO Score(STID,Score) VALUES(2,88) /* FOREIN KEY violation occurs here */
COMMIT TRAN
```
The result is:

    [11:07:58]	Started executing query at Line 16
    (1 row affected) 
    Msg 547, Level 16, State 0, Line 3
    INSERT 语句与 FOREIGN KEY 约束"FK__Score__STID__173876EA"冲突。该冲突发生于数据库"TEST"，表"dbo.student", column 'stuid'。
    语句已终止。 
    Total execution time: 00:00:00.008
	
```sql
BEGIN TRY
BEGIN TRAN
INSERT INTO Score(STID,Score) VALUES(101,126) --Unique constraint violation
INSERT INTO Score(STID,Score) VALUES(2,88)  --foreign key referential constraint violation
COMMIT TRAN
END TRY
BEGIN CATCH
PRINT 'ERROR CATCHED: '+ERROR_MESSAGE()
END CATCH
```
After the RAISEERROR and CATCH,the 2nd INSERT would not be ran just as we discuss in `.2.1` The result is：

	[11:31:40]	Started executing query at Line 15
	(0 rows affected)
	ERROR CATCHED: 违反了 UNIQUE KEY 约束“UN_SCORE_SCORE_201811011122”。不能在对象“dbo.Score”中插入重复键。重复键值为 (101)。 
	Total execution time: 00:00:00.010
	
.3 NULL IN SQLSERVER `NULL` `JOIN` 

	"NULL" which dated to the ancient phylosophy ideal, even type the word here is werid: How could a word been typed even if we never ever touch the entity,in other word,how could 
	a non-entity been presented by a entity.It is the same werid situation when the idea come to the computer language such as SQL script:
	.3.1 null as root of every value,could not been rejected by value judgement
	.3.2 null as root of every value,could not been rejected by JOIN statement
	
```sql
    SELECT TOP 5 --找出收款单单号
            AD.ReceiveOrderno AS ADNONULL,CD.ReceiveOrderno AS CDNONULL,O.ReceiveOrderno AS ONONULL
    FROM    dbo.tbReceiveOrder o WITH(NOLOCK)
            LEFT JOIN dbo.tbReceiveOrderCheckDetail cd WITH(NOLOCK) ON o.ReceiveOrderno = cd.ReceiveOrderno
            LEFT JOIN dbo.tbReceiveOrderAdvanceDetail ad WITH(NOLOCK) ON cd.ReceiveOrderno = ad.ReceiveOrderno
    WHERE   cd.SourceOrderno IN (SELECT TOP 100 SourceOrderno FROM dbo.tbReceiveOrderCheckDetail(NOLOCK) )
            --AND o.Status IN(0,1)
            AND ad.ReceiveOrderno IS NULL
```
And the result:

	ADNONULL	CDNONULL	ONONULL
	NULL	5114011400140	5114011400140
	NULL	5114011400143	5114011400143
	NULL	5114082900175	5114082900175
	NULL	5114082900176	5114082900176
.4  quote
    "As you read the rest of this chapter, keep in mind that this area is at the leading edge of database
    processing. Standards, products, and product capabilities are frequently changing. You
    can keep abreast of these changes by checking the following Web sites: www.w3c.org, www.xml.
    org, http://msdn.microsoft.com, www.oracle.com, www.ibm.com, and www.mysql.com. Learning
    as much as you can about XML and database processing is one of the best ways you can prepare
    yourself for a successful career in database processing."

	
.5 `break` `return` `continue`

	一,return:从查询或过程中无条件退出。可在任何时候用于从过程、批处理或语句块中退出。RETURN 之后的语句是不执行的。 如果用于存储过程，RETURN 不能返回空值。如果强制返回则:将生成警告消息并返回 0 值。
    二,break:退出 WHILE 或 IF…ELSE 语句中最里面的循环。将执行出现在 END 关键字后面的任何语句，END 关键字为循环结束标记。IF 测试通常会启动 BREAK，但并不总是如此。
    三,continue:重新开始 WHILE 循环。在 CONTINUE 关键字之后的任何语句都将被忽略。 
    总之:BREAK 或 CONTINUE。BREAK 语句退出最内层的 WHILE 循环，CONTINUE 语句则重新开始 WHILE 循环。例如，如果没有其他行可以处理，程序可能执行 BREAK 语句。例如，如果要继续执行代码，则可以执行 CONTINUE 语句。

.6 `join`
	INNER JOIN: the same meaning as JOIN, the common intersection between left and right tables
	OUTER JOIN: the same meaning as FULLY JOIN, the union intersection between left and right tables 
	CROSS JOIN: Cartesian product betweem left and right tables,the name 'cross join' is qiute contradictory,since it never join any feild between 2 tables
	CROSS APPLY: In SQL-Server, a table's feild is join with a function,like func(table.feild1)
	SEMI-JOIN: Is not available in SQL-Server, but can be implied by EXISTS statements 
	ANTI-SEMI-JOIN:	Is not available in SQL-Server, but can be implied by NOT EXISTS statements 


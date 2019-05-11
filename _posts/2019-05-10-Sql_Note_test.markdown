2018-10-23

.1 Embbeded Transacton `TRANSACTION` `Embbeded`
A transaction that embbeded in another transaction,when the inner transaction is ROLLBACK,the @@trancount wounld reduced to 0, in fact no matter how manty layers the transactions,the @trancount would always reduced to 0

.2 When an error occurs in transaction `BEGIN CATCH` `BEGIN TRY` `RAISEERROR` `RETURN` `Run Time`
.2.1 Whenever a error was catched, the rest of the batch statements would be ignored, which is similar to RETURN. So a RAISERROR followed by RETURN is meanless in the TRY boundary.
.2.2 After the transaction is ROLLBACK,the variable @ remains.
.2.3 A system parse-time error,however,would not been catched by CATCH, which means you should SET XACT_ABORT ON in case of unrollback error.What about the extra statements when parse-time error occurs? 

An example shows bellow,note that the SET XACT_ABORT is defaut OFF in a sestion
```SQL
BEGIN TRAN
INSERT INTO Score(STID,Score) VALUES(101,126)
INSERT INTO Score(STID,Score) VALUES(2,88) /* FOREIN KEY violation occurs here */
COMMIT TRAN
```

The result is:
[11:07:58]	Started executing query at Line 16(1 row affected) Msg 547, Level 16, State 0, Line 3
INSERT 语句与 FOREIGN KEY 约束"FK__Score__STID__173876EA"冲突。该冲突发生于数据库"TEST"，表"dbo.student", column 'stuid'。
语句已终止。 
Total execution time: 00:00:00.008

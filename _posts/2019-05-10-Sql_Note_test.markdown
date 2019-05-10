2018-10-23

.1 Embbeded Transacton `TRANSACTION` `Embbeded`  

    A transaction that embbeded in another transaction,when the inner transaction is ROLLBACK,the @@trancount wounld reduced to 0, in fact no matter how manty layers the transactions,the @trancount would always reduced to 0   
    
.2 When an error occurs in transaction `BEGIN CATCH` `BEGIN TRY` `RAISEERROR` `RETURN` `Run Time` 

    .2.1 Whenever a error was catched, the rest of the batch statements would be ignored, which is similar to RETURN. So a RAISERROR followed by RETURN is meanless in the TRY boundary. 
	.2.2 After the transaction is ROLLBACK,the variable @ remains.
	.2.3 A system parse-time error,however,would not been catched by CATCH, which means you should SET XACT_ABORT ON in case of unrollback error.What about the extra statements when parse-time error occurs? 

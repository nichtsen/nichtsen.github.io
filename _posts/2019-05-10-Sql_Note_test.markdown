2018-10-23

.1 Embbeded Transacton `TRANSACTION` `Embbeded`

    A transaction that embbeded in another transaction,when the inner transaction is ROLLBACK,the @@trancount wounld reduced to 0, in fact no matter how manty layers the transactions,the @trancount would always reduced to 0

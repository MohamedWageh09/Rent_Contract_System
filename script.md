````sql
DECLARE
    CURSOR contract_cur IS (SELECT * FROM CONTRACTS);
  v_installment_id  INSTALLMENTS_PAID.INSTALLMENT_ID%TYPE;
  v_contract_id     INSTALLMENTS_PAID.CONTRACT_ID%TYPE;
  v_installment_date INSTALLMENTS_PAID.INSTALLMENT_DATE%TYPE;
  v_installment_amount INSTALLMENTS_PAID.INSTALLMENT_AMOUNT%TYPE;
  v_paid  INSTALLMENTS_PAID.PAID%TYPE := 0;
  v_total_months NUMBER;
  v_total_years NUMBER;
BEGIN
  FOR c IN contract_cur LOOP
    IF c.CONTRACT_PAYMENT_TYPE = 'MONTHLY' THEN
      v_total_months := TRUNC(MONTHS_BETWEEN(c.CONTRACT_ENDDATE, c.CONTRACT_STARTDATE));
      v_installment_amount := ((c.CONTRACT_TOTAL_FEES - NVL(c.CONTRACT_DEPOSIT_FEES, 0)) / v_total_months);
      
      FOR i IN 1 .. v_total_months LOOP
        v_installment_id := INSTALLMENTS_PAID_SEQ.NEXTVAL;
        v_contract_id := c.CONTRACT_ID;
        v_installment_date := ADD_MONTHS(c.CONTRACT_STARTDATE, i-1);
        INSERT INTO INSTALLMENTS_PAID (INSTALLMENT_ID, CONTRACT_ID, INSTALLMENT_DATE, INSTALLMENT_AMOUNT, PAID)
        VALUES (v_installment_id, v_contract_id, v_installment_date, v_installment_amount, v_paid);
      END LOOP;
      
    ELSIF c.CONTRACT_PAYMENT_TYPE = 'ANNUAL' THEN
      v_total_years := TRUNC(MONTHS_BETWEEN(c.CONTRACT_ENDDATE, c.CONTRACT_STARTDATE)) / 12;
      v_installment_amount := ((c.CONTRACT_TOTAL_FEES - NVL(c.CONTRACT_DEPOSIT_FEES, 0)) / v_total_years);

              FOR i IN 1 .. v_total_years LOOP
                v_installment_id := INSTALLMENTS_PAID_SEQ.NEXTVAL;
                v_contract_id := c.CONTRACT_ID;
                v_installment_date := ADD_MONTHS(c.CONTRACT_STARTDATE, (i-1) * 12);
                INSERT INTO INSTALLMENTS_PAID (INSTALLMENT_ID, CONTRACT_ID, INSTALLMENT_DATE, INSTALLMENT_AMOUNT, PAID)
                VALUES (v_installment_id, v_contract_id, v_installment_date, v_installment_amount, v_paid);
              END LOOP;
      
       ELSIF c.CONTRACT_PAYMENT_TYPE = 'QUARTER' THEN
      v_total_months := TRUNC(MONTHS_BETWEEN(c.CONTRACT_ENDDATE, c.CONTRACT_STARTDATE)) / 3;
      v_installment_amount := ((c.CONTRACT_TOTAL_FEES - NVL(c.CONTRACT_DEPOSIT_FEES, 0)) / v_total_months);

              FOR i IN 1 .. v_total_months LOOP
                v_installment_id := INSTALLMENTS_PAID_SEQ.NEXTVAL;
                v_contract_id := c.CONTRACT_ID;
                v_installment_date := ADD_MONTHS(c.CONTRACT_STARTDATE, (i-1)* 3);
                INSERT INTO INSTALLMENTS_PAID (INSTALLMENT_ID, CONTRACT_ID, INSTALLMENT_DATE, INSTALLMENT_AMOUNT, PAID)
                VALUES (v_installment_id, v_contract_id, v_installment_date, v_installment_amount, v_paid);
              END LOOP;
      
       ELSIF c.CONTRACT_PAYMENT_TYPE = 'HALF_ANNUAL' THEN
      v_total_months := TRUNC(MONTHS_BETWEEN(c.CONTRACT_ENDDATE, c.CONTRACT_STARTDATE)) / 6;
      v_installment_amount := ((c.CONTRACT_TOTAL_FEES - NVL(c.CONTRACT_DEPOSIT_FEES, 0)) / v_total_months);

              FOR i IN 1 .. v_total_months LOOP
                v_installment_id := INSTALLMENTS_PAID_SEQ.NEXTVAL;
                v_contract_id := c.CONTRACT_ID;
                v_installment_date := ADD_MONTHS(c.CONTRACT_STARTDATE, (i-1)* 6);
        INSERT INTO INSTALLMENTS_PAID (INSTALLMENT_ID, CONTRACT_ID, INSTALLMENT_DATE, INSTALLMENT_AMOUNT, PAID)
        VALUES (v_installment_id, v_contract_id, v_installment_date, v_installment_amount, v_paid);
      END LOOP;
    END IF;
  END LOOP;
END;
````

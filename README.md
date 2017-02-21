
data tmp;
set USERPH.DP_PHA_METRO_CLAIM_BASE;
run;
  
proc sql;
   alter table USERPH.DP_PHA_METRO_CLAIM_BASE
      drop SAMPLE;	  
quit;

proc sql;
   alter table USERPH.DP_PHA_METRO_CLAIM_BASE
      add SAMPLE_CLAIM_RANGE num label='SAMPLE RANGE';	  
quit;

proc sort data=tmp;
BY CLAIM_PAID_AMT1;
run;


proc rank data=tmp out=tmp_1 ties=low groups=100;
var CLAIM_PAID_AMT1;
ranks r_CLAIM_PAID_AMT1;
run;

proc print data=tmp_1;
run;

proc sql;
   update tmp_1
	set SAMPLE_CLAIM_RANGE = r_CLAIM_PAID_AMT1;
quit;
 
proc sort data=tmp_1;
by r_CLAIM_PAID_AMT1;
run;
 
data tmp_1;
set tmp_1;
by r_CLAIM_PAID_AMT1;
retain pct1_CLAIM_PAID_AMT1;
if first.r_CLAIM_PAID_AMT1 then pct1_CLAIM_PAID_AMT1=int(CLAIM_PAID_AMT1);
else pct1_CLAIM_PAID_AMT1=pct1_CLAIM_PAID_AMT1;
run;
 
proc freq data=tmp_1;
table pct1_CLAIM_PAID_AMT1;
run;


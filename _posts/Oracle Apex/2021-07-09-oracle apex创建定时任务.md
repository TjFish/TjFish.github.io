---
title: Oracle创建定时任务
date: 2021-07-09
categories: [Oracle Apex]
tags: [Oracle Apex]  
img_path: /assets/img/
---

使用Oracle创建定时任务，配合发送邮件代码，实现每日发送汇报邮件。

> 可以参考 [Oracle Apex发送邮件](https://tjfish.github.io/posts/oracle-apex%E6%89%8B%E5%8A%A8%E5%8F%91%E9%80%81%E9%82%AE%E4%BB%B6/) ，配合本文实现定时发送邮件。
{: .prompt-tip }


## 创建不带参数Job

每天早上8点执行过程DAILY_REPORT，job_id由数据库分配

```
DECLARE  
job_id  number;  
BEGIN  
SYS.DBMS_JOB.SUBMIT  
( job => job_id   
,what => 'DAILY_REPORT'  
,next_date => sysdate  
,interval => 'TRUNC(SYSDATE + 1) + （8*60）/(24*60)'  ---每天早上8:00
,no_parse => TRUE  
);  
SYS.DBMS_OUTPUT.PUT_LINE('Job Number is: ' || to_char(job_id));  
COMMIT;  
END;  
/ 
```

## 创建带参数Job

```
DECLARE  
job_id  number;  
BEGIN  
SYS.DBMS_JOB.SUBMIT  
( job => job_id   
,what => 'DAILY_REPORT("1234567@qq.com")'  
,next_date => sysdate  
,interval => 'TRUNC(SYSDATE + 1) + （8*60）/(24*60)'  ---每天早上8:00
,no_parse => TRUE  
);  
SYS.DBMS_OUTPUT.PUT_LINE('Job Number is: ' || to_char(job_id));  

COMMIT;  
END;  
/ 
```

对于字符串参数，需要加二个单引号，类似 'P_XXX(''参数值'');

## Oracle Job 管理

### 查看Job

```
select * from user_jobs; --可以查看当前用户所有Job
select * from all_jobs;  --查看所有Job
```

### 删除Job

```
begin
  dbms_job.remove(12); --12为具体的job ID，可以通过select * from user_jobs查询得到
end;
```

### 立即执行Job

```
begin
    dbms_job.run(12);--运行指定Job
end;
```

## 执行频率

下面是一些常用的执行频率，通过设置Job的interval参数修改

- 每天运行一次 'SYSDATE + 1'
- 每小时运行一次 'SYSDATE + 1/24'
- 每10分钟运行一次
  'SYSDATE + 10/（60*24）'
- 每30秒运行一次
  'SYSDATE + 30/(60*24*60)'
- 每隔一星期运行一次
  'SYSDATE + 7'
- 每个月最后一天运行一次
  'TRUNC(LAST_DAY(ADD_MONTHS(SYSDATE,1))) + 23/24'
- 每年1月1号零时
  'TRUNC(LAST_DAY(TO_DATE(EXTRACT(YEAR FROM SYSDATE)||'12'||'01','YYYY-MM-DD'))+1)'
- 每天午夜12点
  'TRUNC(SYSDATE + 1)'
- 每天早上8点30分
  'TRUNC(SYSDATE + 1) + (8*60+30)/(24*60)'
- 每星期二中午12点
  'NEXT_DAY(TRUNC(SYSDATE ), ''TUESDAY'' ) + 12/24'
- 每个月第一天的午夜12点
  'TRUNC(LAST_DAY(SYSDATE ) + 1)'
- 每个月最后一天的23点
  'TRUNC (LAST_DAY (SYSDATE)) + 23 / 24'
- 每个季度最后一天的晚上11点
  'TRUNC(ADD_MONTHS(SYSDATE + 2/24, 3 ), 'Q' ) -1/24'
- 每星期六和日早上6点10分
  'TRUNC(LEAST(NEXT_DAY(SYSDATE, ''SATURDAY"), NEXT_DAY(SYSDATE, "SUNDAY"))) + (6*60+10)/(24*60)'
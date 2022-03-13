---
title: Oracle Apex 手动发送邮件
date: 2021-07-08
categories: [Oracle Apex]
tags: [Oracle Apex]  
img_path: /assets/img/
---

## 开启ACL权限

首先要开启用户的ACL权限（ Access Control List）

**新建ACL配置文件**

```sql
dbms_network_acl_admin.create_acl(acl         => 'email.xml', --xml名称
                                      DESCRIPTION => 'email',           --描述
                                      principal   => 'APEX_190200',    --数据库名,大小写敏感
                                      is_grant    => TRUE,
                                      PRIVILEGE   => 'connect',    --权限名
                                      start_date  => NULL,
                                      end_date    => NULL);
```

**ACL分配给用户**

```sql
 dbms_network_acl_admin.add_privilege(acl        => 'email.xml', --同上xml名称
                                         principal  => 'APEX_190200', --数据库名
                                         is_grant   => TRUE,
                                         privilege  => 'connect', --权限名
                                         start_date => null,
                                         end_date   => null);
```

**ACL注册**

```sql
dbms_network_acl_admin.assign_acl (       -- 该段命令意思是允许访问acl名为utl_sendmail.xml下授权的用户，使用oracle网络访问包，所允许访问的目的主机，及其端口范围。
    acl        => 'email.xml',
    host       => '*'            -- ip地址或者域名
  );
```

**使用下面命令查看是否分配成功**

```sql
SELECT * From  dba_network_acls;

SELECT acl,
       principal,
       privilege,
       is_grant,
       TO_CHAR(start_date, 'DD-MON-YYYY') AS start_date,
       TO_CHAR(end_date, 'DD-MON-YYYY') AS end_date
From dba_network_acl_privileges;
```

**如果要将权限分配给更多的用户**, 执行下面命令

```
EXECUTE DBMS_NETWORK_ACL_ADMIN.ADD_PRIVILEGE('email.xml','APEX_190200', TRUE, 'connect');
```

## 发送邮件

上面ACL部分就完成了，这时候APEX报错应该就不会再是`ORA-24247: 网络访问被访问控制列表 (ACL) 拒绝`

要使用plsql发送邮件,一般会用到UTL.SMTP和UTL.TCP的函数包。Oracle 默认用户是不能使用这两个函数包的，所以要**手动赋予用户权限**

```sql
grant execute on UTL_TCP to APEX_190200;
grant execute on UTL_SMTP to APEX_190200;
```

上面APEX的发送邮件配置就完成了，下面是plsql 发送邮件的示例代码,支持HTML

```sql
create or replace PROCEDURE send_mail (p_to        IN VARCHAR2,
                                       p_subject   IN VARCHAR2,
                                       p_text_msg  IN VARCHAR2 DEFAULT NULL,
                                       p_html_msg  IN VARCHAR2 DEFAULT NULL)
AS
    l_mail_conn   UTL_SMTP.connection;
    l_boundary    VARCHAR2(50) := '----=*#abc1234321cba#*=';
    --下面四个变量请根据实际邮件服务器进行赋值
    v_smtphost           VARCHAR2(30 )       := 'smtp.qq.com';                  --SMTP服务器地址(hotmail为smtp.live.com,测试未通过)
    v_smtpport           number(5 )          := 587;                                       --smtp服务端口
    v_user               VARCHAR2(30 )       := '1509390230@qq.com' ;          --登录SMTP服务器的用户名
    v_pass               VARCHAR2(20 )       := 'tqxobadrrneohdah';                             --登录SMTP服务器的密码
    p_from               VARCHAR2(20 )       := '1509390230@qq.com'; 
BEGIN
  l_mail_conn := UTL_SMTP.open_connection(v_smtphost, v_smtpport);
  --是用 ehlo() 而不是 helo() 函数
  UTL_SMTP.ehlo(l_mail_conn, v_smtphost);
  -- smtp服务器登录校验
  UTL_SMTP.command(l_mail_conn, 'AUTH LOGIN');
  UTL_SMTP.command(l_mail_conn,UTL_RAW.cast_to_varchar2(UTL_ENCODE.base64_encode(UTL_RAW.cast_to_raw(v_user))));
  UTL_SMTP.command(l_mail_conn,UTL_RAW.cast_to_varchar2(UTL_ENCODE.base64_encode(UTL_RAW.cast_to_raw(v_pass))));

  UTL_SMTP.mail(l_mail_conn, p_from);
  UTL_SMTP.rcpt(l_mail_conn, p_to);

  UTL_SMTP.open_data(l_mail_conn);

  UTL_SMTP.write_data(l_mail_conn, 'Date: ' || TO_CHAR(SYSDATE, 'DD-MON-YYYY HH24:MI:SS') || UTL_TCP.crlf);
  UTL_SMTP.write_data(l_mail_conn, 'To: ' || p_to || UTL_TCP.crlf);
  UTL_SMTP.write_data(l_mail_conn, 'From: ' || p_from || UTL_TCP.crlf);
  UTL_SMTP.write_raw_data(l_mail_conn, UTL_RAW.cast_to_raw('Subject: ' || p_subject || UTL_TCP.crlf));
  UTL_SMTP.write_data(l_mail_conn, 'Reply-To: ' || p_from || UTL_TCP.crlf);
  UTL_SMTP.write_data(l_mail_conn, 'MIME-Version: 1.0' || UTL_TCP.crlf);
  UTL_SMTP.write_data(l_mail_conn, 'Content-Type: multipart/alternative; boundary="' || l_boundary || '"' || UTL_TCP.crlf || UTL_TCP.crlf);

  IF p_text_msg IS NOT NULL THEN
    UTL_SMTP.write_data(l_mail_conn, '--' || l_boundary || UTL_TCP.crlf);
    UTL_SMTP.write_data(l_mail_conn, 'Content-Type: text/plain; charset="utf8"' || UTL_TCP.crlf || UTL_TCP.crlf);

    UTL_SMTP.write_raw_data(l_mail_conn, UTL_RAW.cast_to_raw(p_text_msg));
    UTL_SMTP.write_data(l_mail_conn, UTL_TCP.crlf || UTL_TCP.crlf);
  END IF;

  IF p_html_msg IS NOT NULL THEN
    UTL_SMTP.write_data(l_mail_conn, '--' || l_boundary || UTL_TCP.crlf);
    UTL_SMTP.write_data(l_mail_conn, 'Content-Type: text/html; charset="utf8"' || UTL_TCP.crlf || UTL_TCP.crlf);
    UTL_SMTP.write_raw_data(l_mail_conn, UTL_RAW.cast_to_raw(p_html_msg));
    UTL_SMTP.write_data(l_mail_conn, UTL_TCP.crlf || UTL_TCP.crlf);
  END IF;

  UTL_SMTP.write_data(l_mail_conn, '--' || l_boundary || '--' || UTL_TCP.crlf);
  UTL_SMTP.close_data(l_mail_conn);

  UTL_SMTP.quit(l_mail_conn);
END;
/
```

**使用示例**

```sql
DECLARE
  l_html VARCHAR2(32767);
BEGIN
  l_html := '

      <title>Test HTML message</title>


      <p>This is a <b>HTML</b> <i>version</i> of the test message.</p>
      <p><img src="https://github.com/TjFish/Image-Hosting/raw/master/images/background/bg1.jpg">

  ';

  send_mail(p_to        => '1509390230@qq.com',
            p_subject   => 'Test Message',
            p_text_msg  => 'This is a test message.',
            p_html_msg  => l_html);
END;
/
```




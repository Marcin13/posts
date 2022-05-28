---
author: "Marcin"
title: "History of PHP"
date: 2022-03-10T20:50:29Z
description: "PHP Tools, FI, Construction Kit, and PHP/FI"
aliases: []
draft: false
categories: ['PHP']
tags: ['PHP']
ShowToc: true
TocOpen: false
searchHidden: false
---

PHP's development began in _1994_ when **Rasmus Lerdorf** wrote several Common Gateway Interface (CGI) programs in C, 
which he used to maintain his personal homepage. He extended them to work with web forms and to communicate with databases, 
and called this implementation "Personal Home Page/Forms Interpreter" or PHP/FI.

PHP/FI could be used to build simple, dynamic web applications. To accelerate bug reporting and improve the code, 
**Lerdorf** initially announced the release of PHP/FI as "Personal Home Page Tools (PHP Tools) version 1.0" on the 
Usenet discussion group [comp.infosystems.www.authoring.cgi](comp.infosystems.www.authoring.cgi) on June 8, 1995. This release already had the basic 
functionality that PHP has today. This included Perl-like variables, form handling, 
and the ability to embed HTML. The syntax resembled that of Perl, but was simpler, more limited and less consistent.

An example of the early PHP syntax PHP development began in _1994_ when **Rasmus Lerdorf** wrote several 
Common Gateway Interface (CGI) programs in C, which he used to maintain his personal homepage. 
He extended them to work with web forms and to communicate with databases, 
and called this implementation "Personal Home Page/Forms Interpreter" or PHP/FI.

### Example #1 Example PHP/FI Code
```php
<!--include /text/header.html-->

<!--getenv HTTP_USER_AGENT-->
<!--ifsubstr $exec_result Mozilla-->
Hey, you are using Netscape!<p>
<!--endif-->

<!--sql database select * from table where user='$username'-->
<!--ifless $numentries 1-->
Sorry, that record does not exist<p>
<!--endif exit-->
Welcome <!--$user-->!<p>
You have <!--$index:0--> credits left in your account.<p>

<!--include /text/footer.html-->
```

[comp.infosystems.www.authoring.cgi]: http://comp.infosystems.www.authoring.cgi
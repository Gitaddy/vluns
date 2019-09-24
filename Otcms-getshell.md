# Code Execution vulnerability in Otcms v3.85 #

Description:Otcms v3.85 have Code Execution via Admin Panel  create a php file,and then get a webshell. 

## Technical Description: ##

Use the Google Chrome open this test site.download this version（```http://d.otcms.com/php/OTCMS_PHP_V3.85.rar```) and build a test site.

![](004.png)

And we can login in Admin Panel,absolute path information leaks at the database backup：

![](05.png)

Lines 82-92 in the /otcms/inc/classZip.php file, print the absolute path directly, and reveal the website path information.So we can get a absolute path.

![](06.png)


And then Code Execution can get a webshell.The following SQL query page we can execute SQL statement to Export php files.

![](07.png)

But locate in otcms/admin/sysCheckFile_deal.php has the following code lines 760-772, which limits "into outfile" and limits the hexadecimal conversion.

![](09.png)

So the export php file failed.

![](08.png)

Finally,we can bypass limit via annotation.

![](10.png)

Code execution succeeded and create a new php file.

![](11.png)

phpinfo file success created.

![](12.png)
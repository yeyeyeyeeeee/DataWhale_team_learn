在创建数据库之前需要修改数据库密码
  mysql5.7.9之后，就没有了password函数，所以，使用传统的password()函数修改root密码的话，就会提示sql错误。
 我百度查询修改密码的命令行方式大多是mysql5.7.9之前的修改方式，报错。
 set password for root@localhost=password('root');
 报错：ERROR 1064 (42000): You have an error in your SQL syntax; 
      check the manual that corresponds to your MySQL server version for the right syntax to use near 'password('root')' at line 1
 修改密码命令：ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'r00t';


作业1
  进入数据库C:\web\mysql-8.0.17\bin
  C:\web\mysql-8.0.17\bin>mysql -u root -p
    创建数据库CREATE DATABASE Test1;
    进入数据库use Test1;
    创建表CREATE TABLE email (ID INT NOT NULL PRIMARY KEY,Email VARCHAR(255));
    插入数据 INSERT INTO email VALUES('1','a@b.com');
            INSERT INTO email VALUES('2','c@d.com');
            INSERT INTO email VALUES('3','a@b.com');
     选择符合条件的内容
     select Email from email group by Email having count(Email)>1;
     
作业2
   进入数据库C:\web\mysql-8.0.17\bin
  C:\web\mysql-8.0.17\bin>mysql -u root -p
    创建数据库CREATE DATABASE Test2;
    进入数据库use Test2;
    创建表CREATE TABLE World (
          name VARCHAR(50) NOT NULL,
          continent VARCHAR(50) NOT NULL,
          area INT NOT NULL,
          population INT NOT NULL,
          gdp INT NOT NULL
          );
    插入数据 
            INSERT INTO World
              VALUES('Afghanistan','Asia',652230,25500100,20343000);
            INSERT INTO World 
              VALUES('Albania','Europe',28748,2831741,12960000);
            INSERT INTO World 
              VALUES('Algeria','Africa',2381741,37100000,188681000);
            INSERT INTO World
              VALUES('Andorra','Europe',468,78115,3712000);
            INSERT INTO World
              VALUES('Angola','Africa',1246700,20609294,100990000);
     输出表中全部数据
            select*from World
     选择符合条件的内容
     SELECT name,population,area
     FROM World
     WHERE population > 3000000 or (population > 25000000 and gdp > 20000000);
     

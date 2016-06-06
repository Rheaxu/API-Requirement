# API-Requirement
App API Requirement

问题
1. 8个request
2. path中不能有动词
3. token: 考虑是否用middleware检查token
4. 密码不能明文
5. sql要用prepare statement
如
conn.query("insert n, l, k into table UIO values ($1, $2, $3)", [23, "asdf", 09]). end(function(...) {...})
但这个问题在java平台上不会有，因为要用querydsl
6. 图片：二进制存放在s3中，生成url存在数据库中


Restful API requirement
1. login
2. signup
3. logout
4. "forgot password"
5. get user's profile
6. modify user's profile
7. upload picture(avatar)

以下是用户信息的数据结构: （经过讨论，我们决定username 和 email 之间只保留 email）

Id: INT(11)
password: VARCHAR(100) (我们请求用户信息的时候不需要返回此项)
firstName: VARCHAR(100)
lastName: VARCHAR(100)
email: VARCHAR(100)
gender: VARCHAR(45)
birthday: DATE
avatar: VARCHAR (以url形式返回给ios)  图片数据如何存储请自行决定
phone: VARCHAR(20)
other: TEXT

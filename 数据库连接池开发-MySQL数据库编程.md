## VS 2019开发
![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683186972491-911b507c-a0c4-495c-9932-5d71ccdc52ee.png#averageHue=%23c2e9cc&clientId=u3d1ea75f-99ed-4&from=paste&height=596&id=uf70528f4&originHeight=983&originWidth=1177&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=139034&status=done&style=none&taskId=u6345be4a-a467-49f4-9aa6-5dd4001671d&title=&width=713.3332921037794) 
这里的MySQL数据库编程直接采用oracle公司提供的MySQL C/C++客户端开发包，在VS上需要进行相应的头文件和库文件的配置，如下：
1.右键项目 - C/C++ - 常规 - 附加包含目录，填写mysql.h头文件的路径
2.右键项目 - 链接器 - 常规 - 附加库目录，填写libmysql.lib的路径
3.右键项目 - 链接器 - 输入 - 附加依赖项，填写libmysql.lib库的名字
4.把libmysql.dll动态链接库（Linux下后缀名是.so库）放在工程目录下
![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683187081010-20595b47-cbbe-4874-bb07-72e42f99f33a.png#averageHue=%23f1efee&clientId=u2463628f-40cc-4&from=paste&height=496&id=u295ef076&originHeight=818&originWidth=1180&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=96692&status=done&style=none&taskId=u2cd135e4-93dd-4a7c-a9e2-c69fa8bafd3&title=&width=715.1514738168731)![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683187092884-604f4268-37be-4ccf-8102-5b5aa71b8b6b.png#averageHue=%23f2f1f0&clientId=u2463628f-40cc-4&from=paste&height=496&id=u2f434718&originHeight=818&originWidth=1180&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=84772&status=done&style=none&taskId=u1e10e756-13a3-4eaf-910e-3c59295d8b3&title=&width=715.1514738168731)![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683187117543-3ca0d794-e248-4e5a-b5e4-a2c3f76d11e3.png#averageHue=%23ededec&clientId=u2463628f-40cc-4&from=paste&height=496&id=uf992ff09&originHeight=818&originWidth=1180&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=72026&status=done&style=none&taskId=u2c3a984a-4bc6-4c50-8c44-c389d7330c0&title=&width=715.1514738168731)![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683187167422-e9720c4f-2f15-428c-9ce3-1a7419692510.png#averageHue=%23252423&clientId=u2463628f-40cc-4&from=paste&height=330&id=u7a6cbbf0&originHeight=544&originWidth=1213&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=66402&status=done&style=none&taskId=uc9aa50f3-7b9c-4d87-a9c3-ea7ce13c18d&title=&width=735.1514726609043)
## connection.h的书写
```cpp
#pragma once
#include <mysql.h>
#include <string>
#include <ctime>
using namespace std;

/*
实现MySQL数据库的操作
*/
class Connection
{
public:
	// 初始化数据库连接
	Connection();
	// 释放数据库连接资源
	~Connection();
	// 连接数据库
	bool connect(string ip, 
		unsigned short port, 
		string user, 
		string password,
		string dbname);
	// 更新操作 insert、delete、update
	bool update(string sql);
	// 查询操作 select
	MYSQL_RES* query(string sql);

	// 刷新一下连接的起始的空闲时间点
	void refreshAliveTime() { _alivetime = clock(); }
	// 返回存活的时间
	clock_t getAliveeTime()const { return clock() - _alivetime; }
private:
	MYSQL *_conn; // 表示和MySQL Server的一条连接
	clock_t _alivetime; // 记录进入空闲状态后的起始存活时间
};
```
## public.h的书写
```cpp
#pragma once

#define LOG(str) \
	cout << __FILE__ << ":" << __LINE__ << " " << \
	__TIMESTAMP__ << " : " << str << endl;
```
## connection.cpp的书写
```cpp
#include "pch.h"
#include "public.h"
#include "Connection.h"
#include <iostream>
using namespace std;

Connection::Connection()
{
	// 初始化数据库连接
	_conn = mysql_init(nullptr);
}

Connection::~Connection()
{
	// 释放数据库连接资源
	if (_conn != nullptr)
		mysql_close(_conn);
}

bool Connection::connect(string ip, unsigned short port, 
	string username, string password, string dbname)
{
	// 连接数据库
	MYSQL *p = mysql_real_connect(_conn, ip.c_str(), username.c_str(),
		password.c_str(), dbname.c_str(), port, nullptr, 0);
	return p != nullptr;
}

bool Connection::update(string sql)
{
	// 更新操作 insert、delete、update
	if (mysql_query(_conn, sql.c_str()))
	{
		LOG("更新失败:" + sql);
		return false;
	}
	return true;
}

MYSQL_RES* Connection::query(string sql)
{
	// 查询操作 select
	if (mysql_query(_conn, sql.c_str()))
	{
		LOG("查询失败:" + sql);
		return nullptr;
	}
	return mysql_use_result(_conn);
}
```
## 验证
```cpp
#include "pch.h"
#include <iostream>
using namespace std;
#include "Connection.h"
#include "CommonConnectionPool.h"


int main()
{

	Connection conn;
	char sql[1024] = { 0 };
	sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
		"zhang san", 20, "male");
	conn.connect("127.0.0.1", 3306, "root", "toor", "chat");
	conn.update(sql);


	return 0;
}


```

---

查询步骤：
`mysql -u*** -p***;`
`show databases;`
`use ****`;
`show tables`;
查询前十行数据
`select * from user limit 0,10;`
![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683187584933-013a9e87-2671-42c1-8878-d48e81fc18cb.png#averageHue=%23100f0e&clientId=u2463628f-40cc-4&from=paste&height=297&id=u0943a4de&originHeight=490&originWidth=769&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=28028&status=done&style=none&taskId=ub466b860-5bee-4aa5-8e72-3059ef48373&title=&width=466.06057912303004)

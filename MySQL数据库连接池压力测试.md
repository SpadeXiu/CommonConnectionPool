## 压力测试

![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683187942352-c8ebd313-1007-4be6-aa12-28db39941c3f.png#averageHue=%2311100f&clientId=ue250eacf-d2eb-4&from=paste&height=265&id=u183c68a3&originHeight=437&originWidth=856&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=26713&status=done&style=none&taskId=u4c3f2c03-372a-428e-85d0-331650efb6a&title=&width=518.7878488027486)
## 第一组（1000）测试
（模拟1000个人访问数据库） **不使用连接池**
```cpp
#include "pch.h"
#include <iostream>
#include "Connection.h"
#include "CommonConnectionPool.h"
using namespace std;

int main()
{
	clock_t begin = clock();
	for (int i = 0; i < 1000; ++i)
	{
		Connection conn;
		char sql[1024] = { 0 };
		sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
			"zhang san", 20, "male");
		conn.connect("127.0.0.1", 3306, "root", "toor", "chat");
		conn.update(sql);
	}
	clock_t end = clock();
	cout << (end - begin) << "ms" << endl;
	return 0;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683188793680-4f334f38-881a-4138-924b-0daa1317a09f.png#averageHue=%230f0d0d&clientId=ue250eacf-d2eb-4&from=paste&height=216&id=u392b0529&originHeight=357&originWidth=993&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=34112&status=done&style=none&taskId=uf4d732a0-b2e4-4142-bc1b-bdad84f51f3&title=&width=601.8181470340297)
![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683189354885-6ab7d8a4-d90e-4522-a959-26a7c18708f4.png#averageHue=%230e0d0d&clientId=ue250eacf-d2eb-4&from=paste&height=248&id=u194381a7&originHeight=409&originWidth=922&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=35037&status=done&style=none&taskId=u47e0c46a-c81a-4402-9234-a3d209f26e1&title=&width=558.787846490811)
![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683189369971-60ec0c42-5d81-4eff-9132-24b9050ed2c1.png#averageHue=%230f0e0d&clientId=ue250eacf-d2eb-4&from=paste&height=193&id=u99b6454f&originHeight=319&originWidth=921&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=34297&status=done&style=none&taskId=uef32a6e5-ba5e-44a5-8dff-712b7ad8151&title=&width=558.1817859197798)
**我们接下来使用连接池**
```cpp
#include "pch.h"
#include <iostream>
#include "Connection.h"
#include "CommonConnectionPool.h"
using namespace std;

int main()
{
	clock_t begin = clock();
	ConnectionPool* cp = ConnectionPool::getConnectionPool();
	for (int i = 0; i < 1000; ++i)
	{

		shared_ptr<Connection> sp = cp->getConnection();
		char sql[1024] = { 0 };
		sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
			"zhang san", 20, "male");
		sp->update(sql);
	}
	clock_t end = clock();
	cout << (end - begin) << "ms" << endl;
	return 0;
}
```
**我们重复测试3次结果如下**![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683189032864-27f53552-e6cb-4b44-bf55-ebaafe5d4082.png#averageHue=%230f0e0d&clientId=ue250eacf-d2eb-4&from=paste&height=235&id=u894768c2&originHeight=387&originWidth=914&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=33717&status=done&style=none&taskId=u95296e98-647f-459e-ae25-7d1a36f12b0&title=&width=553.939361922561)![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683189042637-f6abfbdd-0e99-4589-a08f-b3d7ae1a91d1.png#averageHue=%23100e0d&clientId=ue250eacf-d2eb-4&from=paste&height=161&id=u28e81b11&originHeight=266&originWidth=916&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=34284&status=done&style=none&taskId=u71947304-81be-44f2-b1a5-6a6ea51f4b9&title=&width=555.1514830646236)![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683189054002-9cd13727-67f8-4d6b-9d35-fcb6844616bf.png#averageHue=%230f0e0d&clientId=ue250eacf-d2eb-4&from=paste&height=219&id=u0ca9d476&originHeight=362&originWidth=917&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=34197&status=done&style=none&taskId=u48916b51-dfe8-402f-afe7-c25557d6c1f&title=&width=555.7575436356548)
## 第二组（5000）测试
**不使用连接池**
```cpp
int main()
{

	clock_t begin = clock();
	for (int i = 0; i < 5000; ++i)
	{
		Connection conn;
		char sql[1024] = { 0 };
		sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
			"zhang san", 20, "male");
		conn.connect("127.0.0.1", 3306, "root", "toor", "chat");
		conn.update(sql);
	}
	clock_t end = clock();
	cout << (end - begin) << "ms" << endl;
	return 0;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683189510073-2af8940c-fe2f-42f6-b57a-41dc90444742.png#averageHue=%230f0e0d&clientId=ue250eacf-d2eb-4&from=paste&height=236&id=ubbe0688d&originHeight=390&originWidth=861&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=35708&status=done&style=none&taskId=u4676dcfb-3431-4e95-835c-14a9de5a91c&title=&width=521.8181516579049)
**使用连接池**
```cpp
int main()
{
	clock_t begin = clock();
	ConnectionPool* cp = ConnectionPool::getConnectionPool();
	for (int i = 0; i < 5000; ++i)
	{

		shared_ptr<Connection> sp = cp->getConnection();
		char sql[1024] = { 0 };
		sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
			"zhang san", 20, "male");
		sp->update(sql);
	}
	clock_t end = clock();
	cout << (end - begin) << "ms" << endl;
	return 0;
}
//使用连接池模拟5000个人访问数据库 
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683189579719-d3c877e3-0f99-41ce-91e2-4863ab65a8af.png#averageHue=%23100e0d&clientId=ue250eacf-d2eb-4&from=paste&height=183&id=u857807e0&originHeight=302&originWidth=866&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=33810&status=done&style=none&taskId=u8fbfe951-9fa6-49c0-a76a-cf39aeffb4a&title=&width=524.8484545130611)
## 第三组（10000）测试
**不使用连接池**
```cpp
int main()
{

	clock_t begin = clock();
	for (int i = 0; i < 10000; ++i)
	{
		Connection conn;
		char sql[1024] = { 0 };
		sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
			"zhang san", 20, "male");
		conn.connect("127.0.0.1", 3306, "root", "toor", "chat");
		conn.update(sql);
	}
	clock_t end = clock();
	cout << (end - begin) << "ms" << endl;
	return 0;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683189702479-3645bc49-346b-4074-96a0-bf508930bb5f.png#averageHue=%23100e0d&clientId=ue250eacf-d2eb-4&from=paste&height=187&id=u390c13eb&originHeight=309&originWidth=830&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=33761&status=done&style=none&taskId=ue8e127b8-26f3-41de-8e9a-fb72aab4f31&title=&width=503.0302739559362)
**带有连接池的**
```cpp
int main()
{
	clock_t begin = clock();
	ConnectionPool* cp = ConnectionPool::getConnectionPool();
	for (int i = 0; i < 10000; ++i)
	{

		shared_ptr<Connection> sp = cp->getConnection();
		char sql[1024] = { 0 };
		sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
			"zhang san", 20, "male");
		sp->update(sql);
	}
	clock_t end = clock();
	cout << (end - begin) << "ms" << endl;
	return 0;
}
 //使用连接池模拟10000个人访问数据库 
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683189763496-8cb3e3e9-18a1-427e-a610-ad6bdb5a35bb.png#averageHue=%23100f0d&clientId=ue250eacf-d2eb-4&from=paste&height=179&id=u273c96eb&originHeight=296&originWidth=833&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=34020&status=done&style=none&taskId=u547d2c35-81a9-4108-9c74-98498b59b3d&title=&width=504.84845566902993)
## MySQL数据库连接池多线程压力测试
**我们使用4线程测试，比较好除**
## 4线程的连接池测试
**第一组（1000）**
```cpp
int main()
{
	clock_t begin = clock();

	thread t1([]() {	
		ConnectionPool *cp = ConnectionPool::getConnectionPool();
		for (int i = 0; i < 250; ++i)
		{
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			shared_ptr<Connection> sp = cp->getConnection();
			sp->update(sql);
		}
	});
	thread t2([]() {
		ConnectionPool *cp = ConnectionPool::getConnectionPool();
		for (int i = 0; i < 250; ++i)
		{
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			shared_ptr<Connection> sp = cp->getConnection();
			sp->update(sql);
		}
	});
	thread t3([]() {
		ConnectionPool *cp = ConnectionPool::getConnectionPool();
		for (int i = 0; i < 250; ++i)
		{
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			shared_ptr<Connection> sp = cp->getConnection();
			sp->update(sql);
		}
	});
	thread t4([]() {
		ConnectionPool *cp = ConnectionPool::getConnectionPool();
		for (int i = 0; i < 250; ++i)
		{
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			shared_ptr<Connection> sp = cp->getConnection();
			sp->update(sql);
		}
	});

	t1.join();
	t2.join();
	t3.join();
	t4.join();

	clock_t end = clock();
	cout << (end - begin) << "ms" << endl;


	return 0;
}


```

![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683190337689-6c3e431a-73dd-4271-9f1a-f03ba7923978.png#averageHue=%230f0e0d&clientId=ue250eacf-d2eb-4&from=paste&height=194&id=u76274efb&originHeight=320&originWidth=885&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=33924&status=done&style=none&taskId=u08d805ed-3a25-4c31-b607-29f53621877&title=&width=536.3636053626549)
**第二组（5000）**
```cpp
int main()
{
	clock_t begin = clock();

	thread t1([]() {
		ConnectionPool* cp = ConnectionPool::getConnectionPool();
		for (int i = 0; i < 1250; ++i)
		{
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			shared_ptr<Connection> sp = cp->getConnection();
			sp->update(sql);
		}
		});
	thread t2([]() {
		ConnectionPool* cp = ConnectionPool::getConnectionPool();
		for (int i = 0; i < 1250; ++i)
		{
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			shared_ptr<Connection> sp = cp->getConnection();
			sp->update(sql);
		}
		});
	thread t3([]() {
		ConnectionPool* cp = ConnectionPool::getConnectionPool();
		for (int i = 0; i < 1250; ++i)
		{
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			shared_ptr<Connection> sp = cp->getConnection();
			sp->update(sql);
		}
		});
	thread t4([]() {
		ConnectionPool* cp = ConnectionPool::getConnectionPool();
		for (int i = 0; i < 1250; ++i)
		{
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			shared_ptr<Connection> sp = cp->getConnection();
			sp->update(sql);
		}
		});

	t1.join();
	t2.join();
	t3.join();
	t4.join();

	clock_t end = clock();
	cout << (end - begin) << "ms" << endl;


	return 0;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683190446138-dce23ec9-37a7-4c6e-9ec2-39e9aa7ca3b5.png#averageHue=%230f0e0d&clientId=ue250eacf-d2eb-4&from=paste&height=188&id=ubb322c92&originHeight=310&originWidth=928&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=35401&status=done&style=none&taskId=u19dbe3ca-a5ba-4583-bead-8982983980e&title=&width=562.4242099169985)**第三组（10000）**
```cpp
int main()
{
	clock_t begin = clock();

	thread t1([]() {
		ConnectionPool* cp = ConnectionPool::getConnectionPool();
		for (int i = 0; i < 2500; ++i)
		{
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			shared_ptr<Connection> sp = cp->getConnection();
			sp->update(sql);
		}
		});
	thread t2([]() {
		ConnectionPool* cp = ConnectionPool::getConnectionPool();
		for (int i = 0; i < 2500; ++i)
		{
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			shared_ptr<Connection> sp = cp->getConnection();
			sp->update(sql);
		}
		});
	thread t3([]() {
		ConnectionPool* cp = ConnectionPool::getConnectionPool();
		for (int i = 0; i < 2500; ++i)
		{
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			shared_ptr<Connection> sp = cp->getConnection();
			sp->update(sql);
		}
		});
	thread t4([]() {
		ConnectionPool* cp = ConnectionPool::getConnectionPool();
		for (int i = 0; i < 2500; ++i)
		{
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			shared_ptr<Connection> sp = cp->getConnection();
			sp->update(sql);
		}
		});

	t1.join();
	t2.join();
	t3.join();
	t4.join();

	clock_t end = clock();
	cout << (end - begin) << "ms" << endl;


	return 0;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683190592090-f5f16051-45dd-43bc-8883-440338e061df.png#averageHue=%2312100f&clientId=ue250eacf-d2eb-4&from=paste&height=196&id=u90a5301a&originHeight=324&originWidth=772&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=35496&status=done&style=none&taskId=u77e81b7e-14fc-46e8-9b1b-5b254a267db&title=&width=467.8787608361238)
## 4线程的不使用连接池的测试
**第一组（1000）**
**同一个用户名密码在4个线程同时进行登录访问，MySQL不允许， 我们需要把数据库事先连接一下**
```cpp

int main()
{
	
	Connection conn;
	conn.connect("127.0.0.1", 3306, "root", "toor", "chat");

	clock_t begin = clock();
	thread t1([]() {
		for (int i = 0; i < 250; ++i)
		{

			Connection conn;
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			conn.connect("127.0.0.1", 3306, "root", "toor", "chat");
			conn.update(sql);
		}
		});
	thread t2([]() {
		for (int i = 0; i < 250; ++i)
		{
			Connection conn;
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			conn.connect("127.0.0.1", 3306, "root", "toor", "chat");
			conn.update(sql);
		}
		});
	thread t3([]() {
		for (int i = 0; i < 250; ++i)
		{
			Connection conn;
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			conn.connect("127.0.0.1", 3306, "root", "toor", "chat");
			conn.update(sql);
		}
		});
	thread t4([]() {
		for (int i = 0; i < 250; ++i)
		{
			Connection conn;
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			conn.connect("127.0.0.1", 3306, "root", "toor", "chat");
			conn.update(sql);
		}
		});

	t1.join();
	t2.join();
	t3.join();
	t4.join();

	clock_t end = clock();
	cout << (end - begin) << "ms" << endl;

	return 0;
}

```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683191182698-6fa13cc6-0f1e-4b83-ac79-09ec7f10a3ed.png#averageHue=%230f0e0d&clientId=u77daebe8-5c7e-4&from=paste&height=244&id=ud696d206&originHeight=402&originWidth=793&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=34726&status=done&style=none&taskId=uf13b7d23-1784-4e31-8d3a-82aa715a11e&title=&width=480.60603282778)
**第二组（5000）**
```cpp
int main()
{

	Connection conn;
	conn.connect("127.0.0.1", 3306, "root", "toor", "chat");

	clock_t begin = clock();
	thread t1([]() {
		for (int i = 0; i < 1250; ++i)
		{

			Connection conn;
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			conn.connect("127.0.0.1", 3306, "root", "toor", "chat");
			conn.update(sql);
		}
		});
	thread t2([]() {
		for (int i = 0; i < 1250; ++i)
		{
			Connection conn;
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			conn.connect("127.0.0.1", 3306, "root", "toor", "chat");
			conn.update(sql);
		}
		});
	thread t3([]() {
		for (int i = 0; i < 1250; ++i)
		{
			Connection conn;
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			conn.connect("127.0.0.1", 3306, "root", "toor", "chat");
			conn.update(sql);
		}
		});
	thread t4([]() {
		for (int i = 0; i < 1250; ++i)
		{
			Connection conn;
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			conn.connect("127.0.0.1", 3306, "root", "toor", "chat");
			conn.update(sql);
		}
		});

	t1.join();
	t2.join();
	t3.join();
	t4.join();

	clock_t end = clock();
	cout << (end - begin) << "ms" << endl;

	return 0;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683191367586-ea31498a-7dcc-4e93-ab48-3b33d94661fe.png#averageHue=%23110f0e&clientId=u77daebe8-5c7e-4&from=paste&height=179&id=uf247d126&originHeight=295&originWidth=792&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=36368&status=done&style=none&taskId=uff05c1c5-ce3d-435d-a26c-29d25c2ab18&title=&width=479.9999722567488)
**第三组（10000）**
```cpp
int main()
{

	Connection conn;
	conn.connect("127.0.0.1", 3306, "root", "toor", "chat");

	clock_t begin = clock();
	thread t1([]() {
		for (int i = 0; i < 2500; ++i)
		{

			Connection conn;
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			conn.connect("127.0.0.1", 3306, "root", "toor", "chat");
			conn.update(sql);
		}
		});
	thread t2([]() {
		for (int i = 0; i < 2500; ++i)
		{
			Connection conn;
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			conn.connect("127.0.0.1", 3306, "root", "toor", "chat");
			conn.update(sql);
		}
		});
	thread t3([]() {
		for (int i = 0; i < 2500; ++i)
		{
			Connection conn;
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			conn.connect("127.0.0.1", 3306, "root", "toor", "chat");
			conn.update(sql);
		}
		});
	thread t4([]() {
		for (int i = 0; i < 2500; ++i)
		{
			Connection conn;
			char sql[1024] = { 0 };
			sprintf(sql, "insert into user(name,age,sex) values('%s',%d,'%s')",
				"zhang san", 20, "male");
			conn.connect("127.0.0.1", 3306, "root", "toor", "chat");
			conn.update(sql);
		}
		});

	t1.join();
	t2.join();
	t3.join();
	t4.join();

	clock_t end = clock();
	cout << (end - begin) << "ms" << endl;

	return 0;
}
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/27860795/1683191465745-fa8d09c4-4c1c-40b6-877f-e6832fc439af.png#averageHue=%23110f0e&clientId=u77daebe8-5c7e-4&from=paste&height=171&id=u1b69c48e&originHeight=282&originWidth=802&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=33691&status=done&style=none&taskId=uebca1aba-1525-4968-a8fe-41c0627ce82&title=&width=486.06057796706125)
## 总记录
验证数据的插入操作所花费的时间，第一次测试使用普通的数据库访问操作，第二次测试使用带连接池的数据库访问操作，对比两次操作同样数据量所花费的时间，性能压力测试结果如下：

| 数据量  | 未使用连接池花费时间  | 使用连接池花费时间 |
| --- | --- | --- |
| 1000  | 单线程：3520ms 四线程：1172ms  | 单线程：1489ms 四线程：976ms |
| 5000  | 单线程：16859ms 四线程：5766ms  | 单线程：7711ms 四线程：4429ms |
| 10000  | 单线程：34437ms 四线程：12616ms  | 单线程：14850ms 四线程：8442ms |


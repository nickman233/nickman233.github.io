---
layout: default_post
title : C++ 通过 ADO 连接 SQL Server 较完整过程
---

# C++ 通过 ADO 连接 SQL Server 较完整过程

首先，给出几个我参考的网站： 
1. [C++使用ADO连接SQLSERVER](http://blog.csdn.net/shellching/article/details/8808771)
2. [C++连接SQL Server 数据库](http://blog.csdn.net/ljh56789/article/details/8061323)
3. [配置SQL Server OBDC数据源](http://www.360doc.com/content/12/0213/18/1912775_186366494.shtml)
4. [SQLSERVER2008 18456错误](http://www.cnblogs.com/496963524-zhangying/articles/2232599.html)

假设已经懂得了 SSMS 的基本操作，能够建立数据库，能够新建表。。。  
## 配置 SQL Server 数据源
因为使用的接口是 OBDC，所以在使用C++连接 SQL Server 数据库前，得配置 OBDC 数据源，不然后面 Open 时就会出现 **[Microsoft][ODBC 驱动程序管理器] 未发现数据源名称并且未指定默认驱动程序** 的错误。  
1. 在控制面板-管理工具中打开 ODBC 数据源，切换到系统DSN页，点击添加。  
![ODBC 数据源](http://7sbplh.com1.z0.glb.clouddn.com/SQLServerOBDC%E6%95%B0%E6%8D%AE%E6%BA%90.png)

2. 选择合适的驱动，这里我选SQL Server，点击完成。  
![添加新数据源](http://7sbplh.com1.z0.glb.clouddn.com/SQLServer%E5%88%9B%E5%BB%BA%E6%96%B0%E6%95%B0%E6%8D%AE%E6%BA%90.png)

3. 选择登录方式为**使用用户输入登录ID 和密码的SQL Server 验证**，在下方输入ID 和密码。在这之前需要创建对应的账号。我的登录过程中出现了开始给出的链接中*18456*的错误，如果遇到可以尝试链接中的解决方法。然后点击*下一步*。 
![1](http://7sbplh.com1.z0.glb.clouddn.com/SQLServer%E5%88%9B%E5%BB%BA%E5%88%B0SQL%20Server%E7%9A%84%E6%96%B0%E6%95%B0%E6%8D%AE%E6%BA%90.png)

4. 点击*下一步*，当然也可以按提示设置。  
![2](http://7sbplh.com1.z0.glb.clouddn.com/SQLServer%E5%88%9B%E5%BB%BA%E5%88%B0SQL%20Server%E7%9A%84%E6%96%B0%E6%95%B0%E6%8D%AE%E6%BA%902.png)

5. 点击*完成*，当然也可以按提示设置。 
![3](http://7sbplh.com1.z0.glb.clouddn.com/SQLServer%E5%88%9B%E5%BB%BA%E5%88%B0SQL%20Server%E7%9A%84%E6%96%B0%E6%95%B0%E6%8D%AE%E6%BA%903.png)

6. 完成了创建，可以点击*测试数据源*。
![测试](http://7sbplh.com1.z0.glb.clouddn.com/SQLServerOBDC%20Microsoft%20SQL%20Server%E5%AE%89%E8%A3%85.png)

7. 不出意外，应该会显示测试成功。
![成功](http://7sbplh.com1.z0.glb.clouddn.com/SQLServer%E6%B5%8B%E8%AF%95.png)

## C++ ADO 连接 SQL Server 简单代码及注释

```c++
// SQLTEST.cpp : 定义控制台应用程序的入口点。
//

#include "stdafx.h"
#include <string>
#include <iostream>
#include <stdlib.h>

int _tmain(int argc, _TCHAR* argv[])
{
	// 初始化COM组件
	::CoInitialize(NULL); 
	std::cout << "test start..." << std::endl;
	// 连接到MS SQL Server
	// 初始化指针
	_ConnectionPtr pMyConnect = NULL;
	HRESULT hr = pMyConnect.CreateInstance(__uuidof(Connection));
	if(FAILED(hr))
		return -1;

	// 初始化链接参数
	_bstr_t strConnect = "Provider=SQLOLEDB;Database=WSCLTEST;";
	// 执行连接数据库
	try
	{
		pMyConnect->Open(strConnect,"yjf","123456",NULL);
	}
	catch(_com_error &e)
	{
		std::cout << e.Description();
		return -1;
	}
	std::cout << "Connect Success!" << std::endl;

	
	_RecordsetPtr pRecordset; 
	if (FAILED(pRecordset.CreateInstance(__uuidof(Recordset))))  
	{  
		return -1;  
	}  
	
	//插入新数据
	char* strSql = "insert into dbo.station1(time, value1, value2, value3, value4) \
					values(2000, 4.35, 245.4, 245.94, 243);";
	try
	{
		pRecordset = pMyConnect->Execute(strSql, NULL, adCmdText);
	}
	catch(_com_error &e)
	{
		std::cout << e.Description();
	}

	//更新数据
	char* strSqlupdate = "update dbo.station1 set value1=20 where time = 1000";
	try
	{
		pRecordset = pMyConnect->Execute(strSqlupdate, NULL, adCmdText);
	}
	catch(_com_error &e)
	{
		std::cout << e.Description();
	}

	//读取数据
	char* strSqlSelect = "select * from dbo.station1";
	try
	{
		pRecordset = pMyConnect->Execute(strSqlSelect, NULL, adCmdText);
	}
	catch(_com_error &e)
	{
		std::cout << e.Description();
	}
	while(!pRecordset->EndofFlie)
	{
		std::cout << (_bstr_t)pRecordset->GetFields()->GetItem("value1")->Value;
		pRecordset->MoveNext();
	}

	pMyConnect->Close();

	system("pause");
	return 0;
}

```


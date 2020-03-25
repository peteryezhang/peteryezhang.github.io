---
layout:     post                    # 使用的布局（不需要改）
title:      使用Powershell 执行数据库语句           # 标题 
subtitle:   一个通用的模板  #副标题
date:       2020-03-25              # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - PowerShell
---

## 前言

有些时候我们需要通过PowerShell脚本的方式在Database中进行ACID操作，这种操作往往发生在跳板机上。  

## PowerShell结构

为了在数据库中执行特定语句，我们往往需要以下几个模块/方法：
1. 一个连接数据库的方法`Inital-DBConnection`
2. 一个执行Sql的方法`Execute-Sql`
3. 一个Main方法用来连接所有逻辑  

## 具体实践

具体的，连接Sql Server主要使用两种方式。
如果是使用Winddows AD 认证的话：
```
$connectionStr = "Server=$SQLServer;Database=$SQLDBName;Trusted_Connection=True"

```
如果是使用账号密码登录的话：
```
$connectionStr = "Data Source=$SQLServer;Initial Catalog=$SQLDBName;user id=$SQLUserName;pwd=$SQLPassword"
```

本次的实例是通过AD认证登录Server
```
Function Inital-DBConnection {
param
(
# [String] $SQLUserName,
# [String] $SQLPassword,
[String] $SQLDBName,
[String] $SQLServer
)
$connectionStr = "Server=$SQLServer;Database=$SQLDBName;Trusted_Connection=True"
#$connectionStr = "Data Source=$SQLServer;Initial Catalog=$SQLDBName;user id=$SQLUserName;pwd=$SQLPassword"
$SqlConnection = New-Object System.Data.SqlClient.SqlConnection
$SqlConnection.ConnectionString = $connectionStr
try{
$SqlConnection.Open()
#$SqlConnection.ConnectionTimeout = 10
Write-Host 'Connected to sql server.'
return $SqlConnection
}
catch [exception] {
Write-Warning ('Connect to database failed with error message:{0}' -f ,$_)
$SqlConnection.Dispose()
return $null
}
}
# Excute SQL Command 
Function Execute-Sql{
param(
[System.Data.SqlClient.SqlConnection]$SqlConnection,
[string]$Command
)
try{
$SqlCmd = $SqlConnection.CreateCommand()
$SqlCmd.CommandText = $Command
$SqlCmd.CommandTimeout = 1000
$SqlCmd.ExecuteNonQuery()
return 1
}
catch [Exception]{
Write-Warning ('Execute Sql Command failed with error message:{0}' -f $_)
return 0
}
}
##Main
try{

$SQLDBName = 
$SQLServer = 
$SqlConnection = Inital-DBConnection -SQLDBName $SQLDBName -SQLServer $SQLServer
$cmd = 

$ExecuteCmd = Execute-Sql -SqlConnection $SqlConnection -Command $cmd
Write-Host $ExecuteCmd
} #EndofTry
catch{
}
finally{
$SqlConnection.Close()
} 


```

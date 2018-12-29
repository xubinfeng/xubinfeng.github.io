# 跟老谭学ZooKeeper视频课程
## 一. zookeeper 5钟使用场景

##  二. zookeeper安装和启动 
- conf下面复制**zoo_sample.cfg**更改为**zoo.cfg** 默认会寻找zoo.cfg配置
- 修改zoo.cfg内的**dataDir=**按照需要指定目录
- bin目录下面启动zookeeper

## 三. 命令的使用 
### 1. help  查看使用帮助
### 2. create 创建节点  
- 参数:-s 有序  -e 临时(客户端关闭就会被删除)  
- 默认为无序持久节点  
- 路径必须是绝对路径

```bash
#创建一个无序的持久节点
create /node1 nodedata1
>> Created /node1
```

```bash
#创建有序的持久节点,会自动在节点name后面增加一个序号 ,有序节点可以多次增加,会自增序列
create -s /node2 nodedata2
>> Created /node20000000001  
```

### 3. ls和ls2 显示节点信息
- ls会列出节点
- ls2会列出节点并显示节点信息(不带data)

### 4. get 获取节点信息
- 使用和ls2差不多,但是get会带data

### 5. stat 获取节点信息
- 使用和get差不多,但是stat**不会带data信息**

### 6. set 更新节点数据
```bash
set /note1 newdata
get /note1
#注意dataVersion = 1 每次都会+1用于版本区分
```

### 7. delete rmr 删除节点
- delete删除节点下面不能有子节点
- rmr 递归删除节点,包括删除子节点 

### 9. setquota设置节点配额,listquota查询节点配额信息,delquota删除节点配额信息
> 节点配额是指子节点数量 
 
- -n选项为设置节点的数量,该数量**包含节点本身**  
- 配额不是强制限制,只是在在日志中出现警告,不会影响操作  


1. setquota命令格式 ```setquota -n <number>  <path>```
```bash
setquota -n 3 /node1
>> Comment: the parts are option -n val 3 path /node1
```

2. listquota命令格式 ```listquota <path>```
```bash
listquota /node1
>> Output quota for /node1 count=3,bytes=-1
>> Output stat for /node1 count=2,bytes=18
```

3. delquota命令格式 ```delquota [-n|-b] <path>```  
> 不加参数就会删除节点下全部的配额及数据长度信息  

```bash
delquota /node1
>>  absolute path is /zookeeper/quota/node1/zookeeper_limits
>>  quota for /node1 does not exist.
```

### 10. history查询历史命令,redo重新执行历史命令
> 提示:该命令和linux中的操作基本相同,只不过linux中redo用!号替代

- history 查询历史命令 命令格式```history```(注意输出历史命令的编号)
- redo 重新执行历史命令,命令格式```redo <number>```,number为历史命令编号

### 11. close关闭连接,connect连接服务器,quit退出客户端
- close关闭连接后不会退出
- close退出后可以直接connect重新进行连接操作
- quit直接退出
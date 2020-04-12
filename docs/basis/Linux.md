# Linux

## 文件系统

## 用户管理

### 基本角色概念

1. 用户

   > - linux是多用户系统，在该台计算机上有多个用户可以同时登陆操作
   > - 每个用户有各自不同的操作权限
   > - linux用户需要至少属于一个组

2. 组

   > - linux用组来管理权限，每个用户被划分到不同的组
   > - linux用户需要至少属于一个组

3. 家目录

   > 每一个用户都有属于自己的家目录，被创建于于`/home`下面

### 用户

1. 添加用户相关指令

   - `useradd [ x ] username`

     - 【 -d /home/shane 】：会在 `/home/shane` 下建立`username`这个用户
     - 【 -g groupname username 】：指定在名为 `groupname` 的组下创建名为 `username` 的用户
     
     创建一个名为 `username` 的用户，在没有指定组的同时，也创建了一个名为 `username` 的组
     
   - `passwd username`
   
     给用户`username`指定或修改密码
   
   - `userdel [ x ] username`
   
     - 【 -r username 】：删除用户及其家目录（**一般不会将用户家目录删除**）
   
       删除用户 `username`，但会保留家目录
2. 查看用户相关指令

   - `id username`

     查询用户 `username` 的相关信息

   - `whoami`

     查询当前是什么用户

3. 切换用户相关指令

   - `su - username`

     切换至 `username` 用户，高权限切换到低权限用户，不需要密码

   - `exit`

     返回至原来的用户

   - `usermod -g groupname username`

     修改用户的组

### 用户组

> ”用户组“ 类似于角色，系统对拥有共性的用户进行分组管理
1. 相关指令

   - `groupadd groupname`

     创建一个名为 `groupname` 的组

   - `groupdel groupname` 

     删除名为 `groupname` 的组


###  用户与组的相关文件

1. 用户文件

   `/etc/passwd` 文件：记录用户的各种信息

   > 行含义为
   >
   > 用户名 : 口令 : 用户标识号 : 组表示号 : 注释性描述 :: 主目录 : 登陆shell

2. 组文件

   `/etc/group` 文件：组配置文件

   > 行含义为
   >
   > 登录名 : 口令 : 最后一次修改时间 : 最小时间间隔 : 最大时间间隔 : 警告时间 : 不活动时间 : 失效时间 :  标志

3. 登陆和密码文件

   `/etc/shadow` 文件：用户密码

   > 行含义为
   >
   > 组名 : 口令 : 组标识号 : 组表示号 : 组内用户列表

   
## 实用指令
### 指定运行级别

| 级别码 | 含义                       |
| ------ | -------------------------- |
| 0      | 关机                       |
| 1      | 单用户（用户找回丢失密码） |
| 2      | 多用户无网络               |
| 3      | 多用户有网络               |
| 4      | unused                     |
| 5      | 图形界面                   |
| 6      | 重启                       |

- 指定配置文件：`/etc/inittab`

- 切换到指定运行级别的指令

  - `init 【0123456】`

- 运用场景

  - 找回丢失的 root 密码

    > 进入单用户级别（ root 不需要密码就可以登陆），修改 root 密码

### 指令

#### 帮助指令

1. `man 指令或配置文件 ` 

   eg. `man ls`

2. `help 指令` 

#### 文件目录类指令

| 指令  | 语法                                                         | 示例                         | 作用                                                         |
| ----- | ------------------------------------------------------------ | ---------------------------- | ------------------------------------------------------------ |
| pwd   | pwd                                                          | pwd                          | 显示当前绝对路径                                             |
| ls    | ls 【options】 目录或是文件                                  | ls -al /home                 | 查看当前目录所有目录信息                                     |
| cd    | cd 绝对路径（根目录开始定位）/相对路径（工作路径开始定位）   | cd ..                        | 切换到指定目录                                               |
| mkdir | mkdir 【options】 要创建的目录                               | mkdir -p /home/animal/tiger  | 用于创建目录                                                 |
| rmdir |                                                              |                              | 删除空目录                                                   |
| rm    | rm 【options】 要删除的文件或文件夹                          | rm -rf /home/a.txt           | 移除文件或目录                                               |
| touch |                                                              | touch /home/a.txt            | 创建空文件                                                   |
| cp    | cp 【options】 source dest                                   | cp -r /usr/a.txt /home/shane | 拷贝文件到指定目录                                           |
| mv    | mv oldFileName newFileName \| mv moveFile  /temp/targetFolder |                              | 移动文件或目录或进行重命名                                   |
| cat   | cat 【options】要浏览的文件                                  | cat -n /etc/profile \| more  | 查看文件内容，以只读的方式                                   |
| \|    |                                                              |                              | 管道：将一个命令的输出通过管道作为另一个命令的输入           |
| more  | 内置快捷操作（space-向下翻一页、Enter-向下翻一行、q-退出、Ctrl+F-向下滚动一屏、Ctrl+B-向上滚动一屏、=-输出当前行号、:f-输出文件名和当前行号） | more /etc/profile            | 基于VI文本编辑器的文本过滤器，以全屏的方式按页展示文件内容   |
| less  | 内置快捷键（space-向下翻一页、q-退出、pagedown-向下一页、pageup-向上一页、/字符串-向下搜索字符、?字符串-向上搜索字符） |                              | 用于分屏查看内容，不是一次性加载文件，根据要显示内容加载，比more更强大，加载大型文件更有效率 |
| >     |                                                              |                              | 输出重定向，覆盖原先内容                                     |
| \>>   |                                                              |                              | 追加指令                                                     |
| echo  |                                                              |                              | 在终端显示参数指定的文字，通常和重定向联合使用               |













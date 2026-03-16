---
title: 超实用！MySQL密码修改全流程（附不同场景操作）
date: 2026-03-16 12:22:32
tags: [MySQL，教程]
categories: [技术分享] 
---



## 一、前言
日常开发/运维中，经常会遇到需要修改MySQL密码的场景（比如密码泄露、定期更换、忘记密码等），本文整理了不同场景下MySQL密码修改的完整步骤，涵盖常规修改、忘记root密码重置等核心场景，步骤可直接复用，方便后续查阅。

## 二、前置准备
1. 确保已安装MySQL，且能通过终端/命令行访问MySQL服务
2. 操作前建议备份MySQL数据（可选，但重要环境必做）
3. 具备对应权限：常规修改需要`UPDATE`权限，重置root密码需要服务器管理员权限

## 三、场景1：知道原密码，常规修改（最常用）
### 3.1 方式1：使用ALTER USER命令（MySQL 8.0+推荐）
MySQL 8.0及以上版本推荐使用此方式，语法更规范：

```
# 1. 登录MySQL
mysql -u 用户名 -p
# 输入原密码后回车

# 2. 执行修改密码命令（修改root用户，密码改为新密码123456）
ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码';

# 3. 刷新权限（使修改生效）
FLUSH PRIVILEGES;

# 4. 退出MySQL，验证新密码
exit
mysql -u root -p  # 输入新密码测试是否登录成功
```

### 3.2 方式 2：使用 SET PASSWORD 命令（兼容低版本）

适用于 MySQL 5.7 及以下版本，也兼容 8.0：

```
# 1. 登录MySQL
mysql -u 用户名 -p

# 2. 设置新密码
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('新密码');  # 5.7及以下
# MySQL 8.0需去掉PASSWORD函数：
# SET PASSWORD FOR 'root'@'localhost' = '新密码';

# 3. 刷新权限
FLUSH PRIVILEGES;
```

## 四、场景 2：忘记 root 密码，重置密码（紧急场景）

如果忘记 root 密码，需要通过跳过权限验证的方式重置，步骤如下（以 Linux 系统为例）：

### 4.1 步骤 1：停止 MySQL 服务

```
# CentOS/RHEL
systemctl stop mysqld

# Ubuntu/Debian
systemctl stop mysql
```

### 4.2 步骤 2：跳过权限验证启动 MySQL

```
# 临时跳过权限表启动
mysqld --skip-grant-tables --shared-memory --console
```

### 4.3 步骤 3：免密码登录并修改密码

```
# 1. 免密码登录MySQL
mysql -u root

# 2. 切换到mysql库（存储用户权限的核心库）
USE mysql;

# 3. 更新密码（根据MySQL版本选择）
# MySQL 5.7及以下
UPDATE user SET authentication_string = PASSWORD('新密码') WHERE User = 'root' AND Host = 'localhost';

# MySQL 8.0+（注意字段名和函数变化）
UPDATE user SET authentication_string = '' WHERE User = 'root';  # 先清空
ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码';

# 4. 刷新权限
FLUSH PRIVILEGES;

# 5. 退出
exit
```

### 4.4 步骤 4：恢复正常启动并验证

```
# 1. 停止临时启动的MySQL进程
killall mysqld

# 2. 正常启动MySQL服务
systemctl start mysqld  # CentOS/RHEL
# systemctl start mysql  # Ubuntu/Debian

# 3. 用新密码登录验证
mysql -u root -p  # 输入新密码测试
```

## 五、注意事项

1. 密码复杂度：生产环境建议设置复杂密码（字母 + 数字 + 特殊符号），避免简单密码被破解

2. 远程用户：如果需要修改远程登录的用户密码，将`localhost`改为`%`（允许所有 IP）或指定 IP

3. MySQL 8.0 变化：8.0 移除了

    ```
    PASSWORD()
    ```

    函数，且默认认证插件为

    ```
    caching_sha2_password
    ```

    ，低版本客户端可能兼容问题，可临时改为

    ```
    mysql_native_password
    ```

    ```
    ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';
    ```

    

4. 权限问题：修改后如果登录失败，检查用户的`Host`字段是否匹配（比如远程登录用`root@%`，本地用`root@localhost`）

## 六、总结

本文覆盖了 MySQL 密码修改的两大核心场景：

- 知道原密码：优先用`ALTER USER`（8.0+）或`SET PASSWORD`（低版本）

- 忘记密码：通过

    ```
    --skip-grant-tables
    ```

    跳过权限验证后重置

    操作时注意区分 MySQL 版本，修改后务必刷新权限，生产环境做好密码安全管控

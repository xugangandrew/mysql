# 附录：用户及 Schema 管理 {#concept_ypy_flg_wdb .concept}

在使用 RDS 的过程中，由于 superuser 不完全放开，因此我们建议用户在使用数据库时遵循单独建立用户并通过 schema 管理用户的私有空间。

**说明：** 本例中，myuser 是建立实例时创建的管理账号，newuser 是当前需要新建的账号。

## 方案一 {#section_jfg_klg_wdb .section}

1.  通过初始帐号 myuser 建立有登录权限的用户。

    ```
    CREATE USER newuser LOGIN PASSWORD'password';
    ```

    参数说明如下;

    -   USER:要创建的用户名，如 **newuser**
    -   password：用户名对应的密码，如 **password**
2.  为新用户建立schema。

    ```
    CREATE SCHEMA newuser;
    GRANT newuser to myuser;
    ALTER SCHEMA newuser OWNER TO newuser;
    REVOKE newuser FROM myuser;
    ```

    **说明：** 

    -   如果在进行`ALTER SCHEMA newuser OWNER TO newuser` 之前没有将 myuser 的角色赋予 newuser，将会出现如下权限问题：

        ```
           ERROR:  must be member of role "newuser"
        ```

    -   从安全角度出发，在处理完 OWNER 的授权后，请将 newuser 移出 myuser 角色以提高安全性。

3.  使用 newuser 登录数据库。

    ```
    psql -U newuser -h intranet4example.pg.rds.aliyuncs.com -p 3433 pg001
    Password for user newuser:
    psql.bin (9.4.4, server 9.4.1)
    Type "help" for help.
    ```


## 方案二 {#section_sh3_qlg_wdb .section}

1.  通过初始帐号 myuser 建立有登录权限的用户。

    ```
    CREATE USER newuser CREATEDB LOGIN PASSWORD'password';
    ```

    参数说明如下;

    -   USER: 要创建的用户名，如 **newuser**
    -   password: 用户名对应的密码，如 **password**
    -   CREATEDB: 给用户赋予建立数据库的权限
2.  使用新用户 newuser 登陆到数据库。

    ```
    psql -U <数据实例域名> -p 3433 -U newuser <数据库名>
    CREATE DATABASE
    ```

3.  为新用户建立schema。

    ```
    CREATE SCHEMA newuser;
    GRANT myuser to newuser;
    ALTER SCHEMA myuser OWNER TO newuser;
    REVOKE newuser FROM myuser;
    ```

    **说明：** 

    -   如果在进行`ALTER SCHEMA newuser OWNER TO newuser`之前没有将 myuser 的角色赋予 newuser，将会出现如下权限问题：

        ```
          ERROR:  must be member of role "newuser"
        ```

    -   从安全角度出发，在处理完 OWNER 的授权后，请将 newuser 移出 myuser 角色以提高安全性。

4.  使用 newuser 登录数据库。

    ```
    psql -U newuser -h intranet4example.pg.rds.aliyuncs.com -p 3433 pg001
    Password for user newuser:
    psql.bin (9.4.4, server 9.4.1)
    Type "help" for help.
    ```



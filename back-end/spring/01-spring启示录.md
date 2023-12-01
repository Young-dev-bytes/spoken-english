一、Spring 启示录

阅读以下代码：

```java
package com.powernode.oa.controller;

import com.powernode.oa.service.UserService;
import com.powernode.oa.service.impl.UserServiceImpl;

public class UserController {

    private UserService userService = new UserServiceImpl();

    public void login(){
        String username = "admin";
        String password = "123456";
        boolean success = userService.login(username, password);
        if (success) {
            // 登录成功
        } else {
            // 登录失败
        }
    }
}
```

```java
package com.powernode.oa.service.impl;

import com.powernode.oa.bean.User;
import com.powernode.oa.dao.UserDao;
import com.powernode.oa.dao.impl.UserDaoImplForMySQL;
import com.powernode.oa.service.UserService;

public class UserServiceImpl implements UserService {

    private UserDao userDao = new UserDaoImplForMySQL();

    public boolean login(String username, String password) {
        User user = userDao.selectByUsernameAndPassword(username, password);
        if (user != null) {
            return true;
        }
        return false;
    }
}

```

```java
package com.powernode.oa.dao.impl;

import com.powernode.oa.bean.User;
import com.powernode.oa.dao.UserDao;

public class UserDaoImplForMySQL implements UserDao {
    public User selectByUsernameAndPassword(String username, String password) {
        // 连接MySQL数据库，根据用户名和密码查询用户信息
        return null;
    }
}

```

可以看出，UserDaoImplForMySQL 中主要是连接 MySQL 数据库进行操作。如果更换到 Oracle 数据库上，则需要再提供一个 UserDaoImplForOracle，如下：

```java
package com.powernode.oa.dao.impl;

import com.powernode.oa.bean.User;
import com.powernode.oa.dao.UserDao;

public class UserDaoImplForOracle implements UserDao {
    public User selectByUsernameAndPassword(String username, String password) {
        // 连接Oracle数据库，根据用户名和密码查询用户信息
        return null;
    }
}

```

很明显，以上的操作正在进行功能的扩展，添加了一个新的类 UserDaoImplForOracle 来应付数据库的变化，这里的变化会引起连锁反应吗？当然会，如果想要切换到 Oracle 数据库上，UserServiceImpl 类代码就需要修改，如下：

```java
package com.powernode.oa.service.impl;

import com.powernode.oa.bean.User;
import com.powernode.oa.dao.UserDao;
import com.powernode.oa.dao.impl.UserDaoImplForOracle;
import com.powernode.oa.service.UserService;

public class UserServiceImpl implements UserService {

    //private UserDao userDao = new UserDaoImplForMySQL();
    private UserDao userDao = new UserDaoImplForOracle();

    public boolean login(String username, String password) {
        User user = userDao.selectByUsernameAndPassword(username, password);
        if (user != null) {
            return true;
        }
        return false;
    }
}

```

1.1 OCP 开闭原则

> 这样一来就违背了开闭原则 OCP。开闭原则是这样说的：在软件开发过程中应当对扩展开放，对修改关闭。也就是说，如果在进行功能扩展的时候，添加额外的类是没问题的，但因为功能扩展而修改之前运行正常的程序，这是忌讳的，不被允许的。因为一旦修改之前运行正常的程序，就会导致项目整体要进行全方位的重新测试。这是相当麻烦的过程。导致以上问题的主要原因是：代码和代码之间的耦合度太高。如下图所示：

<img src="./img/image.png">

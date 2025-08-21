web页面rememberMe功能实现
----------------------
## 参考

* [Spring Security](https://docs.spring.io/spring-security/site/docs/5.0.4.BUILD-SNAPSHOT/reference/htmlsingle/#remember-me)
* [持久化login cookie](http://jaspan.com/improved_persistent_login_cookie_best_practice)


## 服务端DB存储
### To summarize Miller's design（老外2006年的文章，服！）

1. When the user successfully logs in with Remember Me checked, a login cookie is issued in addition to the standard session management cookie.

2. The login cookie contains the user's username and a random number (the "token" from here on) from a suitably large space. The username and token are stored as a pair in a database table.
3. When a non-logged-in user visits the site and presents a login cookie, the username and token are looked up in the database.
   1. If the pair is present, the user is considered authenticated. The used token is removed from the database. A new token is generated, stored in database with the username, and issued to the user via a new login cookie.
   2. If the pair is not present, the login cookie is ignored.
4. Users that are only authenticated via this mechanism are not permitted to access certain protected information or functions such as changing a password, viewing personally identifying information, or spending money. To perform those operations, the user must first successfully submit a normal username/password login form.
5. Since this approach allows the user to have multiple remembered logins from different browsers or computers, a mechanism is provided for the user to erase all remembered logins in a single operation.

### 存在的问题
1. 任然存在login cookie被盗用的风险（rememberMe过期时间内）。受害用户登陆时提示重新登陆一般会以为是过期，不会注意曾经被盗用过。（提示上次登陆时间是个不错的方法，帮助用户发现风险）
2. 上面场景一种可能的解决方案是：受害者登陆时服务端如果发现login cookie无效，那么认为先前的login cookie（包括现在所有的）都不安全，存在被盗用的风险。提示用户并invalid当前所有该用户的
login cookie。缺点是存在DOS攻击风险。

### 优化方案
这里关健是识别盗用和非法访问DOS攻击的场景。方案中增加了一个series id，相对token来说不变（token只一次有效）。在未验证通过场景时如果series id正确说明是盗用；如果未提供series id说明是互联网DOS攻击或者随机调用。

The implementation is no more difficult and requires no more resources than Miller's design. From the summary above, only items 2 and 3 change:

1. When the user successfully logs in with Remember Me checked, a login cookie is issued in addition to the standard session management cookie.
2. The login cookie contains the user's username, **a series identifier**, and a token. The series and token are unguessable random numbers from a suitably large space. All three are stored together in a database table.
3. When a non-logged-in user visits the site and presents a login cookie, the username, series, and token are looked up in the database.
   1. If the triplet is present, the user is considered authenticated. The used token is removed from the database. A new token is generated, stored in database with the username and the same series identifier, and a new login cookie containing all three is issued to the user.
   2. If the username and series are present but the token does not match, a theft is assumed. The user receives a strongly worded warning and all of the user's remembered sessions are deleted.
   3. If the username and series are not present, the login cookie is ignored.


## 客户端Cookie存储（hash加密）
服务端成功验证后返回浏览器下面的cookie。启用rememberMe功能再次访问服务端时，服务端在过期时间内验证hash签名（用户名+过期时间+密码+key）。过期时间内cookie有被盗用的风险。

```
base64(username + ":" + expirationTime + ":" +
md5Hex(username + ":" + expirationTime + ":" password + ":" + key))

username:          As identifiable to the UserDetailsService
password:          That matches the one in the retrieved UserDetails
expirationTime:    The date and time when the remember-me token expires, expressed in milliseconds
key:               A private key to prevent modification of the remember-me token
```

---
layout: post
title: Spring LDAP 认证
categories: [Spring LDAP]
---
Spring提供了专门的LDAP集成库和解决方案。参考：https://spring.io/guides/gs/authenticating-ldap/

参考用户文档中容易产生一个误区。

```java
@Configuration
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

	@Override
	protected void configure(HttpSecurity http) throws Exception {
		http
			.authorizeRequests()
				.anyRequest().fullyAuthenticated()
				.and()
			.formLogin();
	}

	@Override
	public void configure(AuthenticationManagerBuilder auth) throws Exception {
		auth
			.ldapAuthentication()
				.userDnPatterns("uid={0},ou=people")
				.groupSearchBase("ou=groups")
				.contextSource()
					.url("ldap://localhost:8389/dc=springframework,dc=org")
					.and()
				.passwordCompare()
					.passwordEncoder(new LdapShaPasswordEncoder())
					.passwordAttribute("userPassword");
	}

}
```
上面参考代码中下面这部分是不合适的。首先LdapShaPasswordEncoder是deperacated类不推荐使用；其次这种LDAP认证方式会把LDAP当作一个存用户信息的数据库，而不是认证服务器。因为他是先根据用户id从LDAP中查询用户对象，获取后再取出用户密码跟前端传过来的进行比对（当然密码存储不是明文，会做单向加密）。

```java
					.and()
				.passwordCompare()
					.passwordEncoder(new LdapShaPasswordEncoder())
					.passwordAttribute("userPassword");
```

理想中的情况是我们把前端输入的用户名和密码透传给LDAP，由LDAP完成认证服务，应用服务器并不用关心LDAP密码加密方式等。

其实Spring LDAP本身就支持这种认证方式。这里就要区别```BindAuthenticator``` 和 ```PasswordComparisonAuthenticator```。上面的就是PasswordComparisonAuthenticator方式。那么怎么让WebSecurityConfigurerAdapter支持BindAuthenticator呢？

很简单，去掉上面passwordEncoder就会应用BindAuthenticator。而且上面的配置中并不需要提及userPassword。因为BindAuthenticator会中上下文中的authentication对象中获取。Authentication会从basic auth popup form中获取用户名，密码信息。

相关逻辑参考spring 源码如下：

```java

    @Override
    public void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth
                .ldapAuthentication(); // 入口处
	}

		/**
	 * Creates the {@link LdapAuthenticator} to use
	 *
	 * @param contextSource the {@link BaseLdapPathContextSource} to use
	 * @return the {@link LdapAuthenticator} to use
	 */
	private LdapAuthenticator createLdapAuthenticator(
			BaseLdapPathContextSource contextSource) {
		AbstractLdapAuthenticator ldapAuthenticator = passwordEncoder == null ? createBindAuthenticator(contextSource)
				: createPasswordCompareAuthenticator(contextSource); ·
		LdapUserSearch userSearch = createUserSearch();
		if (userSearch != null) {
			ldapAuthenticator.setUserSearch(userSearch);
		}
		if (userDnPatterns != null && userDnPatterns.length > 0) {
			ldapAuthenticator.setUserDnPatterns(userDnPatterns);
		}
		return postProcess(ldapAuthenticator);
	}

```
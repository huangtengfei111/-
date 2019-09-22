# shiro

login接口传入username，password，

```
Subject currentUser = SecurityUtils.getSubject();
```

```java
清理掉已经登录过但不是这个用户的Subject
clearOtherSessionIfPossible(username, currentUser);
   if (currentUser != null) {
      Account account = (Account) currentUser.getPrincipal();
      if (account != null) {
        if (currentUser.isAuthenticated() &&
            !username.equals(account.getUsername())) {
          currentUser.logout();
        }
      }
    }
```

```java
if (!currentUser.isAuthenticated()) {  
  UsernamePasswordToken token = new UsernamePasswordToken(username, password); 
  try {
       postLogin(currentUser); 
       } catch (UnknownAccountException uae) {
       
       }
```



```java
public class BasRealm extends AuthorizingRealm {

	private static final Logger log = LoggerFactory.getLogger(BasRealm.class);

  @Override
  protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
    SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
    Account principal = (Account) principals.getPrimaryPrincipal();

    Role role = principal.getRole();
    if (role != null) {
      info.addRole(role.getValue());
    }
    return info;
  }

  @Override
  protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
    UsernamePasswordToken userPassToken = (UsernamePasswordToken) token;
    String username = userPassToken.getUsername();

    if (username == null) {
      log.error("Username is null.");
      return null;
    }
    // read password hash and salt from db
    try {
      Account account = Account.findFirst("username = ?", username);
      log.info("Account info {}", account);

      if (account == null) {
        log.error("No account found for user [{}]", username);
        return null;
      }

        SimpleAuthenticationInfo info =
            new SimpleAuthenticationInfo(account, account.getPassword(), account.getUsername());

        if (account.getSalt() != null)
          info.setCredentialsSalt(ByteSource.Util.bytes(account.getSalt()));
        return info;
    } catch (Exception e) {
      log.error("Authentication error: {}", e);
      return null;
    }
  }
}
```



sessionmanager，

sercuritymanager, 

subject,

realm
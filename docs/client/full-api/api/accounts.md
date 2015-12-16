{{#template name="apiAccounts"}}

<h2 id="accounts_api"><span>Accounts 账号</span></h2>

流星的账号系统是建立在`userId`之上的, 而`userId`则是由[`publish`](#publish_userId) 和 [`methods`](#method_userId) 支持的. 
这些核心包会将用户文档的概念存储在数据库, 而其余的包则添加 [安全密码验证](#accounts_passwords), 
[集成第三方登录验证服务](#meteor_loginwithexternalservice) 和一个[预制的用户界面](#accountsui)

基本账号系统是在 `accounts-base` 包中, 但应用通常会自动包含以下一个登录提供者包: `accounts-password`, `accounts-facebook`,
                                                   `accounts-github`, `accounts-google`, `accounts-meetup`,
                                                   `accounts-twitter`, 或 `accounts-weibo`.

{{> autoApiBox "Meteor.user"}}

从[`Meteor.users`](#meteor_users)集合中可以获得当前(登录)用户的用户记录

在客户端, 这将会是从服务器端发布的文档中字段的子集(其余字段无法在客户端获得). 默认地, 服务器端发布 `username`, `emails`, 和 `profile`
(用户可写). 查看[`Meteor.users`](#meteor_users)获得更多关于用户文档的字段.

{{> autoApiBox "Meteor.userId"}}

{{> autoApiBox "Meteor.users"}}

该集合包含每个注册用户的文档. 以下是某个用户文档的释例:

    {
      _id: "bbca5d6a-2156-41c4-89da-0329e8c99a4f",  // Meteor.userId()
      username: "cool_kid_13", // unique name
      emails: [
        // each email address can only belong to one user.
        { address: "cool@example.com", verified: true },
        { address: "another@different.com", verified: false }
      ],
      createdAt: Wed Aug 21 2013 15:16:52 GMT-0700 (PDT),
      profile: {
        // The profile is writable by the user by default.
        name: "Joe Schmoe"
      },
      services: {
        facebook: {
          id: "709050", // facebook id
          accessToken: "AAACCgdX7G2...AbV9AZDZD"
        },
        resume: {
          loginTokens: [
            { token: "97e8c205-c7e4-47c9-9bea-8e2ccc0694cd",
              when: 1349761684048 }
          ]
        }
      }
    }

一个用户文档能够包含任意关于某个用户的数据的存储. 流星会对以下字段特殊对待:

- `username`: 唯一的字符串用来标示用户.
- `emails`:  一个对象数组, 其中对象包含了 `address` 和 `verified` 键;
  一个电子邮件地址只能最多属于一个用户. `verified` 是布尔类型, 如果为true, 那么表示该用户已经[验证了电子邮件地址](#accounts_verifyemail), 
  这是通过电子邮件发送一个令牌来实现的.
- `createdAt`: 用户文档创建的日期时间.
- `profile`: 一个该用户能够创建, 更新数据的对象. 如果你不想让用户编辑的数据, 那么不要存储在 `profile` 中, 除非你对于 `Meteor.users` 集合具有一个否定的规则.
- `services`: an Object containing data used by particular
  login services. For example, its `reset` field contains
  tokens used by [forgot password](#accounts_forgotpassword) links,
  and its `resume` field contains tokens used to keep you
  logged in between sessions. 一个包含被特定的登录服务所使用数据的对象. 例如, 它的 `reset` 字段包含着用于 [forgot password](#accounts_forgotpassword) 链接的的令牌,
  它的 `resume` 字段包含令牌, 可以让用户在不同的sessions中, 保持登录(状态).

Like all [Mongo.Collection](#collections)s, you can access all
documents on the server, but only those specifically published by the server are
available on the client. 
和所有的 [Mongo.Collection](#collections) 相似, 在服务器端, 你可以访问所有的数据, 而在客户端, 只有那些被服务器端发不出来的特殊数据才能被访问. 


By default, the current user's `username`, `emails` and `profile` are
published to the client. You can publish additional fields for the
current user with: 默认的, 现有用户的 `username`, `emails` 和 `profile` 会被发布到客户端. 也可以通过如下形式来发布额外的字段:

    // server 服务端
    Meteor.publish("userData", function () {
      if (this.userId) {
        return Meteor.users.find({_id: this.userId},
                                 {fields: {'other': 1, 'things': 1}});
      } else {
        this.ready();
      }
    });

    // client 客户端
    Meteor.subscribe("userData");

If the autopublish package is installed, information about all users
on the system is published to all clients. This includes `username`,
`profile`, and any fields in `services` that are meant to be public
(eg `services.facebook.id`,
`services.twitter.screenName`). Additionally, when using autopublish
more information is published for the currently logged in user,
including access tokens. This allows making API calls directly from
the client for services that allow this.

Users are by default allowed to specify their own `profile` field with
[`Accounts.createUser`](#accounts_createuser) and modify it with
`Meteor.users.update`. To allow users to edit additional fields, use
[`Meteor.users.allow`](#allow). To forbid users from making any modifications to
their user document:

    Meteor.users.deny({update: function () { return true; }});


{{> autoApiBox "Meteor.loggingIn"}}

For example, [the `accounts-ui` package](#accountsui) uses this to display an
animation while the login request is being processed.

{{> autoApiBox "Meteor.logout"}}

{{> autoApiBox "Meteor.logoutOtherClients"}}

For example, when called in a user's browser, connections in that browser
remain logged in, but any other browsers or DDP clients logged in as that user
will be logged out.

{{> autoApiBox "Meteor.loginWithPassword"}}

This function is provided by the `accounts-password` package. See the
[Passwords](#accounts_passwords) section below.


{{> autoApiBox "Meteor.loginWith<ExternalService>"}}

Available functions are:

* `Meteor.loginWithMeteorDeveloperAccount`
* `Meteor.loginWithFacebook`
* `Meteor.loginWithGithub`
* `Meteor.loginWithGoogle`
* `Meteor.loginWithMeetup`
* `Meteor.loginWithTwitter`
* `Meteor.loginWithWeibo`

These functions initiate the login process with an external
service (eg: Facebook, Google, etc), using OAuth. When called they open a new pop-up
window that loads the provider's login page. Once the user has logged in
with the provider, the pop-up window is closed and the Meteor client
logs in to the Meteor server with the information provided by the external
service.

<a id="requestpermissions" name="requestpermissions"></a>

In addition to identifying the user to your application, some services
have APIs that allow you to take action on behalf of the user. To
request specific permissions from the user, pass the
`requestPermissions` option the login function. This will cause the user
to be presented with an additional page in the pop-up dialog to permit
access to their data. The user's `accessToken` &mdash; with permissions
to access the service's API &mdash; is stored in the `services` field of
the user document. The supported values for `requestPermissions` differ
for each login service and are documented on their respective developer
sites:

- Facebook: <http://developers.facebook.com/docs/authentication/permissions/>
- GitHub: <http://developer.github.com/v3/oauth/#scopes>
- Google: <https://developers.google.com/accounts/docs/OAuth2Login#scopeparameter>
- Meetup: <http://www.meetup.com/meetup_api/auth/#oauth2-scopes>
- Twitter, Weibo, Meteor developer accounts: `requestPermissions` currently not supported

External login services typically require registering and configuring
your application before use. The easiest way to do this is with the
[`accounts-ui` package](#accountsui) which presents a step-by-step guide
to configuring each service. However, the data can be also be entered
manually in the `ServiceConfiguration.configurations` collection, which
is exported by the `service-configuration` package.

First, add the service configuration package:

```bash
meteor add service-configuration
```

Then, in your app:

```js
// first, remove configuration entry in case service is already configured
ServiceConfiguration.configurations.remove({
  service: "weibo"
});
ServiceConfiguration.configurations.insert({
  service: "weibo",
  clientId: "1292962797",
  loginStyle: "popup",
  secret: "75a730b58f5691de5522789070c319bc"
});
```

Each external service has its own login provider package and login function. For
example, to support GitHub login, run `$ meteor add accounts-github` and use the
`Meteor.loginWithGithub` function:

    Meteor.loginWithGithub({
      requestPermissions: ['user', 'public_repo']
    }, function (err) {
      if (err)
        Session.set('errorMessage', err.reason || 'Unknown error');
    });

Login service configuration is sent from the server to the client over DDP when
your app starts up; you may not call the login function until the configuration
is loaded. The function `Accounts.loginServicesConfigured()` is a reactive data
source that will return true once the login service is configured; you should
not make login buttons visible or active until it is true.

{{> autoApiBox "currentUser"}}

{{> autoApiBox "loggingIn"}}

{{> autoApiBox "Accounts.config"}}
{{> autoApiBox "Accounts.ui.config"}}

Example:

    Accounts.ui.config({
      requestPermissions: {
        facebook: ['user_likes'],
        github: ['user', 'repo']
      },
      requestOfflineToken: {
        google: true
      },
      passwordSignupFields: 'USERNAME_AND_OPTIONAL_EMAIL'
    });

{{> autoApiBox "Accounts.validateNewUser"}}

This can be called multiple times. If any of the functions return `false` or
throw an error, the new user creation is aborted. To set a specific error
message (which will be displayed by [`accounts-ui`](#accountsui)), throw a new
[`Meteor.Error`](#meteor_error).

Example:

    // Validate username, sending a specific error message on failure.
    Accounts.validateNewUser(function (user) {
      if (user.username && user.username.length >= 3)
        return true;
      throw new Meteor.Error(403, "Username must have at least 3 characters");
    });
    // Validate username, without a specific error message.
    Accounts.validateNewUser(function (user) {
      return user.username !== "root";
    });

If the user is being created as part of a login attempt from a client (eg,
calling [`Accounts.createUser`](#accounts_createuser) from the client, or
[logging in for the first time with an external
service](#meteor_loginwithexternalservice)), these callbacks are called *before*
the [`Accounts.validateLoginAttempt`](#accounts_validateloginattempt)
callbacks. If these callbacks succeed but those fail, the user will still be
created but the connection will not be logged in as that user.

{{> autoApiBox "Accounts.onCreateUser"}}

Use this when you need to do more than simply accept or reject new user
creation. With this function you can programatically control the
contents of new user documents.

The function you pass will be called with two arguments: `options` and
`user`. The `options` argument comes
from [`Accounts.createUser`](#accounts_createuser) for
password-based users or from an external service login flow. `options` may come
from an untrusted client so make sure to validate any values you read from
it. The `user` argument is created on the server and contains a
proposed user object with all the automatically generated fields
required for the user to log in.

The function should return the user document (either the one passed in or a
newly-created object) with whatever modifications are desired. The returned
document is inserted directly into the [`Meteor.users`](#meteor_users) collection.

The default create user function simply copies `options.profile` into
the new user document. Calling `onCreateUser` overrides the default
hook. This can only be called once.

Example:

<!-- XXX replace d6 with _.random once we have underscore 1.4.2 -->

    // Support for playing D&D: Roll 3d6 for dexterity
    Accounts.onCreateUser(function(options, user) {
      var d6 = function () { return Math.floor(Random.fraction() * 6) + 1; };
      user.dexterity = d6() + d6() + d6();
      // We still want the default hook's 'profile' behavior.
      if (options.profile)
        user.profile = options.profile;
      return user;
    });


{{> autoApiBox "Accounts.validateLoginAttempt"}}

Call `validateLoginAttempt` with a callback to be called on login
attempts.  It returns an object with a single method, `stop`.  Calling
`stop()` unregisters the callback.

When a login attempt is made, the registered validate login callbacks
are called with a single argument, the attempt info object:

<dl class="objdesc">
{{#dtdd name="type" type="String"}}
  The service name, such as "password" or "twitter".
{{/dtdd}}

{{#dtdd name="allowed" type="Boolean"}}
  Whether this login is allowed and will be successful (if not aborted
  by any of the validateLoginAttempt callbacks).  False if the login
  will not succeed (for example, an invalid password or the login was
  aborted by a previous validateLoginAttempt callback).
{{/dtdd}}

{{#dtdd name="error" type="Exception"}}
  When `allowed` is false, the exception describing why the login
  failed.  It will be a `Meteor.Error` for failures reported to the
  user (such as invalid password), and can be a another kind of
  exception for internal errors.
{{/dtdd}}

{{#dtdd name="user" type="Object"}}
  When it is known which user was attempting to login, the Meteor user
  object.  This will always be present for successful logins.
{{/dtdd}}

{{#dtdd name="connection" type="Object"}}
  The `connection` object the request came in on. See
  [`Meteor.onConnection`](#meteor_onconnection) for details.
{{/dtdd}}

{{#dtdd name="methodName" type="String"}}
  The name of the Meteor method being used to login.
{{/dtdd}}

{{#dtdd name="methodArguments" type="Array"}}
  An array of the arguments passed to the login method.
{{/dtdd}}
</dl>

A validate login callback must return a truthy value for the login to
proceed.  If the callback returns a falsy value or throws an
exception, the login is aborted.  Throwing a `Meteor.Error` will
report the error reason to the user.

All registered validate login callbacks are called, even if one of the callbacks
aborts the login.  The later callbacks will see the `allowed` field set to
`false` since the login will now not be successful.  This allows later callbacks
to override an error from a previous callback; for example, you could override
the "Incorrect password" error with a different message.

Validate login callbacks that aren't explicitly trying to override a previous
error generally have no need to run if the attempt has already been determined
to fail, and should start with

    if (!attempt.allowed)
      return false;


{{> autoApiBox "Accounts.onLogin"}}

See description of [Accounts.onLoginFailure](#accounts_onloginfailure)
for details.

{{> autoApiBox "Accounts.onLoginFailure"}}

Either the `onLogin` or the `onLoginFailure` callbacks will be called
for each login attempt. The `onLogin` callbacks are called after the
user has been successfully logged in. The `onLoginFailure` callbacks are
called after a login attempt is denied.

These functions return an object with a single method, `stop`.  Calling
`stop()` unregisters the callback.

The callbacks are called with a single argument, the same attempt info
object as [`validateLoginAttempt`](#accounts_validateloginattempt).
{{/template}}

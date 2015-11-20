{{#template name="apiCore"}}

<h2 id="core"><span>Meteor Core</span></h2>

{{> autoApiBox "Meteor.isClient"}}
{{> autoApiBox "Meteor.isServer"}}

{{#note}}
`Meteor.isServer` can be used to limit where code runs, but it does not
prevent code from being sent to the client. Any sensitive code that you
don't want served to the client, such as code containing passwords or
authentication mechanisms, should be kept in the `server` directory.
`Meteor.isServer` 可以被用来限制代码在何处运行, 但不能防止代码不被发送到客户端. 
任何敏感的数据你不想发送到客户端, 如包含密码或验证机制的代码, 应该保存在 `server` 目录下.
{{/note}}

{{> autoApiBox "Meteor.isCordova"}}

{{> autoApiBox "Meteor.startup"}}

On a server, the function will run as soon as the server process is
finished starting. On a client, the function will run as soon as the DOM
is ready.


The `startup` callbacks are called in the same order as the calls to
`Meteor.startup` were made.

On a client, `startup` callbacks from packages will be called
first, followed by `<body>` templates from your `.html` files,
followed by your application code.

    // On server startup, if the database is empty, create some initial data.
    if (Meteor.isServer) {
      Meteor.startup(function () {
        if (Rooms.find().count() === 0) {
          Rooms.insert({name: "Initial room"});
        }
      });
    }

{{> autoApiBox "Meteor.wrapAsync"}}

{{> autoApiBox "Meteor.absoluteUrl"}}

{{> autoApiBox "Meteor.settings"}}

{{> autoApiBox "Meteor.release"}}

{{/template}}
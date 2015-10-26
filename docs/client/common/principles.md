{{#template name="principles"}}

<h2 id="sevenprinciples">Principles of Meteor 流星原则</h2>

- _Data on the Wire_. Meteor doesn't send HTML over the network. The server sends data and
lets the client render it. _数据连线_. 流星不通过网络发送HTML. 服务端发送数据让客户端渲染. 

- _One Language._ Meteor lets you write both the client and the server parts of your
application in JavaScript. _一种语言_. 流星可以让你使用JavaScript来撰写应用的客户端和服务端.

- _Database Everywhere_. You can use the same methods to access your
database from the client or the server. _各处都有数据库_. 你可以使用同样的方法来存取数据库,无论是在客户端或在服务端.

- _Latency Compensation_. On the client, Meteor prefetches data and simulates models to make it look like server method calls return instantly. _延时补偿_. 在客户端, 流星可以预先回取数据并模拟相应的模型,使得服务端方法看上去是立即返回的.

- _Full Stack Reactivity_. In Meteor, realtime is the default. All layers, from
database to template, update themselves automatically when necessary. _全栈式响应性_. 在流星中, 实时性是默认的. 所有的层, 从数据库到模版, 都会在有必要的时候自动更新自身.

- _Embrace the Ecosystem_. Meteor is open source and integrates with existing open source tools and frameworks. _拥抱生态系统_. 流星是开源的, 也继承了现有的开源工具和框架.

- _Simplicity Equals Productivity_. The best way to make something
seem simple is to have it actually _be_ simple. Meteor's main functionality has
clean, classically beautiful APIs. _简单就等于生产率_. 让某件事物看上去简单的最好办法就是, 其自身真的很简单. 流星主要功能是简洁,经典,优美的API.

{{/template}}
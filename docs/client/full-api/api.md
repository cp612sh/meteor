{{#template name="api"}}

<h1 id="api">The Meteor API 流星的API</h1>

Your JavaScript code can run in two environments: the *client* (browser), and
the *server* (a [Node.js](http://nodejs.org/) container on a server).  For each
function in this API reference, we'll indicate if the function is available just
on the client, just on the server, or *Anywhere*. 

你的 JavaScript 代码可以运行在两套环境中: *客户端* (浏览器), 以及
*服务端* (某个在服务器上的 [Node.js](http://nodejs.org/) 容器).  对于每个在此API指南中的函数,  
我们会指明该方法只运行在客户端, 还是只能运行在服务端, 或可以运行在 *两端* .

{{> apiCore}}
{{> apiPubsub}}
{{> apiMethods}}
{{> apiCheck}}
{{> apiConnections}}
{{> apiCollections}}
{{> apiSession}}
{{> apiAccounts}}
{{> apiPasswords}}
{{> apiTemplates}}
{{> apiBlaze}}
{{> apiTimers}}
{{> apiTracker}}
{{> apiReactiveVar}}
{{> apiEjson}}
{{> apiHttp}}
{{> apiEmail}}
{{> apiAssets}}
{{> apiPackagejs}}
{{> apiMobileConfig}}

{{/template}}

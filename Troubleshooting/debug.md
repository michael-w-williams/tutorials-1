# Debuging

If you're interacting with functions via the `fn` CLI, you can enable debug
mode to see the full details of the HTTP requests going to the Fn server and
the responses. The `fn` CLI simply wraps the Fn API to make it easier to
manage your applications and functions. You can always use `curl` but the CLI 
is much more convenient!

You enable debug mode by adding `DEBUG=1` before `fn` on each command.  For
example try the following:

![user input](images/userinput.png)
>```sh
> DEBUG=1 fn ls apps
>```

Which, with debugging turn on, returns the following:

```sh
GET /v2/apps HTTP/1.1
Host: localhost:8080
User-Agent: Go-http-client/1.1
Accept: application/json
Accept-Encoding: gzip


HTTP/1.1 200 OK
Content-Length: 977
Content-Type: application/json; charset=utf-8
Date: Sun, 13 Oct 2019 16:45:56 GMT


{"items":[{"id":"01DQ2STN6KNG8G00GZJ000001Q","name":"tutorial","syslog_url":"tcp://logs3.papertrailapp.com:NNNN","created_at":"2019-10-13T14:54:45.459Z","updated_at":"2019-10-13T15:55:50.628Z"}]}
NAME		ID
tutorial	01DQ2STN6KNG8G00GZJ000001Q
```

Also you can enable debug mode next time you invoke your function. For example:

![user input](images/userinput.png)
>```sh
> DEBUG=1 fn invoke tutorial trouble
>```

```sh
GET /v2/apps?name=tutorial HTTP/1.1
Host: localhost:8080
User-Agent: Go-http-client/1.1
Accept: application/json
Accept-Encoding: gzip


HTTP/1.1 200 OK
Content-Length: 196
Content-Type: application/json; charset=utf-8
Date: Fri, 13 Oct 2019 20:19:43 GMT

{"items":[{"id":"01DQ2STN6KNG8G00GZJ000001Q","name":"tutorial","syslog_url":"tcp://logs3.papertrailapp.com:54995","created_at":"2019-10-13T14:54:45.459Z","updated_at":"2019-10-13T15:55:50.628Z"}]}
GET /v2/fns?app_id=01DQ2STN6KNG8G00GZJ000001Q&name=trouble HTTP/1.1
Host: localhost:8080
User-Agent: Go-http-client/1.1
Accept: application/json
Accept-Encoding: gzip


HTTP/1.1 200 OK
Content-Length: 387
Content-Type: application/json; charset=utf-8
Date: Fri, 13 Oct 2019 20:19:43 GMT

{"items":[{"id":"01DQ2SVR4VNG8G00GZJ000001R","name":"trouble","app_id":"01DQ2STN6KNG8G00GZJ000001Q","image":"angelicaviorato/trouble:0.0.3","memory":128,"timeout":30,"idle_timeout":30,"config":null,"annotations":{"fnproject.io/fn/invokeEndpoint":"http://localhost:8080/invoke/01DQ2SVR4VNG8G00GZJ000001R"},"created_at":"2019-10-13T14:55:21.243Z","updated_at":"2019-10-13T15:00:22.153Z"}]}
POST /invoke/01DQ2SVR4VNG8G00GZJ000001R HTTP/1.1
Host: localhost:8080
User-Agent: Go-http-client/1.1
Content-Length: 0
Content-Type: text/plain
Accept-Encoding: gzip


HTTP/1.1 502 Bad Gateway
Content-Length: 30
Content-Type: application/json; charset=utf-8
Date: Fri, 13 Oct 2019 20:19:45 GMT
Fn-Call-Id: 01DQG8D8XPNG8G00GZJ0000001

{"message":"function failed"}

Error invoking function. status: 502 message: function failed

```

All debug output is written to stderr while the normal response is written
to stdout so it's easy to capture or pipe either for processing

**Go:** [Back to Contents](../README.md)

diagnostic-buildpack
====================

A sample buildpack which is used in diagnosing how cloudfoundry behaves w.r.t. buildpacks

In particular it produces output logs on stdout and stderr to help in diagnosting when a CF instance swallows them.

Usage and sample output:

```
 $cf push diag-buildpack -p ./anyFile.txt -b https://github.com/gberche-orange/diagnostic-buildpack.git

 Updating app diag-buildpack in org myorg / space development as me@me.org...
 OK
 
 Uploading diag-buildpack...
 Uploading app files from: ./bin
 Uploading 1.7K, 3 files
 OK
 
 Stopping app diag-buildpack in org myorg / space development as me@me.org...
 OK
 
 Starting app diag-buildpack in org myorg / space development as me@me.org...
 OK
 -----> Downloaded app package (4.0K)
     Cloning into '/tmp/buildpacks/diagnostic-buildpack'...
     Compile echoing on stdout
 Fri Aug  1 13:07:15 UTC 2014
 Compile echoing on stderr
 Fri Aug  1 13:07:15 UTC 2014
 Compile echoing on stdout after waiting 1s
 -----> Uploading droplet (4.0K)
 
 1 of 1 instances running
 
 App started
 
 Showing health and status for app diag-buildpack in org myorg / space development as me@me.org...
 OK
 
 requested state: started
 instances: 1/1
 usage: 1G x 1 instances
 urls: diag-buildpack.cfapps.io
 
      state     since                    cpu    memory       disk
 #0   running   2014-08-01 03:07:22 PM   0.0%   552K of 1G   56K of 1G
 Endpoint deprecated
 ```

```
$ curl http://diag-buildpack.cfapps.io
hi from a diag buildpack

$ cf logs diag-buildpack --recent
 [...]
2014-08-01T15:44:53.61+0200 [STG]     OUT -----> Downloaded app package (4.0K)
2014-08-01T15:44:53.77+0200 [STG]     OUT -----> Downloaded app buildpack cache (4.0K)
2014-08-01T15:44:53.87+0200 [STG]     ERR     Cloning into '/tmp/buildpacks/diagnostic-buildpack'...
2014-08-01T15:44:54.25+0200 [STG]     OUT     Compile echoing on stdout
2014-08-01T15:44:54.25+0200 [STG]     OUT Fri Aug  1 13:44:54 UTC 2014
2014-08-01T15:44:54.25+0200 [STG]     ERR Compile echoing on stderr
2014-08-01T15:44:54.26+0200 [STG]     OUT Fri Aug  1 13:44:54 UTC 2014
2014-08-01T15:44:55.26+0200 [STG]     OUT Compile echoing on stdout after waiting 1s
2014-08-01T15:44:55.65+0200 [STG]     OUT -----> Uploading droplet (4.0K)
2014-08-01T15:45:02.51+0200 [DEA]     OUT Starting app instance (index 0) with guid 35a50f05-52da-4319-bec2-796503bd85f9
2014-08-01T15:45:03.97+0200 [App/0]   ERR     stderr echoing in release cmd
2014-08-01T15:45:03.97+0200 [App/0]   OUT     stdout echoing in release cmd
2014-08-01T15:45:41.40+0200 [RTR]     OUT diag-buildpack.cfapps.io - [01/08/2014:13:45:41 +0000] "GET / HTTP/1.1" 200 25-937c-4a89-59c0-8e89ac87ee1c response_time:0.002309627 app_id:35a50f05-52da-4319-bec2-796503bd85f9
2014-08-01T15:48:35.50+0200 [App/0]   OUT GET / HTTP/1.1
2014-08-01T15:48:35.50+0200 [App/0]   OUT Host: diag-buildpack.cfapps.io
2014-08-01T15:48:35.50+0200 [App/0]   OUT User-Agent: curl/7.30.0
2014-08-01T15:48:35.50+0200 [App/0]   OUT Accept: */*
2014-08-01T15:48:35.50+0200 [App/0]   OUT Cache-Control: max-stale=0
2014-08-01T15:48:35.50+0200 [App/0]   OUT If-Modified-Since: Fri, 01 Aug 2014 13:45:41 GMT
2014-08-01T15:48:35.50+0200 [App/0]   OUT X-Cf-Applicationid: 35a50f05-52da-4319-bec2-796503bd85f9
2014-08-01T15:48:35.50+0200 [App/0]   OUT X-Cf-Instanceid: 66b81506ac7541109176dd156bc1c1a9f7298dc2f30a4fbebc96fbde7be7d
2014-08-01T15:48:35.50+0200 [App/0]   OUT X-Forwarded-For: 80.12.110.207, 10.10.66.34
2014-08-01T15:48:35.50+0200 [App/0]   OUT X-Forwarded-Port: 80
2014-08-01T15:48:35.50+0200 [App/0]   OUT X-Forwarded-Proto: http
2014-08-01T15:48:35.50+0200 [App/0]   OUT X-Request-Start: 1406900915507
2014-08-01T15:48:35.50+0200 [App/0]   OUT X-Vcap-Request-Id: 0125b780-f752-40f6-401f-7baffd24c061
2014-08-01T15:48:35.50+0200 [App/0]   OUT Accept-Encoding: gzip
```

You may define the `FAIL` env to trigger a staging failure
```
 $ cf push diag-buildpack -p ./anyFile.txt -b https://github.com/gberche-orange/diagnostic-buildpack.git --no-start
 $ cf set-env diag-buildpack FAIL true
 $ cf start diag-buildpack 
```       

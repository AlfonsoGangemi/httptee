# httptee
Tee http diff server for comparing how backends respond to requests.

**httptee** will respond to all requests by proxying them to the *base* backend.
The requests are also sent to the *compare* backend to be diffed.

tested with go1.17.2 on Ubuntu_18.04

## Usage
```
$ go install github.com/AlfonsoGangemi/httptee

$ httptee --help
Usage of httptee:
  -addr=":8080": TCP address to bind
  -base=":8081": main upstream host to proxy
  -compare=":8082": secondary upstream host to proxy
  -verbose=false: verbose logging
```

### Run local
Download the go script
```
$ go mod init local/httptee
$ go get github.com/sergi/go-diff/diffmatchpatch
$ go get github.com/sirupsen/logrus

$ go run . --help

$ go run . -base="server-a:80" -compare="server-b:80" -verbose
```
call service (via browser or curl)
```
curl -s -o /dev/null http://localhost:8080/path?id=116&type=X
```


## Example
Server:
```
$ httptee -base="cache01.nyc.frustra.org:80" -compare="cache01.sfo.frustra.org:80"
INFO[0000] Listening on: :8080
INFO[0002] Request:
GET / HTTP/1.1
Host: localhost:8080
User-Agent: curl/7.42.0
Accept: */*


INFO[0002] Response code: HTTP/1.1 301 Moved Permanently
INFO[0002] Headers differ:
- X-Served-By: cache01.nyc
X-Varnish: 31037300
+ X-Served-By: cache01.sfo
X-Varnish: 10491072
```

Request:
```
$ curl -i localhost:8080
HTTP/1.1 301 Moved Permanently
Date: Tue, 19 May 2015 03:47:02 GMT
Server: Varnish
X-Varnish: 31097580
Vary: Accept-Encoding
X-Served-By: cache01.nyc
Location: https://frustra.org
Content-Length: 21
Connection: keep-alive

301 Moved Permanently
```

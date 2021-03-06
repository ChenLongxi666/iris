## Hardware

* Processor: Intel(R) Core(TM) **i7-4710HQ** CPU @ 2.50GHz 2.50GHz
* RAM: **8.00 GB**

## Software

* OS: Microsoft **Windows** [Version **10**.0.15063], power plan is "High performance"
* HTTP Benchmark Tool: https://github.com/codesenberg/bombardier, latest version **1.1**
* **.NET Core**: https://www.microsoft.com/net/core, latest version **2.0**
* **Iris**: https://github.com/kataras/iris, latest version **8.3** built with [go1.8.3](https://golang.org)

# .NET Core MVC vs Iris MVC

The first test will contain a simple application with a text response and the second will render templates + a layout.

## Simple

We will compare two identical things here, in terms of application, the expected response and the stability of their run times, so we will not try to put more things in the game like `JSON` or `XML` encoders and decoders, just a simple text message. To achieve a fair comparison we will use the MVC architecture pattern on both sides, Go and .NET Core.

### .NET Core MVC

```bash
$ cd netcore-mvc
$ dotnet run -c Release
Hosting environment: Production
Content root path: C:\mygopath\src\github.com\kataras\iris\_benchmarks\netcore-mvc
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

```bash
$ bombardier -c 125 -n 5000000 http://localhost:5000/api/values/5
Bombarding http://localhost:5000/api/values/5 with 5000000 requests using 125 connections
 5000000 / 5000000 [=====================================================================================] 100.00% 2m3s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec     40226.03    8724.30     161919
  Latency        3.09ms     1.40ms   169.12ms
  HTTP codes:
    1xx - 0, 2xx - 5000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:     8.91MB/s
```

### Iris MVC

```bash
$ cd iris-mvc
$ go run main.go
Now listening on: http://localhost:5000
Application started. Press CTRL+C to shut down.
```

```bash
$ bombardier -c 125 -n 5000000 http://localhost:5000/api/values/5
Bombarding http://localhost:5000/api/values/5 with 5000000 requests using 125 connections
 5000000 / 5000000 [======================================================================================] 100.00% 47s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec    105643.81    7687.79     122564
  Latency        1.18ms   366.55us    22.01ms
  HTTP codes:
    1xx - 0, 2xx - 5000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:    19.65MB/s
```

Click [here](screens) to navigate to the screenshots.

### Summary

* Time to complete the `5000000 requests` - smaller is better.
* Reqs/sec - bigger is better.
* Latency - smaller is better
* Throughput - bigger is better.
* Memory usage - smaller is better.
* LOC (Lines Of Code) - smaller is better.

.NET Core MVC Application, written using 86 lines of code, ran for **2 minutes and 3 seconds** serving **40226.03** requests per second within **3.09ms** latency in average and **169.12ms** max, the memory usage of all these was ~123MB (without the dotnet host).

Iris MVC Application, written using 27 lines of code, ran for **47 seconds** serving **105643.71** requests per second within **1.18ms** latency in average and **22.01ms** max, the memory usage of all these was ~12MB.

#### Update: 20 August 2017

As [Josh Clark](https://twitter.com/clarkis117) and [Scott Hanselman‏](https://twitter.com/shanselman)‏ pointed out [on this status](https://twitter.com/shanselman/status/899005786826788865), on .NET Core MVC `Startup.cs` file the line with `services.AddMvc();` can be replaced with `services.AddMvcCore();`. I followed their helpful instructions and re-run the benchmarks. The article now contains the latest benchmark output for the .NET Core application with the change both Josh and Scott noted.

The twitter conversion: https://twitter.com/MakisMaropoulos/status/899113215895982080

For those who want to compare with the standard services.AddMvc(); you can see the old output by pressing [here](screens/5m_requests_netcore-mvc.png).

## MVC + Templates

Let’s run one more benchmark, spawn `1000000 requests` but this time we expect HTML generated by templates via the view engine.

### .NET Core MVC with Templates

```bash
$ cd netcore-mvc-templates
$ dotnet run -c Release
Hosting environment: Production
Content root path: C:\mygopath\src\github.com\kataras\iris\_benchmarks\netcore-mvc-templates
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

```bash
$ bombardier -c 125 -n 1000000 http://localhost:5000
Bombarding http://localhost:5000 with 1000000 requests using 125 connections
 1000000 / 1000000 [=====================================================================================] 100.00% 1m20s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec     11738.60    7741.36     125887
  Latency       10.10ms    22.10ms      1.97s
  HTTP codes:
    1xx - 0, 2xx - 1000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:    89.03MB/s
```

### Iris MVC with Templates

```bash
$ cd iris-mvc-templates
$ go run main.go
Now listening on: http://localhost:5000
Application started. Press CTRL+C to shut down.
```

```bash
$ bombardier -c 125 -n 1000000 http://localhost:5000
Bombarding http://localhost:5000 with 1000000 requests using 125 connections
 1000000 / 1000000 [======================================================================================] 100.00% 37s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec     26656.76    1944.73      31188
  Latency        4.69ms     1.20ms    22.52ms
  HTTP codes:
    1xx - 0, 2xx - 1000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:   192.51MB/s
```

### Summary

* Time to complete the `1000000 requests` - smaller is better.
* Reqs/sec - bigger is better.
* Latency - smaller is better
* Memory usage - smaller is better.
* Throughput - bigger is better.

.NET Core MVC with Templates Application ran for **1 minute and 20 seconds** serving **11738.60** requests per second with **89.03MB/s** within **10.10ms** latency in average and **1.97s** max, the memory usage of all these was ~193MB (without the dotnet host).

Iris MVC with Templates Application ran for **37 seconds** serving **26656.76** requests per second with **192.51MB/s** within **1.18ms** latency in average and **22.52ms** max, the memory usage of all these was ~17MB.

# .NET Core (Kestrel) vs Iris

_Monday, 21 August 2017_

This time we will compare the speed of the “low-level” .NET Core’s server implementation named Kestrel and Iris’ “low-level” handlers, we will test two simple applications, the first will be the same as our previous application but written using handlers and the second test will contain a single route which sets and gets a session value(string) based on a key(string).

## Simple

Spawn `1000000 requests` with 125 different "threads", targeting to a dynamic registered route path, responds with a simple "value" text.

### .NET Core (Kestrel)

```bash
$ cd netcore
$ dotnet run -c Release
Hosting environment: Production
Content root path: C:\mygopath\src\github.com\kataras\iris\_benchmarks\netcore
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

```bash
$ bombardier -c 125 -n 1000000 http://localhost:5000/api/values/5
Bombarding http://localhost:5000/api/values/5 with 1000000 requests using 125 connections
 1000000 / 1000000 [======================================================================================] 100.00% 10s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec     97884.57    8699.94     110509
  Latency        1.28ms   682.63us    61.04ms
  HTTP codes:
    1xx - 0, 2xx - 1000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:    17.73MB/s
```

### Iris

```bash
$ cd iris
$ go run main.go
Now listening on: http://localhost:5000
Application started. Press CTRL+C to shut down.
```

```bash
$ bombardier -c 125 -n 1000000 http://localhost:5000/api/values/5
Bombarding http://localhost:5000/api/values/5 with 1000000 requests using 125 connections
 1000000 / 1000000 [=======================================================================================] 100.00% 8s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec    117917.79    4437.04     125614
  Latency        1.06ms   278.12us    19.03ms
  HTTP codes:
    1xx - 0, 2xx - 1000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:    21.93MB/s
```

### Node.js (Express)

```bash
$ cd expressjs
$ npm install
$ node app.js
Now listening on: http://localhost:5000
Application started. Press CTRL+C to shut down.
```

```bash
$ bombardier -c 125 -n 1000000 http://localhost:5000/api/values/5
Bombarding http://localhost:5000/api/values/5 with 1000000 requests using 125 connections
 1000000 / 1000000 [=======================================================================================] 100.00% 1m25s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec      11665.30   628.41       21978
  Latency        10.72ms   1.45ms    112.10ms
  HTTP codes:
    1xx - 0, 2xx - 1000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:    3.14MB/s
```

### Summary

* Time to complete the `1000000 requests` - smaller is better.
* Reqs/sec - bigger is better.
* Latency - smaller is better
* Throughput - bigger is better.
* LOC (Lines Of Code) - smaller is better.

.NET Core (Kestrel) Application written using **63 code of lines** ran for **10 seconds** serving **97884.57** requests per second with **17.73MB/s** within **1.28ms** latency in average and **61.04ms** max.

Iris Application written using **14 code of lines** ran for **8 seconds** serving **117917.79** requests per second with **21.93MB/s** within **1.06ms** latency in average and **19.03ms** max.

Node.js (Express) Application written using **12 code of lines** ran for **1 minute and 25 seconds** serving **11665.30** requests per second with **3.14MB/s** within **10.72ms** latency in average and **112.10ms** max.

## Sessions

Spawn `5000000 requests` with 125 different "threads" targeting a static request path, sets and gets a session based on the name `"key"` and string value `"value"` and write that session value to the response stream.

### .NET Core (Kestrel) with Sessions

```bash
$ cd netcore-sessions
$ dotnet run -c Release
Hosting environment: Production
Content root path: C:\mygopath\src\github.com\kataras\iris\_benchmarks\netcore-sessions
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

```bash
$ bombardier -c 125 -n 5000000 http://localhost:5000/setget
Bombarding http://localhost:5000/setget with 5000000 requests using 125 connections
 5000000 / 5000000 [====================================================================================] 100.00% 2m40s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec     31844.77   13856.19     253746
  Latency        4.02ms    15.57ms      0.96s
  HTTP codes:
    1xx - 0, 2xx - 5000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:    14.51MB/s
```

### Iris with Sessions

```bash
$ cd iris-sessions
$ go run main.go
Now listening on: http://localhost:5000
Application started. Press CTRL+C to shut down.
```

```bash
$ bombardier -c 125 -n 5000000 http://localhost:5000/setget
Bombarding http://localhost:5000/setget with 5000000 requests using 125 connections
 5000000 / 5000000 [====================================================================================] 100.00% 1m15s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec     66749.70   32110.67     110445
  Latency        1.88ms     9.13ms      1.94s
  HTTP codes:
    1xx - 0, 2xx - 5000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:    20.65MB/s
```

### Node.js (Express) with Sessions

```bash
$ cd expressjs-sessions
$ npm install
$ node app.js
Now listening on: http://localhost:5000
Application started. Press CTRL+C to shut down.
```

```bash
$ bombardier -c 125 -n 5000000 http://localhost:5000/setget
Bombarding http://localhost:5000/setget with 5000000 requests using 125 connections
 5000000 / 5000000 [====================================================================================] 100.00% 15m47s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec      5634.27   2317.30         9945
  Latency       22.17ms    8.19ms     119.08ms
  HTTP codes:
    1xx - 0, 2xx - 5000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:    1.48MB/s
```

### Summary

* Time to complete the `5000000 requests` - smaller is better.
* Reqs/sec - bigger is better.
* Latency - smaller is better
* Throughput - bigger is better.

.NET Core with Sessions Application ran for **2 minutes and 40 seconds** serving **31844.77** requests per second with **14.51MB/s** within **4.02ms** latency in average and **0.96s** max.

Iris with Sessions Application ran for **1 minute and 15 seconds** serving **66749.70** requests per second with **20.65MB/s** within **1.88ms** latency in average and **1.94s** max.

Node.js (Express) with Sessions Application ran for **15 minutes and 47 seconds** serving **5634.27** requests per second with **1.48MB/s** within **22.17ms** latency in average and **119.08ms** max.

> Click [here](screens) to navigate to the screenshots.

### Articles

 **Go vs .NET Core in terms of HTTP performance (Sa, 19 August 2017)**

- https://medium.com/@kataras/go-vs-net-core-in-terms-of-http-performance-7535a61b67b8
- https://dev.to/kataras/go-vsnet-core-in-terms-of-http-performance

**Iris Go vs .NET Core Kestrel in terms of HTTP performance (Mo, 21 August 2017)** 

- https://medium.com/@kataras/iris-go-vs-net-core-kestrel-in-terms-of-http-performance-806195dc93d5

**Thank you all** for the 100% green feedback, have fun!
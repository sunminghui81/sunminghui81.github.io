---
layout: post
keywords: 依赖注入 fx
description: 依赖注入 fx
title: 依赖注入 fx
categories: [Golang]
tags: [依赖注入]
group: archive
icon: google
---

## 概述
Fx是一个golang版本的依赖注入框架，在项目中的引用：
```Go
import "github.com/uber-go/fx"
```

### Fx用途
Fx应用程序，使用依赖注入来消除全局变量，而无需手动将函数连接在一起。
同时，```go.uber.org/fx/fxtest```也为Fx应用程序提供了端到端测试的能力。


## Fx应用

```Go
func NewHandler(logger *log.Logger) (http.Handler, error) {
    logger.Print("Executing NewHandler.")
    return http.HandlerFunc(func(http.ResponseWriter, *http.Request) {
        logger.Print("Got a request.")
    }), nil
}

// 为了实现下面这种长时的http server
//   srv, err := NewServer() // some long-running network server
//   if err != nil {
//     log.Fatalf("failed to construct server: %v", err)
//   }
//   // Construct other objects as necessary.
//   go srv.Start()
//   defer srv.Stop()

// 通过Fx实现的方法，在下面这个构造方法中，实现了server的启动并且返回这个
// server的handler 
func NewMux(lc fx.Lifecycle, logger *log.Logger) *http.ServeMux {
	logger.Print("Executing NewMux.")
	// 第一步，构造一个mux和server（mux -- multiplexer:多路器）
	// 在所有的handle都注册前，不启动server
	mux := http.NewServeMux()
	server := &http.Server{
		Addr:    ":8080",
		Handler: mux,
	}
	// 如果调用newmux，我们知道另一个功能正在使用mux。
    // 在这种情况下，我们将使用Lifecycle类型注册启动并停止我们的HTTP服务器的钩子。
	//
	// Hooks 按照依赖顺序执行. At startup, NewLogger's hooks 在 NewMux的hook之前运行。  
    //  在结束时，循序按照相反顺序
	//
	// OnStart产生错误，会返回一个error，并且中断启动过程，然后执行OnStop部分。
	//
	// OnStop执行错误会日志记录下这个错误，并继续执行其余的Hooks
	lc.Append(fx.Hook{
		// 为了缓解死锁的影响，Fx在OnStart和OnStop 的Hook增加了一个时间限制.
        // 默认的，hooks有总共15秒去执行完. 超时时间会通过 Go's usual context.Context 传递.
		OnStart: func(context.Context) error {
			logger.Print("Starting HTTP server.")
			// In production, we'd want to separate the Listen and Serve phases for
			// better error-handling.
			go server.ListenAndServe()
			return nil
		},
		OnStop: func(ctx context.Context) error {
			logger.Print("Stopping HTTP server.")
			return server.Shutdown(ctx)
		},
	})
	return mux
}

// Register在MUX上安装我们的HTTP处理程序。
//
// Register 是典型的顶层应用函数: 它需要一个普通类型，如servemux，
// 它通常来自第三方库，并将其引入包含我们应用程序逻辑的类型. 
// 在这个例子中，介绍包括注册HTTP处理程序。
// 其他典型示例包括注册RPC程序和启动队列消费者。
//
// FX调用这些函数调用，并从上面的构造函数不同地对待。
// 它们的参数仍然通过依赖项注入提供，并且它们可能仍然返回错误以指示故障，但忽略任何其他返回值。
//
// Unlike constructors, invocations are called eagerly. See the main function
// below for details.
func Register(mux *http.ServeMux, h http.Handler) {
	mux.Handle("/", h)
}

func main() {
	app := fx.New(
		// Provide 所有我们需要的构造函数, 告诉 Fx 我们想要如何去构造
        // *log.Logger, http.Handler, and *http.ServeMux types.
		// Remember that constructors are called lazily, so this block doesn't do
		// much on its own.
		fx.Provide(
			NewLogger,
			NewHandler,
			NewMux,
		),
		// 由于构造函数懒惰地称为，我们需要一些调用启动我们的应用程序。
        // 在这种情况下，我们将使用Register。由于它取决于http.handler和* http.servemux，
        // 因此调用它需要fx来使用上面的构造函数构建这些类型。
        // 由于我们调用NewMux，我们还注册生命周期挂钩以启动并停止HTTP服务器。
		fx.Invoke(Register),

		// 这部分是可选的. 使用下面的代码, 你可以控制Fx在哪logs5它的事件
		// 在本例中, we're using a NopLogger to keep
		// our test silent. Normally, you'll want to use an
		// fxevent.ZapLogger or an fxevent.ConsoleLogger.
		fx.WithLogger(
			func() fxevent.Logger {
				return fxevent.NopLogger
			},
		),
	)

	// In a typical application, we could just use app.Run() here. Since we
	// don't want this example to run forever, we'll use the more-explicit Start
	// and Stop.
	startCtx, cancel := context.WithTimeout(context.Background(), 15*time.Second)
	defer cancel()
	if err := app.Start(startCtx); err != nil {
		log.Fatal(err)
	}

	// Normally, we'd block here with <-app.Done(). Instead, we'll make an HTTP
	// request to demonstrate证明 that our server is running.
	if _, err := http.Get("http://localhost:8080/"); err != nil {
		log.Fatal(err)
	}

	stopCtx, cancel := context.WithTimeout(context.Background(), 15*time.Second)
	defer cancel()
	if err := app.Stop(stopCtx); err != nil {
		log.Fatal(err)
	}

}
```
----
```
Output:

Executing NewLogger.
Executing NewMux.
Executing NewHandler.
Starting HTTP server.
Got a request.
Stopping HTTP server.
```





## 参考
1. [fx Godoc](https://pkg.go.dev/go.uber.org/fx)
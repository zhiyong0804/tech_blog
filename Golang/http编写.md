# http编写

#### 注册捕获panic
```
func SimpleServer(w http.ResponseWriter, r *http.Request) {
  io.WriteString(w, "<h1>Hello world</h1>")
  panic("test panic")
}

func LogPanic(handle http.HandlerFunc) http.HandlerFunc {
  return func(w http.ResponseWriter, r *http.Request) {
    defer func() {
      if ret := recover(); ret != nil {
        fmt.Println("panic", r.RemoteAddr, " ret ", ret)
      }
    }()
    handle(w, r)
  }
}

func main() {
  http.HandleFunc("/test1", LogPanic(SimpleServer))
  if err := http.ListenAndServe(":8088", nil); err != nil {
  }
  select {}
}
```

# Fn Project with Golang 
เนื่อจากได้อ่านบทความของอาจารย์ Chanwit Kaewkasi > https://sites.google.com/site/chanwit/blogs/fnproject-for-implementing-faas

See https://github.com/fnproject/tutorials/blob/master/Introduction/README.md for guide more information.  
# First Function with hello FN ❤️
### install fn 
>```sh
>  brew install fn 
>```
or
>```sh
>  curl -LSs https://raw.githubusercontent.com/fnproject/cli/master/install | sh
>```

### รัน Fn Server 
>```sh
>  fn start
>```
### สร้าง Project hello_fn ด้วย 
>```sh
>  fn init --runtime go hello_fn
>```
### เข้าไปใน hello_fn
>```sh
>  cd hello_fn
>```
### เปลียน name จาก world เป็น fn
>```sh
> cat func.go
>```

```go
package main

import (
	"context"
	"encoding/json"
	"fmt"
	"io"

	fdk "github.com/fnproject/fdk-go"
)

func main() {
	fdk.Handle(fdk.HandlerFunc(myHandler))
}

type Person struct {
	Name string `json:"name"`
}

func myHandler(ctx context.Context, in io.Reader, out io.Writer) {
	p := &Person{Name: "FN"}
	json.NewDecoder(in).Decode(p)
	msg := struct {
		Msg string `json:"message"`
	}{
		Msg: fmt.Sprintf("Hello %s", p.Name),
	}
	json.NewEncoder(out).Encode(&msg)
}
```

### deploy hello_fn เข้า fn
>```sh
> fn deploy --app myapp --local
>```

### เมื่อเสร็จแล้วเรียกฟังชั่นด้วยคำสัง
>```sh
> curl http://localhost:8080/r/myapp/hello_fn
> or:
> fn call myapp /hello_fn
> or:
> curl -H "Content-Type: application/json" -d '{"name":"Bob"}' http://localhost:8080/r/myapp/hello_fn
>```

จะได้ผลลัพท์แบบนี้
>```sh
> {"message":"Hello FN"}
>```

ถึงตรงนี้หลายคนก็อยากจะดูหน้า monitoring ละ ซึ่งมันจะต้องใช้ docker image อีกตัวนึง ก็ตามนี้เลยครับ
>```sh
> docker run --rm -it --link fnserver:api -p 4000:4000 -e "FN_API_URL=http://api:8080" fnproject/ui
>```

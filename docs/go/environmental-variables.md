People are crazy when trying to get environmental variables to work in Go. I mean they are downloading a dependency just to get it to work?

Just rely on `direnv` and create a `.envrc`.

```bash
export DATABASE_URL="test.com"
```

```bash
direnv allow
direnv reload
```

```go
import (
	"os"
	"fmt"
)

func main() {
	fmt.Println(os.Getenv("DATABASE_URL"))
}
```
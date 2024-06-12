## Data Structures

```sh
python3 -m venv env
```

```go
package main

import (
	"fmt"
	"io/ioutil"
	"log"
	"net/http"
	"time"
)

type Page struct {
	URL  string
	size int
}

func responseSize(url string, channel chan Page) {
	response, err := http.Get(url)
	if err != nil {
		log.Fatal(err)
	}
	defer response.Body.Close()
	body, err := ioutil.ReadAll(response.Body)
	if err != nil {
		log.Fatal(err)
	}
	channel <- Page{URL: url, size: len(body)}
}

func main() {
	start := time.Now()
	pages := make(chan Page)
	urls := []string{"https://go.dev/", "https://go.dev/doc/", "https://gobyexample.com/"}
	for _, url := range urls {
		go responseSize(url, pages)
	}
	for i := 0; i < len(urls); i++ {
		page := <-pages
		fmt.Printf("%s: %d\n", page.URL, page.size)
	}
	end := time.Now()
	fmt.Println(end.Sub(start))
}

```

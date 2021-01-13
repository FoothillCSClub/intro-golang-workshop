# Workshop: Intro to Go Programming Language

CS Club Tutorial Workshop for An Introduction to 
the Go Programming Language. 
Based loosely on:
https://golang.org/doc/articles/wiki/.


# New Project 

* Go to https://repl.it/ and create a new Go project.
* Add a folder called "pages"
* Example: https://repl.it/@fractalbach/GolangWikiWorkshop

# Saving and Loading Wiki Pages

```go
package main

import (
    "fmt"
    "io/ioutil"
)

const folder = "pages"

type Page struct {
    Title, Body string
}

func (p *Page) saveToFile() error {
    filename := folder + "/" + p.Title + ".txt"
    return ioutil.WriteFile(filename, []byte(p.Body), 0600)
}

func loadPageFromFile(title string) (*Page, error) {
    filename := folder + "/" + title + ".txt"
    data, err := ioutil.ReadFile(filename)
    page := &Page{
        Title: title, 
        Body: string(data),
    }
    return page, err
}

func main() {
    p1 := Page{Title: "index", Body: "Welcome to CS Club Wiki!"}
    p1.saveToFile()
    p2, _ := loadPageFromFile("index")
    fmt.Println(p2.Body)
}
```



# HTML Template

Create a file called `template.html` in the project's root directory.
This will be used to display each of the wiki pages.
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
</head> 
<body>
  <h1>CS Club Wiki - {{.Title}}</h1>
  <form>
    <p><textarea name="body" rows="20" cols="80">{{.Body}}</textarea></p>
    <p><button type="submit" formmethod="post">Save</button></p>
  </form>
</body>
</html>
```

# Wiki with Networking

```go
package main

import (
    "io/ioutil"
    "net/http"
    "log"
    "html/template"
)

const folder = "pages"

var pageTemplate = template.Must(template.ParseFiles("template.html"))

type Page struct {
    Title, Body string
}

func (p *Page) saveToFile() error {
    filename := folder + "/" + p.Title + ".txt"
    return ioutil.WriteFile(filename, []byte(p.Body), 0600)
}

func loadPageFromFile(title string) (*Page, error) {
    filename := folder + "/" + title + ".txt"
    data, err := ioutil.ReadFile(filename)
    page := &Page{
        Title: title, 
        Body: string(data),
    }
    return page, err
}

func handler(w http.ResponseWriter, r *http.Request) {
    log.Println(r.RemoteAddr, r.Method, r.URL)
    path := r.URL.Path[1:]
    if path == "" {
        path = "index"
    }
    if r.Method == "POST" {
        page := Page{
            Title: path,
            Body: r.FormValue("body"),
        }
        log.Println("PAGE EDITED:%s\n%s", page.Title, page.Body)
        page.saveToFile()
    }
    page, _ := loadPageFromFile(path)
    pageTemplate.Execute(w, page)
}

func main() {
    http.HandleFunc("/", handler)
    log.Println("CS Club Wiki Server has started!")
    log.Fatal(http.ListenAndServe(":8080", nil))
}
```



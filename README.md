# Workshop: Intro to Go Programming Language

CS Club Tutorial Workshop for An Introduction to 
the Go Programming Language. 
Based loosely on:
https://golang.org/doc/articles/wiki/.


# New Project 

For this tutorial, we are going to use https://repl.it/ instead of 
installing Go on our machine, in order to simplify the workshop and
make it easier to dive right in. If you want to install it on your
machine, https://golang.org/doc/install has downloads and 
instructions.

1. If you haven't used repl.it before, you can make an account at
   https://repl.it/signup. I highly reccomend making use of repl.it 
   for stuff other than just this workshop!
  
2. Create a new Go project.
  
3. Add a folder within your project called "pages"


While following this tutorial, it may be helpful to reference golang's
package documenation, since we will be making use of the standard
library. You can find the documentation at https://golang.org/pkg/.


# Wiki Page

Each wiki page will consist of two pieces of information: a Title and
a Body. We can represent those in memory using a data structure. In
Go, there are no object-oriented classes, instead there are only 
data structures that you declare like so:

```go
type Page struct {
    Title string,
    Body string,
}
```

This defines the structure of our Page, where both Title and Body are 
strings. There is a shorthand syntax for when you have two fields
of the same type. This is equivalent to the above:

```go
type Page struct {
	Title, Body string
}
```




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


# Other Stuff

Example project https://repl.it/@fractalbach/wiki

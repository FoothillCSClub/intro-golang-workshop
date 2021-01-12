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



# Serve Pages over Network

HTTP GET

# Editing Wiki Pages

HTTP POST

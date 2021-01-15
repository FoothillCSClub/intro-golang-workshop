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

## The `Page` Data Structure

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

## Saving a File

Next we are going to start writing functions that save and load data
with text files. First, we will need to import several packages from 
the standard library.

```go
import (
    "fmt"
    "io/ioutil"
    "path/filepath"
)
```

And we are going to make use of that folder we named `pages` when we
first created the project. We can save this into a global constant
by using the `const` keyword.

```go
const folder = "pages"
```

Now it is time to write a function to save the Pages data structure 
into a file. Here is what we will write:


```go
func (p *Page) saveToFile() {
    path := filepath.Join(folder, p.Title + ".txt")
    err := ioutil.WriteFile(path, []byte(p.Body), 0644)
    if err != nil { fmt.Println(err) }
}
```

Breaking `saveToFile` down, here is what is happening:

* In our function declaration, we write `func (p *Page) saveToFile()`, 
  which shows that we are operating on a specific instance of the
  `Page` data structure. This is essentially passed as input to the
  function, which we can access with the variable name `p` from 
  within the function. The function returns no values.
 
* `path := filepath.Join(folder, p.Title + ".txt")` will combine the
  paths together, so essentially it returns `pages/title.txt`.
  The filepath package is often used to avoid problems that arise
  with the way filepaths are treated on different operating systems.
  
* `err := ioutil.WriteFile(path, []byte(p.Body), 0644)`. This
  is what actually creates and writes the file into the system. The
  function takes as input a filepath and an array of bytes. In go,
  we can easily convert a string to an array of bytes by using calling
  `[]byte(string)`.  The `0644` is a magic number that is telling
  the system what kind of read/write permissions the file should have.
  Finally, the `ioutil.WriteFile` function returns an error as output,
  which we will save (just in case).
  
* `if err != nil { fmt.Println(err) }`. Just in case there is an
  error when we try to write a file, this will print out the error
  so we can see what happened.

## Loading a File

Now we will write a function to load existing files by reading them
and returning a newly created `Page` data structure. We essentially
are doing the reverse of saving. We retrieve the bytes from the file,
convert it into a string, and create a `Page` that uses that data as 
its body.

```go
func loadPageFromFile(title string) *Page {
    path := filepath.Join(folder, title + ".txt")
    data, err := ioutil.ReadFile(path)
    if err != nil { fmt.Println(err) }
    page := &Page{
        Title: title, 
        Body: string(data),
    }
    return page
}
```

* Notice that `data, err := ioutil.ReadFile(path)` returns two 
  different outputs from the same function. This is a common occurence
  in Go, especially when the second output is an error.
  
* Notice that when we create the new `Page` structure, we are actually
  saving a pointer to it by writing `&Page`.
  
Finally we will write a main function to create a test page, save it,
and then load it again.

```go
func main() {
    p1 := Page{Title: "index", Body: "Welcome to CS Club Wiki!"}
    p1.saveToFile()
    p2 := loadPageFromFile("index")
    fmt.Println(p2.Body)
}
```

When running this, if all goes well, we expect to see this output from
our console:
```
> go build
> ./main
Welcome to CS Club Wiki!
```


# Checkpoint: Load/Save Page

Here is everything we have written so far.

```go
package main

import (
    "fmt"
    "io/ioutil"
    "path/filepath"
)

const folder = "pages"

type Page struct {
    Title, Body string
}

func (p *Page) saveToFile() {
    path := filepath.Join(folder, p.Title + ".txt")
    err := ioutil.WriteFile(path, []byte(p.Body), 0644)
    if err != nil { fmt.Println(err) }
}

func loadPageFromFile(title string) *Page {
    path := filepath.Join(folder, title + ".txt")
    data, err := ioutil.ReadFile(path)
    if err != nil { fmt.Println(err) }
    page := &Page{
        Title: title, 
        Body: string(data),
    }
    return page
}

func main() {
    p1 := Page{Title: "index", Body: "Welcome to CS Club Wiki!"}
    p1.saveToFile()
    p2 := loadPageFromFile("index")
    fmt.Println(p2.Body)
}
```




# Converting Data into a Web Page

## HTML Template

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

## Using the Template

We are going to be another standard library package called `template`.
So add it to your imports, like so...

```go
import (
	// ... other imports ...
    "html/template"
)
```

And now we are going to save a global variable with the prepared 
template.

```go
var pageTemplate = template.Must(template.ParseFiles("template.html"))
```

Later, we will be calling the function `pageTemplate.execute` to 
generate the html using a `Page` data structure as input. The template
system will convert the strings in our template that are called
`{{.Title}}` and `{{.Body}}` and write in the corresponding data
from `Page.Title` and `Page.Body`.


# Wiki with Networking


Here we add some more imports

```go
import (
	// ... other imports ...
    "log"
    "net/http"
)
```

And write what is called a `handler` to read and write from a network
connection.

```go
func handler(w http.ResponseWriter, r *http.Request) {
    log.Println(r.RemoteAddr, r.Method, r.URL)
    path := r.URL.Path[1:]
    if path == "" { path = "index" }
    if r.Method == "POST" {
        page := Page{
            Title: path,
            Body: r.FormValue("body"),
        }
        page.saveToFile()
    }
    page := loadPageFromFile(path)
    pageTemplate.Execute(w, page)
}
```

And finally we will write a main function that will start the server.

```go
func main() {
    http.HandleFunc("/", handler)
    log.Println("CS Club Wiki Server has started!")
    log.Fatal(http.ListenAndServe(":8080", nil))
}
```

# Checkpoint: Final 

```go
package main

import (
    "fmt"
    "io/ioutil"
    "path/filepath"
    "html/template"
    "log"
    "net/http"
)

const folder = "pages"

var pageTemplate = template.Must(template.ParseFiles("template.html"))

type Page struct {
    Title, Body string
}

func (p *Page) saveToFile() {
    path := filepath.Join(folder, p.Title + ".txt")
    err := ioutil.WriteFile(path, []byte(p.Body), 0644)
    if err != nil { fmt.Println(err) }
}

func loadPageFromFile(title string) *Page {
    path := filepath.Join(folder, title + ".txt")
    data, err := ioutil.ReadFile(path)
    if err != nil { fmt.Println(err) }
    page := &Page{
        Title: title, 
        Body: string(data),
    }
    return page
}

func handler(w http.ResponseWriter, r *http.Request) {
    log.Println(r.RemoteAddr, r.Method, r.URL)
    path := r.URL.Path[1:]
    if path == "" { path = "index" }
    if r.Method == "POST" {
        page := Page{
            Title: path,
            Body: r.FormValue("body"),
        }
        page.saveToFile()
    }
    page := loadPageFromFile(path)
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

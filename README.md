# Workshop: Intro to Go Programming Language

CS Club Workshop for An Introduction to the Go Programming Language, presented during the [Holiday Hackathon 2020](https://holiday.foothillcs.club/).


## Background

- General purpose programming language, sometimes described as "C for the 21st century"

- Conceived in 2007 by Robert Griesemer, Rob Pike, and Ken Thompson; officially announced in 2009

- Main Webpage: https://golang.org/

- Development: https://github.com/golang/go

## Language Features

- compiled
- strong type system
- garbage collection
- first-class functions
- system call interface
- package/module system
- generics (are still being added to the language)

Go also prides itself in the features that it _doesn't add_ to the language:

- no inheritance
- no macros
- no operator overloading

The unique feature in Go is a concurrency feature called "goroutines"

- Goroutines are lightweight threads that are cheap to create, and can pass data between each other through "channels"
- Concurrency in Go is influenced by Tony Hoare's concept of [_Communicating Sequential Proccesses_(CSP)](https://en.wikipedia.org/wiki/Communicating_sequential_processes)


## Getting Started

https://golang.org/doc/tutorial/getting-started

## Next Steps

https://github.com/golang/go/wiki

## Go in the Wild

The golang team conducts annual surveys to recieve feedback from people who
are using it. Here are some results from the [2019 Golang Survey](https://blog.golang.org/survey2019-results) about how Go is being used.

![](https://blog.golang.org/survey2019/fig20.svg)

![](https://blog.golang.org/survey2019/fig19.svg)


## References

Donovan, A. and Kernighan, B., 2016. _The Go Programming Language_. Addison-Wesley. https://www.gopl.io/.

Kulesza, T., 2020. _Go Developer Survey 2019 Results_. The Go Blog. https://blog.golang.org/survey2019-results.
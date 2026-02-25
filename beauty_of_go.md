# The beauty of Go

*Gopher — Go’s Iconic Mascot*

I recently started exploring Go for some of my side projects and was really struck by its beauty.

![Gopher](https://miro.medium.com/v2/resize:fit:640/format:webp/1*Rgcubeti6JDVTwMPhcqBCQ.png)

I realized how beautifully it made a balance between ease of use (generally associated with dynamically typed, interpreted languages), and performance and safety (type safety, memory safety) (generally associated with statically typed, compiled languages).

Apart from these, two more features make it really the perfect language for modern systems development. Both these features are explained in more detail in the Strengths section below.

One of them is **first class support for concurrency** in the language (through goroutines and channels, explained below). Concurrency, by its design, enables you to efficiently use your CPU horsepower. Even if your processor just has 1 core, concurrency’s design enables you to use that one core efficiently. That is why you can typically have hundreds of thousands of concurrent goroutines (lightweight threads) running on a single machine. Channels and goroutines are central to distributed systems since they abstract the producer-consumer messaging paradigm.

The other feature I really like about Go is interfaces. **Interfaces enable loosely coupled or decoupled components for your systems**. Meaning that a part of your code can just rely on an interface type and doesn’t really care about who implements the interface or how the interface is actually implemented. Your controller can then supply a dependency which satisfies the interface (implements all the functions in the interface) to that code. This also enables a really clean architecture for unit testing (through dependency injection). Now, your controller can just inject a mock implementation of the interface required by the code to be able to test if it’s doing its job correctly or not.

Keeping all these features in mind, I think Go is really a great language. Especially for use cases like cloud systems development (web servers, CDNs, caches etc), distributed systems, microservices etc. So if you’re an engineer or a startup trying to decide what language you want to explore or try out, do give Go a serious thought.

In this post, I’ll talk about the following aspects of the language:

a) Introduction  
b) Why was Go needed  
c) Target Audience  
d) Go’s strengths  
e) Go’s weaknesses  
f) Towards Go 2  
g) Go’s design philosophy  
h) How to get started  
i) Who is using Go  

---

## Introduction

Go is an open source language, created at Google by Robert Griesemer, Rob Pike, and Ken Thompson. Open source here means that everybody can contribute to the language by opening proposals for new features, fix bugs etc. The language’s code is available on GitHub. Documentation on how you can contribute to the language is provided here.

---

## Why was Go needed

The authors mention that the primary motive for designing a new language was to solve software engineering issues at Google. They also mention that Go was actually developed as an alternative to C++.

Rob Pike mentions the purpose for the Go programming language:

> “Go’s purpose is therefore not to do research into programming language design; it is to improve the working environment for its designers and their coworkers. Go is more about software engineering than programming language research. Or to rephrase, it is about language design in the service of software engineering.”

**Issues that were plaguing the software engineering horizon at Google were** (taken from https://talks.golang.org/2012/splash.article):

a) slow builds — builds would sometime take as long as an hour to complete  
b) uncontrolled dependencies  
c) each programmer using a different subset of the language  
d) poor program understanding (code hard to read, poorly documented, and so on)  
e) duplication of effort  
f) cost of updates  
g) version skew  
h) difficulty of writing automatic tools  
i) cross-language builds  

**For Go to succeed, Go must solve these problems** (taken from https://talks.golang.org/2012/splash.article):

a) Go must work at scale, for large teams of programmers working on them, for programs with large numbers of dependencies.  
b) Go must be familiar, roughly C-like. Google needs to get programmers productive quickly in Go, means that the language cannot be too radical.  
c) Go must be modern. It should have features like concurrency so that programs can make efficient use of multi core machines. It should have built-in networking and web server libraries so that it aids modern development.  

---

## Target Audience

Go is a systems programming language. Go really shines for stuff such as cloud systems (web servers, caches), microservices, distributed systems (due to concurrency support).

---

## Strengths

### a) Statically typed

Go is statically typed. This means that you need to declare types for all your variables and your function arguments (and return variables) at compile time. Although this may sound inconvenient, this is a great advantage since a lot of errors will be found at compile time itself. This factor plays a very big role when your team size increases, since declared types make functions and libraries more readable and more easier to understand.

### b) Compilation Speed

Go code compiles **really fast**, so you don’t need to keep waiting for your code to compile. :) In fact, the `go run` command fires up your Go program so quickly so that you don’t even get a feeling that your code got compiled first. It feels like an interpreted language.

### c) Execution Speed

Go code gets directly compiled to machine code, depending upon the OS (Linux/Windows/Mac) and the CPU instruction set architecture (x86, x86–64, arm etc) of the machine the code is being compiled upon. So, it runs really fast.

### d) Portable

Since the code gets directly compiled to machine code, therefore, the binaries become portable. Portability here means that you can pick up the binary from your machine (let’s say Linux, x86–64) and directly run that on your server (if your server is also running Linux on a x86–64 architecture).

This becomes possible since Go binaries are statically linked, meaning that any shared operating system libraries your program needs are included in the binary at the time of the compilation. They are not dynamically linked at the time of running the program.

This has a huge benefit for deployment of your programs on multiple machines in a data center. If you have 100 machines in your data center, you can simply `scp` your program binary to all of them, as long as the binary is compiled for the same OS and instruction set architecture your machines run on. You don’t need to care about which version of Linux they are running. There is no need for checking/managing dependencies. The binaries simply run and all your services are up :)

### e) Concurrency

Go has first class support for concurrency. Concurrency is one of the major selling points of Go. The language designers have designed the concurrency model around the ‘Communicating Sequential Processes’ paper by Tony Hoare.

**The Go runtime allows you to run hundreds of thousands of concurrent goroutines on a machine**. A Goroutine is a lightweight thread of execution. The Go runtime multiplexes those goroutines over operating system threads. That means that multiple goroutines can run concurrently on a single OS thread. The Go runtime has a scheduler whose job is to schedule these goroutines for execution.

There are two benefits of this approach:

i) A Goroutine when initialized has a stack of 4 KB. This is really tiny as compared to a stack of an OS thread, which is generally 1 MB. This number matters when you need to have hundreds of thousands of different goroutines running concurrently. If you would run more than thousands of OS threads in parallel, the RAM obviously will become a bottleneck.  
ii) Go could have followed the same model as other languages like Java, which support the same concept of threads as OS threads. But in that case, the cost of a context switch between OS threads is much larger than the cost of a context switch between different goroutines.  

Since I’m referring to “concurrency” multiple times in this article, I would advise you to check out Rob Pike’s talk on ‘Concurrency is not parallelism”. In programming, concurrency is the composition of independently executing processes, while parallelism is the simultaneous execution of (possibly related) computations. Unless you have a processor with multiple cores or have multiple processors, you can’t really have parallelism since a CPU core can only execute one thing at a time. On a single core machine, it’s just concurrency that’s doing its job behind the scenes. The OS scheduler schedules different processes (threads actually. ery process has atleast a main thread) for different timeslices on the processor. Therefore, at one moment in time, you can only have one thread(process) running on the processor. Due to the high speed of execution of the instructions, we get the feeling that multiple things are running. But it’s actually just one thing at a time.

Concurrency is about dealing with lots of things at once. Parallelism is about doing lots of things at once.

### f) Interfaces

Interfaces enable loosely coupled systems. An interface type in Go can be defined as a set of functions. That’s it. Any type which implements those functions implicitly implements the interface, i.e. you don’t need to specify that a type implements the interface. This is checked by the compiler automatically at compile time.

This means that a part of your code can just rely on an interface type and doesn’t really care about who implements the interface or how the interface is actually implemented. Your main/controller function can then supply a dependency which satisfies the interface (implements all the functions in the interface) to that code. This also enables a really clean architecture for unit testing (through dependency injection). Now, your test code can just inject a mock implementation of the interface required by the code to be able to test if it’s doing its job correctly or not.

While this is great for decoupling, the other benefit is that you then start thinking about your architecture as different microservices. Even if your application resides on a single server (if you’re just starting out), you architect different functionalities required in your application as different microservices, each implementing an interface it promises. So other services/controllers just call the methods in your interface not actually caring about how they are implemented behind the scenes.

### g) Garbage collection

Unlike C, you don’t need to remember to free up pointers or worry about dangling pointers in Go. The garbage collector automatically does this job.

### h) No exceptions, handle errors yourself

I love the fact that Go doesn’t have the standard exception logic that other languages have. Go forces developers to handle basic errors like ‘couldn’t open file’ etc rather than letting them wrap up all of their code in a try catch block. This also puts pressure on developers to actually think about what needs to be done to handle these failure scenarios.

### i) Amazing tooling

One of the best aspects about Go is its tooling. It has tools like:

i) **Gofmt:** It automatically formats and indents your code so that your code looks like the same as every Go developer on the planet. This has a huge effect on code readability.  
ii) **Go run:** This compiles your code and runs it, both :). So even though Go needs to be compiled, this tool makes you feel like it’s an interpreted language since it just compiles your code so fast that you don’t even feel when the code got compiled.  
iii) **Go get:** This downloads the library from GitHub and copies it to your GoPath so that you can import the library in your project  
iv) **Godoc:** Godoc parses your Go source code — including comments — and produces its documentation in HTML or plain text format. Through godoc’s web interface, you can then see documentation tightly coupled with the code it documents. You can navigate from a function’s documentation to its implementation with one click.  

You can check more tools here.

### j) Great built-in libraries

Go has great built-in libraries to aid modern development. Some of them are:

a) `net/http` — Provides HTTP client and server implementations  
b) `database/sql` — For interaction with SQL databases  
c) `encoding/json` — JSON is treated as a first class member of the standard language :)  
d) `html/templates` — HTML templating library  
e) `io/ioutil` — Implements I/O utility functions  

There is a lot of development going on in the Go horizon. You can find all Go libraries and frameworks for all sorts of tools and use cases here.

---

## Weaknesses

### 1. Lack of generics

Generics let us design algorithms around types-to-be-specified-later. Let’s say you need to write a function to sort a list of integers. Later on, you need to write another function for sorting a list of strings. At that moment, you realize that the code would pretty much look the same but you can’t use the original function since the function can either take a list of type integer or a list of type string as an argument. This would require code duplication.

Therefore, generics let you design algorithms around types which can be specified later. You can design an algorithm for sorting a list of type `T`. Then, you can call the same function with integers/strings/any other type given that there exists an ordering function for that type. Meaning that the compiler can check if one value of that type is bigger than another value of that type or not (since this is needed for sorting)

It is possible to implement some sort of a generic mechanism in Go by using the empty interface (`interface{}`) feature of the language. However, it’s not ideal.

Generics is a highly controversial topic. Some programmers swear by it. While others don’t want them included in the language since generics generally are a trade-off between compilation time and execution time.

This being said, authors of Go have expressed openness towards implementing some sort of generics mechanism in Go. However, it’s not just about generics. Generics can only be implemented into the language if they work well and naturally with all the other features in the language. Let’s wait and see if Go 2 provides some sort of solution for them.

### 2. Lack of dependency management

The Go1 promise means that the Go language and its library cannot change its APIs over the lifetime of Go 1. This means that your source code will continue to compile for both Go 1.5 and Go 1.9. Therefore, most 3rd party Go libraries follow the same promise as well.

Since the primary way to get a 3rd party library from GitHub is through the `go get` tool, therefore, when you do a `go get github.com/vendor/library`, you are hopeful that the latest code in their master branch does not change the library APIs. While this is cool for casual side projects since most libraries don’t break the promise, this isn’t ideal for production deployments.

There should ideally be some way for dependency versioning so that you can simply include the version number of a 3rd party library in your dependency file. Even if their API changes, you don’t need to worry about it since the newer API will come with a newer version. You can later go back to check what changes were made and then take a decision on whether or not to upgrade the version in your dependency file and change your client code according to the changes in the API interface.

Go’s official experiment `dep` should ideally become the solution to this problem soon. Probably in Go 2 :)

---

## Towards Go 2

I really love the way the authors have taken such an open source approach towards the language. If you want a feature to be implemented in Go 2, you need to write a document wherein you need to:

a) describe your use case or problem  
b) illustrate how you cannot solve the use case / problem using Go  
c) describe how big of a problem it really is (some problems just aren’t big enough or concerning enough to be prioritized for solving at a given moment in time).  
d) optionally, propose a solution to how the problem could be solved  

The authors will review it and link it here. All discussions around issues will happen on public mediums like mailing lists and issue trackers.

The two most pressing problems for the language, in my opinion are Generics and Dependency management. Dependency management is more of a release engineering or tooling issue. Hopefully we will see dep (the official experiment) become the official tool to solve the problem. Given that the authors have expressed openness to generics in the language, I’m curious to see how they implement them since generics come at the cost of either compilation time or execution time.

---

## Go’s design philosophy

A couple of things really shined to me from Rob Pike’s *Simplicity is Complicated* talk.

Specifically, the things which I liked were:

a) **Traditionally, every other language wants to just keep adding new features**. This way, all of the languages are just adding bloatedness, too much complexity in their compilers and their specification. If this continues, every language will look like the same in the future because every language will keep adding features it doesn’t have. Consider, JavaScript adding object oriented features. The Go authors deliberately did not include a lot of features in the language. **Only those features were included for which there was consensus from the authors, for the ones which really felt like they did brought value into what can be achieved by the language.**  
b) **Features are like orthogonal vectors in a solution space.** What’s important is the ability to pick and combine different vectors for your use case. **And those vectors should just work with each other naturally. Means that every feature of the language should work predictably with any other.** This way, those set of features cover the whole solution space. Implementing all these features, which work very naturally with each other, brings a lot of complexity into the language implementation. But the language abstracts the complexity and provides you with a simple, easy to understand interface. Therefore, simplicity is just the art of hiding complexity :)  
c) **The importance of readability is often underrated.** Readability is critical, arguably, one of the most important things in designing programming languages, since the importance and also the cost of maintaining software is high. Too many features hurt the readability of a language.  
d) **Readability also means reliability.** If a language is complicated, you must understand more things to read and work on the code. Similarly, to debug it and to be able to fix it. This also means that new developers on your team will need much larger scale up times, to get their understanding on the language upto the point at which they can contribute to your codebase.  

[Getting Started with Go](https://miro.medium.com/v2/resize:fit:720/format:webp/1*5T4RDLyZGZMJBBUKYgtY9g.png)

---

## How to get started

You can download Go and follow the installation instructions from here.

Here is the official guide to get started with Go. Go by example is also a good one.

If you want to read a book, *The Go Programming Language* is an excellent one. It’s written in a similar spirit as the legendary *C Programming Language* book and is authored by Alan A. A. Donovan and Brian W. Kernighan.

You can join the Gophers Slack Channel to engage with community and to participate in discussions around the language.

---

## Who is using Go

A lot of companies have started investing a lot in Go. Here are some of the bigger names:

- Google — Kubernetes, MySQL scaling infrastructure, dl.google.com (Download servers)
- BaseCamp — Go at BaseCamp
- CloudFlare — Blog, ArsTechnica article
- CockroachDB — Why Go was the right choice for CockroachDB
- CoreOS — GitHub, Blog
- DataDog — Go at DataDog
- DigitalOcean — Get your development team started with Go
- Docker — Why did we decide to write Docker in Go
- Dropbox — Open Sourcing our Go Libraries
- Parse — How we moved our API from Ruby to Go and saved our Sanity
- Facebook — GitHub
- Intel — GitHub
- Iron.IO — Go after 2 years in Production/
- MalwareBytes — Handling 1 million requests per minute with golang/
- Medium — How Medium goes Social
- MongoDB — Go Agent
- Mozilla — GitHub
- Netflix — GitHub
- Pinterest — GitHub
- Segment — GitHub
- SendGrid — How to convince your company to go with Golang
- Shopify — Twitter
- SoundCloud — Go at SoundCloud
- SourceGraph — YouTube
- Twitter — Handling Five Billion Sessions a day in Real Time
- Uber — Blog, GitHub

---

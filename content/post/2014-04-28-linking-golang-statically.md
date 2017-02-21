---
title: Linking golang statically
author: vbatts
layout: post
date: 2014-04-28T15:42:05+00:00
url: /2014/04/linking-golang-statically/
aktt_notify_twitter:
  - no
  - no
categories:
  - code

---
If you are not familiar with <a href="http://golang.org/" target="_blank">Golang</a>, do take the <a href="http://tour.golang.org/" target="_blank">go tour</a> or read some of the <a href="http://golang.org/doc/" target="_blank">docs</a> first.

There are a number of reasons that folks are in love with golang. One the most mentioned is the static linking.

As long as the source being compiled is native go, the go compiler will statically link the executable. Though when you need to use <a href="http://golang.org/cmd/cgo/" target="_blank">cgo</a>, then the compiler has to use its external linker. 

## Pure go


`
<pre>
// code-pure.go
package main

import "fmt"

func main() {
        fmt.Println("hello, world!")
}
</pre>
<p>`

Straight forward example. Let&#8217;s compile it.


`
<pre>
$> go build ./code-pure.go
$> ldd ./code-pure
        not a dynamic executable
$> file ./code-pure
./code-pure: ELF 64-bit LSB  executable, x86-64, version 1 (SYSV), statically linked, not stripped
</pre>
<p>`

## cgo

Using a contrived, but that passes through the C barrier:
  

`
<pre>
// code-cgo.go
package main

/*
char* foo(void) { return "hello, world!"; }
*/
import "C"

import "fmt"

func main() {
  fmt.Println(C.GoString(C.foo()))
}
</pre>
<p>`

Seems simple enough. Let&#8217;s compile it


`
<pre>
$> go build ./code-cgo.go
$> file ./code-cgo
./code-cgo: ELF 64-bit LSB  executable, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), not stripped
$> ldd ./code-cgo
        linux-vdso.so.1 (0x00007fff07339000)
        libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f5e62737000)
        libc.so.6 => /lib64/libc.so.6 (0x00007f5e6236e000)
        /lib64/ld-linux-x86-64.so.2 (0x00007f5e62996000)
$> ./code-cgo
hello, world!
</pre>
<p>`

## wait, what?

That code that is using cgo is _not_ statically linked. Why not?

The compile for this does not wholly use golang&#8217;s internal linker, and has to use the external linker. So, this is not surprising, since this is not unlike simple \``gcc -o hello-world.c`\`, which is default to dynamically linked.


`
<pre>
// hello-world.c

int main() {
        puts("hello, world!");
}
</pre>
<p><code></p>
<p><code>
<pre>
$> gcc -o ./hello-world ./hello-world.c                                            
$> ./hello-world                                                                   
hello, world!                                                                      
$> file ./he                                                                       
hello-world    hello-world.c  hex.go                                               
$> file ./hello-world                                                              
./hello-world: ELF 64-bit LSB  executable, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), not stripped 
$> ldd ./hello-world
        linux-vdso.so.1 (0x00007fff5f109000)
        libc.so.6 => /lib64/libc.so.6 (0x00007f0906e53000)
        /lib64/ld-linux-x86-64.so.2 (0x00007f090725e000)
</pre>
<p>`

But for that example, we just have to add the '-static' flag to gcc (and ensure that glibc-static package is available).


`
<pre>
$> gcc -o ./hello-world -static ./hello-world.c
$ file ./hello-world
./hello-world: ELF 64-bit LSB  executable, x86-64, version 1 (GNU/Linux), statically linked, not stripped
$ ldd ./hello-world
        not a dynamic executable
</pre>
<p>`

Let's apply that same logic to our \``go build`\`

## static cgo

Using same `code-cgo.go` source, let's apply that gcc flag, but using the `go build` command.


`
<pre>
$> go build --ldflags '-extldflags "-static"' ./code-cgo.go
$> file ./code-cgo
./code-cgo: ELF 64-bit LSB  executable, x86-64, version 1 (GNU/Linux), statically linked, not stripped
$> ldd ./code-cgo
        not a dynamic executable
$> ./code-cgo
hello, world!
</pre>
<p>`

Cool! Here we've let the go compiler use the external linker, and that linker linked statically from libc.

An explanation of the flags here.

`--ldflags` is passed to the go linker. It takes a string of arguments.
  
On my linux-x86_64 machine, that is the `6l` tool (`5l` for arm and `8l` for ix86). To see the tools available on your host, call `go tool`, and then get help on that tool with `go tool 6l --help`

`'-extldflags ...'` is a flag for the `6l` linker, to pass additional flags to the external linker (in my situation, that is `gcc`).

`"-static"` is the argument to `gcc` (also to `ld`) to link statically.

## gccgo love

Say you have a use case for using/needing the `gccgo` compiler, instead of the go compiler.
  
Again, using our same `code-cgo.go` source, let's compile the code statically using `gccgo`.


`
<pre>
$> go build -compiler gccgo --gccgoflags "-static" ./code-cgo.go
$> file ./code-cgo
./code-cgo: ELF 64-bit LSB  executable, x86-64, version 1 (GNU/Linux), statically linked, not stripped
$> ldd ./code-cgo
        not a dynamic executable
$> ./code-cgo
hello, world!
</pre>
<p>`

Huzzah! Still a static binary, using cgo _and_ giving gccgo a whirl. A quick run-down of these flags.

`-compiler gccgo` instructs the build to use `gccgo` instead of the go compiler (`gc`).

`--gccgoflags ...` are additional arguments passed to the `gccgo` command. See also the `gccgo` man page.

`"-static"` similar to `gcc`, this is the same flag the instructs `gccgo` to link the executable statically.

## more info

If you're curious to what is happening behind the scenes of your compile, the `go build` has an `-x` flag that prints out the commands that it is running. This is often helpful if you are following what is linked in, or to see the commands used during compile such that you can find where and how to insert arguments needed.
[OSCON 2010: Rob Pike, "Public Static Void"](https://www.youtube.com/watch?v=5kj5ApnhPAE)
People in Object-Oriented Programming communities love to harp on the usage of patterns. However, patterns are an additional form of abstraction that abuse the original language. If the language was good enough, there would be no need to fall back on the pattern. 

Taking the example of a subroutine call, these were very difficult to do in early languages, and patterns emerged to do them. These later were replaced by functions in modern languages.

On repetition, Pike argues that we confuse repetition of types with safety, when in reality it results in verbosity.

Some programmers have confused "ease of use" with interpretation and dynamic typing. This confusion came about because of how we got here: grafting an orthodoxy onto a language that couldn't support it cleanly.

Go aims to combine the safety and performance of a statically typed compiled language with the expressiveness and convenience of a dynamically typed interpreted language.
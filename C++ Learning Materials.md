# C++ Learning Materials

Material linked in roughly the order in which a beginner should consume them.  This is a quick version 1 so I can't guarantee how helpful it'll be, like some of the talks may briefly touch on stuff that's too advanced for the section I've put them it.  Also it's mostly talks for now because they're what I was thinking of when I thought to make this; more useful learning resources to come later.


## Stuff to Avoid

* [cplusplus.com](http://www.cplusplus.com/) — historically [it's had some issues](https://stackoverflow.com/questions/6520052/whats-wrong-with-cplusplus-com) (which may be fixed or mitigated at this point); it's what I started off using but moved to cppreference.com (linked below)
* Calling the C++ Standard Library the STL — that's [something historically related but very different](https://en.wikipedia.org/wiki/Standard_Template_Library)
* [Misunderstanding language features then blaming it on the language](https://www.youtube.com/watch?v=B2BFbs0DJzw) — I just put this here because it annoys me, may put it somewhere else later


## Beginner

* [Why C++ Sails When the Vasa Sank](https://www.youtube.com/watch?v=ltCgzYcpFUI) by Scott Meyers — a good answer to "why C++?" — also has "language for library developers" quote
* [cpp.sh](http://cpp.sh/) online C++ compiler, supports C++98/11/14
* [TheChernoProject C++ series](https://www.youtube.com/playlist?list=PLlrATfBNZ98dudnM48yfGUldqGD0S4FFb&disable_polymer=true) — does a good job of explaining what's actually happening in the language — I'd suggest watching these in-order and not minding anything that seems too advanced and coming back later; I'll add other tutorials later for people who don't like videos and/or don't like his accent
* [cppreference.com](cppreference.com) — indispensable reference manual for the C++ Standard Library (and language features to an extent)
* [A Tour of C++](http://www.stroustrup.com/Tour.html) by Bjarne Stroustrup ([Amazon.com](https://www.amazon.com/Tour-C-Depth/dp/0321958314)) if you're familiar with other languages, otherwise leave this for intermediate ~~honestly haven't gotten around to reading this~~
* C things to know (for better or worse):
    * [www.AsciiTable.com](http://www.asciitable.com/)
    * [Characters, Symbols and the Unicode Miracle - Computerphile](https://www.youtube.com/watch?v=MijmeoH9LT4) — C/C++ *can* work with non-ASCII but you need to know how encodings work because C/C++ treat them as byte arrays


## Intermediate

* [CppCon 2014: Herb Sutter "Back to the Basics! Essentials of Modern C++ Style"](https://www.youtube.com/watch?v=xnqTKD8uD64)
* [C++ Core Guidelines](https://github.com/isocpp/CppCoreGuidelines)
* [CppCon 2014: Bjarne Stroustrup "Make Simple Tasks Simple!"](CppCon 2014: Bjarne Stroustrup "Make Simple Tasks Simple!")
* [Scott Meyers – The Most Important Design Guideline](https://www.youtube.com/watch?v=5tg1ONG18H8) — Not C++ specifically but important for understanding common C++ design decisions
* [CppCon 2014: Scott Meyers "Type Deduction and Why You Care"](https://www.youtube.com/watch?v=wQxj20X-tIU)
* [Things that Matter - Scott Meyers | DConf2017](https://www.youtube.com/watch?v=RT46MpK39rQ)


## Advanced

* [godbolt.org](https://godbolt.org/) compiler explorer
* [CppCon 2016: Tim Haines “Improving Performance Through Compiler Switches — Examples from Scientific Computing"](https://www.youtube.com/watch?v=w5Z4JlMJ1VQ)
* [Zero-overhead deterministic exceptions: Throwing values](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2018/p0709r0.pdf) — C++ language proposal by Herb Sutter, 5/2/18; gives a good overview of the (current) problems with C++ exceptions
* [CppCon 2015: Andrei Alexandrescu “std::allocator...”](https://www.youtube.com/watch?v=LIb3L4vKZ7U)


## People

* Bjarne Stroustrup
* Andrei Alexandrescu
* Scott Meyers
* Howard Hinnant
* Herb Sutter
* Alexander Stepanov? — look up talks, papers

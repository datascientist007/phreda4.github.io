# Memory

The use of memory in: r4 is very different from the rest of the languages, the management is basic and easy, the real challenge is to build applications that are designed to use the complicated memory system in this easier.

The first criticism of memory management is that if there is a garbage collection in memory management, that is, if there is a collector, then there is a garbage generator and that is the program that is running!

The idea here is to use the memory directly, I have a memory address where the free zone begins and I will use this memory leaving a mark of use, when I want to return back to this mark.

```
MEM		| -- adr 	; Free memory start
```

This usage record is in a stack, so here is the first clarification, we will use memory but hierarchically, so if we are not going to use a part, this part will always be the last, and therefore, easily running the address of free memory we release this part.
Here the criticism can be: well, of course, if I do the same in another language it will be the same and if, in fact, it is better to think of forth as a way of thinking and not a language in particular, the language will help to use this way of thinking.
Another clarification in this regard can be exemplified with the ordering algorithms, to the question, which is the fastest ordering algorithm the answer is: none, not ordering is faster than ordering, it may seem silly but if one is careful with the choice of the algorithm that is developed and avoids the need to order with some change this can be extremely beneficial. It never hurts to think!

All the aplication you see in the developer package are made with this schema, not use a garbage collector at all.

Then we have a library for use the memory (lib/mem.txt), in this lib define the words:

```
...
#:here 0

#memmap )( 1024
#memmap> 'memmap

::mark | --
	here 0? ( mem dup 'here ! nip ) memmap> !+ 'memmap> ! ;
::empty | --
	memmap> 'memmap =? ( mem nip )( 4 - dup 'memmap> ! @ ) 'here ! ;

::savemem | "" --
	memmap> 4 - @ here over - rot save ;
...
```

`here` is the start of free mem, see in mark word, if `here` is 0 (when start) the set to `mem` and stack in `memmap`.
then if you use this schema, start the program with `mark`, for initialice this var.

A difference with Forth in: r4 the compilation is not controlled, that is, instead of Edit-Compile-Execute time, we only have Edit-Execute time, the idea is to simplify. here is a variable inherited `here` but it has another meaning, in ColorForth it indicates the lungar where this code is compiler, here is the place of free memory for any use.

See lib/mprint.txt library.

```
::, here !+ 'here ! ;
::,c here c!+ 'here ! ;
::,s here swap ( c@+ 1? )( rot c!+ swap ) 2drop 'here ! ;
::,w here swap ( c@+ $ff and 32 >? )( rot c!+ swap ) 2drop 'here ! ;

...

```

You can see how write in adress of here. The idea is use the mem like scratch pad

The Word savemem make a really usefull thing, save from mark to actual here, you can save a string in file simply:

```
:savethis
	mark
	"hola" ,s
	123 ,d
	dup "stack now %d" ,print
	,cr
	"file.txt" savemem
	empty ;
```

Many of the data structures used are intended to be used in a static way, that is, they do not have to be modified when they are being used, for example, if I am going to save a bitmap, it will not be in a compressed format in memory because I would have to expand it to graph it, but if I do not compress it on disk, the load will not convert either.


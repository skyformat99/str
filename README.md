Simple c++ string type with an optional local buffer

```
Str
Simple c++ string type with an optional local buffer, by omar cornut
https://github.com/ocornut/str

LICENSE
This software is in the public domain. Where that dedication is not
recognized, you are granted a perpetual, irrevocable license to copy,
distribute, and modify this file as you see fit.

- This isn't a fully featured string class. 
- It is a simple, bearable replacement to std::string that isn't heap abusive nor bloated (can actually be debugged by humans).
- String are mutable. We don't maintain size so length() is not-constant time. 
- Maximum string size currently limited to 2 MB (we allocate 21 bits to hold capacity).
- Local buffer size is currently limited to 1023 bytes (we allocate 10 bits to hold local buffer size).
- In "non-owned" mode for literals/reference we don't do any tracking/counting of references.
- Overhead is 8-bytes in 32-bits, 16-bits in 64-bits (12 + alignment).
- This code hasn't been tested very much. it is probably incomplete or broken. Made it for my own use.

The idea is that you can provide an arbitrary sized local buffer if you expect string to fit 
most of the time, and then you avoid using costly heap.

No local buffer, always use heap, sizeof()==8~16 (depends if your pointers are 32-bits or 64-bits)

   Str s = "hey";

With a local buffer of 16 bytes, sizeof() == 8~16 + 16 bytes.

   Str16 s = "filename.h"; // copy into local buffer
   Str16 s = "long_filename_not_very_long_but_longer_than_expected.h";   // use heap

With a local buffer of 256 bytes, sizeof() == 8~16 + 256 bytes.

   Str256 s = "long_filename_not_very_long_but_longer_than_expected.h";  // copy into local buffer

Common sizes are defined at the bottom of Str.h, you may define your own.
All StrXXX types derives from Str. So you can pass e.g. Str256* to a function taking Str* and it will be functional.

Functions:

   Str256 s;
   s.set("hello sailor");                   // set (copy)
   s.setf("%s/%s.tmp", folder, filename);   // set (w/format)
   s.append("hello");                       // append. cost a length() calculation!
   s.appendf("hello %d", 42);               // append (w/format). cost a length() calculation!
   s.set_ref("Hey!");                       // set (literal/reference, just copy pointer, no tracking)

Constructor helper for format string: add a trailing 'f' to the type. Underlying type is the same.

   Str256f filename("%s/%s.tmp", folder, filename);             // construct (w/format)
   fopen(Str256f("%s/%s.tmp, folder, filename).c_str(), "rb");  // construct (w/format), use as function param, destruct

Constructor helper for reference/literal:

	StrRef ref("literal");           // copy pointer, no allocation, no string copy
	StrRef ref2(GetDebugName());	 // copy pointer. no tracking of anything whatsoever, know what you are doing!

(Using a template e.g. Str<N> we could remove the LocalBufSize storage but it would make passing 
typed Str<> to functions tricky. Instead we don't use template so you can pass them around as
the base type Str*. Also, templates are ugly.)
```

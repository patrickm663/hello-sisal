# Hello, SISAL
This is my first foray into SISAL (Streams and Iteration in a Single Assignment Language), an old forgotten scientific computing language out of the Lawrence Livermore National Laboratory circa 1985. It feels like a precursor to something like Julia.

For more info and further reading, refer to https://kestrel.nmt.edu/~raymond/software/sisal/sisal.xhtml

## Installing SISAL
The below assumes a UNIX environment. Documentation is scarce, YMMV

1. Download the tarball for SISAL from Sourceforge: https://sourceforge.net/projects/sisal/
2. Extract the tarball and enter the directory:
```sh
tar xvfz sisal-14.1.0.tgz
cd sisal-14.1.0
```
3. Run `./configure`. If neccessary, include the flags below to get your C compiler into the GNU89 standard:
```sh
./configure CFLAGS="-g -O2 -fcommon -std=gnu89 -Wno-int-conversion -Wno-implicit-function-declaration -Wno-implicit-int" LIBS="-lm"
```
4. `make` it happen (may need `sudo` at the end to copy files where they need to be):
```sh
make
sudo make install
```
5. You can safely exit the directory and set up camp elsewhere.

## Compiling and Running SISAL
Assuming we have a simple SISAL programme that takes in an integer value N and prints to screen the squares from 1 to N, it first needs to be compiled and then run:
```sisal
% Generate an array of squares
% Tells the compiler this is the only exposed function
define Main

% Define a custom type
type IntArray = array [integer]; % use real for floats

function square(x: integer returns integer)
  x * x
end function

function Main(N: integer returns IntArray)
  for i in 1, N
    returns array of square(i)
  end for
end function
```
Save this as `squares.sis` and run:
```
sisalc -o squares squares.sis
```
You will see a long output from the C compiler. This is harmless and is just modern GCC complaining about 32-bit pointers because we are not in 1989 any more:
```
 LL Parse, using binary files
* Reading file: squares.sis...

version 1.8       (Mar 28, 1989)

 accepted
   16 lines in program
    0 errors ( calls to corrector)
    0 tokens inserted;    0 tokens deleted.
    0 semantic errors
In file included from squares.c:11:
/usr/local/include/sisal/sisal.h: In function ‘ArrayDump’:
/usr/local/include/sisal/sisal.h:193:31: warning: format ‘%x’ expects argument of type ‘unsigned int’, but argument 3 has type ‘struct Array *’ [-Wformat=]
  193 |   fprintf( stderr, "Array: 0x%x (%d), sizeof: %d\n",Array,Array,sizeof(*Array));
      |                              ~^                     ~~~~~
      |                               |                     |
      |                               unsigned int          struct Array *
/usr/local/include/sisal/sisal.h:193:35: warning: format ‘%d’ expects argument of type ‘int’, but argument 4 has type ‘struct Array *’ [-Wformat=]
  193 |   fprintf( stderr, "Array: 0x%x (%d), sizeof: %d\n",Array,Array,sizeof(*Array));
      |                                  ~^                       ~~~~~
      |                                   |                       |
      |                                   int                     struct Array *
/usr/local/include/sisal/sisal.h:193:48: warning: format ‘%d’ expects argument of type ‘int’, but argument 5 has type ‘long unsigned int’ [-Wformat=]
  193 |   fprintf( stderr, "Array: 0x%x (%d), sizeof: %d\n",Array,Array,sizeof(*Array));
      |                                               ~^                ~~~~~~~~~~~~~~
      |                                                |                |
      |                                                int              long unsigned int
      |                                               %ld
/usr/local/include/sisal/sisal.h:196:32: warning: format ‘%x’ expects argument of type ‘unsigned int’, but argument 3 has type ‘struct ArrayPhys *’ [-Wformat=]
  196 |   fprintf( stderr, "  Phys: 0x%x (%d)\n",Array->Phys,Array->Phys);
      |                               ~^         ~~~~~~~~~~~
      |                                |              |
      |                                unsigned int   struct ArrayPhys *
/usr/local/include/sisal/sisal.h:196:36: warning: format ‘%d’ expects argument of type ‘int’, but argument 4 has type ‘struct ArrayPhys *’ [-Wformat=]
  196 |   fprintf( stderr, "  Phys: 0x%x (%d)\n",Array->Phys,Array->Phys);
      |                                   ~^                 ~~~~~~~~~~~
      |                                    |                      |
      |                                    int                    struct ArrayPhys *
/usr/local/include/sisal/sisal.h:197:31: warning: format ‘%d’ expects argument of type ‘int’, but argument 3 has type ‘LOCK_TYPE’ {aka ‘pthread_mutex_t’} [-Wformat=]
  197 |   fprintf( stderr, "  Mutex: %d\n",Array->Mutex);
      |                              ~^    ~~~~~~~~~~~~
      |                               |         |
      |                               int       LOCK_TYPE {aka pthread_mutex_t}
LL Parse, using binary files
* Reading file: squares.sis...

version 1.8       (Mar 28, 1989)
 **  9*   {returns}  x * x
 error

 accepted
   16 lines in program
    1 errors ( calls to corrector)
    0 tokens inserted;    1 tokens deleted.
    0 semantic errors
In file included from squares.c:11:
/usr/local/include/sisal/sisal.h: In function ‘ArrayDump’:
/usr/local/include/sisal/sisal.h:193:31: warning: format ‘%x’ expects argument of type ‘unsigned int’, but argument 3 has type ‘struct Array *’ [-Wformat=]
  193 |   fprintf( stderr, "Array: 0x%x (%d), sizeof: %d\n",Array,Array,sizeof(*Array));
      |                              ~^                     ~~~~~
      |                               |                     |
      |                               unsigned int          struct Array *
/usr/local/include/sisal/sisal.h:193:35: warning: format ‘%d’ expects argument of type ‘int’, but argument 4 has type ‘struct Array *’ [-Wformat=]
  193 |   fprintf( stderr, "Array: 0x%x (%d), sizeof: %d\n",Array,Array,sizeof(*Array));
      |                                  ~^                       ~~~~~
      |                                   |                       |
      |                                   int                     struct Array *
/usr/local/include/sisal/sisal.h:193:48: warning: format ‘%d’ expects argument of type ‘int’, but argument 5 has type ‘long unsigned int’ [-Wformat=]
  193 |   fprintf( stderr, "Array: 0x%x (%d), sizeof: %d\n",Array,Array,sizeof(*Array));
      |                                               ~^                ~~~~~~~~~~~~~~
      |                                                |                |
      |                                                int              long unsigned int
      |                                               %ld
/usr/local/include/sisal/sisal.h:196:32: warning: format ‘%x’ expects argument of type ‘unsigned int’, but argument 3 has type ‘struct ArrayPhys *’ [-Wformat=]
  196 |   fprintf( stderr, "  Phys: 0x%x (%d)\n",Array->Phys,Array->Phys);
      |                               ~^         ~~~~~~~~~~~
```
While it is tempting to run `./squares 10`, it will not accept standard command line arguments like a modern C or Python script. Because SISAL was built for continuous dataflow processing on mainframes, it explicitly listens for `stdin`. 
```
$ ./squares 10
x86_64-unknown-linux-gnu SISAL 1.2 (PThreads) ?.?

```
You will see it hangs until you provide a value followed by `Enter`. A much cleaner way to automate this is by using `echo`
```
$ echo 10 | ./squares
x86_64-unknown-linux-gnu SISAL 1.2 (PThreads) ?.?
[ 1,10: 1 4 9 16 25 36 49 64 81 100 ]
```
In terms of run time and binary size, I reckon it's near-C speed and small! For instance, the compiled binary for the above code is only 248Kb! As far as scientific computing goes, it performs parallel programming by design and was running on supercomputers back in the day.

## What's Next?
This was an afternoon's work exploring something new so it's difficult to say. I like the syntax so far and the compilation speed, binary size, and run time. It doesn't have most of your modern things like networking capabilities and struggles to parse files (to my knowledge) without relying on C or Fortran, but it's so far been decent to build mathy things with.

Of all the 'dead' languages I've seen (mainly Wikipedia code snippets), this one looks pretty cool. The goal is a bigger project and perhaps testing it as a drop-in bit of math inside Julia (via `ccall`).

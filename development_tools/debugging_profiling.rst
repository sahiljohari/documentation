.. _debugging_profiling:

:tocdepth: 3

Debugging Programs
==================

The goal of debugging and performance optimization is to create code
that runs as correctly and quickly as possible. Debugging is the process
by which programming errors are found and corrected. Performance
optimization is the analysis and improvement of the algorithms and
methods implemented in the code.

Getting Started
---------------

In this tutorial, we will use examples in either C, C++ or Fortran90.
Choose your preferred language of the three by copying the relevant
files on ManeFrame II with:

.. raw:: html

   <div class="sourceCode">

.. code:: bash

    $ cp -R /hpc/examples/workshops/hpc/debugging_profiling_tutorial .

.. raw:: html

   </div>

Debugging and Debuggers
-----------------------

Enabling Debugging Information
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In most compilers (including GNU and PGI), you can enable debugging
information through adding the ``-g`` compiler flag. Add this flag to
the compilation commands in the ``Makefile`` for the target
``driver2.exe``, and then compile the executable,

.. raw:: html

   <div class="sourceCode">

.. code:: bash

    $ make driver2.exe

.. raw:: html

   </div>

Run the new executable. It should die with an error message about a
segmentation violation (segmentation fault) or bus error, depending on
the compiler/OS, e.g.

.. raw:: html

   <div class="sourceCode">

.. code:: bash

    $ ./driver2.exe
    Segmentation fault

.. raw:: html

   </div>

There are many ways to track down this kind of error (e.g. adding print
statements everywhere, staring intently hoping for an epiphany, randomly
changing things to see what happens). In this tutorial we will use the
most efficient debugging approach, that of using a tool to track down
the bug for us.

The tool we will use is the GNU debugger, which can be accessed through
running the faulty executable program from within the debugging program
itself. Load the executable into ``gdb`` with the command

.. raw:: html

   <div class="sourceCode">

.. code:: bash

    $ gdb driver2.exe

.. raw:: html

   </div>

At the ``gdb`` prompt, type ``run`` to start the executable. It will
automatically stop at the line where the segmentation fault occurs.

In another terminal window, you can type ``man gdb`` to learn more about
how to use the debugger (or you can `click here to view the gdb man page
on the web <http://linux.die.net/man/1/gdb>`__.

-  Perhaps the most valuable gdb command is ``print`` that may be used
   to see the internal value of a specified variable, e.g.

   .. raw:: html

      <div class="sourceCode">

   .. code:: bash

       (gdb) print i

   .. raw:: html

      </div>

   will print out the current value of the iteration variable ``i``).

-  The ``help`` command inside of ``gdb`` may be used to find out more
   information on how to use the program itself.
-  The ``quit`` command inside of ``gdb`` will exit the debugger and
   return you to the command line. Alternatively, you may just type
   ``^d`` ([control]-[d]) to exit.

Fixing the Bug
~~~~~~~~~~~~~~

C users:
    Open both the files ``driver2.c`` and ``tridiag_matvec.c``, and see
    if you can find/fix the problem by using ``gdb`` and ``print``
    statements as appropriate.

C++ users:
    Open both the files ``driver2.cpp`` and ``tridiag_matvec.cpp``, and
    see if you can find/fix the problem by using ``gdb`` and ``print``
    statements as appropriate.

F90 users:
    Open both the files ``driver2.f90`` and ``tridiag_matvec.f90``, and
    see if you can find/fix the problem by using ``gdb`` and ``print``
    statements as appropriate.

A word of warning, the location of the segmentation fault or bus error
is not always where the problem is located. Segmentation faults
generally occur due to an attempt within the program to read to or write
from an illegal memory location, i.e. a memory location that is not a
part of a currently-available variable. Examples of bugs that can cause
a seg-fault are iterating outside of the bounds of an array, or a
mismatch between the arguments that a program uses to call a function
and the arguments that the function expects to receive.

Tips for tracking/fixing segmentation faults using a debugger:

1. determine exactly the line of code causing the fault,
2. if the fault is inside a loop, determine exactly which iteration of
   the loop is causing the fault,
3. use print statements in the debugger to see which variable is
   uninitialized, e.g. to see if the array ``x`` has entry ``i`` you
   could use

   .. raw:: html

      <div class="sourceCode">

   .. code:: bash

       (gdb) print x[i]

   .. raw:: html

      </div>

Once you identify the precise location of the segmentation fault, go
back to see where the data is allocated. Was it allocated with a
different size, shape or type? Was it not allocated at all?

If the data is allocated in a different manner than it is being used,
determine which location needs fixing and try your best.

Upon finding and fixing the bug causing the segmentation fault, the
correctly-executing program should write the following line:

.. code:: text

    2-norm of product = 1.414213562373E+00

(or something within roundoff error of this result), and it should write
the file ``r.txt`` that contains the result of the matrix-vector
product. This output vector should contain all 0's except for the first
and last entries, which should be 1.

Advanced Debuggers
~~~~~~~~~~~~~~~~~~

There are many freely-available Linux debugging utilities in addition to
`gdb <https://www.gnu.org/software/gdb/>`__. Most of these are graphical
(i.e. point-and-click), and in fact use ``gdb`` under the hood. Some of
the more popular of these debuggers include:
`ddd <https://www.gnu.org/software/ddd/>`__,
`nemiver <http://projects.gnome.org/nemiver/>`__,
`eclipse <http://www.eclipse.org/eclipse/debug/>`__,
`zerobugs <https://zerobugs.codeplex.com/>`__,
`edb <http://www.woodmann.com/collaborative/tools/index.php/EDB_Linux_Debugger>`__.
However, of this set the ManeFrame cluster currently only has ``gdb``
installed (ask your system administrators for others you want/need).

Additionally, there are some highly advanced non-free Linux debugging
utilities available (all typically graphical), including
`TotalView <http://www.roguewave.com/products/totalview.aspx>`__,
`DDT <http://www.allinea.com/products/ddt/>`__,
`idb <http://software.intel.com/en-us/articles/idb-linux>`__ (only works
with the Intel compilers), and PGI's
`pgdbg <http://www.pgroup.com/products/pgdbg.htm>`__ (graphical) and
pgdebug (text version). Of these, the ManeFrame cluster has both
``pgdbg`` and ``pgdebug``.

The usage of most of the above debuggers is similar to ``gdb``, except
that in graphical debuggers it can be easier to view the
data/instruction stack. The primary benefit of the non-free debuggers is
their support for debugging parallel jobs that use OpenMP, MPI, or
hybrid MPI/OpenMP computing approaches. In fact, some of these
professional tools can even be used to debug code running on GPU
accelerators.

If you're interested in learning more about these, I recommend that you
re-download the tarball for this tutorial, load the ``pgi`` module,
update the Makefile to use the ``-g`` option along with the relevant PGI
compiler (``pgcc``, ``pgc++`` or ``pgfortran``), and launch the job in
the ``pgdbg`` debugger like you did with ``gdb``:

.. raw:: html

   <div class="sourceCode">

.. code:: bash

    $ pgdbg ./driver2.exe

.. raw:: html

   </div>

Press the "play" button to start the executable running, and use the
mouse to interact with the debugger as needed.

SMU pays for a five-seat PGI license, meaning that only five distinct
compilation/debugging processes with the PGI tools may be run
simultaneously. Typically, five is much more than sufficient for a
campus of our size, since users spend most of their time writing code,
preparing input parameters and scripts for running simulations, or
post-processing simulation data; the time spent actually compiling and
using a debugger is minimal. However, if everyone in the workshop tries
this simultaneously, we would obviously exceed the five "seats," which
is why this is left as a personal exercise.

Profiling and Optimizing Programs
=================================

Profiling and performance analysis
----------------------------------

There are two primary mechanisms for profiling code: determining which
routines take the most time, and determining which specific lines of
code would be best to optimize. Thankfully, the `GNU compiler
collection <http://gcc.gnu.org/>`__ includes utilities for both of these
tasks, as will be illustrated below. Utilities with similar
functionality are included with some other compilers, and I recommend
that you look up the corresponding information for your compiler of
choice.

In fact, OS X provides a free suite of programs,
`Xcode <https://developer.apple.com/xcode/>`__, that has incredibly
useful profiling and performance monitoring tools. For users with OS X
Lion or newer, this tool is called
`Instruments <https://developer.apple.com/library/mac/documentation/developertools/conceptual/instrumentsuserguide/Introduction/Introduction.html>`__;
for users with older versions of OS X it is called
`Shark <https://developer.apple.com/legacy/library/documentation/DeveloperTools/Conceptual/SharkUserGuide/SharkUserGuide.pdf>`__.

Generating a profile
~~~~~~~~~~~~~~~~~~~~

In the GNU compilers (and many others), you can enable profiling
information through adding in the ``-p`` compiler flag. Add this
compiler flag to the commands in the ``Makefile`` for the target
``driver1.exe`` [Hint: either put it with the flags in the ``OPT``
variable, or in the compile line before the ``-o`` flag].

Profiling information is generated by running the executable once to
completion. Run the driver as usual:

.. raw:: html

   <div class="sourceCode">

.. code:: bash

    $ ./driver1.exe

.. raw:: html

   </div>

Write down the total runtime required for the program (you will use this
information later on).

When the program has finished, you should see a new file in the
directory called ``gmon.out``. This contains the relevant profiling
data, and was written during the execution of the code.

Examine the profiling information by using the program ``gprof``. You
use this by calling ``gprof``, followed by the executable name. It will
automatically look in the ``gmon.out`` file in that directory for the
profiling data that relates to the executable. Run the command

.. raw:: html

   <div class="sourceCode">

.. code:: bash

    $ gprof driver1.exe

.. raw:: html

   </div>

When you run ``gprof``, it outputs all of the profiling information to
the screen. To enable easier examination of these results, you should
instead send this data to a file. You can redirect this information to
the file ``profiling_data.txt`` with the command

.. raw:: html

   <div class="sourceCode">

.. code:: bash

    $ gprof driver1.exe > profiling_data.txt

.. raw:: html

   </div>

You will then have the readable file ``profiling_data.txt`` with the
relevant profiling information.

Identifying Bottlenecks
~~~~~~~~~~~~~~~~~~~~~~~

Read through the first table of profiling information in this file. The
first column of this table shows the percentage of time spent in each
function called by the driver. Identify which one takes the vast
majority of the time. This bottleneck should be the first routine that
you investigate for optimization.

Look through the routine identified from the previous step -- the
function may be contained in a file with a different name, so you can
use ``grep`` to find which file contains the routine:

.. raw:: html

   <div class="sourceCode">

.. code:: bash

    $ grep -i <routine_name> *

.. raw:: html

   </div>

where ``<routine_name>`` is the function that you identified from the
previous step.

Once you have determined the file that contains the culprit function,
you can use the second utility routine ``gcov`` to determine which lines
in the file are executed the most. To use ``gcov``, you must modify the
compile line once more, to use the compilation flags
``-fprofile-arcs -ftest-coverage``.

Add these compiler flags to the commands in the ``Makefile`` for the
target ``driver1.exe``, recompile, and re-run the executable,

.. raw:: html

   <div class="sourceCode">

.. code:: bash

    $ ./driver1.exe

.. raw:: html

   </div>

You should now see additional files in the directory, including
``driver1.gcda``, ``driver1.gcno``, ``vectors.gcda`` and
``vectors.gcno``. If you do not see these files, revisit the above
instructions to ensure that you haven't missed any steps.

You should now run ``gcov`` on the input file that held the function you
identified from the steps above. For example, if the source code file
was ``file.cpp``, you would run

.. raw:: html

   <div class="sourceCode">

.. code:: bash

    $ gcov file.cpp

.. raw:: html

   </div>

This will output some information to the screen, including the name of a
``.gcov`` file that it creates with information on the program. Open
this new file using ``gedit``, and you will see lines like the
following:

.. code:: text

    -:   51:  // fill in vectors x and y
    > 101: 52: for (i=0; i<l; i++)

    > 10100: 53: for (j=0; j<m; j++)

    > 1010000: 54: for (k=0; k<n; k++) 1000000: 55: x[i][j][k] =
    > random() / (pow(2.0,31.0) - 1.0);

The first column of numbers on the left signify the number of times each
line of code was executed within the program. The second column of
numbers correspond to the line number within the source code file. The
remainder of each line shows the source code itself. From the above
snippet, we see that lines 54 and 55 were executed 1.01 and 1 million
times, respectively, indicating that these would be prime locations for
code optimization.

Find the corresponding lines of code in the function that you identified
from the preceding step. It is here where you should focus your
optimization efforts.

Optimizing Code
~~~~~~~~~~~~~~~

Save a copy of the source code file you plan to modify using the ``cp``
command, e.g.

.. raw:: html

   <div class="sourceCode">

.. code:: bash

    $ cp file.cpp file_old.cpp

.. raw:: html

   </div>

where ``file`` is the file that you have identified as containing the
bottleneck routine (use the appropriate extension for your coding
language). We will use this original file again later in the tutorial.

Now that you know which lines are executed, and how often, you should
remove the ``gcov`` compiler options, but keep the ``-p`` in your
``Makefile``.

Determine what, if anything, can be optimized in this routine. The topic
of code optimization is bigger than we can cover in a single workshop
tutorial, but here are some standard techniques.

Code optimization techniques

1. Is there a simpler way that the arithmetic could be accomplished?
   Sometimes the most natural way of writing down a problem does not
   result in the least amount of effort. For example, we may implement a
   line of code to evaluate the polynomial
   *p*\ (*x*)=2\ *x*\ :sup:`4` − 3*x*\ :sup:`3` + 5*x*\ :sup:`2` − 8*x* + 7
   using either

   .. raw:: html

      <div class="sourceCode">

   .. code:: c

       p = 2.0*x*x*x*x - 3.0*x*x*x + 5.0*x*x - 8*x + 7.0;

   .. raw:: html

      </div>

   or

   .. raw:: html

      <div class="sourceCode">

   .. code:: c

       p = (((2.0*x - 3.0)*x + 5.0)*x - 8.0)*x + 7.0;

   .. raw:: html

      </div>

   The first line requires 10 multiplication and 4 addition/subtraction
   operations, while the second requires only 4 multiplications and 4
   additions/subtractions.

2. Is the code accessing memory in an optimal manner? Computers store
   and access memory from RAM one "page" at a time, meaning that if you
   retrieve a single number, the numbers nearby that value are also
   stored in fast-access cache memory. So, if each iteration of a loop
   uses values that are stored in disparate portions of RAM, each value
   could require retrieval of a separate page. Alternatively, if each
   loop iteration uses values from memory that are stored nearby one
   another, many numbers in a row can be retrieved using a single RAM
   access. Since RAM access speeds are significantly slower than cache
   access speeds, something as small as a difference in loop ordering
   can make a huge difference in speed.
3. Is the code doing redundant computations? While modern computers can
   perform many calculations in the time it takes to access one page of
   RAM, some calculations are costly enough to warrant computing it only
   once and storing the result for later reuse. This is especially
   pertinent for things that are performed a large number of times. For
   example, consider the following two algorithms:

   .. raw:: html

      <div class="sourceCode">

   .. code:: c

       for (i=1; i<10000; i++) {
          d[i] = u[i-1]/h/h - 2.0*u[i]/h/h + u[i+1]/h/h;
       }

   .. raw:: html

      </div>

   and

   .. raw:: html

      <div class="sourceCode">

   .. code:: c

       double hinv2 = 1.0/h/h;
       for (i=1; i<10000; i++) {
          d[i] = (u[i-1] - 2.0*u[i] + u[i+1])*hinv2;
       }   

   .. raw:: html

      </div>

   Since floating-point division is significantly more costly than
   multiplication (roughly 10×), and the division by *h*\ :sup:`2` is
   done redundantly both within and between loop iterations, the second
   of these algorithms is typically much faster than the first.

4. Is the code doing unnecessary data copies? In many programming
   languages, a function can be written to use either *call-by-value* or
   *call-by-reference*.

   In call-by-value, all arguments to a function are copied from the
   calling routine into a new set of variables that are local to the
   called function. This allows the called function to modify the input
   variables without concern about corrupting data in the calling
   routine.

   In call-by-reference, the called function only receives memory
   references to the actual data held by the calling routine. This
   allows the called function to directly modify the data held by the
   calling routine.

   While call-by-reference is obviously more "dangerous," it avoids
   unnecessary (and costly) memory allocation/copying/deallocation in
   the executing code. As such, highly efficient code typically uses
   call-by-reference, with the programmer responsible for ensuring that
   data requiring protection in the calling program is manually copied
   before function calls, or that the functions themselves are
   constructed to avoid modifying the underlying data.

   In C and C++, call-by-value is the default, whereas Fortran uses
   call-by-reference. However in C, pointers may be passed through
   function calls to emulate call-by-reference. In C++, either pointers
   can be sent through function calls, or arguments may be specified as
   being passed by reference (using the ``&`` symbol).

Find what you can fix, so long as you do not change the mathematical
result. Delete and re-compile the executable,

.. raw:: html

   <div class="sourceCode">

.. code:: bash

    $ rm driver1.exe; make driver1.exe

.. raw:: html

   </div>

re-run the executable

.. raw:: html

   <div class="sourceCode">

.. code:: bash

    $ ./driver1.exe

.. raw:: html

   </div>

Re-examine the results using ``gprof``, and repeat the optimization
process until you are certain that the code has been sufficiently
optimized. You should be able to achieve a significant performance
improvement (at least 40% faster than the original).

Write down the total runtime required for your hand-optimized program.
Copy your updated code to the file ``file_new.cpp`` (again, use the
appropriate extension for your coding language).

Compiler Optimizations
~~~~~~~~~~~~~~~~~~~~~~

The compiler may also attempt to optimize the code itself. Try
rebuilding the original (non-optimized) code with the compiler flag
``-O2`` (capital 'o' for "Optimize", followed by a '2' to denote the
optimization level):

1. Replace the current flag ``-O0`` in your ``Makefile`` with the flag
   ``-O2``.
2. Copy the original file back, e.g.

   .. raw:: html

      <div class="sourceCode">

   .. code:: bash

       $ cp file_old.cpp file.cpp

   .. raw:: html

      </div>

3. Delete the old executable,

   .. raw:: html

      <div class="sourceCode">

   .. code:: bash

       $ rm driver1.exe

   .. raw:: html

      </div>

4. Re-compile ``driver1.exe``,

   .. raw:: html

      <div class="sourceCode">

   .. code:: bash

       $ make driver1.exe

   .. raw:: html

      </div>

5. Re-run ``driver1.exe``,

   .. raw:: html

      <div class="sourceCode">

   .. code:: bash

       $ ./driver1.exe

   .. raw:: html

      </div>

Does this result in faster code than the original? Is it faster than
your hand-optimized code? Write down the total run-time required for
this test.

Repeat the above steps, but this time using **both** the ``-O2``
compiler flag **and** your hand-optimized code in ``file_new.cpp``.
Determine you can see how well the code runs when you provide a
hand-optimized code to then allow the compiler to optimize as well. How
does this perform in comparison to the other three runs?

There are a great many compiler optimizations that you can try with your
executable. For a full description of all the possible options available
with the GNU compiler collection, try

.. raw:: html

   <div class="sourceCode">

.. code:: bash

    $ man gcc

.. raw:: html

   </div>

The ``-O#`` options allow specification of optimization levels 0, 1, 2
and 3, each one applies additional optimizations to the previous level.
Typically, compilers also implement a basic ``-O`` flag that defaults to
``-O2``. However, there are additional optimizations that can be
performed by the compiler, as will be discussed in the compiler's man
page or online documentation.

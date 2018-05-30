.. _sec-syntax_differences:

Difference in usage from different languages
============================================


.. include:: defs.rst

General usage
-------------


Example:

.. list-table:: General usage
   :header-rows: 1

   * - Task
     - Python
     - C++
     - MATLAB/Octave
   * - Starting |casadi|
     - .. code-block:: python

            from casadi import *
     - .. code-block:: cpp

            #include <casadi/casadi.hpp>
            using namespace casadi;
     - .. code-block:: octave

            import casadi.*
   * - Printing object
     - .. code-block:: python

            print(A)
     - .. code-block:: cpp

            std::cout << A;
     - .. code-block:: octave

            disp(A)




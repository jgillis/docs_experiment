Symbolic framework
==================

.. include:: defs.rst

At the core of |casadi| is a self-contained symbolic framework that allows the user to construct symbolic expressions using a MATLAB inspired everything-is-a-matrix syntax, i.e. vectors are treated as n-by-1 matrices and scalars as 1-by-1 matrices. All matrices are *sparse* and use a general sparse format -- *compressed column storage* (CCS) -- to store matrices. In the following, we introduce the most fundamental classes of this framework.

The |SX| symbolics
-------------------------
The |SX| data type is used to represent matrices whose elements consist of symbolic expressions made up by a sequence of unary and binary operations. To see how it works in practice, start an interactive Python shell (e.g. by typing ``ipython`` from a Linux terminal or inside a integrated development environment such as Spyder) or launch MATLAB's or Octave's graphical user interface. Assuming |casadi| has been installed correctly, you can import the symbols into the workspace as follows:


.. side-by-side::
    .. code-block:: python

        from casadi import *

    &&

    .. code-block:: octave

        import casadi.*

Now create a variable ``x`` using the syntax:

.. side-by-side::
    .. code-block:: python

        x = MX.sym("x")

    &&

    .. code-block:: octave

        x = MX.sym('x');

This creates a 1-by-1 matrix, i.e. a scalar containing a symbolic primitive called ``x``. This is just the display name, not the identifier. Multiple variables can have the same name, but still be different. The identifier is the return value. You can also create vector- or matrix-valued symbolic variables by supplying additional arguments to ``SX.sym``:

.. side-by-side::
    .. code-block:: python

        y = SX.sym('y',5)
        Z = SX.sym('Z',4,2)

    &&

    .. code-block:: octave

        y = SX.sym('y',5);
        Z = SX.sym('Z',4,2);


which creates a 5-by-1 matrix, i.e. a vector, and a 4-by-2 matrix with symbolic primitives, respectively.

``SX.sym`` is a (static) function which returns an |SX| instance. When variables have been declared, expressions can now be formed in an intuitive way:

.. side-by-side::
    .. exec-block:: python

        x = MX.sym("x") [hidden]
        f = x**2 + 10
        f = sqrt(f)
        print(f)

    &&


    .. exec-block:: octave

        x = MX.sym('x'); [hidden]
        f = x^2 + 10;
        f = sqrt(f);
        display(f)


You can also create constant |SX| instances *without* any symbolic primitives:

* ``B1 = SX.zeros(4,5)``: A dense 4-by-5 empty matrix with all zeros
* ``B2 = SX(4,5)``: A sparse 4-by-5 empty matrix with all zeros
* ``B4 = SX.eye(4)``: A sparse 4-by-4 matrix with ones on the diagonal

.. output-block:: python

    B1 = SX.zeros(4,5)
    B2 = SX(4,5)
    B4 = SX.eye(4)
    print('B1:',B1)
    print('B2:',B2)
    print('B4:',B4)

Note the difference between a sparse matrix with *structural* zeros and a dense matrix with *actual* zeros. When printing an expression with structural zeros, these will be represented as ``00`` to distinguish them from actual zeros ``0``.

The following list summarizes the most commonly used ways of constructing new |SX| expressions:

* ``SX.sym(name,n,m)``: Create an :math:`n`-by-:math:`m` symbolic primitive
* ``SX.zeros(n,m)``: Create an :math:`n`-by-:math:`m` dense matrix with all zeros
* ``SX(n,m)``: Create an :math:`n`-by-:math:`m` sparse matrix with all \emph{structural} zeros
* ``SX.ones(n,m)``: Create an :math:`n`-by-:math:`m` dense matrix with all ones
* ``SX.eye(n)``: Create an :math:`n`-by-:math:`n` diagonal matrix with ones on the diagonal and structural zeros elsewhere.
* ``SX(scalar_type)``: Create a scalar (1-by-1 matrix) with value given by the argument. This method can be used explicitly, e.g. ``SX(9)``, or implicitly, e.g. ``9 * SX.ones(2,2)``.
* ``SX(matrix_type)``: Create a matrix given a numerical matrix given as a NumPy or SciPy matrix (in Python) or as a dense or sparse matrix (in MATLAB/Octave). In MATLAB/Octave e.g. ``SX([1,2,3,4])`` for a row vector, ``SX([1;2;3;4])`` for a column vector and ``SX([1,2;3,4])`` for a 2-by-2 matrix. This method can be used explicitly or implicitly.
* ``repmat(v,n,m)``: Repeat expression :math:`v` :math:`n` times vertically and :math:`m` times horizontally. ``repmat(SX(3),2,1)`` will create a 2-by-1 matrix with all elements 3.
* (*Python only*) ``SX(list)``: Create a column vector (:math:`n`-by-1 matrix) with the elements in the list, e.g. ``SX([1,2,3,4])`` (note the difference between Python lists and MATLAB/Octave horizontal concatenation, which both uses square bracket syntax)
* (*Python only*) ``SX(list of list)``: Create a dense matrix with the elements in the lists, e.g. ``SX([[1,2],[3,4]])`` or a row vector (1-by-:math:`n` matrix) using ``SX([[1,2,3,4]])``.


Note on namespaces
^^^^^^^^^^^^^^^^^^
In MATLAB, if the ``import casadi.*`` command is omitted, you can still use |casadi| by prefixing all the symbols with the package name, e.g. ``casadi.SX`` instead of ``SX``, provided the ``casadi`` package is in the path. We will not do this in the following for typographical reasons, but note that it is often preferable in user code. In Python, this usage corresponds to issuing ``import casadi`` instead of ``from casadi import *``.

Unfortunately, Octave (version 4.0.3) does not implement MATLAB's ``import`` command. To work around this issue, we provide a simple
function ``import.m`` that can be placed in Octave's path enabling the compact syntax used in this guide.

Note for C++ users
^^^^^^^^^^^^^^^^^^
In C++, all public symbols are defined in the ``casadi`` namespace and require the inclusion of the ``casadi/casadi.hpp`` header file.
The commands above would be equivalent to:

.. code-block:: cpp

    #include <casadi/casadi.hpp>
    using namespace casadi;
    int main() {
      SX x = SX::sym("x");
      SX y = SX::sym("y",5);
      SX Z = SX::sym("Z",4,2)
      SX f = pow(x,2) + 10;
      f = sqrt(f);
      std::cout << "f: " << f << std::endl;
      return 0;
    }


|DM|
----
|DM| is very similar to |SX|, but with the difference that the nonzero elements are numerical values and not symbolic expressions. The syntax is also the same, except for functions such as ``SX.sym``, which have no equivalents.

|DM| is mainly used for storing matrices in |casadi| and as inputs and outputs of functions. It is *not* intended to be used for computationally intensive calculations. For this purpose, use the builtin dense or sparse data types in MATLAB, NumPy or SciPy matrices in Python or an expression template based library such as ``eigen``, ``ublas`` or ``MTL`` in C++. Conversion between the types is usually straightforward:

.. side-by-side::
    .. code-block:: python

        C = DM(2,3)

        C_dense = C.full()
        from numpy import array
        C_dense = array(C) # equivalent

        C_sparse = C.sparse()
        from scipy.sparse import csc_matrix
        C_sparse = csc_matrix(C) # equivalent

    &&


    .. code-block:: octave

        C = DM(2,3);
        C_dense = full(C);
        C_sparse = sparse(C);


TODO
More usage examples for |SX| can be found in the tutorials at http://docs.casadi.org/. For documentation of particular functions of this class (and others), find the ``C++ API docs'' on the website and search for information about ``casadi::Matrix``.

The |MX| symbolics
------------------
Let us perform a simple operation using the |SX| above:

.. side-by-side::
    .. exec-block:: python

        x = SX.sym('x',2,2)
        y = SX.sym('y')
        f = 3*x + y
        print(f)
        print(f.shape)

    &&

    .. exec-block:: octave

        x = SX.sym('x',2,2);
        y = SX.sym('y');
        f = 3*x + y;
        disp(f)
        disp(size(f))

As you can see, the output of this operation is a 2-by-2 matrix. Note how the multiplication and the addition were performed element-wise and new expressions (of type |SX|) were created for each entry of the result matrix.

We shall now introduce a second, more general *matrix expression* type |MX|. The |MX| type allows, like |SX|, to build up expressions consisting of a sequence of elementary operations. But unlike |SX|, these elementary operations are not restricted to be scalar unary or binary operations (:math:`\mathbb{R} \rightarrow \mathbb{R}` or :math:`\mathbb{R} \times \mathbb{R} \rightarrow \mathbb{R}`). Instead, the elementary operations that are used to form |MX| expressions are allowed to be general *multiple sparse-matrix valued* input, *multiple sparse-matrix valued* output functions: :math:`\mathbb{R}^{n_1 \times m_1} \times \ldots \times \mathbb{R}^{n_N \times m_N} \rightarrow \mathbb{R}^{p_1 \times q_1} \times \ldots \times \mathbb{R}^{p_M \times q_M}`.

The syntax of |MX| mirrors that of |SX|:

.. side-by-side::
    .. exec-block:: python

        x = MX.sym('x',2,2)
        y = MX.sym('y')
        f = 3*x + y
        print(f)
        print(f.shape)

    &&

    .. exec-block:: octave

        x = MX.sym('x',2,2);
        y = MX.sym('y');
        f = 3*x + y;
        disp(f)
        disp(size(f))

Note how the result consists of only two operations (one multiplication and one addition) using |MX| symbolics, whereas the |SX| equivalent has eight (two for each element of the resulting matrix). As a consequence, |MX| can be more economical when working with operations that are naturally vector or matrix valued with many elements. As we shall see in :numref:`Chapter %s <sec-function>`, it is also much more general since we allow calls to arbitrary functions that cannot be expanded in terms of elementary operations.

|MX| supports getting and setting elements, using the same syntax as |SX|, but the way it is implemented is very different. Test, for example, to print the element in the upper-left corner of a 2-by-2 symbolic variable:

.. side-by-side::
    .. exec-block:: python

        x = MX.sym('x',2,2)
        print(x[0,0])

    &&

    .. exec-block:: octave

        x = MX.sym('x',2,2);
        x(1,1)

The output should be understood as an expression that is equal to the first (i.e. index 0 in C++) structurally non-zero element of ``x``, unlike ``x_0`` in the |SX| case above, which is the name of a symbolic primitive in the first (index 0) location of the matrix.

Similar results can be expected when trying to set elements:

.. side-by-side::
    .. exec-block:: python

        x = MX.sym('x',2)
        A = MX(2,2)
        A[0,0] = x[0]
        A[1,1] = x[0]+x[1]
        print('A:', A)

    &&

    .. exec-block:: octave

        x = MX.sym('x',2);
        A = MX(2,2);
        A(1,1) = x(1);
        A(2,2) = x(1)+x(2);
        display(A)


The interpretation of the (admittedly cryptic) output is that starting with an all zero sparse matrix, an element is assigned to ``x_0``. It is then projected to a matrix of different sparsity and an another element is assigned to ``x_0+x_1``.

Element access and assignment, of the type you have just seen, are examples of operations that can be used to construct expressions. Other examples of operations are matrix multiplications, transposes, concatenations, resizings, reshapings and function calls.

Mixing |SX| and |MX|
--------------------
You can *not* multiply an |SX| object with an |MX| object, or perform any other operation to mix the two in the same expression graph. You can, however, in an |MX| graph include calls to a |Function| defined by |SX| expressions. This will be demonstrated in :numref:`Chapter %s <sec-function>`. Mixing |SX| and |MX| is often a good idea since functions defined by |SX| expressions have a much lower overhead per operation making it much faster for operations that are naturally written as a sequence of scalar operations. The |SX| expressions are thus intended to be used for low level operations (for example the DAE right hand side in :numref:`sec-integrator`), whereas the |MX| expressions act as a glue and enables the formulation of e.g. the constraint function of an NLP (which might contain calls to ODE/DAE integrators, or might simply be too large to expand as one big expression).


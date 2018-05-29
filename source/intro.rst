Introduction
============

.. include:: defs.rst

|casadi| is an open-source software tool for numerical optimization in general and optimal
control (i.e.  optimization involving differential equations) in particular.\\

What ``CasADi`` is and what it is *not*
---------------------------------------


blahbalh


.. code-block:: python

  x = SX.sym("x")
  print jacobian(sin(x),x)




# Python
f = x**2 + 10
f = sqrt(f)
print('f:', f)



.. side-by-side::
    .. code-block:: python

        from casadi import * # [hidden]

        x = SX.sym('x') # [hidden]

        sin(x)

    &&

    .. code-block:: ruby

        require 'my-api'


.. side-by-side::
    .. code-block:: python

        import my-api

    &&

    .. code-block:: ruby

        require 'my-api'



.. side-by-side::
    .. code-block:: python

        import my-api


    &&

    .. code-block:: ruby

        require 'my-api'

    &&

    .. code-block:: c++

        require 'my-api'

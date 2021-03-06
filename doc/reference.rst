-----------------
Library Reference
-----------------

First level variables
=====================

.. py:attribute:: __version__

    The version of the carray package.

.. py:attribute:: min_numexpr_version

    The minimum version of numexpr needed (numexpr is optional).

.. py:attribute:: ncores

    The number of cores detected.

.. py:attribute:: numexpr_here

    Whether minimum version of numexpr has been detected.


First level classes
===================

.. py:class:: cparams(clevel=5, shuffle=True)

    Class to host parameters for compression and other filters.

    Parameters:
      clevel : int (0 <= clevel < 10)
        The compression level.
      shuffle : bool
        Whether the shuffle filter is active or not.

    Notes:
      The shuffle filter may be automatically disable in case it is
      non-sense to use it (e.g. itemsize == 1).

Also, see the :py:class:`carray` and :py:class:`ctable` classes below.

.. _first-level-constructors:

First level functions
=====================

.. py:function:: arange([start,] stop[, step,], dtype=None, **kwargs)

    Return evenly spaced values within a given interval.

    Values are generated within the half-open interval ``[start,
    stop)`` (in other words, the interval including `start` but
    excluding `stop`).  For integer arguments the function is
    equivalent to the Python built-in `range
    <http://docs.python.org/lib/built-in-funcs.html>`_ function, but
    returns a carray rather than a list.

    Parameters:
      start : number, optional
        Start of interval.  The interval includes this value.  The default
        start value is 0.
      stop : number
        End of interval.  The interval does not include this value.
      step : number, optional
        Spacing between values.  For any output `out`, this is the
        distance between two adjacent values, ``out[i+1] - out[i]``.
        The default step size is 1.  If `step` is specified, `start`
        must also be given.
      dtype : dtype
        The type of the output array.  If `dtype` is not given, infer
        the data type from the other input arguments.
      kwargs : list of parameters or dictionary
        Any parameter supported by the carray constructor.

    Returns:
      out : carray
        Array of evenly spaced values.

        For floating point arguments, the length of the result is
        ``ceil((stop - start)/step)``.  Because of floating point overflow,
        this rule may result in the last element of `out` being greater
        than `stop`.

.. py:function::  eval(expression, vm=None, out_flavor=None, **kwargs)

    Evaluate an `expression` and return the result.

    Parameters:
      expression : string
        A string forming an expression, like '2*a+3*b'. The values for
        'a' and 'b' are variable names to be taken from the calling
        function's frame.  These variables may be scalars, carrays or
        NumPy arrays.
      vm : string
        The virtual machine to be used in computations.  It can be 'numexpr'
        or 'python'.  The default is to use 'numexpr' if it is installed.
      out_flavor : string
        The flavor for the `out` object.  It can be 'carray' or 'numpy'.
      kwargs : list of parameters or dictionary
        Any parameter supported by the carray constructor.

    Returns:
      out : carray object
        The outcome of the expression.  You can tailor the
        properties of this carray by passing additional arguments
        supported by carray constructor in `kwargs`.

.. py:function:: fill(shape, dflt=None, dtype=float, **kwargs)

    Return a new carray object of given shape and type, filled with `dflt`.

    Parameters:
      shape : int
        Shape of the new array, e.g., ``(2,3)``.
      dflt : Python or NumPy scalar
        The value to be used during the filling process.  If None, values are
        filled with zeros.  Also, the resulting carray will have this value as
        its `dflt` value.
      dtype : data-type, optional
        The desired data-type for the array, e.g., `numpy.int8`.  Default is
        `numpy.float64`.
      kwargs : list of parameters or dictionary
        Any parameter supported by the carray constructor.

    Returns:
      out : carray
        Array filled with `dflt` values with the given shape and dtype.

    See Also:
      :py:func:`zeros`, :py:func:`ones`

.. py:function:: fromiter(iterable, dtype, count, **kwargs)

    Create a carray/ctable from an `iterable` object.

    Parameters:
      iterable : iterable object
        An iterable object providing data for the carray.
      dtype : numpy.dtype instance
        Specifies the type of the outcome object.
      count : int
        The number of items to read from iterable. If set to -1, means
        that the iterable will be used until exhaustion (not
        recommended, see note below).
      kwargs : list of parameters or dictionary
        Any parameter supported by the carray/ctable constructors.

    Returns:
      out : a carray/ctable object

    Notes:
      Please specify `count` to both improve performance and to save
      memory.  It allows `fromiter` to avoid looping the iterable
      twice (which is slooow).  It avoids memory leaks to happen too
      (which can be important for large iterables).

.. py:function:: ones(shape, dtype=float, **kwargs)

    Return a new carray object of given shape and type, filled with ones.

    Parameters:
      shape : int
        Shape of the new array, e.g., ``(2,3)``.
      dtype : data-type, optional
        The desired data-type for the array, e.g., `numpy.int8`.  Default is
        `numpy.float64`.
      kwargs : list of parameters or dictionary
        Any parameter supported by the carray constructor.

    Returns:
      out : carray
        Array of ones with the given shape and dtype.

    See Also:
      :py:func:`fill`, :py:func:`ones`

.. py:function:: zeros(shape, dtype=float, **kwargs)

    Return a new carray object of given shape and type, filled with zeros.

    Parameters:
      shape : int
        Shape of the new array, e.g., ``(2,3)``.
      dtype : data-type, optional
        The desired data-type for the array, e.g., `numpy.int8`.  Default is
        `numpy.float64`.
      kwargs : list of parameters or dictionary
        Any parameter supported by the carray constructor.

    Returns:
      out : carray
        Array of zeros with the given shape and dtype.

    See Also:
      :py:func:`fill`, :py:func:`zeros`


Utility functions
=================

.. py:function:: blosc_set_nthreads(nthreads)

    Sets the number of threads that Blosc can use.

    Parameters:
      nthreads : int
        The desired number of threads to use.

    Returns:
      out : int
        The previous setting for the number of threads.

.. py:function:: blosc_version()

    Return the version of the Blosc library.

.. py:function:: detect_number_of_cores()

    Return the number of cores on a system.

.. py:function:: set_nthreads(nthreads)

    Sets the number of threads to be used during carray operation.

    This affects to both Blosc and Numexpr (if available).

    Parameters:
      nthreads : int
        The number of threads to be used during carray operation.

    Returns:
      out : int
        The previous setting for the number of threads.

    See Also:
      :py:func:`blosc_set_nthreads`


.. py:function:: test()

    Run all the tests in the test suite.


The carray class
================

.. py:class:: carray(array, cparams=None, dtype=None, dflt=None, expectedlen=None, chunklen=None)

  A compressed and enlargeable in-memory data container.

  `carray` exposes a series of methods for dealing with the compressed
  container in a NumPy-like way.

  Parameters:
    array : a NumPy-like object
      This is taken as the input to create the carray.  It can be any Python
      object that can be converted into a NumPy object.  The data type of
      the resulting carray will be the same as this NumPy object.
    cparams : instance of the `cparams` class, optional
      Parameters to the internal Blosc compressor.
    dtype : NumPy dtype
      Force this `dtype` for the carray (rather than the `array` one).
    dflt : Python or NumPy scalar
      The value to be used when enlarging the carray.  If None, the default is
      filling with zeros.
    expectedlen : int, optional
      A guess on the expected length of this carray.  This will serve to
      decide the best `chunklen` used for compression and memory I/O
      purposes.
    chunklen : int, optional
      The number of items that fits on a chunk.  By specifying it you can
      explicitly set the chunk size used for compression and memory I/O.
      Only use it if you know what are you doing.


carray attributes
-----------------

  .. py:attribute:: cbytes

    The compressed size of this object (in bytes).

  .. py:attribute:: chunklen

    The number of items that fits into a chunk.

  .. py:attribute:: cparams

    The compression parameters for this object.

  .. py:attribute:: dflt

    The value to be used when enlarging the carray.

  .. py:attribute:: dtype

    The NumPy dtype for this object.

  .. py:attribute:: len

    The length of this object.

  .. py:attribute:: nbytes

    The original (uncompressed) size of this object (in bytes).

  .. py:attribute:: shape

    The shape of this object.


carray methods
--------------

  .. py:method:: append(array)

    Append a numpy `array` to this instance.

    Parameters:
      array : NumPy-like object
        The array to be appended.  Must be compatible with shape and type of
        the carray.


  .. py:method:: copy(**kwargs)

    Return a copy of this object.

    Parameters:
      kwargs : list of parameters or dictionary
        Any parameter supported by the carray constructor.

    Returns:
      out : carray object
        The copy of this object.


  .. py:method:: iter(start=0, stop=None, step=1, limit=None, skip=0)

    Iterator with `start`, `stop` and `step` bounds.

    Parameters:
      start : int
        The starting item.
      stop : int
        The item after which the iterator stops.
      step : int
        The number of items incremented during each iteration.  Cannot be
        negative.
      limit : int
        A maximum number of elements to return.  The default is return
        everything.
      skip : int
        An initial number of elements to skip.  The default is 0.

    Returns:
      out : iterator

    See Also:
      :py:meth:`where`, :py:meth:`wheretrue`


  .. py:method:: reshape(newshape)

    Returns a new carray containing the same data with a new shape.

    Parameters:
      newshape : int or tuple of ints
        The new shape should be compatible with the original shape. If
        an integer, then the result will be a 1-D array of that length.
        One shape dimension can be -1. In this case, the value is inferred
        from the length of the array and remaining dimensions.

    Returns:
      reshaped_array : carray
        A copy of the original carray.


  .. py:method:: resize(nitems)

    Resize the instance to have `nitems`.

    Parameters:
      nitems : int
        The final length of the object.  If `nitems` is larger than
        the actual length, new items will appended using `self.dflt`
        as filling values.


  .. py:method:: sum(dtype=None)

    Return the sum of the array elements.

    Parameters:
      dtype : NumPy dtype
        The desired type of the output.  If ``None``, the dtype of
        `self` is used.  An exception is when `self` has an integer
        type with less precision than the default platform integer.
        In that case, the default platform integer is used instead
        (NumPy convention).

    Return value:
      out : NumPy scalar with `dtype`

  .. py:method:: trim(nitems)

    Remove the trailing `nitems` from this instance.

    Parameters:
      nitems : int
        The number of trailing items to be trimmed.

    See Also:
      :py:meth:`append`

  .. py:method:: where(boolarr, limit=None, skip=0)

    Iterator that returns values of this object where `boolarr` is true.

    Parameters:
      boolarr : a carray or NumPy array of boolean type
      limit : int
        A maximum number of elements to return.  The default is return
        everything.
      skip : int
        An initial number of elements to skip.  The default is 0.

    Returns:
      out : iterator

    See Also:
      :py:meth:`iter`, :py:meth:`wheretrue`

  .. py:method:: wheretrue(limit=None, skip=0)

    Iterator that returns indices where this object is true.  Only useful for
    boolean carrays.

    Parameters:
      limit : int
        A maximum number of elements to return.  The default is return
        everything.
      skip : int
        An initial number of elements to skip.  The default is 0.

    Returns:
      out : iterator

    See Also:
      :py:meth:`iter`, :py:meth:`where`


carray special methods
----------------------

  .. py:method::  __getitem__(key):

    x.__getitem__(key) <==> x[key]

    Returns values based on `key`.  All the functionality of
    ``ndarray.__getitem__()`` is supported (including fancy indexing),
    plus a special support for expressions:

    Parameters:
      key : string
        It will be interpret as a boolean expression (computed via
        `eval`) and the elements where these values are true will be
        returned as a NumPy array.

    See Also:
      eval


  .. py:method::  __setitem__(key, value):

    x.__setitem__(key, value) <==> x[key] = value

    Sets values based on `key`.  All the functionality of
    ``ndarray.__setitem__()`` is supported (including fancy indexing),
    plus a special support for expressions:

    Parameters:
      key : string
        It will be interpret as a boolean expression (computed via
        `eval`) and the elements where these values are true will be
        set to `value`.

    See Also:
      eval


The ctable class
================

.. py:class:: ctable(cols, names=None, **kwargs)

    This class represents a compressed, column-wise, in-memory table.

    Create a new ctable from `cols` with optional `names`.  The
    columns are carray objects.

    Parameters:
      cols : tuple or list of carray/ndarray objects, or structured ndarray
        The list of column data to build the ctable object.
        This can also be a pure NumPy structured array.
      names : list of strings or string
        The list of names for the columns.  Alternatively, it can be
        specified as a string such as 'f0 f1' or 'f0, f1'.  If not
        passed, the names will be chosen as 'f0' for the first column,
        'f1' for the second and so on so forth (NumPy convention).
      kwargs : list of parameters or dictionary
        Allows to pass additional arguments supported by carray
        constructors in case new carrays need to be built.

    Notes:
      Columns passed as carrays are not be copied, so their settings
      will stay the same, even if you pass additional arguments
      (cparams, chunklen...).


ctable attributes
-----------------

  .. py:attribute:: cbytes

    The compressed size of this object (in bytes).

  .. py:attribute:: cols

    The ctable columns (dict).

  .. py:attribute:: cparams

    The compression parameters for this object.

  .. py:attribute:: dtype

    The NumPy dtype for this object.

  .. py:attribute:: len

    The length of this object.

  .. py:attribute:: names

   The names of the columns (list).

  .. py:attribute:: nbytes

    The original (uncompressed) size of this object (in bytes).

  .. py:attribute:: shape

    The shape of this object.


ctable methods
--------------

  .. py:method:: addcol(newcol, name=None, pos=None, **kwargs)

    Add a new `newcol` carray or ndarray as column.

    Parameters:
      newcol : carray or ndarray
        If a carray is passed, no conversion will be carried out.
        If conversion to a carray has to be done, `kwargs` will
        apply.
      name : string, optional
        The name for the new column.  If not passed, it will
        receive an automatic name.
      pos : int, optional
        The column position.  If not passed, it will be appended
        at the end.
      kwargs : list of parameters or dictionary
        Any parameter supported by the carray constructor.

    Notes:
      You should not specify both `name` and `pos` arguments,
      unless they are compatible.

    See Also:
      :py:func:`delcol`


  .. py:method:: append(rows)

    Append `rows` to this ctable.

    Parameters:
      rows : list/tuple of scalar values, NumPy arrays or carrays
        It also can be a NumPy record, a NumPy recarray, or
        another ctable.


  .. py:method:: copy(**kwargs)

    Return a copy of this ctable.

    Parameters:
      kwargs : list of parameters or dictionary
        Any parameter supported by the carray/ctable constructor.

    Returns:
      out : ctable object
        The copy of this ctable.

  .. py:method:: delcol(name=None, pos=None)

    Remove the column named `name` or in position `pos`.

    Parameters:
      name: string, optional
        The name of the column to remove.
      pos: int, optional
        The position of the column to remove.

    Notes:
      You must specify at least a `name` or a `pos`.  You should
      not specify both `name` and `pos` arguments, unless they
      are compatible.

    See Also:
      :py:func:`addcol`


  .. py:method:: eval(expression, **kwargs)

    Evaluate the `expression` on columns and return the result.

    Parameters:
      expression : string
        A string forming an expression, like '2*a+3*b'. The values
        for 'a' and 'b' are variable names to be taken from the
        calling function's frame.  These variables may be column
        names in this table, scalars, carrays or NumPy arrays.
      kwargs : list of parameters or dictionary
        Any parameter supported by the `eval()` first level function.

    Returns:
      out : carray object
        The outcome of the expression.  You can tailor the
        properties of this carray by passing additional arguments
        supported by carray constructor in `kwargs`.

    See Also:
      :py:func:`eval` (first level function)


  .. py:method:: iter(start=0, stop=None, step=1, outcols=None, limit=None, skip=0)

    Iterator with `start`, `stop` and `step` bounds.

    Parameters:
      start : int
        The starting item.
      stop : int
        The item after which the iterator stops.
      step : int
        The number of items incremented during each iteration.  Cannot be
        negative.
      outcols : list of strings or string
        The list of column names that you want to get back in results.
        Alternatively, it can be specified as a string such as 'f0 f1'
        or 'f0, f1'.  If None, all the columns are returned.  If the
        special name 'nrow__' is present, the number of row will be
        included in output.
      limit : int
        A maximum number of elements to return.  The default is return
        everything.
      skip : int
        An initial number of elements to skip.  The default is 0.

    Returns:
      out : iterable

    See Also:
      :py:meth:`ctable.where`


  .. py:method:: resize(nitems)

    Resize the instance to have `nitems`.

    Parameters:
      nitems : int
        The final length of the instance.  If `nitems` is larger than the
        actual length, new items will appended using `self.dflt` as
        filling values.


  .. py:method:: trim(nitems)

    Remove the trailing `nitems` from this instance.

    Parameters:
      nitems : int
        The number of trailing items to be trimmed.

    See Also:
      :py:meth:`ctable.append`


  .. py:method:: where(expression, outcols=None, limit=None, skip=0)

    Iterate over rows where `expression` is true.

    Parameters:
      expression : string or carray
        A boolean Numexpr expression or a boolean carray.
      outcols : list of strings or string
        The list of column names that you want to get back in results.
        Alternatively, it can be specified as a string such as 'f0 f1'
        or 'f0, f1'.  If None, all the columns are returned.  If the
        special name 'nrow__' is present, the number of row will be
        included in output.
      limit : int
        A maximum number of elements to return.  The default is return
        everything.
      skip : int
        An initial number of elements to skip.  The default is 0.

    Returns:
      out : iterable
        This iterable returns rows as NumPy structured types (i.e. they
        support being mapped either by position or by name).

    See Also:
      :py:meth:`ctable.iter`


ctable special methods
----------------------

  .. py:method::  __getitem__(key):

    x.__getitem__(y) <==> x[y]

    Returns values based on `key`.  All the functionality of
    ``ndarray.__getitem__()`` is supported (including fancy indexing),
    plus a special support for expressions:

    Parameters:
      key : string
        The corresponding ctable column name will be returned.  If not
        a column name, it will be interpret as a boolean expression
        (computed via `ctable.eval`) and the rows where these values are
        true will be returned as a NumPy structured array.

    See Also:
      :py:meth:`ctable.eval`

  .. py:method::  __setitem__(key, value):

    x.__setitem__(key, value) <==> x[key] = value

    Sets values based on `key`.  All the functionality of
    ``ndarray.__setitem__()`` is supported (including fancy indexing),
    plus a special support for expressions:

    Parameters:
      key : string
        The corresponding ctable column name will be set to `value`.
        If not a column name, it will be interpret as a boolean
        expression (computed via `ctable.eval`) and the rows where these
        values are true will be set to `value`.

    See Also:
      :py:meth:`ctable.eval`

.. # Links
.. _API: https://cdn.turbonomic.com/wp-content/uploads/docs/VMT_REST2_API_PRINT.pdf
.. _vmt-connect: https://github.com/rastern/vmt-connect/

Quickstart Guide
================

vmt-util provides utility functions and classes for `vmt-connect`_, and does not
itself provide any direct API methods or calls. The most useful functions will
likely be ``evaluate``, and ``unit_cast`` which has two pre-built wrappers:
``mem_cast`` and ``cpu_cast``.


Unit Conversions
----------------

The base conversion utility ``unit_cast`` is a general purpose unit converter
for scales that have a static conversion factor, such as metric measurements.
Two convenience wrapper functions are provided to make repetetive conversions
easier.

Suppose we have pulled VM statistics and wish to convert the VMem from the reported
value in KB, to something more useful such as GB for reporting. Assuming we have
stored our value in a variable called `vm_memory`, we might do like so:

.. code:: python

   mem_cast(vm_memory)

And if we wanted it in an alternate unit, such as TB or MB, we could specify
the destination unit to the cast function.

.. code:: python

   # memory in TB
   mem_cast(vm_memory, 'TB')

   # memory in GB
   mem_cast(vm_memory, 'MB')


Likewise, there is a convenience wrapper for CPU speeds reported in Mhz, which
defaults to Ghz.

.. code:: python

   # speed in Ghz
   cpu_cast(cpu_speed)

   # speed in Thz
   cpu_cast(cpu_speed, 'THZ')



Expression Parser
-----------------

The ``evaluate`` function provides an extensible expression parser which may be
adapted as needed to create domain specific languages for advanced configurations.
The base expression syntax is CPython.

.. code:: python

   # basic expression evaluation
   exp = '1 + 10 / 5'
   evaluate(exp)

   # returns 3

   # this also returns 3
   exp = 'int(5 / 2 + 1.4)'
   evaluate(exp)

Control flow capabilities are intentionally not present within the base expression
syntax, though as shown above, functions are, including most safe default Pthon
functions. Additionally, the parser may be extended with custom functions using
a mapper dictionary.

.. code:: python

   import random

   # our own unique function
   def number():
       return random.random() * 10

   # map our function to the name we want the parser to recognize
   map = {"get_num": number}

   exp = 'get_num() < 5'
   evaluate(exp, map)

   # returns True or False depending on results of get_num() at runtime

Multiple logic conditions can be combined, the same as in Python.

.. code:: python

   def number():
       return random.random() * 10

   def status_check():
       # produce some conditional response
       return True

   map = {"get_num": number, "status": status_check}
   exp = '(get_num() < 5 and get_num() > 1) or !status()'
   evaluate(exp, map)


###########
Development
###########


.. contents:: Here are some pointers for hacking on ``fortran-magic``.

*******
Testing
*******

Create a virtual environment and install this package in editable mode for
ease of iteration:

.. code-block:: shell-session

   $ python -m pip install --upgrade pip virtualenv
   $ python -m virtualenv --python=python3.7 .venv
   $ .venv/bin/python -m pip install --editable .

Then launch an IPython interpreter and begin experimenting. For example,
if a breakpoint is set via

.. code-block:: diff

   diff --git a/fortranmagic.py b/fortranmagic.py
   index ac73fd6..ac804b1 100644
   --- a/fortranmagic.py
   +++ b/fortranmagic.py
   @@ -332,6 +332,7 @@ class FortranMagics(Magics):
                # py3
                base_str_class = str

   +        breakpoint()
            kw = ['--%s=%s' % (k, v) for k, v in vars(args).items()
                  if isinstance(v, base_str_class)]

then in the test environment invoke ``.venv/bin/ipython``

.. code-block:: ipython

   In [1]: %load_ext fortranmagic

   In [2]: %%fortran --f90flags="-march=native"
      ...:
      ...: subroutine f(x, y, z)
      ...:
      ...:    real*8, intent(in) :: x, y
      ...:    real*8, intent(out) :: z
      ...:
      ...:    z = x + y
      ...:
      ...: end subroutine f
      ...:
      ...:
   > ${HERE}/fortran_magic/fortranmagic.py(336)fortran()
   -> kw = ['--%s=%s' % (k, v) for k, v in vars(args).items()
   (Pdb) pp args
   Namespace(arch=None, compiler=None, debug=False, extra=[], f77flags=None, f90flags='"-march=native"', fcompiler=None, link=[], noarch=False, noopt=False, opt=None, verbosity=0)
   (Pdb) c

   In [3]: f(10.5, 1.5)
   Out[3]: 12.0

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
   index 12b6e25..987defc 100644
   --- a/fortranmagic.py
   +++ b/fortranmagic.py
   @@ -343,6 +343,7 @@ class FortranMagics(Magics):
                    kw.append('--%s=%s' % (k, ' '.join(v)))

            f2py_args.extend(kw)
   +        breakpoint()

            # link resource
            if args.link:

then in the test environment invoke ``.venv/bin/ipython``

.. code-block:: ipython

   In [1]: %load_ext fortranmagic

   In [2]: %%fortran --f90flags="-Wall -Wextra" --f90flags="-march=native -Werror"
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
   > ${HERE}/fortranmagic.py(349)fortran()
   -> if args.link:
   (Pdb) pp line
   '--f90flags="-Wall -Wextra" --f90flags="-march=native -Werror"'
   (Pdb) pp line_args
   ['--f90flags=-Wall -Wextra', '--f90flags=-march=native -Werror']
   (Pdb) pp args
   Namespace(arch=None, compiler=None, debug=False, extra=[], f77flags=None, f90flags=['-Wall -Wextra', '-march=native -Werror'], fcompiler=None, link=[], noarch=False, noopt=False, opt=None, verbosity=0)
   (Pdb) pp kw
   ['--f90flags=-Wall -Wextra -march=native -Werror']
   (Pdb) c

   In [3]: f(10.5, 1.5)
   Out[3]: 12.0

PySophus
======

Overview
--------

**PySophus** defines python bindings for [Sophus](https://github.com/strasdat/Sophus) library.

> Sophus is a c++ implementation of Lie oproups commonly used for 2d and 3d
> geometric problems (i.e. for Computer Vision or Robotics applications).
> Among others, this package includes the special orthogonal groups SO(2) and
> SO(3) to present rotations in 2d and 3d as well as the special Euclidean group
> SE(2) and SE(3) to represent rigid body transformations (i.e. rotations and
> translations) in 2d and 3d.

Dependencies
------------

This package uses:
- [eigency](https://github.com/wouterboomsma/eigency) For basic Eigen support.
- [Sophus](https://github.com/strasdat/Sophus) for Lie algebra support.

Supported operations
--------------------
*Updated on 23/03/2017*

So far, only basic operations are supported:
- `SE3`, `SO3` representation
- Most common group operations (`exp`, `log`, `vee`, `hat`)
- Operators on groups (group multiplication `*`)
- Single axis factories (`rotX`, `rotY`, `rotZ`, `trans(x,y,z)`, `transX`, `transY`, `transZ`)
- ... (Look at `sophus.pxd` for a complete list of supported operations)

If you require any functionality that is not currently in the wrapper, feel free to open a merge request.

How to install
--------------
Install `cython` if you don't have already

```
pip install cython
```

Then, setup `eigency`

```
git clone https://github.com/wouterboomsma/eigency.git
cd eigency
python setup.py
# put in your bashrc
export PYTHONPATH="$PYTHONPATH:<path to eigency>"
```

Then, clone this repository, and run

```
git clone https://github.com/arntanguy/PySophus.git
cd PySophus
git submodule init
git submodule update
python setup.py build_ext --inplace
# put in your bashrc
export PYTHONPATH="$PYTHONPATH:<path to PySophus>"
# alternately, install it to your default dist-packages
python setup.py install
```

Usage
-----

Python usage is transparent, the naming convention of Sophus is kept for the bindings

```python
from sophus import *
import numpy as np

# Default SE3 group element (Identity)
T = SE3()
# Single axis factory: rotation around axis
Tx = SE3.rotX(1.3)
Ty = SE4.rotY(0.5)
# Group operators
T_prod = Tx * Ty

# Group Exponential and Log operations
t = T_prod.log()
T = SE3.exp(t)
R = T.so3()
r = R.log()
R = SO3.exp(r)

# Conversion to/from numpy
numpy_mat = T.matrix()
numpy_mat[0,3] = 2;
T = SE3(numpy_mat)
T.log()

x = np.array([1,0,0,0,0,0]).T
T = SE3.exp(x)
print(T)
```


Notes
-----

- Eigency expects `F_CONTIGUOUS` layout, numpy works by defaults on C_CONTIGUOUS arrays

```python
# Create C_CONTIGUOUS array
T2_C = np.array([[1, 0, 0, 0],
                     [0, math.cos(angle), -math.sin(angle), 0],
                     [0, math.sin(angle), math.cos(angle), 0],
                     [0, 0, 0, 1]])

# Create F_CONTIGUOUS array
T2_F = np.array([[1, 0, 0, 0],
                     [0, math.cos(angle), -math.sin(angle), 0],
                     [0, math.sin(angle), math.cos(angle), 0],
                     [0, 0, 0, 1]], order='F')

# T2_F is F_CONTIGUOUS, memory is simply mapped to eigency
T_F = SE3(T2_F)

# T2_C is first converted from C_CONTIGUOUS to F_CONTIGUOUS, which causes a copy
T_C = SE3(T2_numpy)
```

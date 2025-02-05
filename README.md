# py-filelock

[![PyPI](https://img.shields.io/pypi/v/filelock?style=flat-square)](https://pypi.org/project/filelock/)
[![Supported Python
versions](https://img.shields.io/pypi/pyversions/filelock.svg)](https://pypi.org/project/filelock/)
[![Documentation
status](https://readthedocs.org/projects/filelock/badge/?version=latest&style=flat-square)](https://filelock.readthedocs.io/en/latest/?badge=latest)
[![Code style:
black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)
[![Downloads](https://pepy.tech/badge/filelock/month)](https://pepy.tech/project/filelock/month)
[![check](https://github.com/tox-dev/py-filelock/actions/workflows/check.yml/badge.svg)](https://github.com/tox-dev/py-filelock/actions/workflows/check.yml)

This package contains a single module, which implements a platform independent
file lock in Python, which provides a simple way of inter-process communication:

```Python
from src.filelock import Timeout, FileLock

lock = FileLock("high_ground.txt.lock")
with lock:
    open("high_ground.txt", "a").write("You were the chosen one.")
```

**Don't use** a *FileLock* to lock the file you want to write to, instead create
a separate *.lock* file as shown above.

![animated example](https://raw.githubusercontent.com/tox-dev/py-filelock/main/example/example.gif)


## Similar libraries

Perhaps you are looking for something like

*   https://pypi.python.org/pypi/pid/2.1.1
*   https://docs.python.org/3.6/library/msvcrt.html#msvcrt.locking
*   or https://docs.python.org/3/library/fcntl.html#fcntl.flock


## Installation

*py-filelock* is available via PyPi:

```
$ pip3 install filelock
```


## Documentation

The documentation for the API is available on
[readthedocs.org](https://filelock.readthedocs.io/).


### Examples

A *FileLock* is used to indicate another process of your application that a
resource or working
directory is currently used. To do so, create a *FileLock* first:

```Python
from src.filelock import Timeout, FileLock

file_path = "high_ground.txt"
lock_path = "high_ground.txt.lock"

lock = FileLock(lock_path, timeout=1)
```

The lock object supports multiple ways for acquiring the lock, including the
ones used to acquire standard Python thread locks:

```Python
with lock:
    open(file_path, "a").write("Hello there!")

lock.acquire()
try:
    open(file_path, "a").write("General Kenobi!")
finally:
    lock.release()
```

The *acquire()* method accepts also a *timeout* parameter. If the lock cannot be
acquired within *timeout* seconds, a *Timeout* exception is raised:

```Python
try:
    with lock.acquire(timeout=10):
        open(file_path, "a").write("I have a bad feeling about this.")
except Timeout:
    print("Another instance of this application currently holds the lock.")
```

The lock objects are recursive locks, which means that once acquired, they will
not block on successive lock requests:

```Python
def cite1():
    with lock:
        open(file_path, "a").write("I hate it when he does that.")

def cite2():
    with lock:
        open(file_path, "a").write("You don't want to sell me death sticks.")

# The lock is acquired here.
with lock:
    cite1()
    cite2()

# And released here.
```


## FileLock vs SoftFileLock

The *FileLock* is platform dependent while the *SoftFileLock* is not. Use the
*FileLock* if all instances of your application are running on the same host and
a *SoftFileLock* otherwise.

The *SoftFileLock* only watches the existence of the lock file. This makes it
ultra portable, but also more prone to dead locks if the application crashes.
You can simply delete the lock file in such cases.


## Contributions

Contributions are always welcome, please make sure they pass all tests before
creating a pull request. Never hesitate to open a new issue, although it may
take some time for me to respond.


## License

This package is [public domain](./LICENSE).

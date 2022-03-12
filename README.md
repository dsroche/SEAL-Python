## Microsoft SEAL For Python

Microsoft [**SEAL**](https://github.com/microsoft/SEAL) is an easy-to-use open-source ([MIT licensed](https://github.com/microsoft/SEAL/blob/master/LICENSE)) homomorphic encryption library developed by the Cryptography Research group at Microsoft.

[**pybind11**](https://github.com/pybind/pybind11) is a lightweight header-only library that exposes C++ types in Python and vice versa, mainly to create Python bindings of existing C++ code.

This is a python binding for the Microsoft SEAL library.

It's modified by Dan Roche so that you can save/load from Python `bytes`
objects.


## Contents

* [Build](#build)
* [Note](#note)
  * [Serialize](#serialize)
  * [Other](#other)
* [FAQ](#faq)



## Build
* #### Linux
  Clang++ (>= 5.0) or GNU G++ (>= 6.0), CMake (>= 3.12)

  ```shell
  # Optional
  sudo apt-get install git build-essential cmake python3 python3-dev python3-pip

  # Get the repository or download from the releases
  git clone https://github.com/Huelse/SEAL-Python.git
  cd SEAL-Python

  # Numpy is essential
  pip3 install -r requirements.txt

  # Init the SEAL and pybind11
  git submodule update --init --recursive
  # Get the newest repositories (dev only)
  # git submodule update --remote

  # Build the SEAL lib
  cd SEAL
  cmake -S . -B build -DSEAL_USE_MSGSL=OFF -DSEAL_USE_ZLIB=OFF -DSEAL_USE_ZSTD=OFF
  cmake --build build
  cd ..

  # Run the setup.py
  python3 setup.py build_ext -i
  ```
  
  CMake Options: `-DSEAL_BUILD_EXAMPLES=ON` and [more](https://github.com/microsoft/SEAL#basic-cmake-options)

* #### Windows

  Visual Studio 2019 or newer is required. And use the **x64 Native Tools Command Prompt for Visual Studio 2019**  command prompt to configure and build the Microsoft SEAL library. It's usually can be found in your Start Menu.

  ```shell
  # Same as above
  # Build the SEAL library
  cmake -S . -B build -G Ninja -DSEAL_USE_MSGSL=OFF -DSEAL_USE_ZLIB=OFF -DSEAL_USE_ZSTD=OFF
  cmake --build build

  # Run the setup.py
  python setup.py build_ext -i
  ```

  In general, the Ninja generator is better than the "Visual Studio 16 2019" generator, and there is more information in the Microsoft SEAL official [illustrate](https://github.com/microsoft/SEAL#building-microsoft-seal-manually).


* #### Docker
  requires: [Docker](https://www.docker.com/)

  To build source code into a docker image (from this directory):
  ```shell
  docker build -t huelse/seal -f Dockerfile .
  ```

  To use the image by running it as an interactive container:
  ```shell
  docker run -it huelse/seal
  ```

## Note

* #### Serialize to/from files

  You can use the `save` and `load` methods to serialize to/from files.
  Example:

  ```python
  cipher.save('cipher')

  load_cipher = Ciphertext()
  load_cipher.load(context, 'cipher')  # work if the context is valid.
  ```

  Support type: `Encryptionparams, Ciphertext, Plaintext, SecretKey, Publickey, Relinkeys, Galoiskeys`

  There was a way to use `pickle` before, but that method was extremely
  inefficient.

  Instead, if you want to avoid files, use the `to_bytes` and
  `from_bytes` methods. Note that you usually need the context to deserialize,
  and so `from_bytes` is generally a class method that takes a `SEALContext`
  object as the first argument.

  Here's a short example:

  ```python
  parms = EncryptionParameters(scheme_type.ckks)
  context = SEALContext(parms)

  # ... create ciphext object 'ct'

  data = ct.to_bytes()

  # ... send the data or do whatever you want with it

  recreated_ct = Ciphertext.from_bytes(context, data)
  ```

* #### Other

  This is a fork of [SEAL-Python](https://github.com/Huelse/SEAL-Python) by me,
  Dan Roche. Feel free to submit any issues here, or more likely, submit
  to the upstream library and I will try to keep this for up to date.

## FAQ

1. ImportError: undefined symbol

   Build a shared SEAL library `cmake . -DBUILD_SHARED_LIBS=ON`, and get the `libseal.so`,

   then change the path in `setup.py`, and rebuild.

2. ImportError: libseal.so... cannot find

   a. `sudo ln -s /path/to/libseal.so  /usr/lib`

   b. add `/usr/local/lib` or the `SEAL/native/lib` to `/etc/ld.so.conf` and refresh it `sudo ldconfig`

   c. build in cmake.

3. BuildError: C++17 at least

4. ModuleNotFoundError: No module named 'seal'

   The `.so` or `.pyd` file must be in the current directory, or you have `install` it already.



## Original SEAL-Python Contributors

* Professor: [Dr. Chen](https://zhigang-chen.github.io/)

* [Contributors](https://github.com/Huelse/SEAL-Python/graphs/contributors)

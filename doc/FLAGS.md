Config Flags
============

Component Loading
-----------------
The main components of nchat are split into separate libraries, which by
default are built as shared libraries and dynamically loaded at run-time.
The rationale is to minimize memory usage, startup time and also build
time.

Using build config flags one can change how the internal components are
loaded.

Link internal libraries dynamically:

    HAS_DYNAMICLOAD=OFF


Link internal libraries statically:

    HAS_SHARED_LIBS=OFF


Feature Flags
=============

Enabling / Disabling Protocol Support
-------------------------------------
Protocol components are controlled by the following CMake flags. This fork
supports WhatsApp; Telegram remains available temporarily as an unsupported
legacy component, and Signal is optional:

    HAS_DUMMY=ON
    HAS_TELEGRAM=OFF
    HAS_WHATSAPP=ON
    HAS_SIGNAL=OFF

It is possible to enable / disable protocols by passing one or multiple flags
to cmake:

    mkdir -p build && cd build
    cmake -DHAS_SIGNAL=ON .. && make -s

Similarly the `make.sh` script provides options, example:

    ./make.sh --no-whatsapp build

The unsupported legacy Telegram component can be enabled explicitly. It also
requires `gperf`, which is not installed by `./make.sh deps`:

    NCHAT_CMAKEARGS="-DHAS_TELEGRAM=ON" ./make.sh build


Custom CMake Args
=================
One can pass custom arguments to CMake via the make.sh script by setting the
environment variable `NCHAT_CMAKEARGS`. Example:

    NCHAT_CMAKEARGS="-DHAS_DUMMY=OFF -DHAS_SIGNAL=ON" ./make.sh build

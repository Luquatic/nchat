# Repository Guide

## Scope

- nchat is feature-complete and in maintenance mode. Read `doc/SCOPE.md` before feature work; accepted changes are primarily fixes, protocol updates, portability, packaging, and documentation.
- Keep fixes narrowly scoped. Upstream policy rejects unrelated refactoring or reformatting and generally avoids new dependencies or changes under bundled third-party trees such as `lib/*/ext/`.

## Architecture

- `src/` is the ncurses application and UI. `src/main.cpp` creates protocol implementations and dynamically loads their shared libraries by default.
- `lib/common/src/protocol.h` is the shared protocol contract and message model. Changes there commonly require matching updates across protocol adapters and cache serialization.
- `lib/ncutil/` owns shared configuration, cache, serialization, logging, and platform utilities.
- `lib/duchat`, `lib/wmchat`, and `lib/sgchat` implement the supported Dummy, WhatsApp, and Signal protocols. WhatsApp and Signal bridge C++ adapters to Go code in their `go/` directories.
- Build-generated headers live under build directories, including `generated/composescript.h` and `lib/ncutil/generated/buildinfo.h`; do not edit them.

## Build And Test

- Use `./make.sh build` for the normal Release build in `build/`; use `./make.sh debug` for a Debug build in `dbgbuild/`.
- This fork supports WhatsApp by default. Telegram remains in the source temporarily but is unsupported and defaults off; Signal is optional and also defaults off. Use `NCHAT_CMAKEARGS="-D..." ./make.sh build` for CMake flags, or the wrapper's `--no-whatsapp` option. WhatsApp or Signal requires Go 1.23+; an explicit legacy Telegram build also requires `gperf`.
- `make.sh` intentionally limits parallel jobs based on compiler and available RAM; do not replace it with unrestricted parallelism casually.
- After configuration, build a focused target with `cmake --build build --target <target>` rather than rebuilding everything.
- `./make.sh tests` performs a Telegram-free Release build and then runs all project tests with CTest. For a configured tree, run one test with `ctest --test-dir build -R '^test001$' --output-on-failure` (replace the test name as needed).
- CI's comprehensive command is `./make.sh all -y`; it also installs dependencies, generates documentation, and installs the program, so it is not the routine local verification command.

## Formatting And Generated Documentation

- `./make.sh src` runs `go fmt` and Uncrustify across all project-owned source directories, not only changed files. It requires `uncrustify` and can produce a broad diff; inspect and retain only intentional formatting changes.
- `src/nchat.1` is generated from `build/bin/nchat` by `./make.sh doc` using `help2man`; regenerate it when command-line help or version output changes rather than editing generated sections manually.

# Replace files atomically with bounded staged writes

See [API.md](docs/API.md) for the integration contract. Files are staged exclusively
beside the destination, written in 64 KiB native calls, fsync'd (FlushFileBuffers
on Windows), closed
and published through rename (POSIX) or ReplaceFileW/MoveFileExW (Windows).

Replacement is atomic for ordinary local filesystems which support the native
operation. Atomic visibility is distinct from crash durability: the parent
directory is not fsynced, macOS F_FULLFSYNC is not requested, and network filesystem
semantics are outside this guarantee. No cross-process lock is held. A process
crash before publication can leave a uniquely named `.buoy-save-…tmp` file; normal
Abort, destructor and reported failures remove it best-effort. Cleanup failures
do not hide the original error. New POSIX files are private 0600.

## Install

From a Buoy consumer project with BPM installed:

```sh
bpm add atomicfilekit --git https://github.com/BuoyBay/atomicfilekit.git --rev refs/heads/main
```

BPM records the resolved commit in its lockfile. Keep that lockfile with the
consumer project for reproducible installs.

## API entrypoints

- `AtomicFileException`
- `IsCommitted`

## Build from source

With the native `build-packages` driver from [devtools](https://github.com/BuoyBay/devtools)
on your `PATH`, run from this checkout:

```sh
build-packages --root "$PWD" --compile-consumer atomicfilekit
```

The driver checks source targets and compiles an import consumer. See the
[API guide](docs/API.md) for behavior, limits and any host prerequisites.

## Local Buoy setup

On your own machine, install Buoy and run:

```sh
buoy ai init --both
```


## Contributing

[Contributing](CONTRIBUTING.md) · [License](LICENSE)

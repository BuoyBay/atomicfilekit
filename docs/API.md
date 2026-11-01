# AtomicFileKit

See [API.md](API.md) for the integration contract. Files are staged exclusively
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

POSIX ACLs, extended attributes, alternate streams and non-mode metadata are not
copied. The existing POSIX group and nine rwx bits are preserved. Existing hard
links and unusual ownership/special permissions are rejected
instead of silently changing their semantics. Parent directories must be trusted.
This API intentionally does not claim compare-and-swap against concurrent writers.

Windows replacement keeps a unique original backup until publication succeeds.
Partial replacement error 1177 triggers restoration; a failed restoration retains
both original and staged data with explicit recovery paths. This exceptional
failure can leave the destination missing. If backup deletion fails after a
successful save, the transaction stays committed; the text helper reports an
`AtomicFileException` whose `IsCommitted` is True. See the
[documented Windows failure states](https://learn.microsoft.com/windows/win32/api/winbase/nf-winbase-replacefilew).

## Verification


Maintainers run the private validation suite before release. See the repository README for public build instructions.


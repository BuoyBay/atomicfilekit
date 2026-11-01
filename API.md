# AtomicFileKit integration contract

`Import atomicfilekit` exports:

- `New AtomicFileTransaction(destination As String, maxBytes As Integer = 1073741824)`.
- `Append(data As Bytes)`; copies no whole-file buffer, rejects total above maxBytes.
- Read-only `Count`, `IsClosed`, `IsCommitted`.
- `Commit()` publishes the staged bytes using same-directory atomic replacement.
- `CommitExpectedText(expected As String)` first compares the destination's exact
  UTF-8 bytes to expected, then publishes. Empty expected means an existing empty
  file. Missing/unreadable/different destinations reject. This is optimistic
  concurrency, with a race between final check and replacement; no locking/CAS.
- `Abort()` is idempotent and does not throw for native cleanup failures;
  destructor aborts an unfinished transaction. Read `CleanupError` afterwards:
  a nonempty value means cleanup failed. `RetainedPath` identifies a staged file
  whose deletion failed. Callers must show that error instead of claiming clean
  cancellation. A close failure is recorded even if deletion succeeds.
- `AtomicWriteText(destination As String, text As String, expected As String)`
  performs a checked Studio save. Call only with the last read/saved text.
  This text convenience helper allocates a whole UTF-8 copy of the new text,
  and the expected-text comparison allocates a whole UTF-8 copy of expected text.
  Use Append for bounded streaming exports; apply input text limits in editors.
- `AtomicFileException` reports owned I/O or target safety failures. Its
  `IsCommitted` property is True only when publication succeeded but cleanup of
  the Windows original backup failed. In that case the new destination is saved;
  report the retained backup path rather than claiming the save was aborted.
  Invalid UTF-8 in the text helper or expected text is rejected with this type.

The destination stays unchanged before Commit, including cancellation via Abort.
Commit consumes the transaction, including on failure. Callers must not access a
transaction from multiple threads concurrently. Publish is synchronous and cannot
be cancelled after Commit begins.

The final destination must be a regular file or missing, never a symlink/reparse
point. POSIX existing targets must be owned by the current effective user, have a
single hard link and owner-write permission, and have no setuid/setgid/sticky bits.
Their group and nine rwx bits are preserved; failure to set the group rejects the
save. New files use 0600. Windows existing files use ReplaceFileW with a unique
original backup to preserve destination ACLs; new files inherit the directory ACL.
On Windows replacement error 1177 the backup is restored without overwriting a
newcomer. If recovery also fails, both backup and staged content remain on disk,
with their paths in CleanupError. This exceptional state may leave the destination
missing. A committed replacement with failed backup deletion remains committed.
Parent directories are trusted; this does not defend against a hostile actor
renaming parents or swapping entries during the operation. ACLs/xattrs/ownership
and hard-link identity beyond the stated rules are not preserved on POSIX.

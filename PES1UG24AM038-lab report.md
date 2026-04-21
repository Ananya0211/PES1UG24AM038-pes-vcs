# PES1UG24AM038-VCS Lab Report

**Name:** Ananya S 
**SRN:** PES1UG24AM038
**Platform:** Ubuntu 24.04

---

## Build Instructions

```bash
sudo apt update && sudo apt install -y gcc build-essential libssl-dev
export PES_AUTHOR="Ananya S <PES1UG24AM038>"
make all
```

---

## Phase 1 — Object Storage Foundation

Files modified: `object.c`

`object_write` prepends a `"<type> <size>\0"` header to the data, hashes the
whole thing with SHA-256, skips writing if the object already exists
(deduplication), creates the shard directory, writes to a temp file, fsyncs,
and renames atomically. This guarantees the object store is never left with a
partial file even on a crash.

`object_read` reverifies the SHA-256 after reading (integrity check), parses
the type and declared size from the header, validates the declared size against
the actual byte count, then returns the data portion in a caller-owned buffer.

### Screenshot 1A — `./test_objects`

![Phase 1A](images/1a.png)

### Screenshot 1B — `find .pes/objects -type f`

![Phase 1B](images/1b.png)

---

## Phase 2 — Tree Objects

Files modified: `tree.c`, `Makefile`

`tree_from_index` heap-allocates the `Index` struct (it is ~5.6 MB, which
exceeds the safe stack budget), loads the index, sorts entries by path, then
calls the recursive `write_tree_level` helper.

`write_tree_level` walks the sorted entries at one directory level. For each
plain file entry (no `/` in the remaining path) it adds a blob `TreeEntry`. For
each directory it groups all entries that share the top-level component,
recurses to get the subtree's hash, and adds a `040000` tree entry. After
processing all entries it serialises the `Tree` struct and writes it to the
object store.

### Screenshot 2A — `./test_tree`

![Phase 2A](images/2a.png)

### Screenshot 2B — `xxd` of a raw tree object

![Phase 2B](images/2b.png)

---
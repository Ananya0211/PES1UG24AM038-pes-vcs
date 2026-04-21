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
---
title: "Important limitations of the storage system's snapshotting feature"
date: 2021-12-17
authors:
  - name: HPC Staff
---

The native snapshotting mechanism our storage system does not support the preservation of **hard-links** in snapshots.
That means that if multiple files points to the same blocks of data on disk, **only one of those files will be preserved in snapshots** (you can find a gentle introduction to hard-links [here](https://www.redhat.com/sysadmin/linking-linux-explained)).

That fact implies that, in those scenarios where hard-links are in use, there may be loss of information when recovering files from snapshots (since only one hard-link for each data file would be recovered). Examples of such scenarios include:

- Software installations that make use of hard-links (such as **conda virtual environments**).
- Hard-links manually created by users.
- Files generated as output by applications, if the software creates hard-links (e.g. as a way to avoid data duplication). Fortunately, such cases are quite rare.

Unfortunately, there is nothing we can do to overcome this limitation, so please **avoid the use of hard-links as much as possible and use soft-links instead**.

---
layout: post
title: NSA Codebreakers 2024
date: 2025-01-20 13:08:20 +0530
#categories: 
permalink: "/nsa-codebreakers-2024"
---

### Task 1

```console
$file shipped.db
```
- Tried to open with OpenSQLite browser but couldn't open it. Shows encrypted.

- Then found out it is not an encrypted file:

```console
$fcrackzip -v -u -D -p /usr/share/wordlists/rockyou.txt shipping.db                         
'mimetype' is not encrypted, skipping
'Configurations2/toolpanel/' is not encrypted, skipping
'Configurations2/progressbar/' is not encrypted, skipping
'Configurations2/statusbar/' is not encrypted, skipping
'Configurations2/toolbar/' is not encrypted, skipping
'Configurations2/floater/' is not encrypted, skipping
'Configurations2/popupmenu/' is not encrypted, skipping
'Configurations2/menubar/' is not encrypted, skipping
'manifest.rdf' is not encrypted, skipping
found id 6ecd93cd, 'shipping.db' is not a zipfile ver 2.xx, skipping
no usable files found
```

- After Unzipping, tried to view content of content.xml: All Gibbrish

- Tried to reconstruct it in readable format: 
```console
$zip -r ../files.odt *
```
- Word  & Libreoffice don't show good format, not readable and cannot be analyzed

- Reconstructing to Sqlite file using python script.

- Finally reconstructed after trial and error.

- Found Guardian Armamenet a suspicious company from Thumbnails.png after Googling all companies names

Random Entry for a different addresss at No:899 which is suspicous

**Boom Flag**

![Task 1 Badge](/assets/task1.png)

### Task 2

Create ZFS Pool on Ubuntu using following commands:

```console
$lsblk
$sudo fallocate -l 1G /tmp/zfs-disk.img
$sudo losetup -fP /tmp/zfs-disk.img
$losetup -a
$lsblk
$sudo zpool create nkfipool /dev/loop46
$sudo zfs create nkfipool/rjfs
$zfs list -t snapshot
$zpool list
```

If directory is hidden:
```console
$sudo zfs get snapdir nkfipool/rjfs
$sudo zfs set snapdir=visible nkfipool/rjfs
$sudo zfs get snapdir nkfipool/rjfs
```

Info on Pool:

```console
$sudo zdb -vvv nkfipool/rjfs
```

Manually Recover each Snapshot SEQUENTIALLY using source GUID & Destination GUID:

View GUID: 
```console
$ for f in *; do file "$f"; done | cut -d':' -f1,4,5 | column -t -s ':'
$ sudo zfs receive nkfipool/rjfs > logseq24006643128753-i
```
 
Check directory of each recovered snapshot: :/nkfipool/rjfs/planning/pages
```console
$ ls
 contents.md        'Elliptic curve recovery.md'   LLM.md   'token generation.md'  'ZFS snapshots.md'
'CUDA elliptic.md'   golang.md                     todo.md   tools.md
```

Do checksum of each file

Write Script to do checksum of each file, then print it:

```console
#!/bin/bash

# Base directory where all logseq directories are stored
BASE_DIR="/nkfipool/rjfs/.zfs/snapshot"

# List of logseq directories
LOGSEQ_DIRS=(
    "logseq11079254764090"
    "logseq11652828730004"
    "logseq11865316111135"
    "logseq1275510227140"
    "logseq169607977607"
    "logseq1838778124435"
    "logseq18584850428477"
    "logseq211821980113211"
    "logseq24006643128753"
    "logseq259372600032543"
    "logseq263292687127458"
    "logseq269718728817"
    "logseq27077118912783"
    "logseq30280209123973"
    "logseq307981931423438"
    "logseq4015160425167"
    "logseq73041633518013"
    "logseq77311709316810"
    "logseq81871640320073"
    "logseq98931947620095"
)

# Iterate through each logseq directory
for logseq_dir in "${LOGSEQ_DIRS[@]}"; do
    # Directory containing files
    PAGES_DIR="$BASE_DIR/$logseq_dir/planning/pages"
    
    # Check if the directory exists
    if [ -d "$PAGES_DIR" ]; then
        #echo "Processing files in $PAGES_DIR"
	echo ""
        
        # Iterate through each file in the pages directory
        for file in "$PAGES_DIR"/*; do
            if [ -f "$file" ]; then
                # Print the SHA256 checksum of the file
                sha256sum "$file"
            fi
        done
    else
        #echo "Directory $PAGES_DIR does not exist or is inaccessible."
	echo ""
    fi
done
```

**Boom You've got all checksums**

![Task 2 Badge](/assets/task2.png)
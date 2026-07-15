## Essential icommands with examples

### Connecting and navigating

```bash
# initialise a session (prompts for password)
$ iinit

# print your current working collection
$ ipwd

# list the contents of a collection (replace USER.NAME with correct credential)
$ ils /PHE/home/USER.NAME@phe.gov.uk

# list with metadata and resource info
$ ils -l /PHE/home/USER.NAME@phe.gov.uk/data.csv
```

### Uploading and downloading

```bash
# upload a single file (results.csv) to your home collection
$ iput results.csv

# upload and register checksum (-K) with verbose output (-v)
$ iput -Kv big_dataset.h5 /myZone/home/alice/projects/

# download a data object to the current local directory
$ iget /myZone/home/alice/projects/big_dataset.h5

# download and verify checksum
$ iget -K /myZone/home/alice/projects/big_dataset.h5 ./local_copy.h5
```

### Managing collections

```bash
# create a new collection (directory)
$ imkdir /myZone/home/alice/results_2024

# move / rename a data object
$ imv /myZone/home/alice/old_name.txt /myZone/home/alice/new_name.txt

# copy a data object within iRODS (no local transfer)
$ icp /myZone/home/alice/template.json /myZone/home/alice/projects/config.json

# remove a data object (use -r for collections, -f to skip trash)
$ irm /myZone/home/alice/scratch/temp_file.txt
```

### Working with metadata (AVUs)

```bash
# add metadata to a data object
$ imeta add -d /myZone/home/alice/results.csv  species  "Homo sapiens"
$ imeta add -d /myZone/home/alice/results.csv  tissue   "liver"
$ imeta add -d /myZone/home/alice/results.csv  quality  "pass"

# add metadata to a collection
$ imeta add -C /myZone/home/alice/projects/  project  "EBI-2024-001"

# list all metadata on an object
$ imeta ls -d /myZone/home/alice/results.csv
```

Example output of `imeta ls -d results.csv`:

```
attribute: species
value: Homo sapiens
units:
----
attribute: tissue
value: liver
units:
----
attribute: quality
value: pass
units:
```

### Searching with iquest

`iquest` queries the iCAT catalog using an SQL-like syntax.

```bash
# find all data objects owned by alice larger than 1 GB
$ iquest "select DATA_NAME, DATA_SIZE \
        where DATA_OWNER_NAME = 'alice' \
        and DATA_SIZE > '1073741824'"

# find objects with a specific AVU
$ iquest "select COLL_NAME, DATA_NAME \
        where META_DATA_ATTR_NAME = 'tissue' \
        and META_DATA_ATTR_VALUE = 'liver'"

# list all collections under a path
$ iquest "select COLL_NAME \
        where COLL_NAME like '/myZone/home/alice/%'"
```

### Access control

```bash
# grant nicola read access to a data object
$ ichmod read nicola /myZone/home/alice/results.csv

# grant a group ngsservice write access to a collection (recursively)
$ ichmod -r write ngsservice /myZone/home/alice/projects/

# view current permissions
$ ils -A /myZone/home/alice/results.csv
```

### Replication and resources

```bash
# list all available storage resources
$ ilsresc

# Output
fast_resc:unixfilesystem
s3_resc:s3

# replicate a data object to a second resource
$ irepl -R archive_resource /myZone/home/alice/important.h5

# verify all replicas are consistent
$ ichksum -a /myZone/home/alice/important.h5
```

### Quick-reference table

| Task | Command |
|---|---|
| Upload file | `iput file.txt` |
| Download file | `iget /zone/home/u/file.txt` |
| List collection | `ils -l /zone/home/u/` |
| Add metadata | `imeta add -d obj attr val` |
| Query metadata | `imeta qu -d attr = val` |
| Search catalog | `iquest "select …"` |
| Set permissions | `ichmod read user obj` |
| Replicate data | `irepl -R resource obj` |
| Verify checksums | `ichksum -a obj` |
| End session | `iexit full` |

> iRODS is particularly powerful when you combine its metadata and rule engine — for example, automatically tagging every uploaded file with a timestamp AVU and replicating it to a backup resource without any manual steps.

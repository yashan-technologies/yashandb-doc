YashanDB supports importing vector data through the yasldr tool.

yasldr supports importing vector data in text format, CLOB format, and FVECS binary format. When you need to quickly import large amounts of vector data, importing through FVECS binary format files can achieve higher import efficiency.

The text format and CLOB format import methods are similar to those for normal business data import. Please refer to [yasldr Reference Guide](../Tools Guide/yasldr/User Guide for yasldr) for operations. This chapter focuses on introducing the import of FVECS binary format vector data.

> **Note**:
>
> Since the FVECS binary format file can only handle vector data with FLOAT32 precision, when processing FLOAT64 vector data is required, please import it through text format or CLOB format.

## Prerequisites

FVECS binary format vector data files have been generated, and each FVECS file contains only one vector data. [Appendix: Generating FVECS Binary Files from Vector Text](#charToFvecs) provides a sample script.

This chapter assumes that two vectors need to be imported, and therefore generates two vector files: `vector_1.fvecs` and `vector_2.fvecs`.

## Operation Steps

1. Connect to the database as a system administrator, create an import operation user and grant necessary privileges:

```sql
-- Connect to the database as sys user
$ yasql sys/*******@192.168.1.2:1688

-- Create vector database user
CREATE USER vector_user IDENTIFIED BY "password";

-- Grant connect permission
GRANT CONNECT TO vector_user;

-- Grant resource permission
GRANT RESOURCE TO vector_user;
```

2. Connect to the database with the newly created user, create a base table for storing vector data:

```sql
-- Connect to the database as vector_user
$ yasql vector_user/password@127.0.0.1:1688

-- Create vector table
CREATE TABLE vector_table (
    id INT NOT NULL,
    embedding VECTOR(1024, FLOAT32)
) ORGANIZATION HEAP;
```

3. Prepare the data file to be imported on the database server. Using the aforementioned vector table as an example, create a .dat file here containing the id column and the vector column.

```bash
$ vi /home/yasldr/vector.dat
1|vector_1.fvecs
2|vector_2.fvecs
```

4. In the local directory of the database server, create the control file `load_vector.ctl` with the following content:

```bash
$ vi /home/yasldr/load_vector.ctl
LOAD DATA INFILE '/home/yasldr/vector.dat'
FIELDS TERMINATED BY '|'
INTO TABLE vector_table
(
    id,
    file1 FILLER
    embedding LOBFILE(file1 format "fvecs") terminated BY eof
)
```

5. Use yasldr tool to execute FVECS vector data import, MODE must be set to BASIC:

```bash
$ yasldr vector_user/password@127.0.0.1:1688 MODE=BASIC CONTROL_FILE=/home/yasldr/load_vector.ctl
YashanDB Loader Enterprise Edition Release {version_number} x86_64 a533f66
2 rows successfully loaded.
Check /home/yasdb/vector_table_stats.log for more info.
[YASLDR] execute succeeded
```

6. After the import is completed, verify whether the data has been correctly imported.

```sql
-- Count imported vector data rows
SQL> SELECT COUNT(*) FROM vector_table;

          COUNT(*)
-----------------
                2
```

<span id="charToFvecs" name="charToFvecs"></span>

## Appendix: Generating FVECS Binary Files from Vector Text

This example assumes that three vector data entries have been written into three separate text files.

```bash
cat '[1.0,2.0,3.0]' > vector_1.txt
cat '[4.0,5.0,6.0]' > vector_2.txt
cat '[7.0,8.0,9.0]' > vector_3.txt
```

The following is an example Python script that converts a text file into an FVECS binary file.

```py
#!/usr/bin/env python3
"""
fvecs format: Each vector starts with 4 bytes int32 (dimension count, little-endian), followed by the corresponding number of 4-byte float32 (little-endian)
"""

import os
import struct


def parse_vector_text(text):
    """Parse text format vector [val1, val2, ...]"""
    text = text.strip()
    if text.startswith("[") and text.endswith("]"):
        text = text[1:-1]
    values = []
    for v in text.split(","):
        v = v.strip()
        if v:
            values.append(float(v))
    return values


def write_fvecs(filename, vectors):
    """Write vector list to fvecs binary file (FLOAT32 format)"""
    with open(filename, "wb") as f:
        for vec in vectors:
            dim = len(vec)
            # Write dimension (int32, little-endian)
            f.write(struct.pack("<i", dim))
            # Write vector data (float32, little-endian)
            for val in vec:
                f.write(struct.pack("<f", val))


def read_vector_file(filepath):
    """Read text vector file"""
    with open(filepath, "r") as f:
        content = f.read()
    return parse_vector_text(content)


def main():
    base_dir = os.path.dirname(os.path.abspath(__file__)) or "."

    # Read existing vector text files and convert to fvecs
    # Vector text file content format: [1.0,2.0,3.0,.....,10.0]
    print("=== Generate fvecs file ===")
    vector_files = [
        ("vector_1.txt", "vector_1.fvecs"),
        ("vector_2.txt", "vector_2.fvecs"),
        ("vector_3.txt", "vector_3.fvecs"),
    ]

    for txt_file, fvecs_file in vector_files:
        txt_path = os.path.join(base_dir, txt_file)
        fvecs_path = os.path.join(base_dir, fvecs_file)

        print(f"Generate {fvecs_file}...")

        # Read vector data
        vector = read_vector_file(txt_path)
        print(f"  Dimension: {len(vector)}")

        # Write fvecs file
        write_fvecs(fvecs_path, [vector])
        print(f"  Written: {fvecs_path}")

    print("\nfvecs file generation completed!")


if __name__ == "__main__":
    main()
```

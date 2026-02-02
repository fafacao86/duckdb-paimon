# DuckDB Paimon Extension
## Getting started
First step to getting started is to create your own repo from this template by clicking `Use this template`. Then clone your new repository using 
```sh
git clone --recurse-submodules git@github.com:qusijun/duckdb-paimon.git
```
Note that `--recurse-submodules` will ensure DuckDB is pulled which is required to build the extension.

## Building
Now to build the extension, run:
```sh
make
```
The main binaries that will be built are:
```sh
./build/release/duckdb
./build/release/test/unittest
./build/release/extension/paimon/paimon.duckdb_extension
```
- `duckdb` is the binary for the duckdb shell with the extension code automatically loaded. 
- `unittest` is the test runner of duckdb. Again, the extension is already linked into the binary.
- `paimon.duckdb_extension` is the loadable binary as it would be distributed.

### Tips for speedy builds
DuckDB extensions currently rely on DuckDB's build system to provide easy testing and distributing. This does however come at the downside of requiring the template to build DuckDB and its unittest binary every time you build your extension. To mitigate this, we highly recommend installing [ccache](https://ccache.dev/) and [ninja](https://ninja-build.org/). This will ensure you only need to build core DuckDB once and allows for rapid rebuilds.

To build using ninja and ccache ensure both are installed and run:

```sh
GEN=ninja make
```

## Running the extension
To run the extension code, simply start the shell with `./build/release/duckdb`. This shell will have the extension pre-loaded.  
```sql
require paimon

# Attach warehouse root; schema "default" comes from default.db
statement ok
ATTACH 'test/data' AS pc (TYPE paimon);

# Scan table via catalog (schema from LoadTableSchema, not from first batch)
query II
SELECT * FROM pc.default.word_count ORDER BY 1, 2;
----
0	1
1	1
2	2
3	1
4	1
5	2
7	1
9	1
c	1
d	1
f	1
flink	3
hello	1
paimon	4
world	2
```

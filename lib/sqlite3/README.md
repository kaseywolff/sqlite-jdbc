this file contains necessary files to compile sqlite with SEE

based on the Makefile build of sqlite, the below commands can be used to compile libsqlite3.a
(ensure you are in the `sqlite3` directory before running these commands)
```bash
gcc -o src/sqlite3.o -c \
	-DSQLITE_ENABLE_LOAD_EXTENSION=1 \
	-DSQLITE_HAVE_ISNAN \
	-DHAVE_USLEEP=1 \
	-DSQLITE_ENABLE_COLUMN_METADATA \
	-DSQLITE_CORE \
	-DSQLITE_ENABLE_FTS3 \
	-DSQLITE_ENABLE_FTS3_PARENTHESIS \
	-DSQLITE_ENABLE_FTS5 \
	-DSQLITE_ENABLE_RTREE \
	-DSQLITE_ENABLE_STAT4 \
	-DSQLITE_ENABLE_DBSTAT_VTAB \
	-DSQLITE_ENABLE_MATH_FUNCTIONS \
	-DSQLITE_THREADSAFE=1 \
	-DSQLITE_DEFAULT_MEMSTATUS=0 \
	-DSQLITE_DEFAULT_FILE_PERMISSIONS=0666 \
	-DSQLITE_MAX_VARIABLE_NUMBER=250000 \
	-DSQLITE_MAX_MMAP_SIZE=1099511627776 \
	-DSQLITE_MAX_LENGTH=2147483647 \
	-DSQLITE_MAX_COLUMN=32767 \
	-DSQLITE_MAX_SQL_LENGTH=1073741824 \
	-DSQLITE_MAX_FUNCTION_ARG=127 \
	-DSQLITE_MAX_ATTACHED=125 \
	-DSQLITE_MAX_PAGE_COUNT=4294967294 \
	-DSQLITE_DISABLE_PAGECACHE_OVERFLOW_STATS \
	-Iinclude \
	src/sqlite3.c
```

you can check to ensure the proper header file was included in the object file by running
```bash
nm ./src/sqlite3.o | grep sqlite3_activate_see
```

followed by
```bash
ar rcs lib/libsqlite3.a src/sqlite3.o
```


finally, when you run `make native`, you can run
```bash
make native SQLITE_OBJ=lib/sqlite3/lib/libsqlite3.a SQLITE_HEADER=lib/sqlite3/include/sqlite3.h
```
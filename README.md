# sqlite-jdbc + DBeaver 🦫
this readme will walk you through how to use sqlite-jdbc and DBeaver so you can have a gui to use with your SQLite3-SEE encrypted database. sqlite-see requires the purchase of a license, therefore, the sqlite3.c, sqlite3.h, and shell.c files in this repo are blank and simply placeholders. make sure you add your own sqlite3-see files before you begin.

[documentation for SQLite-SEE](https://www.sqlite.org/see/doc/release/www/readme.wiki)

## sqlite-jdbc

### setup to build sqlite-jdbc from source

1. install JDK (min version 11)

  - [download here](https://www.oracle.com/java/technologies/downloads/)
  - this will probably be added to `/Library/Java/JavaVirtualMachines/` (if you're on a mac) so you will need to add this path
  - in your `.zshrc` file, or wherever you typically add to your path:
      ```
      # added for sqlite-jdbc
      export JAVA_HOME=/Library/Java/JavaVirtualMachines/<jdk-xx>.jdk/Contents/Home # change the jdk version to whichever one you downloaded 
      export PATH=$JAVA_HOME/bin:$PATH
      ```

  - verify everything is set up correctly by running the following
      ```bash
      java -version
      # should  output something similar to:
      # java version "21.0.4" 2024-07-16 LTS
      # Java(TM) SE Runtime Environment (build 21.0.4+8-LTS-274)
      # Java HotSpot(TM) 64-Bit Server VM (build 21.0.4+8-LTS-274, mixed mode, sharing)

      javac -version
      # should  output something similar to:
      # javac 21.0.4
      ```

2. install maven using homebrew
    ```bash
    brew install maven
    ```

  - verify everything is set up correctly by running the following
     ```bash
    mvn -v
    # should  output something similar to:
    # Apache Maven 3.9.9 (1234abcd5678efgh9101112ijkl)
    # Maven home: /opt/homebrew/Cellar/maven/3.9.9/libexec
    # Java version: 21.0.4, vendor: Oracle Corporation, runtime: /Library/Java/JavaVirtualMachines/jdk-21.jdk/Contents/Home
    # Default locale: en_US, platform encoding: UTF-8
    # OS name: "...", version: "...", arch: "...", family: "mac"
    ```

### build from source

not much to it if you're just building on mac. 
  - documentation:
    - `CONTRIBUTING.md`
    - `README.md` (located in `./lib/sqlite3`) for instructions on compiling SQLite3-SEE to be used in this driver.

once you've compiled sqlite, run the following:

```bash
# per instructions from CONTRIBUTING.md
make native

# actually generate the .jar file needed in the connection for DBeaver
mvn package

# after this completes, you'll see a file named
# sqlite-jdbc-3.46.1.2-SNAPSHOT.jar
# or similar
# at the root of the target directory. this is what will be used to link to DBeaver
```


## DBeaver 🦫


[download free version (community edition)](https://dbeaver.io/download/)

[documentation](https://dbeaver.com/docs/dbeaver/)

[sqlite instructions](https://dbeaver.com/docs/dbeaver/Database-driver-SQLite/)

[connecting to db](https://dbeaver.com/docs/dbeaver/Create-Connection/)


### adding our sqlite-see jdbc

1. go to `Database > Driver Manager`

2. find `SQLite` and click copy

3. change the name to `SQLite-SEE` and the description to reflect you are using SEE as well

  - the fields should autopopulate, but if not, make sure you have the below filled out
   
    Class Name: org.sqlite.JDBC
    
    URL Template: jdbc:sqlite:{file}

4. go to the `Libraries` tab
  - delete the items that are there (probably `org.xerial:sqlite-jdbc:RELEASE` and an apache license)
  - click `Add File` and locate the `sqlite-jdbc-3.46.1.2-SNAPSHOT.jar` file you built above.
    > note: if there's a chance you might accidentally delete the sqlite-jdbc repo, go ahead and put the jar in a safe place to avoid future problems


### setting things up to properly open an encrypted database

5. either click the New Database Connection icon (a plug with a + 🔌➕) or go to `Database > New Database Connection`

6. find and select the SQLite-SEE driver you created above

7. 'Path:' is where you will add your encrypted database (assuming that you have it saved on your machine). click `Open` and add it

8. click `Connection details` (towards the bottom right)

9. click `Connection initialization settings` (towards the bottom left) 

10. click `Configure`

11. here, you will need to `Add` the below PRAGMA commands _*separately*_, otherwise you'll get an error stating the file is not a database. also make sure you know what type of key you need (ie `key`, `textkey`, or `hexkey`)

    ```
    PRAGMA activate_extensions='see-7bb07b8d471d642e';
    PRAGMA textkey='your-secret-key';
    ```
12. click `Finish` and you should be good to go! 🥳



## troubleshooting

- if you don't add your jdk path, `make native` won't work. make sure your path is correct!
- using brew to install maven is definitely the way to go if you're on a mac..
- it may be silly, but if you accidentally exit all of the windows within DBeaver, it'll probably look like your database isn't opening. reset the windows by going to `Window > Reset Perspective` (major face palm)

## tips

- 🔎 command to search a binary to make sure it contains your sqlite-see stuff

    ```bash
    # assuming you are in the same directory as your sqlite library file
    nm -gU libsqlitejdbc.dylib | grep sqlite3_activate_see

    # assuming you are not in the same directory as your sqlite library file
    nm -gU target/sqlite-3.46.1-Mac-aarch64/libsqlitejdbc.dylib | grep sqlite3_activate_see

    # should output something like
    # 0000000000026c1c T _sqlite3_activate_see
    # if it exists
    ```

  - `nm`: used to display information about symbols in the specified File, which can be an object file, an executable file, or an object-file library
  - `-g` (or `--extern-only`): option to display only external symbols
  - `-U` (or `--defined-only`): option to display only defined symbols for each object file. not to be confused with `-u`, which is for undefined symbols
  - `|` (pipe): used to send the output of one command as the input to another command
  - `grep`: used to search for specific patterns within files
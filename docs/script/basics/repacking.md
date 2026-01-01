In this section, you will learn how to setup your script exporter. 

!!! note

    This section assumes that you have already [setup the codebase properly](./extracting.md).

## Installing Java

### Using Java 5

For the most consistent results, we need to use the same Java version as the XIII Trilogy, go to the [Oracle website and download the Java 5 SDK](https://www.oracle.com/java/technologies/java-archive-javase5-downloads.html) and install it.


!!! tip

    (Note, this no longer works due to oracle requiring MFA on all accounts).
    You can either create an account with your email or us this throwaway account below, to avoid creating an account with your email: 
    ```
    email: rebil93626@oziere.com
    Password: TrilogyXIIIOraclePwd5!
    ```

### Using newer JDKs (Experimental)

The most recent version of WhiteCLBTool can process class files generated from any version of Java, not just Java 5.

However there is the possibility that using newer language features or compilers may introduce instructions or structures that the game engine cannot handle.

Using a newer JDK version is the least friction approach, as it is much easier and safer to use a newer JDK, however case should be taken when writing or
modifying scripts to try and keep to the limited syntax options available in older versions (You can set the target version in your IDE to help with this at
syntax highlighting time).

## Setting up the IntelliJ IDEA external tool

If you had followed the IntelliJ IDEA setup guide before, then follow these steps given below.

We are now going to setup an external tool to compile our .java files to .clb in IntelliJ:

* Open IntelliJ, then go to File->Settings->Tools->ExternalTools:

<figure markdown>
  ![Image](../../assets/script/basics/intelliJExtTool1.png)
</figure>

* Click on the "+" button to create a new one and name it however you want. The following window will appear:

<figure markdown>
  ![Image](../../assets/script/basics/intelliJExtTool2.png)
</figure>

The most important fields are the following:

!!! note
    - Program: put your **WhiteCLBtool.exe** location here, between quotes ("")
    - Arguments: put your **javac.exe** location, from your Java install folder, between quotes ("") and after that put
    ```
    "$FilePathRelativeToSourcepath$"
    ```
    - Working Directory: put 
    ```
    $Sourcepath$
    ```

That's it, you will now be able to generate your clb file with a single click, from your .java file by right-clicking and then choosing **External Tools -> YourToolName**.


## Setting up a task for VS Code

If you had followed the VS Code setup guide before, then follow these steps given below.

We are going to setup a VS Code task, that can be used to quickly compile .java files to .clb:

* Open VS Code and in the search bar at the top, type ``> Tasks: Open User Tasks``. select this option and it will open a ``tasks.json`` file.

<figure markdown>
  ![Image](../../assets/script/basics/vscodeSearchTask.png)
</figure>

* In the json file and inside the "tasks" object, we will create a new task called WhiteCLBtool. you can refer to this image below and setup the task according to it. make sure that you put the location of the **WhiteCLBtool.exe** on your PC for the ``command`` object's value.

<figure markdown>
  ![Image](../../assets/script/basics/vscodeSetupTask.png)
</figure>

!!! note
    - Change the path separator characters from `\` to `\\`. 

    - The javac file location can be from any Java 5+ SDK installation on your PC.
    
    - Here is the json object, which you can use as a reference:
    ```json
     {
        "label": "WhiteCLBtool",
        "type": "process",
        "command": "U:\\Documents\\Visual Studio Projects\\C# stuff\\App projects\\FFXIII Mod Programs\\WhiteCLBtool\\bin\\Release\\WhiteCLBtool.exe",
        "options": {
            "cwd": "${fileDirname}"
        },
        "args": [
            "-c", 
            "C:\\Program Files\\Java\\jdk1.5.0_22\\bin\\javac.exe",
            "${fileBasename}"
        ],
        "group": {
            "kind": "build"
        }
    }   
    ```

## Compilation and Repacking workflow

Scripts fall into 2 broad categories. There are the base system classes (which will be unpacked as `sys/script_decomp`), and script files loaded as part of other areas of the system (such as `zone` and `btscene`).

While WhiteCLBTool can be used to recompile and convert a .java file all the way into a .clb script, compilation of some scrips (especially those with cross-script references) can be somewhat awkward. In these cases, the compilation can be performed upfront, and the generated .class file converted into a .clb using WhiteCLBTool directly, e.g.:

```
WhiteCLBtool -c someScript.class
```

### Modifying base system scripts

Modifying the base classes is the easiest place to start, as it does not depend on information from other locations. The `javac` step of compiling the `.java` file into a `.class` file should mostly just work as is assuming your directories are setup as expected.

### Modifying non-sys script files

Zone files will often depend on other script files within the same zone, making compilation slightly awkward. At current, WhiteCLBTool cannot correctly handle these files, as the compilation will fail (at least on Java 6+ compilers) without the referenced files available, and no source path option is passed to the compiler. Not only will cross-script links within a single zone cause issues, but also the lack of access to the core system files has the same effect.
To improve the workflow, you can build a JAR file of the system scripts, and provide this to `javac` on the class path argument. The steps for doing this are outlined below:

- Navigate to the folder containing the system script files
- Build a list of java files to compile (using `dir`, `find`, `Get-ChildItem`, etc.) and place into a file called `files.txt` or similar in the root of this directory. (Note: exclude `snd/SoundSession` if it exists, as it is unused in all games and causes errors).
- Compile the list of files using `javac`, you may manually need to fix any issues introduced by the decompilation process.
- Package the built class files into an archive using `jar`.
- Return to the folder for the zone/btl script you wish to modify
- Run javac as follows: `javac name-of-script.java --source-path . -implicit:none -cp ../../path/to/sys.jar`
- This should build the class file as expected with the references resolved at build time (but not transitively built into .class files).

Alternatively, you can unpack the clbs twice, once with the `--class` option to retain the pre-built class files, and then use these to reference against when compiling modified .java files without having to rebuild the entire tree manually.

### Common issues from decompilation

Older versions of WhiteCLBTool will sometimes mis-detect class keywords and add an extra `public` keyword to any variable containing the string `class` - this is resolved in the latest release.

Several classes may not be correctly detected as `abstract` and will need the keyword added.

Sometimes variables will be type overlapped in scripts (i.e. the code will attempt to assign a `boolean` to an `int` value without casting), usually it is simpler to just create a new varible rather than treating everything as `Object` and casting everywhere. Either way is in theory identical in net effect so prioritise your own ease of use.

As CLB files are generated from single `.class` files at a time, issues in associated scripts can be ignored/removed just to get past the compilation process and then reinstated afterwards, however if you're planning on making substantial changes to a wide range of scripts it may be simpler in the long run to squash out the errors from decompilation up front.
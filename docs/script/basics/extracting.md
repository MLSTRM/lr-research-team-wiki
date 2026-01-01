In this section, you will learn how to generate your codebase from the game files and setup the necessary tools for editing the scripts.

* Download the [WhiteCLBtool archive](https://mega.nz/file/Gs4BTagZ#8WBXdNcNnv8C0t94Lwe-bl7AhUkL-Fl7dV-qvfi4oVI) and unzip it. by doing so, you will get a folder, inside which the WhiteCLBtool program will be present along with a Libraries folder.

* We will need to install Java SDK version 19 or above, for decompilng the CLB files with the WhiteCLBtool. go to the [Oracle website and download the Java SDK](https://www.oracle.com/in/java/technologies/downloads/) and install it.


## Setting up your code editor

We will require an code editor program to view or write our clb scripts. for this, we can go either with IntelliJ IDEA or VS Code and so depending on your choice, use one of the setup links given below:

* [Setting up IntelliJ](./setup-intellij.md)

* [Setting up VS Code](./setup-vscode.md)

## Generating your codebase

!!! note

    This section assumes that you have already setup the code editor and have unpacked your game files with the Nova Chrysalia mod manager.



* Copy the path of your game folder, for which you want to generate the codebase. example of Final Fantasy XIII's  game folder path on my PC, is given below:
``` 
"S:\Steam Library Games 2\steamapps\common\FINAL FANTASY XIII"
```

* Now open a terminal or command prompt inside the folder where you the WhiteCLBtool program is present and in the terminal, type `WhiteCLBtool -d` along with the path to your game's folder. example with Final Fantasy XIII's game folder path on my PC, is once again given below:
```
WhiteCLBtool -d "S:\Steam Library Games 2\steamapps\common\FINAL FANTASY XIII"
```

* The tool will start converting all the clb files of the game to clean .java files with proper folder structure and then will move the .java files, inside a **clbs_decompiled** folder. this particular folder will be generated inside the game folder and you can move this folder elsewhere and rename it to something more meaningful like **"FFXIIICodebase"** for example.

That's it, the codebase is now ready to be used for reading and writing clb scripts.

You can now proceed to this [page](./repacking.md) to setup the repacking utilities.

For more advanced use-cases, and working with scripts that have a large amount of cross-file references, you may find it easier to work with .class files over the raw .java files, simplifying the recompilation process later on. To unpack all of the clb files as .class files without decompilation, pass the `--class` option to the decompilation command.
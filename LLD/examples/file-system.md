# Problem Statement
Design an in-memory file system to simulate the following functions:

`ls`: Given a path in string format. If it is a file path, return a list that only contains this file’s name. If it is a directory path, return the list of file and directory names in this directory. Your output (file and directory names together) should in lexicographic order.

`mkdir`: Given a directory path that does not exist, you should make a new directory according to the path. If the middle directories in the path don’t exist either, you should create them as well. This function has void return type.

`addContentToFile`: Given a file path and file content in string format. If the file doesn’t exist, you need to create that file containing given content. If the file already exists, you need to append given content to original content. This function has void return type.

`readContentFromFile`: Given a file path, return its content in string format.

# Design
- Here if we closely see file system is recursive structure i.e, One directory can have many files and further many directories. Which leads us to use **Composite Design Pattern**
- The Data Structure will look like maintaining a Trie where each Node will help certain leaf nodes i.e, File and certain non-leaf nodes i.e, Directory.

# Code

```java
package com.lld;

import java.util.HashMap;
import java.util.Map;

public class InMemoryFileSystem {
    FileSystem root;

    public InMemoryFileSystem() {
        root = new Directory("");
    }

    public void ls(String path){
        System.out.println("Listing all FileSystem for path: " + path);
        Directory curr = (Directory) root;
        for(String name: path.split("/")){
            if(name == null || name.isBlank()) continue;
            curr = (Directory) curr.fileSystem.get(name);
        }
        curr.ls();
    }

    public void mkdir(String path){
        Directory curr = (Directory) root;
        for(String name: path.split("/")){
            if(name == null || name.isBlank()) continue;
            if(!curr.fileSystem.containsKey(name)) {
                curr.fileSystem.put(name, new Directory(name));
            }
            curr = (Directory) curr.fileSystem.get(name);
        }
    }

    public void addContentToFile(String path, String content){
        FileSystem curr = root;
        String[] splittedPath = path.split("/");
        String fileName = splittedPath[splittedPath.length-1];
        for(int i=0;i<splittedPath.length-1;i++){
            String name = splittedPath[i];
            if(name == null || name.isBlank()) continue;
            if(!((Directory)curr).fileSystem.containsKey(name)) {
                ((Directory)curr).fileSystem.put(name, new Directory(name));
            }
            curr = ((Directory)curr).fileSystem.get(name);
        }
        if(!((Directory)curr).fileSystem.containsKey(fileName)) {
            ((Directory)curr).fileSystem.put(fileName, new File(fileName));
        }
        File file = (File) ((Directory)curr).fileSystem.get(fileName);
        file.appendContent(content);
    }

    public String readContentToFile(String path){
        FileSystem curr = root;
        String[] splittedPath = path.split("/");
        String fileName = splittedPath[splittedPath.length-1];
        for(int i=0;i<splittedPath.length-1;i++){
            String name = splittedPath[i];
            if(name == null || name.isBlank()) continue;
            curr = ((Directory)curr).fileSystem.get(name);
        }
        File file = (File) ((Directory)curr).fileSystem.get(fileName);
        return file.getContent();
    }
}

abstract class FileSystem {
    String name;

    public FileSystem(String name) {
        this.name = name;
    }

    public void ls(){
        ls("");
    }
    abstract void ls(String prefix);
}
class File extends FileSystem {
    StringBuilder content;

    public File(String name) {
        super(name);
        content = new StringBuilder();
    }

    public void appendContent(String newContent){
        content.append(newContent);
    }

    public String getContent(){
        return content.toString();
    }

    @Override
    public void ls(String prefix) {
        System.out.println(prefix + name);
    }
}
class Directory extends FileSystem {
    Map<String, FileSystem> fileSystem;

    public Directory(String name) {
        super(name);
        this.fileSystem = new HashMap();
    }

    @Override
    public void ls(String prefix) {
        String currentPath = prefix + name + "/";
        System.out.println(currentPath);
        for(FileSystem fs: fileSystem.values()) {
            fs.ls(currentPath);
        }
    }
}

class Test {
    public static void main(String[] args) {
        InMemoryFileSystem fileSystem = new InMemoryFileSystem();
        fileSystem.mkdir("/movies/bollywood");
        fileSystem.mkdir("/movies/hollywood");
        fileSystem.ls("/");
        fileSystem.addContentToFile("/notes/threads.txt","Threads are awesome !!");
        fileSystem.addContentToFile("/notes/threads.txt"," Really ??");
        System.out.println("Reading - /notes/threads.txt\n\t"+fileSystem.readContentToFile("/notes/threads.txt"));
        fileSystem.addContentToFile("/notes/threads.txt"," Yes Boss");
        System.out.println("Reading - /notes/threads.txt\n\t"+fileSystem.readContentToFile("/notes/threads.txt"));
        fileSystem.ls("/movies");
        fileSystem.ls("/");
    }
}

/* OUTPUT:

Listing all FileSystem for path: /
/
/movies/
/movies/hollywood/
/movies/bollywood/
Reading - /notes/threads.txt
	Threads are awesome !! Really ??
Reading - /notes/threads.txt
	Threads are awesome !! Really ?? Yes Boss
Listing all FileSystem for path: /movies
movies/
movies/hollywood/
movies/bollywood/
Listing all FileSystem for path: /
/
/movies/
/movies/hollywood/
/movies/bollywood/
/notes/
/notes/threads.txt

*/

```

# Further Enhancements
- Add `move` method which will move directory from one path to another path.
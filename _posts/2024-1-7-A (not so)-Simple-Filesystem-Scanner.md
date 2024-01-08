---
layout: post
title:  "A (not so) Simple Filesystem Scanner"
date:   2024-1-7 21:09:56 -0600
categories: jekyll update
---

The inspiration behind this project was relatively simple, my buddy was curious where the big files on his computer were. He had 1.5/2TB of storage filled and wondered if there was any leftover junk he could easily get rid of. As an aspiring software engineer, naturally he went: "Where is the Microsoft bloatware on my machine?". So we found out.

```python
import os 

def recursion_step(directory):
    try:
    
        entries = os.listdir(directory)
        
        file_sizes = 0 
        for entry  in entries: 

            entry = os.path.join(directory, entry)

            if os.path.isfile(entry):
                file_sizes += os.path.getsize(entry)
            elif os.path.isdir(entry):
                file_sizes += recursion_step(entry)
        
        depth = directory.count(os.sep)

        message = " "*depth 
        message += " " + directory 

        is_gb = file_sizes / (1024 * 1024 * 1024) > 1

        if is_gb: 
            message += " " + str(file_sizes / (1024 * 1024 * 1024)) + " GB "
        else: 
            message += " " + str(file_sizes) + " B "

        global_entries.append(message)
        return file_sizes
        
    except:
        return -1

def print_relevant(global_entires, threshold=0): 
    for line in global_entires:
        temp = line
        if len(temp) - len(temp.lstrip()) < threshold + 3:
            print(line)


start = "D:\\"

global_entries = []

recursion_step(start)

global_entries.reverse()

print_relevant(global_entries, threshold=0)
```




This small little program scans any path or `start` variable to shows all its children folders with their respective sizes. 

![Folders and their sizes](/images/file_scanner_example.png "Title")



<br> So why did this little script take 4 hours...<br><br> 

 When you want to access a folder or a files's metadata, typically you will access some sort of `os.stat` , `os.path.getsize`. Problem, insert 

>  The 'os.path.getsize' API returns the apparent size of the file at *path*, that is, the number of bytes the file reports itself as consuming. However, it's often useful to get the actual size of the file, or the size of file on disk. It would be helpful if one could get this same information from 'os.path.getsize'. 
        - [Source](https://bugs.python.org/issue41092)

So getting the size knowing the path ends up resulting in SIGNIFICANTLY smaller values than the actual size of the path itself. The object most likely is some object that contains meta information and pointers to the child chunks but not containing the actual values itself. Do I understand why Windows is like this, no, is this already implemented in the Windows Explorer, yes, does this make me want to rm -rf system32, yes. So that approach was a dead end. 

Rant aside, I remembered that some operating systems implement Filesystems as BTrees, but either way Trees are a good mental model for this problem. Files are leaf nodes and Folders are other parent nodes to which it  can have children. So since Windows wouldn't expose this to me, i'll just calculate it myself. With a simple recursive graph algorithm ( I can't be bothered to remember which proper technique name it is) I calculate the size of all the files in the child's folder and I give it to the parent.

After running the script, we found a culprit. Unreal Engine. Precisely, 2 separate versions of Unreal taking up over 150 gigabytes of storage. Microsoft gets to live another day.
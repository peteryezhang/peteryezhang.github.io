---
layout:     post                    # 使用的布局（不需要改）
title:     File and Stream I/O    # 标题 
subtitle:     #副标题
date:       2020-08-14             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - .NET
    
---

# Overview

A file is an ordered and named collection of bytes that has persistent storage.  

In contrast, a stream is a sequence of bytes that you can use to read from and write to a *backing store*, which can be one of several storage mediums (for example, disks or memory).  

*Just as there are several backing stores other than disks, there are several kinds of streams other than file streams, such as network, memory, and pipe streams.*  

## Files and directories

Here are some commonly used file and directory classes:  

+ File - provides **static** methods for creating, copying, deleting, moving, and opening files, and helps create a FileStream object.

+ FileInfo - provides **instance** methods for creating, copying, deleting, moving, and opening files, and helps create a FileStream object.

+ Directory - provides **static** methods for creating, moving, and enumerating through directories and subdirectories.

+ DirectoryInfo - provides **instance** methods for creating, moving, and enumerating through directories and subdirectories.

+ Path - provides methods and properties for processing directory strings in a cross-platform manner.


## Streams

The abstract base class Stream supports reading and writing **bytes**.   

Streams involve three fundamental operations:  

+ Reading - transferring data from a stream into a data structure, such as an array of bytes.

+ Writing - transferring data to a stream from a data source.

+ Seeking - querying and modifying the current position within a stream.

Here are some commonly used stream classes:  

+ `FileStream` - for reading and writing to a file.
+ `MemoryStream` - for reading and writing to memory as the backing store.
+ `BufferedStream` - for improving performance of read and write operations.
+ `NetworkStream` - for reading and writing over network sockets.
+ `PipeStream` - for reading and writing over anonymous and named pipes.
+ `CrytoStream` - for linking data streams to cryptographic transformations.

## Readers and writers

+ BinaryReader and BinaryWriter – for reading and writing primitive data types as binary values.

+ StreamReader and StreamWriter – for reading and writing **characters** by using an encoding value to convert the characters to and from bytes.

+ StringReader and StringWriter – for reading and writing characters to and from strings.

+ TextReader and TextWriter – **serve as the abstract base classes** for other readers and writers that read and write characters and strings, but not binary data.

## Common I/O Tasks

### Common File Tasks

#### Create a text file	

+ `File.Create`

#### Write to a text file

+ `StreamWriter`

```
    using (StreamWriter outputFile = new StreamWriter(Path.Combine(docPath, "WriteTextAsync.txt")))
    {
        await outputFile.WriteAsync("This is a sentence.");
    }
```

+ `File class`

```
    // Write the text to a new file named "WriteFile.txt".
    File.WriteAllText(Path.Combine(docPath, "WriteFile.txt"), text);

    // Append new lines of text to the file
    File.AppendAllLines(Path.Combine(docPath, "WriteFile.txt"), lines);
```

#### Read from a text file	

```
    using (var sr = new StreamReader("TestFile.txt"))
    {
        ResultBlock.Text = await sr.ReadToEndAsync();
    }
```

## Asynchronous File I/O

Asynchronous operations enable you to perform resource-intensive I/O operations without blocking the main thread.   

```
private async void Button_Click(object sender, RoutedEventArgs e)
{
    string UserDirectory = @"c:\Users\exampleuser\";

    using (StreamReader SourceReader = File.OpenText(UserDirectory + "BigFile.txt"))
    {
        using (StreamWriter DestinationWriter = File.CreateText(UserDirectory + "CopiedFile.txt"))
        {
            await CopyFilesAsync(SourceReader, DestinationWriter);
        }
    }
}

public async Task CopyFilesAsync(StreamReader Source, StreamWriter Destination)
{
    char[] buffer = new char[0x1000];
    int numRead;
    while ((numRead = await Source.ReadAsync(buffer, 0, buffer.Length)) != 0)
    {
        await Destination.WriteAsync(buffer, 0, numRead);
    }
}
```

## Handling I/O errors in .NET

+ System.IO.IOException, the base class of all System.IO exception types. It is thrown for errors whose return codes from the operating system don't directly map to any other exception type.
+ System.IO.FileNotFoundException.
+ System.IO.DirectoryNotFoundException.
+ DriveNotFoundException.
+ System.IO.PathTooLongException.
+ System.OperationCanceledException.
+ System.UnauthorizedAccessException.
+ System.ArgumentException, which is thrown for invalid path characters on .NET Framework and on .NET Core 2.0 and previous versions.
+ System.NotSupportedException, which is thrown for invalid colons in .NET Framework.
+ System.Security.SecurityException, which is thrown for applications running in limited trust that lack the necessary permissions on .NET Framework only. (Full trust is the default on .NET Framework.)



## Reference

1. [About Processes and Threads](https://docs.microsoft.com/en-us/windows/win32/procthread/about-processes-and-threads)


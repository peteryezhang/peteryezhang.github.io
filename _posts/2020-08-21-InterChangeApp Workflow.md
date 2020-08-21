---
layout:     post                    # 使用的布局（不需要改）
title:     InterChangeApp Workflow    # 标题 
subtitle:     #副标题
date:       2020-08-21             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - .NET
    
---
## Inbound梗概

**IP/BPMO XML to datahub**

1. IP prepareInbound

+ download xml package from S3 bucket


```
    Dictionary<string, string> customerFilesToProcess = FileHandler.GetInboundFiles(folderPath, jobId, sourceSystem, true);

```
```
    IEnumerable<string> fileEntries = Directory.EnumerateFiles(folderPath);


    Parallel.ForEach(
        ()=>
        {
            MoveFileFolder(folderBasePath, Constants.FOLDERS.IN, Constants.FOLDERS.ERROR, fileName, Path.GetExtension(file));

        }
    )
```
+ Create Inbound Packets


Case Status: Pending 
```
FilePath

    Parallel.ForEach(
        ()=>
        {
            List<CasePacketTransfer> newCases = new List<CasePacketTransfer>();

            newCases.Add(CreateSingleInboundPacket(cust.CaseNumber, cust.CustomerNumber, cust.ReviewNumber, cust.Documents, filePath, pendingStatusId,
        }
    )

```

+ Save changes

```
unitOfWork.Save();

context.SaveChanges();
```


2. IP execute inbound

+ Get files by jobId and status

+ Move files from In folder to Processing

```
    filePath = FileHandler.MoveFileFolderSourceSystemDocPath(folderPath, Constants.FOLDERS.IN, Constants.FOLDERS.PROCESSING, filePath, sourceSystem);


    if (File.Exists(file))
    {
        string movepath = Path.Combine(destinationFolderPath, documentName + extension);
        if (!File.Exists(movepath))
        {
            File.Move(file, movepath);
        }
    }

```

+ Get Xml stream

```
        public static Stream getXMLStream(string filePath)
        {
            MemoryStream result = new MemoryStream();
            byte[] bytes = null;
            using (var zip = ZipFile.Open(filePath, ZipArchiveMode.Read))
            {
                foreach (ZipArchiveEntry entry in zip.Entries)
                {
                    if (Path.GetExtension(entry.Name) == ".xml")
                    {
                        MemoryStream tempStream = new MemoryStream();
                        entry.Open().CopyTo(tempStream);
                        bytes = tempStream.ToArray();
                        break;
                    } 
                }
            }
            return new MemoryStream(bytes);
        }
```  

+ XML stream to object

```
BPMOXSD.kisRootComplexType xsdRoot = (BPMOXSD.kisRootComplexType)IPFileHandler.readFromStream(xmlStream, typeof(BPMOXSD.kisRootComplexType));

public static Object readFromStream(Stream stream, Type objType)
{
    XmlSerializer xSerializer = new XmlSerializer(objType);
    Object result = xSerializer.Deserialize(stream);
    return result;
}
```

1. **克隆过程中的各个阶段**:

   - `Cloning into 'name'...`：开始克隆过程，创建一个名为 `name` 的本地目录。
   - `remote: Enumerating objects: n, done.`：服务器正在列举仓库中的所有对象（如提交、文件、文件夹等），总共有 n 个对象。
   - `remote: Counting objects: 100% (n/n), done.`：完成对对象的计数，共计 n 个对象。
   - `remote: Compressing objects: 100% (n/n), done.`：服务器正在压缩这些对象，以减少网络传输所需的数据量。
   - `remote: Total n (delta n), reused n (delta n), pack-reused n`：显示了总对象数、复用对象数和需要下载的对象数。
   - `Receiving objects: 100% (n/n), n MiB | n MiB/s, done.`：显示了已接收的对象百分比和总大小，以及下载速率。
   - `Resolving deltas: 100% (n/n), done.`：解析了所有的差异数据（deltas），这是 Git 优化存储和传输所需数据的方式。

2. **警告信息**:

   - `warning: the following paths have collided...`：这是一个警告，表明在仓库中有两个文件名在不区分大小写的文件系统（如 Windows）中发生了冲突。具体来说，文件 `public/lib/imgs/FASTPI.png` 和 `public/lib/imgs/FastPI.png` 在不区分大小写的文件系统中会被视为相同的文件名。然而，在区分大小写的文件系统（如 Linux）中，它们被视为不同的文件。

###### 底层原理

- Git 使用对象来表示数据，如提交、树（文件夹）和 blob（文件内容）。每个对象都有一个唯一的 SHA-1 哈希作为其标识。
- 差异数据（deltas）是 Git 存储历史更改的一种方式。通过记录文件之间的差异，而不是存储每个文件的完整副本，Git 能有效减少存储空间的需求。
- 当在一个不区分大小写的文件系统中工作时，Git 会警告可能的文件名冲突。这是因为在这样的文件系统中，`FASTPI.png`和`FastPI.png`会被视为相同的文件，而在区分大小写的文件系统中则不会。这可能导致在不同系统间协作时出现问题。

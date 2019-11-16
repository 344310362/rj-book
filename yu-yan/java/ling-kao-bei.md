## 引言

> 应用程序读取磁盘文件的过程中，是需要调用操作系统指令，让操作系统先从磁盘读区到内存，然后再从内存拷贝到应用程序，所谓的零拷贝技术就是**避免**CPU将数据从一块存储拷贝到另外一块存储，让CPU解脱出来专注于别的任务。

![](/assets/272719-9b800f62a9c0e47d.png)

## 零拷贝的实现

零拷贝实际的实现并没有真正的标准，取决于操作系统如何实现这一点。零拷贝完全依赖于操作系统。操作系统支持，就有；不支持，就没有。不依赖Java本身。

### 传统IO

```
read(file,tmp_buf,len)
write(socket,tmp_buf,len)
```

1. 程序使用read\(\)系统调用。系统由用户态转换为内核态\(第一次上线文切换\)，磁盘中的数据有DMA（Direct Memory Access\)的方式读取到内核缓冲区\(kernel buffer\)。DMA过程中CPU不需要参与数据的读写，而是DMA处理器直接将硬盘数据通过总线传输到内存中。
2. 系统由内核态转换为用户态（第二次上下文切换），当程序要读取的数据已经完成写入内核缓冲区以后，程序会将数据由内核缓存区，写入用户缓存区），这个过程需要CPU参与数据的读写。
3. 程序使用write\(\)系统调用。系统由用户态切换到内核态\(第三次上下文切换\)，数据从用户态缓冲区写入到网络缓冲区\(Socket Buffer\)，这个过程需要CPU参与数据的读写。
4. 系统由内核态切换到用户态（第四次上下文切换），网络缓冲区的数据通过DMA的方式传输到网卡的驱动\(存储缓冲区\)中\(protocol engine\)

可以看到，传统的I/O方式会经过4次用户态和内核态的切换\(上下文切换\)，两次CPU中内存中进行数据读写的过程。这种拷贝过程相对来说比较消耗资源。

### 内存映射方式I/O

```
tmp_buf = mmap(file, len);
write(socket, tmp_buf, len);
```

这是使用的系统调用方法，这种方式的I/O原理就是将用户缓冲区（user buffer）的内存地址和内核缓冲区（kernel buffer）的内存地址做一个映射，也就是说系统在用户态可以直接读取并操作内核空间的数据。

1. mmap\(\)系统调用首先会使用DMA的方式将磁盘数据读取到内核缓冲区，然后通过内存映射的方式，使用户缓冲区和内核读缓冲区的内存地址为同一内存地址，也就是说不需要CPU再讲数据从内核读缓冲区复制到用户缓冲区。
2. 当使用write\(\)系统调用的时候，cpu将内核缓冲区（等同于用户缓冲区）的数据直接写入到网络发送缓冲区（socket buffer），然后通过DMA的方式将数据传入到网卡驱动程序中准备发送。

这种方式虽然减少了cpu拷贝的次数，但是用户态和内核态的切换依旧有四次，同时需要注意在进行这种内存映射的时候，有可能会出现并发线程操作同一块内存区域而导致的严重的数据不一致问题，所以需要进行合理的并发编程来解决这些问题。

### 其它

要想做到完全零拷贝，需要硬件的配合支持。

## Java的实现

### NIO的零拷贝

```
  File file = new File("test.zip");
  RandomAccessFile raf = new RandomAccessFile(file, "rw");
  FileChannel fileChannel = raf.getChannel();
  SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("", 1234));
  // 直接使用了transferTo()进行通道间的数据传输
  fileChannel.transferTo(0, fileChannel.size(), socketChannel);
```

NIO的零拷贝由transferTo\(\)方法实现。transferTo\(\)方法将数据从FileChannel对象传送到可写的字节通道（如Socket Channel等）。在内部实现中，由native方法**transferTo0**\(\)来实现，它依赖底层操作系统的支持。在UNIX和Linux系统中，调用这个方法将会引起sendfile\(\)系统调用。

使用场景一般是：

* 较大，读写较慢，追求速度
* 内存不足，不能加载太大数据
* 这种方式只适用对文件的简单搬移，不能对文件进行操作。

### NIO的直接内存

处于传统IO（BIO）与零拷贝之间

```
  File file = new File("test.zip");
  RandomAccessFile raf = new RandomAccessFile(file, "rw");
  FileChannel fileChannel = raf.getChannel();
  MappedByteBuffer buffer = fileChannel.map(FileChannel.MapMode.READ_ONLY, 0, fileChannel.size());
```

* IO，可以把磁盘的文件经过内核空间，读到JVM空间，然后进行各种操作，最后再写到磁盘或是发送到网络，效率较慢但支持数据文件操作。
* 零拷贝则是直接在内核空间完成文件读取并转到磁盘（或发送到网络）。由于它没有读取文件数据到JVM这一环，因此程序无法操作该文件数据，尽管效率很高！

而直接内存则介于两者之间，效率一般且可操作文件数据。直接内存（mmap技术）将文件直接映射到内核空间的内存，返回==一个操作地址（address）==，它解决了文件数据需要拷贝到JVM才能进行操作的窘境。而是直接在内核空间直接进行操作，省去了内核空间拷贝到用户空间这一步操作。

NIO的直接内存是由**MappedByteBuffer**实现的。核心即是map\(\)方法，该方法把文件映射到内存中，获得内存地址addr，然后通过这个addr构造MappedByteBuffer类，以暴露各种文件操作API。

由于MappedByteBuffer申请的是堆外内存，因此不受Minor GC控制，只能在发生Full GC时才能被回收。而**DirectByteBuffer**改善了这一情况，它是MappedByteBuffer类的子类，同时它实现了DirectBuffer接口，维护一个Cleaner对象来完成内存回收。因此它既可以通过Full GC来回收内存，也可以调用clean\(\)方法来进行回收。

另外，直接内存的大小可通过jvm参数来设置：-XX:MaxDirectMemorySize。

NIO的MappedByteBuffer还有一个兄弟叫做HeapByteBuffer。顾名思义，它用来在堆中申请内存，本质是一个数组。由于它位于堆中，因此可受GC管控，易于回收。




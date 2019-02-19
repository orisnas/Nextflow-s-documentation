.. _script-page:

******************
Nextflow 脚本语言
******************


Nextflow脚本语言是Groovy编程语言的扩展. Groovy是一种用于Java虚拟机的强大编程语言. Nextflow语法被专门用于简化以声明方式编写计算管道.

Nextflow可以执行任何一段Groovy代码或使用任何用于JVM平台的库.

有关Groovy编程语言的详细描述，请参考这些链接:

* `Groovy User Guide <http://groovy-lang.org/documentation.html>`_
* `Groovy Cheat sheet <http://refcardz.dzone.com/refcardz/groovy>`_
* `Groovy in Action <http://www.manning.com/koenig2/>`_


下面您可以找到Nextflow脚本语言中使用的最重要的语言构造的速成课程.

.. warning:: 
    Nextflow使用 ``UTF-8`` 作为源文件和应用程序文件的默认文件字符编码。使用您最喜欢的文本编辑器编辑Nextflow脚本时，请确保使用 ``UTF-8`` 编码.

语言基础知识
==================


Hello world
------------

打印一些东西可以简单的使用 ``print`` 或 ``println`` 方法之一。

::

    println "Hello, World!"

两者之间的唯一区别是println方法隐式地向打印的字符串追加一个`new line` character.


变量
----------

要定义一个变量，只需给它赋一个值::

    x = 1
    println x

    x = new java.util.Date()
    println x

    x = -3.1499392
    println x

    x = false
    println x

    x = "Hi"
    println x


列表
------

列表对象可以通过将列表项放在方括号中来定义::

    myList = [1776, -1, 33, 99, 0, 928734928763]

您可以使用方括号符号访问列表中的给定项(索引从0开始)::

    println myList[0]

想要获得列表的长度，可以使用size方法::

    println myList.size()


了解更多关于列表的信息:

* `Groovy Lists tutorial <http://groovy-lang.org/groovy-dev-kit.html#Collections-Lists>`_
* `Groovy List SDK <http://docs.groovy-lang.org/latest/html/groovy-jdk/java/util/List.html>`_
* `Java List SDK <http://docs.oracle.com/javase/7/docs/api/java/util/List.html>`_


映射
-----

映射用于存储关联数组或字典. 它们是异构的、命名的数据的无序集合::

    scores = [ "Brett":100, "Pete":"Did not finish", "Andrew":86.87934 ]


请注意，映射中存储的每个值都可以是不同的类型. ``Brett`` 是整数, ``Pete`` 是字符串,而
 ``Andrew`` 是浮点数.

我们可以通过两种主要方式访问映射中的值::

    println scores["Pete"]
    println scores.Pete


要向映射添加数据或修改映射，语法类似于向list添加值::

    scores["Pete"] = 3
    scores["Cedric"] = 120


了解更多关于映射:

* `Groovy Maps tutorial <http://groovy-lang.org/groovy-dev-kit.html#Collections-Maps>`_
* `Groovy Map SDK <http://docs.groovy-lang.org/latest/html/groovy-jdk/java/util/Map.html>`_
* `Java Map SDK <http://docs.oracle.com/javase/7/docs/api/java/util/Map.html>`_


.. _script-multiple-assignment:

多重赋值
----------------------

数组或列表对象可用于一次分配多个变量::

    (a, b, c) = [10, 20, 'foo']
    assert a == 10 && b == 20 && c == 'foo'

赋值运算符左侧的三个变量由列表中相应的项初始化.

Read more about `Multiple assignment <http://www.groovy-lang.org/semantics.html#_multiple_assignment>`_ in the Groovy documentation.


条件执行
----------------------

任何编程语言最重要的特性之一都是能够在不同的条件下执行不同的代码. 最简单的方法是使用if构造::

    x = Math.random()
    if( x < 0.5 ) {
        println "You lost."
    }
    else {
        println "You won!"
    }



字符串
-------

字符串可以定义为用单引号或双引号( ``'`` 或 ``"`` 字符)包围的文本::

    println "he said 'cheese' once"
    println 'he said "cheese!" again'


字符串可以用 ``+`` 连接::

    a = "world"
    print "hello " + a + "\n"


.. _string-interpolation:

字符串插值
--------------------

单引号和双引号字符串之间有一个重要的区别: 双引号字符串支持变量插值，而单引号字符串不支持.

实际上，双引号字符串可以包含任意变量的值，方法是在其名称前面加上 ``$`` 字符，或者使用 ``${expression}`` 语法(类似于Bash/shell脚本)来包含任何表达式的值::

    foxtype = 'quick'
    foxcolor = ['b', 'r', 'o', 'w', 'n']
    println "The $foxtype ${foxcolor.join()} fox"

    x = 'Hello'
    println '$x + $y'

这段代码打印::

    The quick brown fox
    $x + $y


多行字符串
-------------------

可以通过使用三重单引号或双引号来分隔跨越多行的文本块::

    text = """
        hello there James
        how are you today?
        """

.. note:: 像前面一样，双引号内的多行字符串支持变量插值，而单引号内的多行字符串不支持.


和在Bash/shell脚本中一样，用 ``\`` 字符在多行字符串中结束一行可以防止新行字符将该行与下面的行分隔开来::

    myLongCmdline = """ blastp \
                    -in $input_query \
                    -out $output_file \
                    -db $blast_database \
                    -html
                    """

    result = myLongCmdline.execute().text

在前面的示例中, ``blastp`` 及其 ``In`` 、 ``out`` 、 ``-db`` 和 ``-html`` 开关及其参数实际上是一行.


.. _script-closure:

闭包
=========

简单地说,一个闭包就是一个代码块,可以作为参数传递给一个函数。因此，您可以定义一段代码，然后像传递字符串或整数一样传递它。

更正式地说，您可以创建定义为第一类对象的函数.

::

    square = { it * it }


表达式 ``it * it`` 周围的大括号告诉脚本解释器将该表达式视为代码. ``it`` 标识符是一个隐式变量，表示在调用函数时传递给函数的值.

一旦编译了函数对象，就会被赋值给变量 ``square`` ，就像之前的其他变量赋值一样。现在我们可以这样做::

    println square(9)

并得到值81.


这并不十分有趣，直到我们发现可以将函数 ``square`` 作为参数传递给其他函数或方法. 有一些内建函数就将像这样的函数作为参数. 列表上的 ``collect`` 方法就是一个例子::

    [ 1, 2, 3, 4 ].collect(square)


这个表达式表示:创建一个值为1、2、3和4的数组，然后调用它的 ``collect`` 方法，传入我们在上面定义的闭包. ``collect`` 方法遍历数组中的每个项，调用该项的闭包，然后将结果放入一个新数组中，结果为::

    [ 1, 4, 9, 16 ]


有关更多可以调用“闭包”作为参数的方法，请参见 `Groovy GDK 文档 <http://docs.groovy-lang.org/latest/html/groovy-jdk/>`_.


默认情况下，闭包接受一个名为 ``it`` 的参数，但是您也可以使用多个自定义命名的参数创建闭包. 例如， ``Map.each()`` 方法可以接受一个带有两个参数的闭包，它将键和映射中每个键-值对的关联值绑定到该闭包. 这里，我们在闭包中使用了明显的变量名 ``key`` 和 ``value`` ::


    printMapClosure = { key, value ->
        println "$key = $value"
    }

    [ "Yue" : "Wu", "Mark" : "Williams", "Sudha" : "Kumari" ].each(printMapClosure)


Prints::


    Yue=Wu
    Mark=Williams
    Sudha=Kumari


闭包还有另外两个重要的特性。首先，它可以访问定义它的作用域中的变量，以便与它们交互。


其次，可以以匿名的方式定义闭包，这意味着它没有给定名称，而是在需要使用它的地方定义.

作为展示这两个特性的示例，请参见下面的代码片段::

    myMap = ["China": 1 , "India" : 2, "USA" : 3]

    result = 0
    myMap.keySet().each( { result+= myMap[it] } )

    println result


Learn more about closures in the `Groovy documentation <http://groovy-lang.org/closures.html>`_

.. _script-regexp:

正则表达式
====================

正则表达式是文本处理的瑞士军刀。它们为程序员提供了从字符串中匹配和提取模式的能力. 

正则表达式可以通过 ``~/pattern/`` 语法 和  ``=~`` , ``==~`` 操作符使用.

用 ``=~`` 来检查给定的模式是否出现在字符串中::

    assert 'foo' =~ /foo/       // return TRUE
    assert 'foobar' =~ /foo/    // return TRUE


使用 ``==~`` 检查字符串是否与给定的正则表达式模式完全匹配.  

::

    assert 'foo' ==~ /foo/       // return TRUE
    assert 'foobar' ==~ /foo/    // return FALSE


值得注意的是， ``~`` 操作符从给定的字符串创建Java ``Pattern`` 对象，而 ``=~`` 操作符创建Java ``Matcher`` 对象.

::

    x = ~/abc/
    println x.class
    // prints java.util.regex.Pattern

    y = 'some string' =~ /abc/
    println y.class
    // prints java.util.regex.Matcher


正则表达式支持是从Java导入的. Java正则表达式语言和API被记录在
`Pattern Java 文档 <http://download.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html>`_ 中.

你也可能对这篇文章感兴趣: `Groovy: Don't Fear the RegExp <https://web.archive.org/web/20170621185113/http://www.naleid.com/blog/2008/05/19/dont-fear-the-regexp>`_.


字符串替换
--------------------

要替换给定字符串中出现的模式，请使用 ``replaceFirst`` 和 ``replaceAll`` 方法::

     x = "colour".replaceFirst(/ou/, "o")
     println x
     // prints: color

     y = "cheesecheese".replaceAll(/cheese/, "nice")
     println y
     // prints: nicenice



捕获组
----------------

您可以匹配包含组的模式. 首先使用 ``=~`` 运算符创建一个matcher对象。然后，您可以索引matcher对象以查找匹配项: ``matcher[0]`` 返回一个列表，该列表表示字符串中正则表达式的第一个匹配项。第一个列表元素是匹配整个正则表达式的字符串，其余元素是匹配每个组的字符串。

它是这样工作的::

    programVersion = '2.7.3-beta'
    m = programVersion =~ /(\d+)\.(\d+)\.(\d+)-?(.+)/

    assert m[0] ==  ['2.7.3-beta', '2', '7', '3', 'beta']
    assert m[0][1] == '2'
    assert m[0][2] == '7'
    assert m[0][3] == '3'
    assert m[0][4] == 'beta'


应用一些语法糖，你可以在一行代码中做同样的事情::

    programVersion = '2.7.3-beta'
    (full, major, minor, patch, flavor) = (programVersion =~ /(\d+)\.(\d+)\.(\d+)-?(.+)/)[0]

    println full    // 2.7.3-beta
    println major   // 2
    println minor   // 7
    println patch   // 3
    println flavor  // beta


删除字符串的一部分
-------------------------

可以使用正则表达式模式删除字符串值的一部分。找到的第一个匹配项替换为空字符串::

    // define the regexp pattern
    wordStartsWithGr = ~/(?i)\s+Gr\w+/

    // apply and verify the result
    ('Hello Groovy world!' - wordStartsWithGr) == 'Hello world!'
    ('Hi Grails users' - wordStartsWithGr) == 'Hi users'


从字符串中删除第一个5字符的单词::

    assert ('Remove first match of 5 letter word' - ~/\b\w{5}\b/) == 'Remove  match of 5 letter word'


从字符串中删除第一个数字及其后面的空格::

    assert ('Line contains 20 characters' - ~/\d+\s+/) == 'Line contains characters'



.. _script-file-io:

文件 和 I/O
==============

要访问和处理文件，使用 ``file`` 方法，返回一个文件系统对象，对象是文件路径字符串::

  myFile = file('some/path/to/my_file.file')


 ``file`` 方法可以引用文件或者目录，这取决于在文件系统中字符串路径指的是什么。

当使用通配符 ``*`` 、 ``?`` 、 ``[]`` 和 ``{}`` 时，参数被解释为 `glob`_ 路径匹配器， ``file`` 方法返回一个列表对象，其中包含名称与指定模式匹配的文件的路径，如果没有找到匹配，则返回一个空列表::

  listOfFiles = file('some/path/*.fa')

.. note:: glob模式中的两个星号( ``**`` )类似于 ``*`` ，但是匹配文件系统路径中的任意数量的目录。


默认情况下，通配符不匹配目录或隐藏文件。例如，如果希望在结果列表中包含隐藏文件，可以添加可选参数 ``hidden`` ::

  listWithHidden = file('some/path/*.fa', hidden: true)

以下是文件的可用选项:

=============== ===================
Name            Description
=============== ===================
glob            When ``true`` interprets characters ``*``, ``?``, ``[]`` and ``{}`` as glob wildcards, otherwise handles them as normal characters (default: ``true``)
type            Type of paths returned, either ``file``, ``dir`` or ``any`` (default: ``file``)
hidden          When ``true`` includes hidden files in the resulting paths (default: ``false``)
maxDepth        Maximum number of directory levels to visit (default: `no limit`)
followLinks     When ``true`` follows symbolic links during directory tree traversal, otherwise treats them as files (default: ``true``)
checkIfExists   When ``true`` throws an exception of the specified path do not exist in the file system (default: ``false``)
=============== ===================


.. tip:: 如果您是一个Java极客，您可能会有兴趣知道 ``file`` 方法返回一个`Path <http://docs.oracle.com/javase/7/docs/api/java/nio/file/Path.html>`_ 对象，它允许您使用Java程序中通常使用的方法。


See also: :ref:`Channel.fromPath <channel-path>`.

.. _glob: http://docs.oracle.com/javase/tutorial/essential/io/fileOps.html#glob

基本的读/写
------------------

给定一个文件变量，使用前面示例中所示的 ``file`` 方法声明，读取文件可以非常简单的获取文件的 ``text`` 属性的值，这将返回文件内容作为字符串值::

  print myFile.text


类似地，您可以通过简单地将字符串值赋值给文件的 ``text`` 属性来将其保存到文件中:: 

  myFile.text = 'Hello world!'


.. note::  已存在的文件内容被赋值操作覆盖，该操作还隐式地创建不存在的文件。

为了在不删除现有内容的情况下向文件追加字符串值，可以使用append方法::

  myFile.append('Add this line\n')


或使用 ``<<`` 运算符, 这是一种更惯用的方法, 将文本内容附加到一个文件::

  myFile << 'Add a line more\n'


二进制数据也可以用同样的方式管理，只是使用文件属性 ``bytes`` 而不是 ``text`` 。因此，以下示例读取文件并将它的内容作为字节数组返回::

  binaryContent = myFile.bytes

或者你可以将一个字节数组数据缓冲区保存到一个文件中,通过简单地写::

  myFile.bytes = binaryBuffer


.. warning::  上述方法在单个变量或缓冲器中立即读取和写入所有文件内容。因此，在处理大文件时不建议使用它们，因为大文件需要更多的内存高效的方法，例如逐行读取文件或使用固定大小的缓冲区。


逐行读取文件
--------------------------

In order to read a text file line by line you can use the method ``readLines()`` provided by the file object, which
returns the file content as a list of strings::

    myFile = file('some/my_file.txt')
    allLines  = myFile.readLines()
    for( line : allLines ) {
        println line
    }


This can also be written in a more idiomatic syntax::

    file('some/my_file.txt')
        .readLines()
        .each { println it }


.. note:: The method ``readLines()`` reads all the file content at once and returns a list containing all the lines. For
  this reason, do not use it to read big files.


To process a big file, use the method ``eachLine``, which reads only a single line at a time into memory::

    count = 0
    myFile.eachLine {  str ->
            println "line ${count++}: $str"
        }



Advanced file reading operations
-----------------------------------

The classes ``Reader`` and ``InputStream`` provide fine control for reading text and binary files, respectively._


The method ``newReader`` creates a `Reader <http://docs.oracle.com/javase/7/docs/api/java/io/Reader.html>`_ object
for the given file that allows you to read the content as single characters, lines or arrays of characters::

    myReader = myFile.newReader()
    String line
    while( line = myReader.readLine() ) {
        println line
    }
    myReader.close()


The method ``withReader`` works similarly, but automatically calls the ``close`` method for you when you have finished
processing the file. So, the previous example can be written more simply as::

    myFile.withReader {
        String line
        while( line = myReader.readLine() ) {
            println line
        }
    }

The methods ``newInputStream`` and ``withInputStream`` work similarly. The main difference is that they create an
`InputStream <http://docs.oracle.com/javase/7/docs/api/java/io/InputStream.html>`_ object useful for writing binary
data.

Here are the most important methods for reading from files:

=============== ==============
Name            Description
=============== ==============
getText         Returns the file content as a string value
getBytes        Returns the file content as byte array
readLines       Reads the file line by line and returns the content as a list of strings
eachLine        Iterates over the file line by line, applying the specified :ref:`closure <script-closure>`
eachByte        Iterates over the file byte by byte, applying the specified :ref:`closure <script-closure>`
withReader      Opens a file for reading and lets you access it with a `Reader <http://docs.oracle.com/javase/7/docs/api/java/io/Reader.html>`_ object
withInputStream Opens a file for reading and lets you access it with an `InputStream <http://docs.oracle.com/javase/7/docs/api/java/io/InputStream.html>`_ object
newReader       Returns a `Reader <http://docs.oracle.com/javase/7/docs/api/java/io/Reader.html>`_ object to read a text file
newInputStream  Returns an `InputStream <http://docs.oracle.com/javase/7/docs/api/java/io/InputStream.html>`_ object to read a binary file
=============== ==============


Read the Java documentation for `Reader <http://docs.oracle.com/javase/7/docs/api/java/io/Reader.html>`_ and
`InputStream <http://docs.oracle.com/javase/7/docs/api/java/io/InputStream.html>`_ classes to learn more about
methods available for reading data from files.


Advanced file writing operations
----------------------------------

The ``Writer`` and ``OutputStream`` classes provide fine control for writing text and binary files,
respectively, including low-level operations for single characters or bytes, and support for big files.

For example, given two file objects ``sourceFile`` and ``targetFile``, the following code copies the
first file's content into the second file, replacing all ``U`` characters with ``X``::

    sourceFile.withReader { source ->
        targetFile.withWriter { target ->
            String line
            while( line=source.readLine() ) {
                target << line.replaceAll('U','X')
            }
        }
    }


Here are the most important methods for writing to files:

=================== ==============
Name                Description
=================== ==============
setText             Writes a string value to a file
setBytes            Writes a byte array to a file
write               Writes a string to a file, replacing any existing content
append              Appends a string value to a file without replacing existing content
newWriter           Creates a `Writer <http://docs.oracle.com/javase/7/docs/api/java/io/Writer.html>`_ object that allows you to save text data to a file
newPrintWriter      Creates a `PrintWriter <http://docs.oracle.com/javase/7/docs/api/java/io/PrintWriter.html>`_ object that allows you to write formatted text to a file
newOutputStream     Creates an `OutputStream <http://docs.oracle.com/javase/7/docs/api/java/io/OutputStream.html>`_ object that allows you to write binary data to a file
withWriter          Applies the specified closure to a `Writer <http://docs.oracle.com/javase/7/docs/api/java/io/Writer.html>`_ object, closing it when finished
withPrintWriter     Applies the specified closure to a `PrintWriter <http://docs.oracle.com/javase/7/docs/api/java/io/PrintWriter.html>`_ object, closing it when finished
withOutputStream    Applies the specified closure to an `OutputStream <http://docs.oracle.com/javase/7/docs/api/java/io/OutputStream.html>`_ object, closing it when finished
=================== ==============

Read the Java documentation for the `Writer <http://docs.oracle.com/javase/7/docs/api/java/io/Writer.html>`_,
`PrintWriter <http://docs.oracle.com/javase/7/docs/api/java/io/PrintWriter.html>`_ and
`OutputStream <http://docs.oracle.com/javase/7/docs/api/java/io/OutputStream.html>`_ classes to learn more about
methods available for writing data to files.


List directory content
----------------------

Let's assume that you need to walk through a directory of your choice. You can define the ``myDir`` variable
that points to it::

    myDir = file('any/path')

The simplest way to get a directory list is by using the methods ``list`` or ``listFiles``,
which return a collection of first-level elements (files and directories) of a directory::

    allFiles = myDir.list()
    for( def file : allFiles ) {
        println file
    }

.. note:: The only difference between ``list`` and ``listFiles`` is that the former returns a list of strings, and the latter a
   list of file objects that allow you to access file metadata, e.g. size, last modified time, etc.


The ``eachFile`` method allows you to iterate through the first-level elements only
(just like ``listFiles``). As with other `each-` methods, ``eachFiles`` takes a closure as a parameter::

    myDir.eachFile { item ->
        if( item.isFile() ) {
            println "${item.getName()} - size: ${item.size()}"
        }
        else if( item.isDirectory() ) {
            println "${item.getName()} - DIR"
        }
    }


Several variants of the above method are available. See the table below for a complete list.

=================== ==================
Name                Description
=================== ==================
eachFile            Iterates through first-level elements (files and directories). `Read more <http://docs.groovy-lang.org/latest/html/groovy-jdk/java/io/File.html#eachFile(groovy.io.FileType,%20groovy.lang.Closure)>`_
eachDir             Iterates through first-level directories only. `Read more <http://docs.groovy-lang.org/latest/html/groovy-jdk/java/io/File.html#eachDir(groovy.lang.Closure)>`_
eachFileMatch       Iterates through files and dirs whose names match the given filter. `Read more <http://docs.groovy-lang.org/latest/html/groovy-jdk/java/io/File.html#eachFileMatch(java.lang.Object,%20groovy.lang.Closure)>`_
eachDirMatch        Iterates through directories whose names match the given filter. `Read more <http://docs.groovy-lang.org/latest/html/groovy-jdk/java/io/File.html#eachDirMatch(java.lang.Object,%20groovy.lang.Closure)>`_
eachFileRecurse     Iterates through directory elements depth-first. `Read more <http://docs.groovy-lang.org/latest/html/groovy-jdk/java/io/File.html#eachFileRecurse(groovy.lang.Closure)>`_
eachDirRecurse      Iterates through directories depth-first (regular files are ignored). `Read more <http://docs.groovy-lang.org/latest/html/groovy-jdk/java/io/File.html#eachDirRecurse(groovy.lang.Closure)>`_
=================== ==================


See also: Channel :ref:`channel-path` method.


Create directories
------------------

Given a file variable representing a nonexistent directory, like the following::

  myDir = file('any/path')

the method ``mkdir`` creates a directory at the given path, returning ``true`` if the directory is created
successfully, and ``false`` otherwise::

   result = myDir.mkdir()
   println result ? "OK" : "Cannot create directory: $myDir"

.. note:: If the parent directories do not exist, the above method will fail and return ``false``.

The method ``mkdirs`` creates the directory named by the file object, including any nonexistent parent directories::

    myDir.mkdirs()


Create links
------------

Given a file, the method ``mklink`` creates a *file system link* for that file using the path specified as a parameter::

  myFile = file('/some/path/file.txt')
  myFile.mklink('/user/name/link-to-file.txt')


Table of optional parameters:

==================  ================
Name                Description
==================  ================
hard                When ``true`` creates a *hard* link, otherwise creates a *soft* (aka *symbolic*) link. (default: ``false``)
overwrite           When ``true`` overwrites any existing file with the same name, otherwise throws a `FileAlreadyExistsException <http://docs.oracle.com/javase/8/docs/api/java/nio/file/FileAlreadyExistsException.html>`_ (default: ``false``)
==================  ================


Copy files
----------

The method ``copyTo`` copies a file into a new file or into a directory, or copie a directory to a new
directory::

  myFile.copyTo('new_name.txt')


.. note:: If the target file already exists, it will be replaced by the new one. Note also that, if the target is
  a directory, the source file will be copied into that directory, maintaining the file's original name.


When the source file is a directory, all its content is copied to the target directory::

  myDir = file('/some/path')
  myDir.copyTo('/some/new/path')


  If the target path does not exist, it will be created automatically.

.. tip:: The ``copyTo`` method mimics the semantics of the Linux command ``cp -r <source> <target>``, with the
         following caveat: While Linux tools often treat paths ending with a slash (e.g. ``/some/path/name/``)
         as directories, and those not (e.g. ``/some/path/name``) as regular files, Nextflow (due to its use of
         the Java files API) views both these paths as the same file system object. If the path exists, it is
         handled according to its actual type (i.e. as a regular file or as a directory). If the path does not
         exist, it is treated as a regular file, with any missing parent directories created automatically.



Move files
----------

You can move a file by using the method ``moveTo``::

  myFile = file('/some/path/file.txt')
  myFile.moveTo('/another/path/new_file.txt')


.. note:: When a file with the same name as the target already exists, it will be replaced by the source. Note
          also that, when the target is a directory, the file will be moved to (or within) that directory,
          maintaining the file's original name.

When the source is a directory, all the directory content is moved to the target directory::

  myDir = file('/any/dir_a')
  myDir.moveTo('/any/dir_b')


Please note that the result of the above example depends on the existence of the target directory. If the target
directory exists, the source is moved into the target directory, resulting in the path::

  /any/dir_b/dir_a

If the target directory does not exist, the source is just renamed to the target name, resulting in the path::

  /any/dir_b


.. tip:: The ``moveTo`` method mimics the semantics of the Linux command ``mv <source> <target>``, with the
         same caveat as that given for ``copyTo``, above.


Rename files
------------

You can rename a file or directory by simply using the ``renameTo`` file method::

  myFile = file('my_file.txt')
  myFile.renameTo('new_file_name.txt')


Delete files
------------

The file method ``delete`` deletes the file or directory at the given path, returning ``true`` if the
operation succeeds, and ``false`` otherwise::

  myFile = file('some/file.txt')
  result = myFile.delete
  println result ? "OK" : "Can delete: $myFile"


.. note:: This method deletes a directory ONLY if it does not contain any files or sub-directories. To
          delete a directory and ALL its content (i.e. removing all the files and sub-directories it may
          contain), use the method ``deleteDir``.


Check file attributes
---------------------

The following methods can be used on a file variable created by using the ``file`` method:

==================  ================
Name                Description
==================  ================
getName             Gets the file name e.g. ``/some/path/file.txt`` -> ``file.txt``
getBaseName         Gets the file name without its extension e.g. ``/some/path/file.tar.gz`` -> ``file.tar``
getSimpleName       Gets the file name without any extension e.g. ``/some/path/file.tar.gz`` -> ``file``
getExtension        Gets the file extension e.g. ``/some/path/file.txt`` -> ``txt``
getParent           Gets the file parent path e.g. ``/some/path/file.txt`` -> ``/some/path``
size                Gets the file size in bytes
exists              Returns ``true`` if the file exists, or ``false`` otherwise
isEmpty             Returns ``true`` if the file is zero length or does not exist, ``false`` otherwise
isFile              Returns ``true`` if it is a regular file e.g. not a directory
isDirectory         Returns ``true`` if the file is a directory
isHidden            Returns ``true`` if the file is hidden
lastModified        Returns the file last modified timestamp i.e. a long as Linux epoch time
==================  ================


For example, the following line prints a file name and size::

  println "File ${myFile.getName() size: ${myFile.size()}"



Get and modify file permissions
-------------------------------

Given a file variable representing a file (or directory), the method ``getPermissions`` returns a
9-character string representing the file's permissions using the
`Linux symbolic notation <http://en.wikipedia.org/wiki/File_system_permissions#Symbolic_notation>`_
e.g. ``rw-rw-r--``::

    permissions = myFile.getPermissions()


Similarly, the method ``setPermissions`` sets the file's permissions using the same notation::

    myFile.setPermissions('rwxr-xr-x')


A second version of the ``setPermissions`` method sets a file's permissions given three digits representing,
respectively, the `owner`, `group` and `other` permissions::

    myFile.setPermissions(7,5,5)


Learn more about `File permissions numeric notation <http://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation>`_.

HTTP/FTP files
--------------

Nextflow provides transparent integration of HTTP/S and FTP protocols for handling remote resources
as local file system objects. Simply specify the resource URL as the argument of the `file` object::

    pdb = file('http://files.rcsb.org/header/5FID.pdb')

Then, you can access it as a local file as described in the previous sections::

    println pdb.text

The above one-liner prints the content of the remote PDB file. Previous sections provide code examples
showing how to stream or copy the content of files.

.. note:: Write and list operations are not supported for HTTP/S and FTP files.


Counting records
----------------

countLines
^^^^^^^^^^

The ``countLines`` methods counts the lines in a text files.
::

    def sample = file('/data/sample.txt')
    println sample.countLines()


Files whose name ends with the ``.gz`` suffix are expected to be GZIP compressed and
automatically uncompressed.

countFasta
^^^^^^^^^^

The ``countFasta`` method counts the number of records in `FASTA <https://en.wikipedia.org/wiki/FASTA_format>`_
formatted file.
::

    def sample = file('/data/sample.fasta')
    println sample.countFasta()

Files whose name ends with the ``.gz`` suffix are expected to be GZIP compressed and
automatically uncompressed.

countFastq
^^^^^^^^^^

The ``countFastq`` method counts the number of records in a `FASTQ <https://en.wikipedia.org/wiki/FASTQ_format>`_
formatted file.
::

    def sample = file('/data/sample.fastq')
    println sample.countFastq()

Files whose name ends with the ``.gz`` suffix are expected to be GZIP compressed and
automatically uncompressed.



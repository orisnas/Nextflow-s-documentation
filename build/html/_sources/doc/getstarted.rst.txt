开始
=========


所需环境
----------


安装
--------

Nextflow是一个独立的可执行包, 这意味着它不需要特殊的安装过程.

它仅仅简单的两步:

1. 下载可执行包. 复制粘贴后面的命令到你的终端窗口中:``wget -qO- https://get.nextflow.io | bash``. 它将会在当前目录创建一个可执行文件 ``nextflow``.

2. (此步可选)将 ``nextflow`` 文件转移到 ``$PATH`` 变量可以找到的目录中.(这仅仅是为了避免你每次运行它的时候都需要记住或输入 ``nextflow`` 的绝对路径).

.. tip::
   在你没有安装 ``wget`` 的情况下, 你可以使用 ``curl`` 来代替, 在命令行中输入后面的命令: ``curl -s https://get.nextflow.io | bash``.


第一个脚本
-----------

将下面的例子复制到你习惯的文本编辑器中, 然后保存到名字为 ``tutorial.nf`` 的文件中.

 ::
 
  #!/usr/bin/env nextflow
  
  params.str = 'Hello world!'
  
  process splitLetters {
  
      output:
      file 'chunk_*' into letters mode flatten
  
      """
      printf '${params.str}' | split -b 6 - chunk_
      """
  }
  
  
  process convertToUpper {
  
      input:
      file x from letters
  
      output:
      stdout result
  
      """
      cat $x | tr '[a-z]' '[A-Z]'
      """
  }
  
  result.subscribe {
      println it.trim()
  }
  

这个脚本定义了2个processes.  


在终端中输入下面的命令来执行这个脚本::

  nextflow run tutorial.nf


它将输出一些东西,类似下面的文本::

   N E X T F L O W  ~  version 19.01.0
   Launching `tutorial.nf` [prickly_goldwasser] - revision: 361b274147
   [warm up] executor > local
   [4b/65537c] Submitted process > splitLetters
   [7d/c27fbe] Submitted process > convertToUpper (1)
   [62/9488a7] Submitted process > convertToUpper (2)
   WORLD!
   HELLO
   

可以看到,第一个process执行了一次,而第二个执行了两次. 最后, 结果字符串被打印出来.




Modify and resume
^^^^^^^^^^^^^^^^^^^


Pipeline parameters
^^^^^^^^^^^^^^^^^^^^

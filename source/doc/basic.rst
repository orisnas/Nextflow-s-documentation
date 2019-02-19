***************
基本理念
***************


`Nextflow` 是一个交互式的流程框架, 一个特定领域编程语言(a programming `DSL <http://en.wikipedia.org/wiki/Domain-specific_language>`_ ). 它使数据密集型计算流程的串写变得容易.

它是围绕这个想法设计的-- Linux 是数据科学的通用平台. Linux提供了很多简单但强大的命令行和脚本工具使我们可以很方便的操作复杂的数据.

`Nextflow` 扩展了这一特点, 它增加了定义复杂的程序交互和高级并行计算环境的能力,这些都是基于`dataflow`编程模型.


Processes and channels
----------------------

事实上, 一个 Nextflow 流程脚本是通过将不同的processes 连接在一起而生成.
每一个process 可以用任意的脚本语言来编写, 而这些脚本语言可以通过Linux平台来执行 (Bash, Perl, Ruby, Python, etc.).

所有的Processes 都是独立的执行的, 它们之间相互隔离, i.e. 它们并不共享公共（可写）状态.
它们通讯的唯一方式是通过异步 FIFO 队列( 在Nextflow中叫做 `channels` ).

任何 process 可以定义一个或多个 channels 作为 `input` and `output`. 这些 processes 之间的交互, 以及根本的管道执行流自己本身, 都含蓄的由这些 input 和 output 的申明来定义.

A Nextflow script looks like this::

    // Script parameters
    params.query = "/some/data/sample.fa"
    params.db = "/some/path/pdb"

    db = file(params.db)
    query_ch = Channel.fromPath(params.query)

    process blastSearch {
        input:
        file query from query_ch

        output:
        file "top_hits.txt" into top_hits_ch

        """
        blastp -db $db -query $query -outfmt 6 > blast_result
        cat blast_result | head -n 10 | cut -f 2 > top_hits.txt
        """
    }

    process extractTopHits {
        input:
        file top_hits from top_hits_ch

        output:
        file "sequences.txt" into sequences_ch

        """
        blastdbcmd -db $db -entry_batch $top_hits > sequences.txt
        """
    }



上面的例子定义了两个processes. 它们的执行顺序并不是有它们在脚本的顺序来决定, 事实上, ``blastSearch``
process 在脚本中位于 ``extractTopHits`` 前 (it could also be written the other way around).

反而是因为, 第一个process 在他的 ``output`` 申明中定义了 channel ``top_hits_ch`` , 而 process ``extractTopHits`` 在它的 ``input`` 申明中定义了这个 channel . 这样通讯连接就建立了.

通过这个 channel的连接意味着 ``extractTopHits`` 一直等待着 ``blastSearch`` 的output, 一旦channel中有内容它就开始运行.

.. TODO describe that both processes are launched at the same time

Read the :ref:`Channel <channel-page>` and :ref:`Process <process-page>` sections to learn more about these features.


执行抽象
---------------------

当过程定义了必须执行什么命令或脚本时，执行器(``executor``)确定脚本如何实际运行在目标系统上。

如果特殊指明, processes会在本地电脑上执行. 本地执行对与流程开发和测试是非常有用的. 但是对生产环境的流程, 往往 HPC(高性能计算, High Performance Computing) 和 cloud platform 是需要的.

换句话说,  `Nextflow` 提供了流程的功能逻辑和底层执行系统之间的抽象化.
Thus it is possible to write a pipeline once and to seamlessly run it on your computer, a grid platform, or the cloud,
without modifying it, by simply defining the target execution platform in the configuration file.

支持下面的成批调度程序:

* `Open grid engine <http://gridscheduler.sourceforge.net/>`_
* `Univa grid engine <http://www.univa.com/>`_
* `Platform LSF <http://www.ibm.com/systems/technicalcomputing/platformcomputing/products/lsf/>`_
* `Linux SLURM <https://computing.llnl.gov/linux/slurm/>`_
* `PBS Works <http://www.pbsworks.com/gridengine/>`_
* `Torque <http://www.adaptivecomputing.com/products/open-source/torque/>`_
* `HTCondor <https://research.cs.wisc.edu/htcondor/>`_


支持下面的云平台:

* `Amazon Web Services (AWS) <https://aws.amazon.com/>`_
* `Google Cloud Platform (GCP) <https://cloud.google.com/>`_
* `Kubernetes <https://kubernetes.io/>`_

Read the :ref:`executor-page` to learn more about the Nextflow executors.


脚本语言
------------------

`Nextflow` 被设计成具有最小的学习曲线，而不需要学习一种新的编程语言. 在大多数情况下，用户可以利用他们当前的技能来开发下一个tflow工作流. 然而，它还提供了强大的脚本DSL.

Nextflow 脚本是 `Groovy 编程语言 <http://en.wikipedia.org/wiki/Groovy_(programming_language)>`_ 的扩展, 而Groovy是Java编程语言的超集.Groovy可以被认为是Java的Python，因为它简化了代码的编写，而且更容易接近.

Read the :ref:`script-page` section to learn about the Nextflow scripting language.


.. TODO Running pipeline


.. TODO Pipeline parameters


配置选项
---------------------

流程配置属性在流程执行目录中名为 ``nextflow.config`` 的文件中定义. 

此文件可用于定义要使用哪个executor、流程(process)的环境变量、管道(pipeline)参数等. 

基本配置文件可能是这样的::

	process { 
	  executor='sge'
	  queue = 'cn-el6' 
	}


Read the :ref:`config-page` section to learn more about the Nextflow configuration file and settings.




---
title: Verwenden von Caffe in Azure HDInsight Spark für verteiltes Deep Learning
description: Verwenden von Caffe in Azure HDInsight Spark für verteiltes Deep Learning
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/17/2017
ms.openlocfilehash: d0d68263485c5ab6e57a349317b1975862470cc2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721516"
---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>Verwenden von Caffe in Azure HDInsight Spark für verteiltes Deep Learning


## <a name="introduction"></a>Einführung

Deep Learning wirkt sich auf zahlreiche Bereiche aus – vom Gesundheitswesen über Logistik bis hin zur Fertigung. Unternehmen nutzen Deep Learning zur Lösung komplexer Aufgaben wie [Bildklassifizierung](https://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), [Spracherkennung](https://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html), Objekterkennung und maschinelle Übersetzung. 

Es gibt [viele beliebte Frameworks](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software), einschließlich [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), [Tensorflow](https://www.tensorflow.org/), [Apache MXNet](https://mxnet.apache.org/), Theano usw. [Caffe](https://caffe.berkeleyvision.org/) ist eines der berühmtesten, nicht symbolischen (imperativen) neuronalen Netzwerkframeworks und wird in vielen Bereichen verwendet, einschließlich beim maschinellen Sehen. Darüber hinaus kombiniert [CaffeOnSpark](https://yahoohadoop.tumblr.com/post/139916563586/caffeonspark-open-sourced-for-distributed-deep) Caffe mit Apache Spark, wodurch Deep Learning einfach auf einem vorhandenen Hadoop-Cluster verwendet werden kann. Sie können Deep Learning zusammen mit Spark-ETL-Pipelines verwenden, um die Systemkomplexität und die Wartezeit für das vollständige Lernen von Lösungen zu reduzieren.

[HDInsight](https://azure.microsoft.com/services/hdinsight/) ist ein Apache Hadoop-Cloudangebot mit optimierten Open-Source-Analyseclustern für Apache Spark, Apache Hive, Apache Hadoop, Apache HBase, Apache Storm, Apache Kafka und ML Services. HDInsight ist durch eine SLA mit 99,9 Prozent gedeckt. Alle diese Big Data-Technologien und ISV-Anwendungen können ganz einfach als verwaltete Cluster mit Sicherheit und Überwachung für Unternehmen bereitgestellt werden.

Dieser Artikel veranschaulicht die Installation von [CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark) für einen HDInsight-Cluster. Dieser Artikel verwendet ebenfalls die integrierte MNIST-Demo, um die Verwendung von verteiltem Deep Learning mit HDInsight Spark auf CPUs zu veranschaulichen.

Zum Ausführen des Tasks sind vier Schritte erforderlich:

1. Installieren der erforderlichen Abhängigkeiten auf allen Knoten
2. Erstellen von CaffeOnSpark für HDInsight auf dem Hauptknoten
3. Verteilen der erforderlichen Bibliotheken auf alle Workerknoten
4. Stellen Sie ein Caffe-Modell zusammen, und führen Sie dieses verteilt aus.

Da es sich bei HDInsight um eine PaaS-Lösung handelt, bietet sie hervorragende Plattformfunktionen. Daher ist die Durchführung einiger Aufgaben einfach. Eine der in diesem Blogbeitrag verwendeten Funktionen heißt [Skriptaktion](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux). Damit können Sie mit Shell-Befehlen Clusterknoten (Hauptknoten, Workerknoten oder Edgeknoten) anpassen.

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>Schritt 1:  Installieren der erforderlichen Abhängigkeiten auf allen Knoten

Zuerst müssen Sie die Abhängigkeiten installieren. Die Caffe-Website und die [CaffeOnSpark-Website](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) bieten nützliche Wikis für das Installieren der Abhängigkeiten für Spark im YARN-Modus. HDInsight verwendet ebenfalls Spark im YARN-Modus. Für die HDInsight-Plattform müssen Sie jedoch noch ein paar weitere Abhängigkeiten hinzufügen. Dafür verwenden Sie eine Skriptaktion, die auf allen Hauptknoten und Workerknoten ausgeführt wird. Diese Skriptaktion dauert etwa 20 Minuten, da diese Abhängigkeiten auch von anderen Paketen abhängig sind. Sie sollten sie an einem Ort ablegen, der über Ihren HDInsight-Cluster aufgerufen werden kann, beispielsweise an einem GitHub-Speicherort oder im standardmäßigen Blobspeicherkonto.

    #!/bin/bash
    #Please be aware that installing the below will add additional 20 mins to cluster creation because of the dependencies
    #installing all dependencies, including the ones mentioned in https://caffe.berkeleyvision.org/install_apt.html, as well a few packages that are not included in HDInsight, such as gflags, glog, lmdb, numpy
    #It seems numpy will only needed during compilation time, but for safety purpose you install them on all the nodes

    sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler maven libatlas-base-dev libgflags-dev libgoogle-glog-dev liblmdb-dev build-essential  libboost-all-dev python-numpy python-scipy python-matplotlib ipython ipython-notebook python-pandas python-sympy python-nose

    #install protobuf
    wget https://github.com/google/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
    sudo tar xzvf protobuf-2.5.0.tar.gz -C /tmp/
    cd /tmp/protobuf-2.5.0/
    sudo ./configure
    sudo make
    sudo make check
    sudo make install
    sudo ldconfig
    echo "protobuf installation done"


Die Skriptaktion umfasst zwei Schritte. Der erste Schritt besteht in der Installation aller erforderlichen Bibliotheken. Diese Bibliotheken enthalten die erforderlichen Bibliotheken zum Kompilieren von Caffe (z.B. GFlags, Glog) und zum Ausführen von Caffe (z.B. NumPy). Sie verwenden Libatlas für die CPU-Optimierung, können aber jederzeit das CaffeOnSpark-Wiki zum Installieren anderer Optimierungsbibliotheken befolgen, wie z.B. MKL oder CUDA (für GPU).

Der zweite Schritt beinhaltet das Herunterladen, Kompilieren und Installieren von Protobuf 2.5.0 für Caffe während der Laufzeit. Protobuf 2.5.0 [ist erforderlich](https://github.com/yahoo/CaffeOnSpark/issues/87); allerdings ist diese Version nicht als Paket auf Ubuntu 16 verfügbar. Daher müssen Sie die Version aus dem Quellcode kompilieren. Im Internet gibt es auch einige Ressourcen zur Vorgehensweise beim Kompilieren. Weitere Informationen finden Sie [hier](https://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html).

Zum Einstieg können Sie auch nur diese Skriptaktion in Ihrem Cluster für alle Workerknoten und Hauptknoten (für HDInsight 3.5) ausführen. Sie können die Skriptaktionen auf einem vorhandenen Cluster ausführen oder Skriptaktionen während der Clustererstellung verwenden. Weitere Informationen zu Skriptaktionen finden Sie [hier](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) in der Dokumentation.

![Skriptaktionen zum Installieren von Abhängigkeiten](./media/apache-spark-deep-learning-caffe/Script-Action-1.png)


## <a name="step-2-build-caffe-on-apache-spark-for-hdinsight-on-the-head-node"></a>Schritt 2: Erstellen von Caffe auf Apache Spark für HDInsight auf dem Hauptknoten

Der zweite Schritt umfasst das Erstellen von Caffe auf dem Hauptknoten und das anschließende Verteilen der kompilierten Bibliotheken auf alle Workerknoten. In diesem Schritt müssen Sie [eine SSH-Verbindung mit Ihrem Hauptknoten herstellen](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix). Anschließend müssen Sie den [CaffeOnSpark-Buildprozess](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) befolgen. Im Folgenden finden Sie das Skript, das Sie verwenden können, um CaffeOnSpark mit einigen zusätzlichen Schritten zu erstellen. 

    #!/bin/bash
    git clone https://github.com/yahoo/CaffeOnSpark.git --recursive
    export CAFFE_ON_SPARK=$(pwd)/CaffeOnSpark

    pushd ${CAFFE_ON_SPARK}/caffe-public/
    cp Makefile.config.example Makefile.config
    echo "INCLUDE_DIRS += ${JAVA_HOME}/include" >> Makefile.config
    #Below configurations might need to be updated based on actual cases. For example, if you are using GPU, or using a different BLAS library, you may want to update those settings accordingly.
    echo "CPU_ONLY := 1" >> Makefile.config
    echo "BLAS := atlas" >> Makefile.config
    echo "INCLUDE_DIRS += /usr/include/hdf5/serial/" >> Makefile.config
    echo "LIBRARY_DIRS += /usr/lib/x86_64-linux-gnu/hdf5/serial/" >> Makefile.config
    popd

    #compile CaffeOnSpark
    pushd ${CAFFE_ON_SPARK}
    #always clean up the environment before building (especially when rebuiding), or there will be errors such as "failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2"
    make clean 
    #the build step usually takes 20~30 mins, since it has a lot maven dependencies
    make build 
    popd
    export LD_LIBRARY_PATH=${CAFFE_ON_SPARK}/caffe-public/distribute/lib:${CAFFE_ON_SPARK}/caffe-distri/distribute/lib

    hadoop fs -mkdir -p wasb:///projects/machine_learning/image_dataset

    ${CAFFE_ON_SPARK}/scripts/setup-mnist.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/mnist_*_lmdb wasb:///projects/machine_learning/image_dataset/

    ${CAFFE_ON_SPARK}/scripts/setup-cifar10.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/cifar10_*_lmdb wasb:///projects/machine_learning/image_dataset/

    #put the already compiled CaffeOnSpark libraries to wasb storage, then read back to each node using script actions. This is because CaffeOnSpark requires all the nodes have the libarries
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-public/distribute/lib/
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-distri/distribute/lib/* /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-public/distribute/lib/* /CaffeOnSpark/caffe-public/distribute/lib/

Sie müssen möglicherweise mehr erledigen, als in der CaffeOnSpark-Dokumentation angegeben ist. Die Änderungen sind:
- Nehmen Sie nur Änderungen an der CPU vor, und verwenden Sie Libatlas für diesen bestimmten Zweck.
- Fügen Sie die Datasets dem Blobspeicher hinzu. Bei diesem handelt es sich um einen freigegebenen Speicherort, der für alle Workerknoten zur späteren Verwendung zugänglich ist.
- Legen Sie die kompilierten Caffe-Bibliotheken im Blobspeicher ab, und kopieren Sie diese Bibliotheken später mithilfe von Skriptaktionen auf alle Knoten, um zusätzliche Kompilierungszeit zu vermeiden.


### <a name="troubleshooting-an-ant-buildexception-has-occurred-exec-returned-2"></a>Problembehandlung: Eine Ant BuildException ist aufgetreten: zurückgegebene Ausführungen: 2

Beim ersten Versuch, CaffeOnSpark zu erstellen, wird manchmal Folgendes zurückgegeben:

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2

Bereinigen Sie das Coderepository mit „make clean“, und führen Sie anschließend „make build“ aus, um dieses Problem zu beheben, solange Sie über die richtigen Abhängigkeiten verfügen.

### <a name="troubleshooting-maven-repository-connection-time-out"></a>Problembehandlung: Maven-Repository-Verbindungstimeout

Maven generiert manchmal einen Verbindungstimeoutfehler, der folgendem Codeausschnitt ähnelt:

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

Sie müssen nach einigen Minuten einen erneuten Versuch vornehmen.


### <a name="troubleshooting-test-failure-for-caffe"></a>Problembehandlung: Testfehler für Caffe

Sie sehen bei der letzten Überprüfung für CaffeOnSpark wahrscheinlich einen Testfehler. Dieser steht vermutlich im Zusammenhang mit der UTF-8-Codierung, sollte sich allerdings nicht auf die Nutzung von Caffe auswirken.

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>Schritt 3: Verteilen der erforderlichen Bibliotheken auf alle Workerknoten

Der nächste Schritt besteht im Verteilen der Bibliotheken (im Wesentlichen der Bibliotheken in „CaffeOnSpark/caffe-public/distribute/lib/“ und „CaffeOnSpark/caffe-distri/distribute/lib/“) auf alle Knoten. In Schritt 2 haben Sie diese Bibliotheken im Blobspeicher abgelegt, und in diesem Schritt kopieren Sie die Bibliotheken mithilfe von Skriptaktionen auf alle Hauptknoten und Workerknoten.

Führen Sie dazu eine Skripaktion wie im folgenden Codeausschnitt dargestellt aus:

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

Stellen Sie sicher, dass Sie auf den richtigen, für Ihren Cluster spezifischen Speicherort zeigen.

Da Sie die Bibliotheken in Schritt 2 im Blobspeicher abgelegt haben, der für alle Knoten zugänglich ist, werden sie in diesem Schritt auf alle Knoten kopiert.

## <a name="step-4-compose-a-caffe-model-and-run-it-in-a-distributed-manner"></a>Schritt 4: Zusammenstellen und verteiltes Ausführen eines Caffe-Modells

Caffe wird installiert, nachdem die oben stehenden Schritte ausgeführt wurden. Der nächste Schritt besteht im Schreiben eines Caffe-Modells. 

Caffe verwendet eine „ausdrucksbasierte Architektur“, in der Sie zum Erstellen eines Modells meist einfach nur eine Konfigurationsdatei ohne Codierung definieren müssen. Sehen wir uns das genauer an. 

Das trainierte Modell ist ein Beispielmodell für das MNIST-Training. Die MNIST-Datenbank handschriftlicher Ziffern verfügt über einen Trainingssatz mit 60.000 Beispielen und einen Testsatz mit 10.000 Beispielen. Dabei handelt es sich um eine Teilmenge eines größeren Satzes, der von NIST zur Verfügung gestellt wird. Die Größe der Ziffern wurde normalisiert, und die Ziffern wurden in einem Bild mit fester Größe zentriert. CaffeOnSpark verfügt über einige Skripts zum Herunterladen des Datasets und zum Konvertieren in das richtige Format.

CaffeOnSpark stellt einige Netzwerktopologiebeispiele für das MNIST-Training bereit. Die Teilung der Netzwerkarchitektur (die Topologie des Netzwerks) und die Optimierung sind schön gestaltet. In diesem Fall sind zwei Dateien erforderlich: 

Die Datei „Solver“ (${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt) wird für das Überwachen der Optimierung und das Generieren von Parameterupdates verwendet. Sie definiert beispielsweise, ob die CPU oder GPU verwendet wird, legt die Dynamik fest, gibt die Anzahl der Iterationen an usw. Sie definiert außerdem, welche Neuronennetzwerktopologie das Programm verwenden soll (die zweite benötigte Datei). Weitere Informationen zu Solver finden Sie in der [Caffe-Dokumentation](https://caffe.berkeleyvision.org/tutorial/solver.html).

Da Sie für dieses Beispiel CPU statt GPU verwenden, muss die letzte Zeile wie folgt geändert werden:

    # solver mode: CPU or GPU
    solver_mode: CPU

![Caffe-Konfiguration](./media/apache-spark-deep-learning-caffe/Caffe-1.png)

Sie können die anderen Zeilen nach Bedarf anpassen.

Die zweite Datei (${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt) definiert, wie das Neuronennetzwerk und die entsprechende Eingabe- und Ausgabedatei aussehen. Sie müssen zudem die Datei entsprechend dem Trainingsdatenspeicherort aktualisieren. Ändern Sie den folgenden Teil in „lenet_memory_train_test.prototxt“ (Sie müssen auf den richtigen, für Ihren Cluster spezifischen Speicherort zeigen):

- Ändern Sie „file:/Users/mridul/bigml/demodl/mnist_train_lmdb“ in „wasb:///projects/machine_learning/image_dataset/mnist_train_lmdb“.
- Ändern Sie „file:/Users/mridul/bigml/demodl/mnist_test_lmdb/“ in „wasb:///projects/machine_learning/image_dataset/mnist_test_lmdb“.

![Caffe-Konfiguration](./media/apache-spark-deep-learning-caffe/Caffe-2.png)

Weitere Informationen zum Definieren des Netzwerks finden Sie in der [Caffe-Dokumentation im MNIST-Dataset](https://caffe.berkeleyvision.org/gathered/examples/mnist.html).

In diesem Artikel wird dieses MNIST-Beispiel verwendet. Führen Sie folgende Befehle über den Hauptknoten aus:

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

Der oben stehende Befehl verteilt die erforderlichen Dateien (lenet_memory_solver.prototxt und lenet_memory_train_test.prototxt) an jeden YARN-Container. Der Befehl legt ebenfalls den entsprechenden Pfad jedes Spark-Treibers/-Executors auf „LD_LIBRARY_PATH“ fest. „LD_LIBRARY_PATH“ wird im oben stehenden Codeausschnitt definiert und verweist auf den Speicherort, der über CaffeOnSpark-Bibliotheken verfügt. 

## <a name="monitoring-and-troubleshooting"></a>Überwachung und Problembehandlung

Da Sie den YARN-Clustermodus verwenden, bei dem der Spark-Treiber für einen willkürlichen Container (und einen willkürlichen Workerknoten) geplant wird, sollten Sie in der Konsolenausgabe in etwa Folgendes sehen:

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

Wenn Sie wissen möchten, was geschehen ist, müssen Sie normalerweise das Protokoll des Spark-Treibers abrufen, das weitere Informationen enthält. In diesem Fall wechseln Sie zur YARN-Benutzeroberfläche, um die relevanten YARN-Protokolle zu suchen. Sie können die YARN-Benutzeroberfläche anhand dieser URL aufrufen: 

    https://yourclustername.azurehdinsight.net/yarnui
   
![YARN-Benutzeroberfläche](./media/apache-spark-deep-learning-caffe/YARN-UI-1.png)

Sie können einen Blick darauf werfen, wie viele Ressourcen dieser bestimmten Anwendung zugeordnet sind. Sie können auf den Link „Scheduler“ klicken und sehen dann, dass für diese Anwendung neun Container ausgeführt werden. Sie fordern YARN auf, acht Executors bereitzustellen. Ein weiterer Container ist für den Treiberprozess bestimmt. 

![YARN-Scheduler](./media/apache-spark-deep-learning-caffe/YARN-Scheduler.png)

Sie können bei Fehlern die Treiberprotokolle oder die Containerprotokolle überprüfen. Treiberprotokolle erhalten Sie durch Klicken auf die Anwendungs-ID auf der YARN-Benutzeroberfläche und anschließendes Klicken auf die Schaltfläche „Protokolle“. Die Treiberprotokolle werden in STDERR geschrieben.

![YARN-Benutzeroberfläche 2](./media/apache-spark-deep-learning-caffe/YARN-UI-2.png)

Beispiel: Sie sehen möglicherweise einige der unten aufgeführten Fehler aus den Treiberprotokollen, die angeben, dass zu viele Executors zugeordnet sind.

    17/02/01 07:26:06 ERROR ApplicationMaster: User class threw exception: java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
    java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
        at com.yahoo.ml.caffe.CaffeOnSpark.trainWithValidation(CaffeOnSpark.scala:261)
        at com.yahoo.ml.caffe.CaffeOnSpark$.main(CaffeOnSpark.scala:42)
        at com.yahoo.ml.caffe.CaffeOnSpark.main(CaffeOnSpark.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.spark.deploy.yarn.ApplicationMaster$$anon$2.run(ApplicationMaster.scala:627)

In manchen Fällen tritt das Problem eher bei Executors statt bei Treibern auf. Überprüfen Sie in diesem Fall die Containerprotokolle. Sie können jederzeit die Containerprotokolle und dann die fehlerhaften Container abrufen. Beispiel: Dieser Fehler tritt möglicherweise bei der Ausführung von Caffe auf.

    17/02/01 07:12:05 WARN YarnAllocator: Container marked as failed: container_1485916338528_0008_05_000005 on host: 10.0.0.14. Exit status: 134. Diagnostics: Exception from container-launch.
    Container id: container_1485916338528_0008_05_000005
    Exit code: 134
    Exception message: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

    Stack trace: ExitCodeException exitCode=134: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

        at org.apache.hadoop.util.Shell.runCommand(Shell.java:933)
        at org.apache.hadoop.util.Shell.run(Shell.java:844)
        at org.apache.hadoop.util.Shell$ShellCommandExecutor.execute(Shell.java:1123)
        at org.apache.hadoop.yarn.server.nodemanager.DefaultContainerExecutor.launchContainer(DefaultContainerExecutor.java:225)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:317)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:83)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)


    Container exited with a non-zero exit code 134

In diesem Fall müssen Sie die ID des fehlerhaften Containers abrufen (im Beispiel oben lautet diese „container_1485916338528_0008_05_000005“). Dann müssen Sie 

    yarn logs -containerId container_1485916338528_0008_03_000005

über den Hauptknoten ausführen. Die Überprüfung des Containerfehlers ergab, dass er durch die Verwendung des GPU-Modus (wenn stattdessen der CPU-Modus hätte verwendet werden sollen) in „lenet_memory_solver.prototxt“ verursacht wird.

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.


## <a name="getting-results"></a>Abrufen von Ergebnissen

Da Sie acht Executors zuordnen und die Netzwerktopologie einfach ist, sollte die Ausführung des Ergebnisses nur ca. 30 Minuten dauern. In der Befehlszeile sehen Sie, dass Sie das Modell unter „wasb:///mnist.model“ und die Ergebnisse in einem Ordner mit der Bezeichnung „wasb:///mnist_features_result“ ablegen.

Sie erhalten die Ergebnisse durch Ausführen der folgenden Eingabe:

    hadoop fs -cat hdfs:///mnist_features_result/*

Das Ergebnis sieht folgendermaßen aus:

    {"SampleID":"00009597","accuracy":[1.0],"loss":[0.028171852],"label":[2.0]}
    {"SampleID":"00009598","accuracy":[1.0],"loss":[0.028171852],"label":[6.0]}
    {"SampleID":"00009599","accuracy":[1.0],"loss":[0.028171852],"label":[1.0]}
    {"SampleID":"00009600","accuracy":[0.97],"loss":[0.0677709],"label":[5.0]}
    {"SampleID":"00009601","accuracy":[0.97],"loss":[0.0677709],"label":[0.0]}
    {"SampleID":"00009602","accuracy":[0.97],"loss":[0.0677709],"label":[1.0]}
    {"SampleID":"00009603","accuracy":[0.97],"loss":[0.0677709],"label":[2.0]}
    {"SampleID":"00009604","accuracy":[0.97],"loss":[0.0677709],"label":[3.0]}
    {"SampleID":"00009605","accuracy":[0.97],"loss":[0.0677709],"label":[4.0]}

Das SampleID-Element stellt die ID im MNIST-Dataset dar. Die Bezeichnung ist die Nummer, die das Modell kennzeichnet.


## <a name="conclusion"></a>Zusammenfassung

In dieser Dokumentation haben Sie versucht, CaffeOnSpark durch Ausführen eines einfachen Beispiels zu installieren. HDInsight ist eine vollständig verwaltete, über die Cloud verteilte Computeplattform und eignet sich optimal zum Ausführen von Machine Learning- und Advanced Analytics-Workloads für große Datasets. Bei verteiltem Deep Learning können Sie Caffe in HDInsight Spark ausführen, um Deep Learning-Aufgaben durchzuführen.


## <a name="seealso"></a>Weitere Informationen
* [Übersicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Szenarien
* [Apache Spark mit Machine Learning: Analysieren von Gebäudetemperaturen mithilfe von Spark in HDInsight und HVAC-Daten](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>Verwalten von Ressourcen
* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](apache-spark-resource-manager.md)


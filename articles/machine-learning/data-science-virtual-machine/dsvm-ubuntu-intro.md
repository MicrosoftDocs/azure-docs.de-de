---
title: Erstellen einer Ubuntu Linux-Data Science Virtual Machine
titleSuffix: Azure
description: Konfigurieren und erstellen Sie eine Data Science Virtual Machine für Linux (Ubuntu) in Azure, um Analysen und Machine Learning-Vorgänge durchzuführen.
services: machine-learning
documentationcenter: ''
author: gopitk
ms.author: gokuma
manager: cgronlun
ms.custom: seodec18
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: 5a9fdebc8db0c2a1acc20a894f80cfcc87fb89d5
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236491"
---
# <a name="provision-the-data-science-virtual-machine-for-linux-ubuntu"></a>Bereitstellen der Data Science Virtual Machine für Linux (Ubuntu)

Die Data Science Virtual Machine (DSVM) für Linux ist ein Ubuntu-basiertes VM-Image, das den Einstieg in maschinelles Lernen, wie Deep Learning, in Azure erleichtert. Deep Learning-Tools:

* [Caffe](https://caffe.berkeleyvision.org/): Deep Learning-Framework mit Fokus auf Geschwindigkeit, Ausdrucksfähigkeit und Modularität
* [Caffe2](https://github.com/caffe2/caffe2): Plattformübergreifende Version von Caffe
* [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK): Deep Learning-Softwaretoolkit von Microsoft Research
* [H2O](https://www.h2o.ai/): Open Source-Plattform und grafische Benutzeroberfläche für Big Data
* [Keras](https://keras.io/): Allgemeine API für neuronale Netze in Python für TensorFlow, Microsoft Cognitive Toolkit und Theano
* [MXNet](https://mxnet.io/): Flexible, effiziente Deep Learning-Bibliothek mit vielen Sprachbindungen
* [NVIDIA DIGITS](https://developer.nvidia.com/digits): Grafisches System, das häufige Deep Learning-Aufgaben vereinfacht
* [PyTorch](https://pytorch.org/): Eine allgemeine Python-Bibliothek mit Unterstützung für dynamische Netzwerke
* [TensorFlow](https://www.tensorflow.org/): Open Source-Bibliothek für Computerintelligenz von Google
* [Theano](http://deeplearning.net/software/theano/): Python-Bibliothek zum Definieren, Optimieren und effizienten Auswerten mathematischer Ausdrücke einschließlich mehrdimensionaler Arrays
* [Torch](http://torch.ch/): Framework für wissenschaftliche Berechnungen mit weit reichender Unterstützung für Machine Learning-Algorithmen
* CUDA, cuDNN und der NVIDIA-Treiber
* Viele Jupyter-Beispiel-Notebooks

Bei den Bibliotheken handelt es sich zwar um die GPU-Versionen, diese können jedoch auch über die CPU ausgeführt werden.

Die Data Science Virtual Machine für Linux enthält auch beliebte Tools für Data Science- und Entwicklungsaktivitäten, wie z.B.:

* Microsoft R Server Developer Edition mit Microsoft R Open
* Anaconda Python-Distribution (Versionen 2.7 und 3.5), einschließlich gängiger Bibliotheken für die Datenanalyse
* JuliaPro: eine betreute Distribution der Sprache Julia mit gängigen wissenschaftlichen und Datenanalyse-Bibliotheken
* Eigenständige Spark-Instanz und Hadoop für einen einzelnen Knoten (HDFS, Yarn)
* JupyterHub: Jupyter Notebook-Server für mehrere Benutzer mit Unterstützung von R, Python, PySpark, Julia-Kernels
* Azure Storage-Explorer
* Azure-Befehlszeilenschnittstelle für die Verwaltung von Azure-Ressourcen
* Machine Learning-Tools
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): Ein schnelles Machine Learning-System, das verschiedene Verfahren unterstützt, z. B. Online, Hashing, Allreduce, Reductions, Learning2Search, Active und Interactive Learning.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): Ein Tool, das eine schnelle und präzise Boosted Tree-Implementierung ermöglicht.
  * [Rattle](https://togaware.com/rattle/): Ein grafisches Tool, das den Einstieg in Datenanalysen und Machine Learning in R vereinfacht.
  * [LightGBM](https://github.com/Microsoft/LightGBM): Ein schnelles, verteiltes, hochleistungsfähiges Gradient Boosting-Framework.
* Azure SDK in Java, Python, node.js, Ruby, PHP
* Bibliotheken in R und Python zur Verwendung in Azure Machine Learning und anderen Azure-Diensten
* Entwicklungstools und -Editoren (RStudio, PyCharm, IntelliJ, Emacs, vim)

Die Durchführung des Data Science-Vorgangs umfasst eine Aufgabensequenz:

1. Suchen, Laden und Vorverarbeiten von Daten
1. Erstellen und Testen von Modellen
1. Bereitstellen der Modelle für die Nutzung in intelligenten Anwendungen

Datenanalysten verwenden verschiedene Tools zum Ausführen dieser Aufgaben. Es kann sehr zeitaufwändig sein, die richtigen Versionen der Software zu finden und diese dann herunterzuladen, zu kompilieren und zu installieren.

Mit der Data Science Virtual Machine für Linux können Sie diesen Aufwand erheblich reduzieren. Nutzen Sie sie, um Ihr Analyseprojekt entscheidend voranzubringen. Sie können in verschiedenen Sprachen arbeiten, z.B. R, Python, SQL, Java und C++. Mit dem in die VM integrierten Azure SDK können Sie Ihre Anwendungen mit verschiedenen Diensten unter Linux für die Microsoft-Cloudplattform erstellen. Außerdem haben Sie Zugriff auf andere Sprachen wie Ruby, Perl, PHP und node.js, die ebenfalls vorinstalliert sind.

Für dieses Data Science VM-Image fallen keine Softwaregebühren an. Sie zahlen nur die Gebühren für die Azure-Hardwarenutzung, die basierend auf der Größe der VM berechnet werden, die Sie bereitstellen. Weitere Informationen zu Computegebühren finden Sie im [Azure Marketplace auf der Seite mit den VM-Preisen](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Andere Versionen der Data Science Virtual Machine

Ein [CentOS](linux-dsvm-intro.md)-Image ist ebenfalls verfügbar, das viele der gleichen Tools bietet wie das Ubuntu-Image. Ein [Windows](provision-vm.md)-Image ist ebenfalls verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie eine Data Science Virtual Machine für Linux erstellen können, benötigen Sie ein Azure-Abonnement. Informationen zum Bezug finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/free/).

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Erstellen Ihrer Data Science Virtual Machine für Linux

Mit den folgenden Schritten erstellen Sie eine Instanz der Data Science Virtual Machine für Linux:

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com/#create/microsoft-dsvm.linux-data-science-vm-ubuntulinuxdsvmubuntu)zur Auflistung der virtuellen Computer. Wenn Sie sich noch nicht bei Ihrem Azure-Konto angemeldet haben, erhalten Sie eine entsprechende Aufforderung. 
1. Klicken Sie (unten auf der Seite) auf **Erstellen**, um den Assistenten aufzurufen.![configure-data-science-vm](./media/dsvm-ubuntu-intro/configure-data-science-virtual-machine.png)
1. Die folgenden Abschnitte enthalten die Eingaben für jeden Schritt des Assistenten (im rechten Teil der obigen Abbildung aufgelistet), mit dem die Microsoft Data Science Virtual Machine erstellt wird. Die zum Konfigurieren der einzelnen Schritte erforderlichen Eingaben sind:

   a. **Grundlagen**:

   * **Name**: Der Name des Data Science-Servers, den Sie erstellen.
   * **VM-Datenträgertyp**: Wählen Sie **SSD Premium** aus, wenn Sie ein SSD (Solid State Drive) bevorzugen. Andernfalls wählen Sie **HDD Standard** aus. 
   * **Benutzername**: Die ID für die erste Kontoanmeldung.
   * **Kennwort**: Das erste Kontokennwort (Sie können anstelle eines Kennworts einen öffentlichen SSH-Schlüssel verwenden).
   * **Abonnement**: Wenn Sie über mehrere Abonnements verfügen, wählen Sie eines aus, über das der Computer erstellt und abgerechnet wird. Sie müssen für dieses Abonnement über Berechtigungen zum Erstellen von Ressourcen verfügen.
   * **Ressourcengruppe**: Sie können eine neue Gruppe erstellen oder eine vorhandene Gruppe verwenden.
   * **Standort**: Wählen Sie das Datencenter aus, das am besten geeignet ist. Normalerweise handelt es sich dabei um das Rechenzentrum, in dem der größte Teil Ihrer Daten gespeichert ist oder das Ihrem physischen Standort am nächsten ist, um den schnellsten Netzwerkzugriff zu erreichen.

   b. **Größe**:

   * Wählen Sie einen Servertyp aus, der die funktionalen Anforderungen und den Kostenrahmen erfüllt. Wählen Sie einen virtuellen Computer der NC- oder ND-Klasse für GPU-basierte VM-Instanzen aus. Auf der Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/) sind die Regionen mit GPUs aufgelistet.

   c. **Einstellungen**:

   * In den meisten Fällen können Sie einfach die Standardeinstellungen verwenden. Bewegen Sie den Mauszeiger über den jeweiligen Informationslink, um Hilfe zu bestimmten Feldern anzuzeigen, falls Sie auch nicht standardmäßige Einstellungen verwenden möchten.

   d. **Zusammenfassung**:

   * Stellen Sie sicher, dass alle eingegebenen Informationen richtig sind. Es wird ein Link zu den Nutzungsbedingungen bereitgestellt. Für die VM gelten keine über die Computekosten für die Servergröße, die Sie im Schritt **Größe** ausgewählt haben, hinausgehenden Kosten. Klicken Sie auf **Erstellen**, um die Bereitstellung zu starten. 

Die Bereitstellung sollte ungefähr 5 Minuten dauern. Der Status der Bereitstellung wird im Azure-Portal angezeigt.

## <a name="how-to-access-the-data-science-virtual-machine-for-linux"></a>Zugreifen auf die Data Science Virtual Machine für Linux

Es gibt drei Methoden, um auf die Ubuntu-DSVM zuzugreifen:

1. SSH für Terminalsitzungen
1. X2Go für grafische Sitzungen
1. JupyterHub und JupyterLab für Jupyter-Notebooks

Sie können Azure Notebooks auch eine Data Science VM zuordnen, um Jupyter-Notebooks auf der VM auszuführen und die Einschränkungen der kostenlosen Dienstebene zu umgehen. Weitere Informationen finden Sie unter [Verwalten und Konfigurieren von Projekten – Computeebene](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).

### <a name="ssh"></a>SSH

Nachdem die VM erstellt wurde, können Sie sich mithilfe von SSH an der VM anmelden. Verwenden Sie dabei die Kontoanmeldeinformationen, die Sie im Abschnitt **Grundlagen** von Schritt 3 für die Textshell-Schnittstelle erstellt haben. Unter Windows können Sie ein SSH-Clienttool wie [PuTTY](https://www.putty.org)herunterladen. Falls Sie einen grafischen Desktop bevorzugen (X Windows System), können Sie die X11-Weiterleitung von PuTTY verwenden oder den X2Go-Client installieren.

> [!NOTE]
> Der X2Go-Client hat bei Tests besser abgeschnitten als die X11-Weiterleitung. Es wird empfohlen, den X2Go-Client als grafische Desktop-Benutzeroberfläche zu nutzen.

### <a name="x2go"></a>X2Go

Die Linux-VM wird mit X2Go-Server bereits bereitgestellt und ist zum Akzeptieren von Clientverbindungen bereit. Führen Sie auf dem Client die folgenden Schritte aus, um eine Verbindung mit dem grafischen Desktop des virtuellen Linux-Computers herzustellen:

1. Laden Sie den X2Go-Client für Ihre Clientplattform von [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient)herunter, und installieren Sie ihn.
1. Führen Sie den X2Go-Client aus, und wählen Sie die Option **Neue Sitzung**aus. Es wird ein Konfigurationsfenster mit mehreren Registerkarten geöffnet. Geben Sie die folgenden Konfigurationsparameter ein:
   * **Registerkarte „Sitzung“:**
     * **Host**: Der Hostname oder die IP-Adresse Ihrer Linux Data Science VM.
     * **Anmeldung**: Der Benutzername für die Linux-VM.
     * **SSH-Port**: Übernehmen Sie den Standardwert 22.
     * **Sitzungstyp**: Ändern Sie den Wert in XFCE. Derzeit unterstützt die Linux-VM nur den XFCE-Desktop.
   * **Registerkarte „Medien“** : Sie können die Soundunterstützung und die Clientausgabe deaktivieren, wenn Sie diese Funktionen nicht benötigen.
   * **Freigegebene Ordner**: Wenn Verzeichnisse von Ihren Clientcomputern auf der Linux-VM bereitgestellt werden sollen, fügen Sie auf dieser Registerkarte die Clientcomputerverzeichnisse hinzu, die Sie für die VM freigeben möchten.

Nachdem Sie sich bei der VM angemeldet haben, indem Sie entweder den SSH-Client oder den grafischen XFCE-Desktop über den X2Go-Client nutzen, können Sie die Tools verwenden, die auf der VM installiert und konfiguriert sind. Auf dem XFCE-Desktop können Sie Anwendungsmenü-Tastenkombinationen und Desktopsymbole für viele Tools anzeigen.

### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub und JupyterLab

Die Ubuntu-DSVM führt [JupyterHub](https://github.com/jupyterhub/jupyterhub), einen Jupyter-Server für mehrere Benutzer, aus. Wenn eine Verbindung hergestellt werden soll, rufen Sie die URL „https:\//IP-Ihrer-VM:8000“ auf Ihrem Laptop oder Computer auf, geben Sie den Benutzernamen und das Kennwort ein, mit dem Sie die VM erstellt haben, und melden Sie sich an. Zum Stöbern und Ausprobieren stehen Ihnen viele Beispielnotebooks zur Verfügung.

JupyterLab, die nächste Generation von Jupyter-Notebooks, und JupyterHub, sind ebenfalls verfügbar. Melden Sie sich für den Zugriff bei JupyterHub an, und rufen Sie dann die URL „https:\//your-vm-ip:8000/user/your-username/lab“ auf. Sie können JupyterLab als Standardnotebookserver festlegen, indem Sie diese Zeile zu */etc/jupyterhub/jupyterhub_config.py* hinzufügen:

```python
c.Spawner.default_url = '/lab'
```

## <a name="tools-installed-on-the-data-science-virtual-machine-for-linux"></a>Auf der Data Science Virtual Machine für Linux installierte Tools

### <a name="deep-learning-libraries"></a>Deep Learning-Bibliotheken

#### <a name="cntk"></a>CNTK

Das Microsoft Cognitive Toolkit ist ein Open Source-Toolkit für Deep Learning. Python-Bindungen sind in der Stammumgebung und der py35 Conda-Umgebung verfügbar. Es enthält auch ein Befehlszeilentool (cntk), das sich bereits im Pfad (PATH) befindet.

Python-Beispiel-Notebooks stehen in JupyterHub zur Verfügung. Um an der Befehlszeile ein einfaches Beispiel auszuführen, führen Sie die folgenden Befehle in der Shell aus:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Weitere Informationen finden Sie auf [GitHub](https://github.com/Microsoft/CNTK) im Abschnitt zu CNTK und im [CNTK-Wiki](https://github.com/Microsoft/CNTK/wiki).

#### <a name="caffe"></a>Caffe

Caffe ist ein Deep Learning-Framework des Berkeley Vision and Learning Center. Es ist unter „/opt/caffe“ verfügbar. Beispiele finden Sie unter „/opt/caffe/examples“.

#### <a name="caffe2"></a>Caffe2

Caffe2 ist ein Deep Learning-Framework von Facebook, das auf Caffe basiert. Es ist in Python 2.7 in der Conda-Stammumgebung verfügbar. Führen Sie den folgenden Befehl über die Shell aus, um es zu aktivieren:

```bash
source /anaconda/bin/activate root
```

Einige Beispiel-Notebooks sind in JupyterHub verfügbar.

#### <a name="h2o"></a>H2O

H2O ist eine schnelle, verteilte In-Memory-Plattform für Machine Learning und Predictive Analytics. Ein Python-Paket ist sowohl in der Stammumgebung als auch der py35 Anaconda-Umgebung installiert. Ein R-Paket ist ebenfalls installiert. H2O kann mithilfe von `java -jar /dsvm/tools/h2o/current/h2o.jar` über die Befehlszeile gestartet werden. Dabei stehen verschiedene konfigurierbare [Befehlszeilenoptionen](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) zur Verfügung. Sie können die Flow-Webbenutzeroberfläche aufrufen, indem Sie für den Einstieg zu http://localhost:54321 navigieren. Beispiel-Notebooks stehen auch in JupyterHub zur Verfügung.

#### <a name="keras"></a>Keras

Keras ist eine allgemeine API für neuronale Netzwerke in Python, die TensorFlow, Microsoft Cognitive Toolkit oder Theano überlagernd ausgeführt werden kann. Sie ist in der Stammumgebung und der py35 Python-Umgebung verfügbar.

#### <a name="mxnet"></a>MXNet

MXNet ist ein für Effizienz und Flexibilität konzipiertes Deep Learning-Framework. Es umfasst R- und Python-Bindungen auf der DSVM. Beispiel-Notebook sind in JupyterHub verfügbar, Beispielcode unter „/dsvm/samples/mxnet“.

#### <a name="nvidia-digits"></a>NVIDIA DIGITS

Das NVIDIA Deep Learning GPU Training System, bekannt als DIGITS, ist ein System zum Vereinfachen häufiger Deep Learning-Aufgaben wie das Verwalten von Daten, das Bestimmen und Trainieren von neuronalen Netzen auf GPU-Systemen sowie das Überwachen der Leistung in Echtzeit mit erweiterten Visualisierungsfunktionen.

DIGITS ist als Dienst mit der Bezeichnung „digits“ verfügbar. Starten Sie den Dienst, und navigieren Sie zu http://localhost:5000, um zu beginnen.

DIGITS ist auch als Python-Modul in der Conda-Stammumgebung installiert.

#### <a name="tensorflow"></a>TensorFlow

TensorFlow ist die Deep Learning-Bibliothek von Google. Es handelt sich dabei um eine Open Source-Softwarebibliothek für numerische Berechnungen unter Verwendung von Datenflussdiagrammen. TensorFlow ist in der py35 Python-Umgebung verfügbar, JupyterHub enthält einige Beispiel-Notebooks.

#### <a name="theano"></a>Theano

Theano ist eine Python-Bibliothek für effiziente numerische Berechnungen. Sie ist in der Stammumgebung und der py35 Python-Umgebung verfügbar. 

#### <a name="torch"></a>Torch

Torch ist ein Framework für wissenschaftliche Berechnungen mit weit reichender Unterstützung für Machine Learning-Algorithmen. Es ist unter „/dsvm/tools/torch“ verfügbar, und die interaktive Sitzung und der LuaRocks-Paket-Manager stehen an der Befehlszeile zur Verfügung. Beispiele sind unter „/dsvm/samples/torch“ verfügbar.

PyTorch ist auch in der Anaconda-Stammumgebung verfügbar. Beispiele sind unter „/dsvm/samples/pytorch“ verfügbar.

### <a name="microsoft-r-server"></a>Microsoft R Server

R ist eine der beliebtesten Sprachen für Datenanalyse und Machine Learning. Wenn Sie R für Ihre Analysen verwenden möchten, können Sie Microsoft R Server (MRS) mit Microsoft R Open (MRO) und Math Kernel Library (MKL) auf der VM verwenden. Mit der MKL werden die mathematischen Vorgänge in Analysealgorithmen optimiert. MRO ist vollständig kompatibel mit CRAN-R, und alle in CRAN veröffentlichten R-Bibliotheken können auf der MRO-Plattform installiert werden. MRS bietet Ihnen Skalierung und Operationalisierung von R-Modelle in Webdiensten. Sie können Ihre R-Programme in einem Standard-Editor wie RStudio, vi oder Emacs bearbeiten. Der Emacs-Editor ist bereits vorinstalliert. Das Emacs-Paket „ESS“ (Emacs Speaks Statistics) vereinfacht die Verwendung von R-Dateien innerhalb des Emacs-Editors.

Um die R-Konsole zu starten, geben Sie in der Shell einfach **R** ein. Über diesen Befehl gelangen Sie zu einer interaktiven Umgebung. Um Ihr R-Programm zu entwickeln, verwenden Sie normalerweise einen Editor wie Emacs oder vi und führen dann die Skripts in R aus. Mit RStudio verfügen Sie über eine vollständige grafische IDE zum Entwickeln Ihres R-Programms.

Es ist auch ein R-Skript vorhanden, mit dem Sie bei Bedarf die [20 beliebtesten R-Pakete](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) installieren können. Dieses Skript kann ausgeführt werden, wenn Sie sich auf der interaktiven R-Benutzeroberfläche befinden. Sie können (wie bereits erwähnt) darauf zugreifen, indem Sie in der Shell **R** eingeben.  

### <a name="python"></a>Python

Anaconda Python wird mit Python 2.7- und 3.5-Umgebungen installiert. Die 2.7-Umgebung wird als _root_ bezeichnet, und die 3.5-Umgebung als _py35_. Diese Distribution enthält die Python-Basisversion sowie etwa 300 der beliebtesten Pakete für Mathematik, Entwicklung und Datenanalysen.

Standardmäßig wird die py35-Umgebung verwendet. So aktivieren Sie die root-Umgebung (2.7)

```bash
source activate root
```

So aktivieren Sie erneut die py35-Umgebung

```bash
source activate py35
```

Geben Sie zum Aufrufen einer interaktiven Python-Sitzung in der Shell einfach **python** ein. 

Installieren Sie mithilfe von ```conda``` oder ```pip``` weitere Python-Bibliotheken. Für Pip aktivieren Sie zuerst die richtige Umgebung, wenn Sie die Voreinstellung nicht wünschen:

```bash
source activate root
pip install <package>
```

Oder geben Sie den vollständigen Pfad zu Pip an:

```bash
/anaconda/bin/pip install <package>
```

Für Conda sollten Sie immer den Umgebungsnamen angeben (_py35_ oder _root_):

```bash
conda install <package> -n py35
```

Wenn Sie sich in einer grafischen Benutzeroberfläche befinden oder die X11-Weiterleitung eingerichtet haben, können Sie **pycharm** eingeben, um die PyCharm Python-IDE zu starten. Sie können die standardmäßigen Text-Editoren verwenden. Außerdem können Sie Spyder nutzen, eine Python-IDE, die als Bündel mit Anaconda Python-Distributionen bereitgestellt wird. Für Spyder wird ein grafischer Desktop oder die X11-Weiterleitung benötigt. Eine Verknüpfung mit Spyder wird auf dem grafischen Desktop bereitgestellt.

### <a name="jupyter-notebook"></a>Jupyter Notebook

Zur Anaconda-Distribution gehört außerdem Jupyter Notebook, eine Umgebung zum Freigeben von Code und Analysen. Auf Jupyter Notebook wird über JupyterHub zugegriffen. Sie melden sich mit Ihrem lokalen Linux-Benutzernamen und dem dazugehörigen Kennwort an.

Der Jupyter Notebook-Server wurde bereits mit Python 2-, Python 3- und R-Kernels vorkonfiguriert. Es gibt ein Desktopsymbol namens „Jupyter Notebook“, um den Browser für den Zugriff auf den Notebook-Server zu starten. Wenn Sie sich per SSH oder X2Go-Client bei der VM angemeldet haben, können Sie auch [https://localhost:8000/](https://localhost:8000/) besuchen, um auf den Jupyter-Notebookserver zuzugreifen.

> [!NOTE]
> Fahren Sie fort, falls Sie Zertifikatwarnungen erhalten.

Sie können von jedem Host aus auf den Jupyter Notebook-Server zugreifen. Geben Sie einfach *https://\<VM-DNS-Name oder -IP-Adresse\>:8000/* ein.

> [!NOTE]
> Port 8000 wird in der Firewall standardmäßig geöffnet, wenn der virtuelle Computer bereitgestellt wird. 

Wir haben einige Beispiel-Notebooks zusammengestellt – eines für Python und eines für R. Der Link zu den Beispielen wird Ihnen auf der Startseite für die Notebooks angezeigt, nachdem Sie sich mit Ihrem lokalen Linux-Benutzernamen und dem dazugehörigen Kennwort bei dem Jupyter Notebook authentifiziert haben. Sie können ein neues Notebook erstellen, indem Sie **Neu** und dann den entsprechenden Sprachkernel wählen. Wenn die Schaltfläche **Neu** nicht angezeigt wird, klicken Sie oben links auf das **Jupyter**-Symbol, um die Startseite des Notebook-Servers zu öffnen.

### <a name="apache-spark-standalone"></a>Apache Spark – eigenständige Instanz

Eine eigenständige Instanz von Apache Spark ist auf der Linux DSVM vorinstalliert, damit Sie Spark-Anwendungen zunächst lokal entwickeln können, bevor Sie diese in großen Clustern testen und bereitstellen. Sie können PySpark-Programme über den Jupyter-Kernel ausführen. Wenn Sie Jupyter öffnen und auf die Schaltfläche **Neu** klicken, wird eine Liste mit verfügbaren Kernels angezeigt. „Spark - Python“ ist der PySpark-Kernel, mit dem Sie Spark-Anwendungen in der Sprache Python erstellen können. Sie können auch eine Python-IDE wie PyCharm oder Spyder verwenden, um Spark-Programme zu erstellen. In dieser eigenständigen Instanz wird der Spark-Stapel innerhalb des aufrufenden Clientprogramms ausgeführt. Dies beschleunigt und vereinfacht das Troubleshooting von Problemen im Vergleich zur Entwicklung in einem Spark-Cluster.

Ein PySpark-Beispiel-Notebook wird auf Jupyter bereitgestellt. Sie finden dieses Notebook im Basisverzeichnis von Jupyter im Verzeichnis „SparkML“ ($HOME/Notebooks/SparkML/PySpark). 

Wenn Sie in R für Spark programmieren, können Sie Microsoft R Server, SparkR oder sparklyr verwenden. 

Vor dem Ausführen im Spark-Kontext in Microsoft R Server müssen Sie einen einmaligen Setup-Schritt ausführen, um eine lokale Hadoop HDFS- und Yarn-Instanz für einen Knoten zu aktivieren. Standardmäßig gilt, dass die Hadoop-Dienste installiert, aber auf der DSVM deaktiviert sind. Um sie zu aktivieren, müssen Sie den folgenden Befehle beim ersten Mal als root ausführen:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Sie können die Hadoop-bezogenen Dienste anhalten, wenn Sie sie nicht benötigen. Führen Sie dazu ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` aus.

Ein Beispiel, in dem gezeigt wird, wie MRS im Remote-Spark-Kontext (dies ist die eigenständige Spark-Instanz auf der DSVM) entwickelt und getestet wird, ist im Verzeichnis */dsvm/samples/MRS* bereitgestellt und verfügbar.

### <a name="ides-and-editors"></a>IDEs und Editoren

Sie haben die Wahl zwischen mehreren Code-Editoren, einschließlich vi/VIM, Emacs, PyCharm, RStudio und IntelliJ. IntelliJ, RStudio und PyCharm sind grafische Editoren, und Sie müssen bei einem grafischen Desktop angemeldet sein, um sie verwenden zu können. Diese Editoren verfügen über Verknüpfungen auf dem Desktop und im Anwendungsmenü, mit denen sie gestartet werden können.

**VIM** und **Emacs** sind textbasierte Editoren. Unter Emacs haben wir ein Add-On-Paket mit dem Namen Emacs Speaks Statistics (ESS) installiert, um die Nutzung von R im Emacs-Editor zu vereinfachen. Weitere Informationen finden Sie unter [ESS](https://ess.r-project.org/).

**LaTeX** wird über das texlive-Paket zusammen mit einem [aucte](https://www.gnu.org/software/auctex/manual/auctex/auctex.html)x-Paket als Emacs-Add-On installiert. Es dient zum Vereinfachen der Erstellung Ihrer LaTeX-Dokumente in Emacs.  

### <a name="databases"></a>Datenbanken

#### <a name="graphical-sql-client"></a>Grafischer SQL-Client

**SQuirrel SQL**, ein grafischer SQL-Client, wurde bereitgestellt, damit Verbindungen mit verschiedenen Datenbanken (z.B. Microsoft SQL Server und MySQL) hergestellt und SQL-Abfragen ausgeführt werden können. Sie können SQuirrel SQL in einer grafischen Desktopsitzung (z. B. mithilfe des X2Go-Clients) über ein Desktopsymbol ausführen, oder über den folgenden Befehl in der Shell:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Vor der ersten Verwendung müssen Sie Ihre Treiber und Datenbankaliase einrichten. Die JDBC-Treiber befinden sich am folgenden Speicherort:

*/usr/share/java/jdbcdrivers*

Weitere Informationen finden Sie unter [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Befehlszeilentools für den Zugriff auf Microsoft SQL Server

Das ODBC-Treiberpaket für SQL Server verfügt auch über zwei Befehlszeilentools:

**bcp**: Mit diesem Hilfsprogramm werden Daten per Massenkopiervorgang zwischen einer Instanz von Microsoft SQL Server und einer Datendatei in einem vom Benutzer angegebenen Format kopiert. Das Hilfsprogramm bcp kann zum Importieren großer Mengen an neuen Zeilen in SQL Server-Tabellen oder zum Exportieren von Daten aus Tabellen in Datendateien verwendet werden. Zum Importieren von Daten in eine Tabelle müssen Sie entweder eine Formatdatei verwenden, die für diese Tabelle erstellt wurde, oder die Struktur der Tabelle und die Arten von Daten verstehen, die für die Tabellenspalten gültig sind.

Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**sqlcmd**: Mit dem Hilfsprogramm sqlcmd können Sie Transact-SQL-Anweisungen, Systemprozeduren und Skriptdateien an der Eingabeaufforderung eingeben. Für dieses Hilfsprogramm wird ODBC verwendet, um Transact-SQL-Batches auszuführen.

Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

> [!NOTE]
> Bei diesem Hilfsprogramm gibt es einige Unterschiede zwischen Linux- und Windows-Plattformen. Ausführliche Informationen dazu finden Sie in der -Dokumentation.

#### <a name="database-access-libraries"></a>Bibliotheken für den Datenbankzugriff

In R und Python sind Bibliotheken für den Zugriff auf Datenbanken verfügbar.

* In R können Sie mit den Paketen **RODBC** und **dplyr** SQL-Anweisungen auf dem Datenbankserver abfragen oder ausführen.
* In Python ermöglicht die Bibliothek **pyodbc** den Datenbankzugriff mit ODBC als zugrunde liegender Schicht.  

### <a name="azure-tools"></a>Azure-Tools

Die folgenden Azure-Tools werden auf dem virtuellen Computer installiert:

* **Azure-Befehlszeilenschnittstelle**: Mit der Azure-Befehlszeilenschnittstelle können Sie Azure-Ressourcen über Shellbefehle erstellen und verwalten. Geben Sie zum Aufrufen der Azure-Tools einfach **azure help**ein. Weitere Informationen finden Sie auf der [Dokumentationsseite zur Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Microsoft Azure Storage-Explorer**: Microsoft Azure Storage-Explorer ist ein grafisches Tool zum Navigieren durch die Objekte, die Sie in Ihrem Azure Storage-Konto gespeichert haben, und zum Hoch- und Herunterladen der Daten in und aus Azure-Blobs. Sie können über das Symbol der Desktopverknüpfung auf den Storage-Explorer zugreifen. Sie können ihn auch über eine Eingabeaufforderung der Shell aufrufen, indem Sie **StorageExplorer**eingeben. Sie müssen über einen X2Go-Client angemeldet sein oder die X11-Weiterleitung eingerichtet haben.
* **Azure-Bibliotheken**: Im Folgenden finden Sie einige der vorinstallierten Bibliotheken.
  
  * **Python**: Die installierten zu Azure gehörenden Bibliotheken in Python sind **azure**, **azureml**, **pydocumentdb** und **pyodbc**. Mit den ersten drei Bibliotheken können Sie auf Azure-Speicherdienste, Azure Machine Learning und Azure Cosmos DB (eine NoSQL-Datenbank in Azure) zugreifen. Mit der vierten Bibliothek, pyodbc (zusammen mit dem Microsoft ODBC-Treiber für SQL Server), können Sie unter Verwendung einer ODBC-Schnittstelle über Python auf SQL Server, Azure SQL-Datenbank und Azure SQL Data Warehouse zugreifen. Geben Sie **pip list** ein, um alle aufgeführten Bibliotheken anzuzeigen. Achten Sie darauf, dass dieser Befehl sowohl in der Python 2.7- als auch in der Python 3.5-Umgebung ausgeführt wird.
  * **R**: Die installierten zu Azure gehörenden Bibliotheken in R sind **AzureML** und **RODBC**.
  * **Java**: Sie finden die Liste mit den Azure Java-Bibliotheken im Verzeichnis **/dsvm/sdk/AzureSDKJava** auf der VM. Die wichtigsten Bibliotheken sind Azure-Speicher- und -Verwaltungs-APIs, Azure Cosmos DB und JDBC-Treiber für SQL Server.  

Sie können über den vorinstallierten Firefox-Browser auf das [Azure-Portal](https://portal.azure.com) zugreifen. Im Azure-Portal können Sie Azure-Ressourcen erstellen, verwalten und überwachen.

### <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning ist ein vollständig verwalteter Clouddienst, mit dem Sie Predictive Analytics-Lösungen erstellen, bereitstellen und freigeben können. Sie erstellen Ihre Experimente und Modelle mit Azure Machine Learning Studio. Sie können mit einem Webbrowser auf der Data Science Virtual Machine darauf zugreifen, indem Sie [Microsoft Azure Machine Learning](https://studio.azureml.net)besuchen.

Nachdem Sie sich bei Azure Machine Learning Studio angemeldet haben, haben Sie Zugriff auf einen Zeichenbereich zum Experimentieren, in dem Sie einen logischen Ablauf für die Machine Learning-Algorithmen erstellen können. Außerdem haben Sie Zugriff auf ein Jupyter Notebook, das in Azure Machine Learning gehostet wird und nahtlos für die Experimente in Machine Learning Studio genutzt werden kann. Operationalisieren Sie die von Ihnen erstellten Machine Learning-Modelle, indem Sie sie mit einer Webdienst-Schnittstelle umschließen. Durch das Operationalisieren von Machine Learning-Modellen können Clients, die in einer beliebigen Sprache geschrieben sind, Vorhersagen aus diesen Modellen aufrufen. Weitere Informationen finden Sie in der [Dokumentation zu Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Sie können Ihre Modelle auch in R oder Python auf der VM erstellen und diese dann in der Produktion in Azure Machine Learning bereitstellen. Wir haben Bibliotheken in R (**AzureML**) und Python (**azureml**) installiert, um diese Funktionalität zu ermöglichen.

Informationen zum Bereitstellen von Modellen in R und Python in Azure Machine Learning finden Sie im Artikel [Zehn Dinge, die Sie mit der Data Science Virtual Machine machen können](vm-do-ten-things.md) (hier insbesondere im Abschnitt „Erstellen von Modellen mit R oder Python und Operationalisieren dieser Modelle durch Verwenden von Azure Machine Learning“).

> [!NOTE]
> Diese Anweisungen wurden für die Windows-Version von Data Science VM geschrieben. Die Informationen zum Bereitstellen von Modellen für Azure Machine Learning gelten allerdings für die Linux-VM.

### <a name="machine-learning-tools"></a>Machine Learning-Tools

Die VM enthält einige Machine Learning-Tools und -Algorithmen, die vorkompiliert und lokal vorinstalliert wurden. Das umfasst:

* **Vowpal Wabbit**: Ein Algorithmus für schnelles Onlinelernen.
* **xgboost**: Ein Tool, das optimierte Boosted Tree-Algorithmen bereitstellt.
* **Rattle**: R-basiertes grafisches Tool zum einfachen Durchsuchen und Modellieren von Daten.
* **Python**: Anaconda Python wird als Paket mit Machine Learning-Algorithmen für Bibliotheken wie Scikit-learn bereitgestellt. Sie können andere Bibliotheken installieren, indem Sie den `pip install` -Befehl verwenden.
* **LightGBM**: Ein schnelles, verteiltes Gradient-Boosted-Hochleistungsframework auf der Grundlage von Entscheidungsstrukturalgorithmen.
* **R**: Für R ist eine umfassende Bibliothek mit Machine Learning-Funktionen verfügbar. Einige Bibliotheken sind vorinstalliert, z. B. lm, glm, randomForest und rpart. Sie können andere Bibliotheken installieren, indem Sie Folgendes ausführen:
  
        install.packages(<lib name>)

Im Folgenden finden Sie einige zusätzliche Informationen zu den ersten drei Machine Learning-Tools in der Liste.

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit ist ein Machine Learning-System, das verschiedene Verfahren einsetzt, wie z.B. Online, Hashing, Allreduce, Reductions, Learning2Search, Active und Interactive Learning.

Verwenden Sie die folgenden Befehle, um das Tool für ein einfaches Beispiel auszuführen:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

In diesem Verzeichnis sind noch weitere, größere Demos enthalten. Informationen zu Vowpal Wabbit finden Sie [in diesem Abschnitt auf GitHub](https://github.com/JohnLangford/vowpal_wabbit) und im [Vowpal Wabbit-Wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>XGBoost

Dies ist eine Bibliothek, die für Boosted (Tree)-Algorithmen entworfen und optimiert wurde. Das Ziel dieser Bibliothek besteht darin, die Rechenleistung von Computern erheblich zu verbessern, um Struktur-Boosting-Vorgänge in großem Umfang zu ermöglichen, die skalierbar, portabel und präzise sind.

Die Bibliothek wird als Befehlszeilentool und als R-Bibliothek bereitgestellt.

Um diese Bibliothek in R zu verwenden, können Sie eine interaktive R-Sitzung starten (indem Sie in der Shell einfach **R** eingeben) und die Bibliothek laden.

Hier ist ein einfaches Beispiel angegeben, das Sie an der Eingabeaufforderung von R ausführen können:

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

Hier sind die Befehle für die Shell zum Ausführen der xgboost-Befehlszeile angegeben:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Eine MODEL-Datei wird in das angegebene Verzeichnis geschrieben. Informationen zu diesem Demonstrationsbeispiel finden Sie [auf GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Weitere Informationen zu xgboost finden Sie auf der [xgboost-Dokumentationsseite](https://xgboost.readthedocs.org/en/latest/) und im zugehörigen [GitHub-Repository](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Rattle

Rattle (**R** **A**nalytical **T**ool **T**o **L**earn **E**asily) verwendet GUI-basierte Durchsuchungs- und Modellierungsvorgänge für Daten. Mit dem Tool werden statistische und visuelle Zusammenfassungen von Daten dargestellt, Daten transformiert, die leicht modelliert werden können, nicht überwachte und überwachte Modelle aus den Daten erstellt, Leistungsdaten von Modellen grafisch dargestellt und neue Datasets bewertet. Außerdem wird R-Code generiert, mit dem die Vorgänge in der UI repliziert werden, die direkt in R ausgeführt oder als Ausgangspunkt für weitere Analysen verwendet werden können.

Um Rattle ausführen zu können, müssen Sie in einer grafischen Desktopsitzung angemeldet sein. Geben Sie am Terminal ```R``` ein, um die R-Umgebung aufzurufen. Geben Sie an der R-Eingabeaufforderung die folgenden Befehle ein:

```R
library(rattle)
rattle()
```

Eine grafische Benutzeroberfläche mit einer Reihe von Registerkarten wird geöffnet. Im Folgenden finden Sie die Schnellstartschritte, die in Rattle erforderlich sind, um ein Beispiel-Wetterdataset zu verwenden und ein Modell zu erstellen. In einigen der folgenden Schritte werden Sie zum automatischen Installieren und Laden einiger erforderlicher R-Pakete aufgefordert, die sich nicht bereits im System befinden.

> [!NOTE]
> Falls Sie für die Installation des Pakets im Systemverzeichnis (Standardeinstellung) keinen Zugriff haben, wird unter Umständen in Ihrem R-Konsolenfenster eine Aufforderung mit der Frage angezeigt, ob Pakete in Ihrer persönlichen Bibliothek installiert werden sollen. Wählen Sie *y* , wenn diese Aufforderungen angezeigt werden.

1. Klicken Sie auf **Ausführen**.
1. Ein Dialogfeld wird geöffnet, in dem Sie gefragt werden, ob Sie das Beispiel-Wetterdataset verwenden möchten. Klicken Sie auf **Yes** , um das Beispiel zu laden.
1. Klicken Sie auf die Registerkarte **Model** .
1. Klicken Sie auf **Execute** , um eine Entscheidungsstruktur zu erstellen.
1. Klicken Sie auf **Draw** , um die Entscheidungsstruktur anzuzeigen.
1. Klicken Sie auf das Optionsfeld **Forest**, und klicken Sie auf **Execute**, um eine Random Forest zu erstellen.
1. Klicken Sie auf die Registerkarte **Evaluate** .
1. Klicken Sie auf das Optionsfeld **Risk**, und klicken Sie auf **Execute**, um zwei Leistungsdarstellungen (Risk [Cumulative]) anzuzeigen.
1. Klicken Sie auf die Registerkarte **Log** , um den generierten R-Code für die obigen Vorgänge anzuzeigen.
   (Aufgrund eines Fehlers in der aktuellen Version von Rattle müssen Sie im Text des Protokolls vor *Export this log ...* ein *#* -Zeichen eingeben.)
1. Klicken Sie auf die Schaltfläche **Export**, um die R-Skriptdatei *weather_script.R* im Basisordner zu speichern.

Sie können Rattle und R jetzt beenden. Als Nächstes können Sie das generierte R-Skript ändern, oder Sie können es unverändert verwenden und bei Bedarf ausführen, um die auf der Rattle-Benutzeroberfläche ausgeführten Schritte zu wiederholen. Dies ist besonders für R-Anfänger eine einfache Möglichkeit, auf einer einfachen grafischen Benutzeroberfläche schnell Analysen und Machine Learning-Vorgänge durchzuführen, während automatisch Code in R zum Ändern bzw. Lernen generiert wird.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich weiter:

* Die exemplarische Vorgehensweise [Data Science auf der Data Science Virtual Machine für Linux](linux-dsvm-walkthrough.md) zeigt Ihnen, wie Sie mehrere allgemeine Data Science-Aufgaben mit der hier bereitgestellten Linux Data Science VM ausführen. 
* Informieren Sie sich über die unterschiedlichen Data Science-Tools auf der Data Science VM, indem Sie die in diesem Artikel beschriebenen Tools ausprobieren. Sie können auch *dsvm-more-info* in der Shell auf dem virtuellen Computer ausführen, um eine grundlegende Einführung und Hinweise auf weitere Informationen zu den Tools auf der VM zu erhalten.  
* Erfahren Sie, wie Sie mithilfe des [Team Data Science-Prozesses](https://aka.ms/tdsp)systematisch vollständige Analyselösungen erstellen.
* Besuchen Sie die [Azure AI Gallery](https://gallery.azure.ai/), um Beispiele zu Machine Learning und zur Datenanalyse zu erhalten, in denen Azure-KI-Dienste verwendet werden.

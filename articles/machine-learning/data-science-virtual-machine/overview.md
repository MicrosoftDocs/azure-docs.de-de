---
title: Einführung in Azure Data Science Virtual Machine für Linux und Windows | Microsoft-Dokumentation
description: Wichtige Analyseszenarien und -komponenten für Windows und Linux Data Science Virtual Machines.
keywords: Data Science-Tools, virtuelle Computer für Data Science, Tools für Data Science, Linux Data Science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/22/2019
ms.author: gokuma
ms.openlocfilehash: 384cb274496670e0b0b5a33e001e78a0babed3f0
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427791"
---
# <a name="introduction-to-azure-data-science-virtual-machine-for-linux-and-windows"></a>Einführung in Azure Data Science Virtual Machine für Linux und Windows

Der virtuelle Computer für Data Science (DSVM) ist ein benutzerdefiniertes VM-Image in der Microsoft Azure-Cloud, das speziell für Data Science konfiguriert wurde. Es hat viele beliebte Data Science und andere Tools vorinstalliert und vorkonfiguriert, damit Sie sofort intelligente Anwendungen für die erweiterte Analyse erstellen können. Er ist unter Windows Server und Linux verfügbar. Wir bieten eine Windows-Edition von DSVM für Windows Server 2016 und 2012. Wir bieten Linux-Editionen von DSVM für Ubuntu 16.04 LTS und CentOS 7.4.

In diesem Thema wird erläutert, welche Möglichkeiten die Data Science VM bietet. Außerdem werden einige der wichtigsten Szenarien für die Verwendung der VM sowie die wichtigsten Features für die Windows- und Linux-Versionen vorgestellt. Darüber hinaus enthält dieser Artikel Anweisungen zum Einstieg in die Verwendung der VMs.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>Was kann ich mit dem virtuellen Computer für Data Science tun?
Das Ziel des virtuellen Computers für Data Science (DSVM) ist, für Datenexperten aller Fähigkeitsebenen und branchenübergreifend eine reibungslose, vorkonfigurierte und vollständig integrierte Data Science-Umgebung bereitzustellen. Statt auf eigene Faust einen vergleichbaren Arbeitsbereich bereitzustellen, können Sie eine DSVM nutzen – was Ihnen Tage oder sogar _Wochen_ bei den mit Installation, Konfiguration und Paketverwaltung einhergehenden Prozessen spart. Nachdem Ihre DSVM zugeordnet wurde, können Sie sofort mit der Arbeit an Ihren Data Science-Projekten beginnen.

Die Data Science-VM ist für das Arbeiten mit einer Vielzahl von Verwendungsszenarien konzipiert und konfiguriert. Sie können Ihre Umgebung vertikal hoch- oder herunterskalieren, wenn sich Ihre Anforderungen ändern. Außerdem können Sie Ihre bevorzugte Sprache zum Programmieren von Data Science-Aufgaben verwenden und weitere Tools installieren, um das System exakt an Ihre Anforderungen anzupassen.

## <a name="key-scenarios"></a>Wichtige Szenarios
In diesem Abschnitt werden einige wichtige Szenarios genannt, für die Data Science-VM bereitgestellt werden kann.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Vorkonfigurierter Analyse-Desktop in der Cloud
Die Data Science-VM stellt eine Basiskonfiguration für Data Science-Teams bereit, die ihre lokalen Desktops mit einem verwalteten Cloud-Desktop ersetzen möchten. Diese Grundlage stellt sicher, dass alle Datenanalysten in einem Team ein konsistentes Setup zum Überprüfen von Experimenten und besserer Zusammenarbeit haben. Sie trägt ferner zu einer Kostenreduzierung durch eine Verringerung des Aufwands für die Systemverwaltung. Dies wiederum führt zu einer Zeitersparnis beim Auswerten, Installieren und Warten der verschiedenen Softwarepakete, die für erweiterte Analysen erforderlich sind.

### <a name="data-science-training-and-education"></a>Data Science-Schulung und -Ausbildung
Ausbilder in Unternehmen und Dozenten, die Data Science-Kurse leiten, stellen in der Regel ein Image eines virtuellen Computers bereit. Auf diese Weise wird sichergestellt, dass für die Kursteilnehmer eine konsistente Umgebung eingerichtet wird und die Beispiele erwartungsgemäß funktionieren. Die Data Science-VM erstellt eine bedarfsgerechte Umgebung mit einem konsistenten Setup, das den Support erleichtert und Inkompatibilitäts-Probleme vermeidet. Wenn diese Umgebungen häufig bereitgestellt werden müssen, insbesondere für kürzere Schulungen, bringt dies erhebliche Vorteile.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Bei Bedarf flexible Kapazität für umfangreiche Projekte
Data Science Hackathons/Wettbewerbe oder umfangreiche Datenmodelle und Auswertungen erfordern skalierte Hardwarekapazität, in der Regel für kurze Zeit. Die Data Science VM kann dazu beitragen, die Data Science-Umgebung bei Bedarf schnell auf skalierten Servern zu replizieren, auf denen Experimente ausgeführt werden können, die leistungsstarke Computingressourcen erfordern.

### <a name="custom-compute-power-for-azure-notebooks"></a>Benutzerdefinierte Computeleistung für Azure Notebooks

[Azure Notebooks](/azure/notebooks/azure-notebooks-overview) ist ein kostenloser gehosteter Dienst zum Entwickeln, Ausführen und Freigeben von Jupyter Notebooks in der Cloud ohne Installation. Die kostenlose Dienstebene ist jedoch auf 4 GB Arbeitsspeicher und 1 GB Daten beschränkt. Um sämtliche Einschränkungen aufzuheben, können Sie ein Notebooks-Projekt an eine Data Science-VM oder eine beliebige andere VM mit einem Jupyter-Server anfügen. Wenn Sie sich mit einem Konto über Azure Active Directory (z. B. einem Unternehmenskonto) bei Azure Notebooks anmelden, zeigt Notebooks automatisch virtuelle Data Science-VMs in allen Abonnements an, die mit diesem Konto verknüpft sind. Weitere Informationen finden Sie unter [Verwalten und Konfigurieren von Projekten](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier) im Abschnitt zum Compute-Tarif.

### <a name="short-term-experimentation-and-evaluation"></a>Kurzfristige Experimente und Auswertungen
Die Data Science-VM kann zur Auswertung oder zum Lernen von Tools wie Microsoft ML Server, SQL Server, Visual Studio-Tools, Jupyter, Deep Learning-/ML-Toolkits und neue Tools, die in der Community beliebt sind, mit minimalem Einrichtungsaufwand verwendet werden. Da die Data Science VM schnell eingerichtet werden kann, kann sie auch in anderen kurzfristigen Szenarios verwendet werden. Dies umfasst z.B. die Replikation veröffentlichter Experimente, das Ausführen von Demos und exemplarischer Vorgehensweisen in Onlinesitzungen sowie Konferenz-Tutorials.

### <a name="deep-learning"></a>Deep Learning
Der virtuelle Computer für Data Science kann zum Modelltraining mit Deep Learning-Algorithmen auf Basis von GPU-Hardware (Grafikprozessoren) verwendet werden. Durch die Skalierungsfunktionen für virtuelle Computer der Azure-Cloud hilft DSVM Ihnen beim Einsatz von GPU-basierter Hardware in der Cloud nach Bedarf. Sie können zu einer GPU-basierten VM wechseln, wenn Sie große Modelle trainieren oder schnelle Berechnungen benötigen, während Sie den gleichen Betriebssystem-Datenträger beibehalten.  Die Windows Server 2016-Edition von DSVM enthält vorinstallierte GPU-Treiber und -Frameworks sowie GPU-Versionen der Deep Learning-Frameworks. Unter der Linux-Edition ist Deep Learning für GPUs sowohl auf CentOS- als auch auf Ubuntu-DSVMs aktiviert. Sie können die Ubuntu-, CentOS- oder Windows 2016-Edition der Data Science VM auf einer Azure-VM ohne GPU-Aktivierung bereitstellen. In diesem Fall werden aber alle Deep Learning-Frameworks auf den CPU-Modus zurückgesetzt.

## <a name="whats-included-in-the-data-science-vm"></a>Was ist in der Data Science-VM enthalten?
Der virtuelle Computer für Data Science hat viele beliebte Data Science- und Deep Learning-Tools bereits installiert und konfiguriert. Außerdem enthält er Tools, die die Nutzung verschiedener Daten- und Analyseprodukte von Azure vereinfachen, z.B. Microsoft ML Server (R, Python) für die Erstellung von Vorhersagemodellen oder SQL Server 2017 für die Untersuchung von Datasets in größerem Umfang. Die Data Science VM umfasst zahlreiche weitere Tools aus der Open Source-Community und von Microsoft, außerdem Beispielcode und Notebooks. Die folgende Tabelle enthält eine Aufzählung und einen Vergleich der wichtigsten Komponenten Windows- und Linux-Editionen des virtuellen Computers für Data Science.


| **Tool**                                                           | **Windows-Edition** | **Linux-Edition** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) mit verbreiteten vorinstallierten Paketen   |J                      | J             |
| [Microsoft ML Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition enthält: <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler): paralleles und verteiltes Hochleistungsframework (R und Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;*   [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) – neue moderne ML-Algorithmen von Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [R- und Python-Operationalisierung](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |J                      | J |
| [Microsoft Office](https://products.office.com/en-us/business/office-365-proplus-business-software) Pro-Plus mit gemeinsamer Aktivierung: Excel, Word und PowerPoint   |J                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2.7 und 3.5 mit beliebten vorinstallierten Paketen    |J                      |J              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) mit beliebten vorinstallierten Paketen                         |J                      |J              |
| Relationale Datenbanken                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Datenbanktools                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * ODBC/JDBC-Treiber| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (Abfrage-Tool), <br /> * bcp, sqlcmd <br /> * ODBC/JDBC-Treiber|
| Skalierbare In-Database-Analyse mit SQL Server-ML-Diensten (R, Python) | J     |N              |
| **[Jupyter-Notebook-Server](https://jupyter.org/) mit folgenden Kernels,**                                  | J     | J |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | J | J |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python | J | J |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | J | J |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | J | J |
|     &nbsp;&nbsp;&nbsp;&nbsp;*   [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (nur Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | J |
| JupyterHub (Notebook-Server für mehrere Benutzer)| N | J |
| JupyterLab (Notebook-Server für mehrere Benutzer) | N | Y (nur Ubuntu) |
| **Entwicklungstools, IDEs und Code-Editoren**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/) mit Git-Plug-In, Azure HDInsight (Hadoop), Data Lake, SQL Server Data Tools, [Node.js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs) und [R Tools für Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) | J | N |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Visual Studio Code](https://code.visualstudio.com/) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Atom](https://atom.io/) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Juno (Julia IDE)](https://junolab.org/)| J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim und Emacs | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git und GitBash | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* .NET Framework | J | N |
| Power BI Desktop | J | N |
| SDKs zum Zugriff auf Azure und Cortana Intelligence Sammlung von Diensten | J | J |
| **Datenverschiebungs- und -verwaltungstools** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure-Speicher-Explorer | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure PowerShell | J | N |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | J | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Blob-FUSE-Treiber](https://github.com/Azure/azure-storage-fuse) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | J | N |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [DocDB-Datenmigrationstool](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | J | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft-Datenverwaltungsgateway:](https://msdn.microsoft.com/library/dn879362.aspx) Verschieben von Daten zwischen lokalen Quellen und der Cloud | J | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Unix/Linux-Befehlszeilenprogramme | J | J |
| [Apache Drill](https://drill.apache.org) für das Durchsuchen von Daten | J | J |
| **Tools für maschinelles Lernen** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Integration mit [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Xgboost](https://github.com/dmlc/xgboost) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Rattle](https://togaware.com/rattle/) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (nur Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CatBoost](https://tech.yandex.com/catboost/) | N | Y (nur Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/), [Sparkling Water](https://www.h2o.ai/sparkling-water/) | N | Y (nur Ubuntu) |
| **Deep Learning-Tools** <br>Alle Tools können mit einer GPU oder CPU verwendet werden. |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Tensorflow](https://www.tensorflow.org/) | J (Windows 2016) | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Horovod](https://github.com/uber/horovod) | N | J (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [MXNet](https://mxnet.io/) | J (Windows 2016) | J|
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Caffe &amp; Caffe2](https://github.com/caffe2/caffe2) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Chainer](https://chainer.org/) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Torch](http://torch.ch/) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Theano](https://github.com/Theano/Theano) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Keras](https://keras.io/)| N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyTorch](https://pytorch.org/)| N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;*   [Nvidia Digits](https://github.com/NVIDIA/DIGITS) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet Model Server](https://github.com/awslabs/mxnet-model-server) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow Serving](https://www.tensorflow.org/serving/) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorRT](https://developer.nvidia.com/tensorrt) | N | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA, cuDNN, NVIDIA-Treiber](https://developer.nvidia.com/cuda-toolkit) | J | J |
| **Big Data Platform (nur Devtest)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* Lokale [Spark](https://spark.apache.org/)-Instanz | J | J |
| &nbsp;&nbsp;&nbsp;&nbsp;* Lokale [Hadoop](https://hadoop.apache.org/) (HDFS, YARN) | N | J |

## <a name="get-started"></a>Erste Schritte

### <a name="windows-data-science-vm"></a>Virtueller Windows-Computer für Data Science
* Weitere Informationen zur Erstellung und Verwendung eines virtuellen Windows-Computers für Data Science (Data Science VM, DSVM) finden Sie unter [Bereitstellen der Data Science Virtual Machine für Windows in Azure](provision-vm.md). Weitere Informationen zum Ausführen verschiedener Aufgaben für Ihr Data Science-Projekt auf der Windows-DSVM finden Sie unter [Zehn Dinge, die Sie mit der Data Science Virtual Machine machen können](vm-do-ten-things.md).

### <a name="linux-data-science-vm"></a>Virtueller Linux-Computer für Data Science
* Weitere Informationen zur Erstellung und Verwendung eines virtuellen Ubuntu-Computers für Data Science finden Sie unter [Bereitstellen der Data Science Virtual Machine für Linux (Ubuntu)](dsvm-ubuntu-intro.md). Weitere Informationen zur Erstellung und Verwendung eines virtuellen CentOS-Computers für Data Science finden Sie unter [Bereitstellen einer Linux CentOS Data Science-VM in Azure](linux-dsvm-intro.md).
* Eine exemplarische Vorgehensweise, die zeigt, wie Sie verschiedene allgemeine Data Science-Aufgaben mit dem virtuellen Linux-Computer (CentOS und Ubuntu) ausführen, finden Sie unter [Data Science auf der Linux Data Science Virtual Machine](linux-dsvm-walkthrough.md).

## <a name="next-steps"></a>Nächste Schritte
[R-Entwicklerleitfaden für Azure](/azure/architecture/data-guide/technology-choices/r-developers-guide)

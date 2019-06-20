---
title: Erstellen einer Windows-Data Science Virtual Machine
titleSuffix: Azure
description: Konfigurieren und erstellen Sie für Analysen und Machine Learning einen virtuellen Computer für Data Science in Azure.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: gokuma
ms.openlocfilehash: 64642da88850e9a0a17173bc50ae9e750cab55a2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235101"
---
# <a name="provision-a-windows-data-science-virtual-machine-on-azure"></a>Bereitstellen einer Data Science Virtual Machine für Windows in Azure

Die Microsoft Windows Data Science Virtual Machine (DSVM) ist ein Windows Server 2016 Virtual Machine (VM)-Image auf Azure, das vorinstalliert und mit Tools für Datenanalyse und Machine Learning konfiguriert wird.

## <a name="included-data-science-tools"></a>Enthaltene Data Science-Tools

Die folgenden Tools sind in einer DSVM enthalten:

* [Python-SDK für Azure Machine Learning Service](../service/index.yml)
* [Microsoft Machine Learning Server Developer Edition](https://docs.microsoft.com/machine-learning-server/index)
* Anaconda Python Distribution
* Jupyter Notebook mit R, Python und PySpark-Kernels
* Microsoft Visual Studio Community
* Microsoft Power BI Desktop
* Microsoft SQL Server 2017 Developer Edition
* Eine eigenständige Apache Spark-Instanz für lokale Entwicklung und Tests
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Machine Learning und Datenanalysetools:
  * Deep Learning-Frameworks – ein umfangreicher Satz von KI-Frameworks sind auf dem virtuellen Computer enthalten: [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet und Keras
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) – ein schnelles Machine Learning-System, das verschiedene Verfahren unterstützt, wie z.B. Online, Hashing, Allreduce, Reductions, Learning2Search sowie Active und Interactive Learning
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/) – ein Tool, das eine schnelle und präzise Boosted Tree-Implementierung ermöglicht
  * [Rattle](https://togaware.com/rattle/) – ein Tool, das die ersten Schritte von Datenanalysen und Machine Learning in R vereinfacht. Es umfasst eine GUI-basierte Datenuntersuchung und -modellierung mit automatischer R-Codegenerierung.
  * [Weka](https://www.cs.waikato.ac.nz/ml/weka/) – eine Software für visuelles Data Mining und Machine Learning in Java.
  * [Apache Drill](https://drill.apache.org/) – eine schemalose SQL-Abfrage-Engine für Apache Hadoop, NoSQL und Cloud Storage Sie unterstützt ODBC- und JDBC-Schnittstellen für NoSQL- und Dateiabfragen mit BI-Standardtools wie Power BI, Microsoft Excel und Tableau.
* Bibliotheken in R und Python zur Verwendung in Azure Machine Learning und anderen Azure-Diensten
* Git, einschließlich Git Bash, für die Arbeit mit Quellcoderepositorys einschließlich GitHub und Azure DevOps. Git stellt mehrere gängige Linux-Befehlszeilenprogramme bereit, auf die sowohl in Git Bash als auch über eine Eingabeaufforderung zugegriffen werden kann. Beispiele sind awk, sed, perl, grep, find, wget und curl.

### <a name="about-data-science"></a>Informationen zu Data Science

Der Data Science-Vorgang umfasst eine Aufgabensequenz:

1. Suchen, Laden und Vorverarbeiten von Daten
1. Kompilieren und testen von Modellen
1. Bereitstellen der Modelle für die Nutzung in intelligenten Anwendungen

Datenanalysten verwenden verschiedene Tools für diese Aufgaben. Es kann zeitaufwändig sein, die richtigen Softwareversionen zu finden und dann herunterzuladen und zu installieren. Die DSVM erspart Zeit, indem sie ein einsatzbereites Image bietet, das mit vorinstallierten und konfigurierten gängigen Tools auf Azure bereitgestellt werden kann.

Die DSVM bringt Ihr Analyseprojekt entscheidend voran. Sie können in verschiedenen Sprachen arbeiten, z. B. R, Python, SQL und C#. Visual Studio bietet eine einfach zu bedienende integrierte Entwicklungsumgebung (IDE) zum Entwickeln und Testen des Codes. Mit dem Azure SDK der VM können Sie Ihre Anwendungen mit verschiedenen Diensten auf der Cloudplattform von Microsoft erstellen.

Für dieses Data Science VM-Image fallen keine Softwaregebühren an. Sie zahlen nur die Azure-Nutzungsgebühren. Diese hängen von der Größe des virtuellen Computers ab, den Sie bereitstellen. Weitere Informationen zu Compute-Gebühren finden Sie auf der Seite [Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) im Abschnitt **Preisübersicht**.

### <a name="other-dsvm-versions"></a>Andere DSVM-Versionen

* Ein [Ubuntu](dsvm-ubuntu-intro.md)-Image. Er verfügt über viele Tools, die denen der DSVM gleichen, sowie einige zusätzliche Deep Learning-Frameworks.
* Ein [Linux CentOS](linux-dsvm-intro.md)-Image.
* Die [Windows Server 2012-Edition](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) der Data Science VM. Einige Tools sind nur in der Windows Server 2016-Edition verfügbar. Ansonsten gilt dieser Artikel auch für die Windows Server 2012-Edition.

## <a name="prerequisite"></a>Voraussetzung

Um eine Microsoft Data Science Virtual Machine zu erstellen, benötigen Sie ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.com/free).

## <a name="create-your-dsvm"></a>Erstellen Ihrer DSVM

So erstellen Sie eine DSVM-Instanz:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)zur Auflistung der virtuellen Computer. Wenn Sie sich noch nicht bei Ihrem Azure-Konto angemeldet haben, erhalten Sie eine entsprechende Aufforderung.
1. Wählen Sie unten auf dem Bildschirm die Option **Erstellen**.

   ![configure-data-science-vm](./media/provision-vm/configure-data-science-virtual-machine.png)

1. Sie müssen die folgenden Informationen eingeben, um die einzelnen Schritte zu konfigurieren, die im rechten Bereich des Screenshot angezeigt werden:

   1. **Grundlagen**:
      * **Name**: der Name der DSVM
      * **VM-Datenträgertyp**: **SSD** oder **HDD**. Wählen Sie für eine GPU-Instanz „NC_v1“, wie NVidia Tesla K80-basiert, **HDD** als Datenträgertyp aus.
      * **Benutzername**: die Administratorkonto-ID
      * **Passwort**: das Kennwort des Administratorkontos
      * **Abonnement**: Wenn Sie über mehrere Abonnements verfügen, wählen Sie eines aus, über das der Computer erstellt und abgerechnet wird.
      * **Ressourcengruppe**. Sie können eine neue Gruppe erstellen oder eine vorhandene Gruppe verwenden.
      * **Standort**. Wählen Sie das Rechenzentrum aus, das am besten geeignet ist. Um den schnellsten Netzwerkzugriff zu erreichen, handelt es sich dabei um das Rechenzentrum, in dem der größte Teil Ihrer Daten gespeichert ist oder das Ihrem physischen Standort am nächsten ist.
   1. **Größe**: Wählen Sie einen Servertyp aus, der die funktionalen Anforderungen und den Kostenrahmen erfüllt. Wählen Sie **Alle anzeigen** aus, um weitere Optionen für VM-Größen anzuzeigen.
   1. **Einstellungen**:  
      * **Managed Disks verwenden**. Wählen Sie **Verwaltet**, wenn Sie möchten, dass Azure die Datenträger für die VM verwaltet. Andernfalls müssen Sie ein neues oder vorhandenes Speicherkonto angeben.  
      * **Weitere Parameter**. Sie können die Standardwerte verwenden. Zeigen Sie mit der Maus auf den jeweiligen Informationslink, um Hilfe zu bestimmten Feldern anzuzeigen, falls Sie auch andere als die Standardwerte verwenden möchten.
   1. **Zusammenfassung**: Stellen Sie sicher, dass alle eingegebenen Informationen richtig sind. Klicken Sie auf **Erstellen**.

> [!NOTE]
> * Für die VM gelten keine über die Computekosten für die Servergröße, die Sie unter **Größe** ausgewählt haben, hinausgehenden Kosten.
> * Die Bereitstellung dauert ungefähr 10 bis 20 Minuten. Den Status Ihrer VM können Sie über das Azure-Portal einsehen.

## <a name="how-to-access-the-dsvm"></a>Zugreifen auf die DSVM

Nachdem die VM erstellt und bereitgestellt wurde, können Sie sich mithilfe von Remotedesktop mit den Anmeldeinformationen des Administratorkontos anmelden, die Sie zuvor im Abschnitt **Grundlagen** erstellt haben. Sie können nun beginnen, die auf der VM installierten und konfigurierten Tools zu verwenden. Für viele der Tools gibt es Desktopsymbole und Kacheln im Startmenü.

Sie können Azure Notebooks auch eine Data Science VM zuordnen, um Jupyter-Notebooks auf der VM auszuführen und die Einschränkungen der kostenlosen Dienstebene zu umgehen. Weitere Informationen finden Sie unter [Verwalten und Konfigurieren von Projekten – Computeebene](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>In der Microsoft Data Science Virtual Machine installierte Tools

Erfahren Sie mehr über die Tools, die bereits auf der DSVM installiert sind:

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer Edition

Sie können Microsoft Enterprise Library für skalierbares R oder Python für Ihre Analysen verwenden, da die Machine Learning Server Developer Edition auf der VM installiert ist. Machine Learning Server, zuvor als Microsoft R Server bekannt, ist eine in großem Maße bereitstellbare Analyseplattform für Unternehmen. Sie ist für R und Python verfügbar. Sie ist zudem skalierbar, wird kommerziell unterstützt und ist sicher.

Machine Learning Server unterstützt verschiedene Aufgaben für Big Data-Statistiken, Vorhersagemodellierung und Machine Learning. Die gesamte Bandbreite der Analytik wird unterstützt: Durchsuchen, Analyse, Visualisierung und Modellierung. Durch die Verwendung und Erweiterung der Open-Source-Sprachen R und Python ist Machine Learning Server kompatibel mit R- und Python-Skripts und -Funktionen. Darüber hinaus ist es auch mit CRAN-, Pip- und Conda-Paketen kompatibel, um Daten auf Unternehmensebene zu analysieren.

Mit der Möglichkeit, Daten parallel und in großen Stücken verarbeiten zu können, hebt Machine Learning Server die Speicherbeschränkungen der Open-Source-Sprache R auf. Somit können Sie Analysen von Daten, deren Größe die des Hauptspeichers bei weitem überschreitet, ausführen. Das auf dem virtuellen Computer befindliche Visual Studio Community Sie enthält die R-Tools für Visual Studio und die Python-Tools für die Visual Studio-Erweiterung (PTVS), die eine vollständige IDE für das Arbeiten mit R oder Python bieten. Auf der VM werden außerdem andere IDEs bereitgestellt, z. B. [RStudio](https://www.rstudio.com) und die [PyCharm Community Edition](https://www.jetbrains.com/pycharm/).

### <a name="python"></a>Python

Für die Entwicklung mit Python wurden die Anaconda Python-Distributionen 2.7 und 3.6 installiert. Diese Distributionen enthalten die Python-Basisversion sowie etwa 300 der beliebtesten Pakete für Mathematik, Entwicklung und Datenanalysen. Sie können PTVS verwenden, das in Visual Studio Community 2017 installiert ist. Oder Sie können eine der mit Anaconda gebündelten IDEs verwenden, z. B. IDLE oder Spyder. Suchen und starten Sie eines dieser Pakete (Windows-Taste+S).

> [!NOTE]
> Um unter Anaconda Python 2.7 auf Python Tools für Visual Studio zu verweisen, müssen Sie für jede Version eine angepasste Umgebung erstellen. Um diese Umgebungspfade in Visual Studio 2017 Community zu erstellen, navigieren Sie zu **Tools**   > **Python Tools** > **Python Environments**. Wählen Sie dann **+ Benutzerdefiniert** aus.

Anaconda Python 3.6 wird unter **C:\Anaconda** installiert. Anaconda Python 2.7wird unter **c:\Anaconda\envs\python2** installiert. Ausführliche Schritte hierzu finden Sie in der [PTVS-Dokumentation](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### <a name="the-jupyter-notebook"></a>Das Jupyter Notebook

Zur Anaconda-Distribution gehört außerdem das Jupyter Notebook, eine Umgebung zum Freigeben von Code und Analysen. Der Jupyter Notebook-Server wurde bereits mit Python 2.7, Python 3.x, PySpark, Julia und R-Kernels konfiguriert. Es ist ein Desktopsymbol mit dem Namen **Jupyter Notebook** vorhanden, um den Jupyter-Server und den Browser für den Zugriff auf den Notebook-Server zu starten.

In den Python- und R-Paketen sind mehrere Beispiele für Notebooks enthalten. Nach dem Zugriff auf Jupyter zeigen die Notebooks, wie man mit den folgenden Technologien arbeitet:

* Machine Learning Server
* SQL Server Machine Learning Services, datenbankinterne Analysen
* Python
* Microsoft Cognitive ToolKit
* Tensorflow
* Andere Azure-Technologien

Der Link zu den Beispielen wird Ihnen auf der Startseite für die Notebooks angezeigt, nachdem Sie sich mit dem im vorherigen Schritt festgelegten Kennwort am Jupyter Notebook authentifiziert haben.

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

Die DSVM enthält Visual Studio Community. Dies ist eine kostenlose Version der beliebten IDE von Microsoft, die Sie zur Evaluierung und für kleine Teams verwenden können. Weitere Informationen finden Sie in den [Lizenzierungsbedingungen](https://www.visualstudio.com/support/legal/mt171547).

Öffnen Sie Visual Studio über das Desktopsymbol oder das **Startmenü**. Suchen Sie nach Programmen (Windows-Taste+S), gefolgt von **Visual Studio**. Von dort aus können Sie Projekte in Sprachen wie C#, Python, R und node.js erstellen. Installierte Plug-Ins erleichtert die Arbeit mit den folgenden Azure-Diensten:

* Azure Data Catalog
* Azure HDInsight Hadoop und Spark
* Azure Data Lake

Es gibt auch ein Plug-In namens **Azure Machine Learning for Visual Studio Code**, das sich nahtlos in Azure Machine Learning integrieren lässt und Sie bei der schnellen Erstellung von KI-Anwendungen unterstützt.

> [!NOTE]
> Es kann eine Meldung angezeigt werden, die besagt, dass der Evaluierungszeitraum abgelaufen ist. Geben Sie die Anmeldeinformationen Ihres Microsoft-Kontos ein. Oder erstellen Sie ein neues kostenloses Konto für den Zugriff auf Visual Studio Community.

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition

Die DSVM wird mit einer Entwicklerversion von SQL Server 2017 mit Machine Learning Services ausgeliefert. Diese SQL Server-Edition ist entweder in R oder Python erhältlich und kann datenbankinterne Analysen ausführen. Machine Learning Services bietet eine Plattform zum Entwickeln und Bereitstellen von intelligenten Anwendungen. Sie können diese Sprachen sowie viele Pakete aus der Community zum Erstellen von Modellen und Generieren von Vorhersagen mithilfe Ihrer SQL Server-Daten nutzen. So ist die Analytics-Komponente nah an den Daten angeordnet, weil Machine Learning Services (datenbankintern) die Sprachen R und Python in SQL Server integriert. Durch diese Integration vermeiden Sie die Kosten und Sicherheitsrisiken, die mit einer Datenverschiebung verbunden sind.

> [!NOTE]
> Die Developer Edition von SQL Server ist nur für Entwicklungs- und Testzwecke bestimmt. Sie benötigen eine Lizenz für die Ausführung in der Produktion.

Sie können auf SQL Server zugreifen, indem Sie Microsoft SQL Server Management Studio starten. Ihr VM-Name wird als **Servername** angegeben. Verwenden Sie die Windows-Authentifizierung, wenn Sie sich als Administrator unter Windows anmelden. Wenn SQL Server Management Studio gestartet wurde, können Sie andere Benutzer erstellen, Datenbanken erstellen, Daten importieren und SQL-Abfragen ausführen.

Um datenbankinterne Analysen mit SQL Machine Learning Services zu ermöglichen, führen Sie nach der Anmeldung als Serveradministrator in SQL Server Management Studio einmal den folgenden Befehl aus:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Ersetzen Sie `%COMPUTERNAME%` durch den VM-Namen.

### <a name="azure"></a>Azure

Mehrere Azure-Tools werden auf dem virtuellen Computer installiert:

* Über eine Desktopverknüpfung gelangen Sie zur Azure SDK-Dokumentation.
* Verwenden Sie **AzCopy** zum Kopieren von Daten in Ihr und aus Ihrem Azure Storage-Konto verwendet. Um die Nutzung anzuzeigen, geben Sie an einer Eingabeaufforderung **Azcopy** ein.
* Verwenden Sie **Azure Storage Explorer**, um die in Ihrem Azure Storage-Konto gespeicherten Objekte zu durchsuchen. Außerdem können Sie Daten in den und aus dem Azure Storage kopieren. Um auf dieses Tool zuzugreifen, geben Sie **Storage-Explorer** in das Feld **Suche** ein. Oder Sie suchen das Tool im Windows-**Startmenü**.
* **Adlcopy** kopiert Daten in Azure Data Lake. Geben Sie **adlcopy** in einer Befehlszeile ein, um die Nutzung anzuzeigen.
* **dtui** kopiert Daten in und aus Azure Cosmos DB, einer NoSQL-Datenbank in der Cloud. Geben Sie an einer Eingabeaufforderung **dtui** ein.
* **Azure Data Factory Integration Runtime** kopiert Daten zwischen lokalen Datenquellen und der Cloud. Es wird in Tools wie Azure Data Factory verwendet.
* Verwenden Sie **Microsoft Azure PowerShell** zur Verwaltung Ihrer Azure-Ressourcen in der PowerShell-Skriptsprache. Es ist auch auf Ihrem virtuellen Computer installiert.

### <a name="power-bi"></a>Power BI

Auf der DSVM wurde **Power BI Desktop** installiert, um Sie bei der Erstellung von Dashboards und Visualisierungen zu unterstützen. Verwenden Sie dieses Tool, um Daten aus verschiedenen Quellen abzurufen, Dashboards und Berichte zu erstellen und diese in der Cloud zu veröffentlichen. Weitere Informationen finden Sie auf der [Power BI](https://powerbi.microsoft.com)-Website. Sie finden den Power BI Desktop im **Startmenü**.

> [!NOTE]
> Für den Zugriff auf Power BI benötigen Sie ein Microsoft Office 365-Konto.

### <a name="azure-machine-learning-service-python-sdk"></a>Python-SDK für Azure Machine Learning Service

Das Azure Machine Learning-SDK für Python wird von Data Scientists und KI-Entwicklern zum Erstellen und Ausführen von Machine Learning-Workflows mit [Azure Machine Learning Service](../service/overview-what-is-azure-ml.md) verwendet. Sie können mit dem Dienst in Jupyter Notebooks oder anderen Python-IDE anhand von Open Source-Frameworks wie TensorFlow und scikit-learn interagieren.

Informationen zum Einstieg mit dem Python-SDK finden Sie unter [Verwenden von Python zum Einstieg in Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

Das Python-SDK ist in Microsoft Data Science Virtual Machine vorinstalliert.

## <a name="more-microsoft-development-tools"></a>Weitere Microsoft-Entwicklungstools

Sie können den [Microsoft-Webplattform-Installer](https://www.microsoft.com/web/downloads/platform.aspx) verwenden, um weitere Microsoft-Entwicklungstools zu suchen und herunterzuladen. Es gibt auch eine Verknüpfung mit dem Tool auf dem Desktop der Microsoft Data Science Virtual Machine.  

## <a name="important-directories-on-the-vm"></a>Wichtige Verzeichnisse auf dem virtuellen Computer

| Item | Verzeichnis |
| --- | --- |
| Jupyter Notebook-Serverkonfigurationen | C:\ProgramData\jupyter |
| Basisverzeichnis für Jupyter Notebook-Beispiele | C:\dsvm\notebooks und c:\benutzer\\<benutzername\>\notebooks |
| Weitere Beispiele | C:\dsvm\samples |
| Anaconda, Standard: Python 3.6 | C:\Anaconda |
| Anaconda Python 2.7-Umgebung | C:\Anaconda\envs\python2 |
| Microsoft Machine Learning Server (eigenständig) Python | C:\Programme\Microsoft\ML Server\PYTHON_SERVER |
| R-Standardinstanz, Machine Learning Server (eigenständig) | C:\Programme\Microsoft\ML Server\R_SERVER |
| SQL Machine Learning Services-Instanzverzeichnis (datenbankintern) | C:\Programme\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Verschiedene Tools | C:\dsvm\tools |

> [!NOTE]
> Unter der Windows Server 2012-Edition der DSVM und der Windows Server 2016-Edition vor März 2018 ist Python 2.7 die Anaconda-Standardumgebung. Die sekundäre Umgebung ist Python 3.5 unter **C:\Anaconda\envs\py35**.

## <a name="next-steps"></a>Nächste Schritte

* Schauen Sie sich die Tools für die Data Science-VM an, indem Sie das Startmenü auswählen **.**
* Erfahren Sie mehr über Azure Machine Learning Service, indem Sie [Was ist Azure Machine Learning Service?](../service/overview-what-is-azure-ml.md) lesen und die verfügbaren [Schnellstarts und Tutorials](../service/index.yml) ausprobieren.
* Navigieren Sie im Explorer zu **C:\Programme\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts**, um Beispiele für die RevoScaleR-Bibliothek in R zu finden, die Datenanalysen auf Unternehmensebene unterstützt.  
* Lesen Sie den Artikel [Zehn Dinge, die Sie mit der Data Science Virtual Machine machen können](https://aka.ms/dsvmtenthings).
* Erfahren Sie, wie Sie mithilfe des [Team Data Science-Prozesses](../team-data-science-process/index.yml)systematisch vollständige Analyselösungen erstellen.
* Besuchen Sie den [Azure KI-Katalog](https://gallery.cortanaintelligence.com), um Beispiele zu Machine Learning und zur Datenanalyse zu erhalten, in denen Azure Machine Learning und dazugehörige Datendienste in Azure verwendet werden. Wir haben auch ein Symbol für diese Galerie im Startmenü und auf dem Desktop der virtuellen Maschine bereitgestellt **.**

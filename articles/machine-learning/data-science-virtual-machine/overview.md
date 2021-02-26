---
title: Worum handelt es sich bei Azure Data Science Virtual Machine?
titleSuffix: Azure Data Science Virtual Machine
description: 'Übersicht über Azure Data Science Virtual Machine: ein einfach zu verwendender virtueller Computer auf der Azure-Cloudplattform mit vorinstallierten und konfigurierten Tools und Bibliotheken für Data Science-Vorgänge.'
keywords: Data Science-Tools, virtuelle Computer für Data Science, Tools für Data Science, Linux Data Science
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 04/02/2020
ms.openlocfilehash: bd2333d89e4d1789b3464606b49f624609ef67d5
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518758"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Was ist Azure Data Science Virtual Machine für Linux und Windows?

Data Science Virtual Machine (DSVM) ist ein angepasstes, auf der Azure-Cloudplattform basierendes VM-Image, das speziell für Data Science erstellt wurde. Es hat viele beliebte Data Science-Tools vorinstalliert und vorkonfiguriert, damit Sie sofort intelligente Anwendungen für die erweiterte Analyse erstellen können.

Die DSVM-Instanz ist für folgende Betriebssysteme verfügbar:

+ Windows Server 2019
+ Ubuntu 18.04 LTS

## <a name="comparison-with-azure-machine-learning"></a>Vergleich mit Azure Machine Learning

DSVM ist ein benutzerdefiniertes VM-Image für Data Science, [Azure Machine Learning](../overview-what-is-azure-ml.md) (AzureML) hingegen ist eine End-to-End-Plattform, die Folgendes umfasst:

+ Vollständig verwaltete Computeressourcen
  + Compute-Instanzen
  + Computecluster für verteilte ML-Aufgaben
  + Rückschlusscluster für die Echtzeitbewertung
+ Datenspeicher (z. B. Blob, ADLS Gen2, SQL DB)
+ Experimentnachverfolgung
+ Modellverwaltung
+ Notebooks
+ Umgebungen (Verwalten von Conda- und R-Abhängigkeiten)
+ Bezeichnungen
+ Pipelines (Automatisieren von Data Science-End-to-End-Workflows)

### <a name="comparison-with-azureml-compute-instances"></a>Vergleich mit AzureML-Compute-Instanzen

Bei [Azure Machine Learning Compute-Instanzen](../concept-compute-instance.md) handelt es sich um ein vollständig konfiguriertes und __verwaltetes__ VM-Image, DSVM hingegen eine __nicht verwaltete__ VM.

Nachfolgend sind die wichtigsten Unterschiede zwischen diesen beiden Produktangeboten beschrieben:


|Funktion |Data Science<br>VM |AzureML<br>Compute-Instanz  | 
|---------|---------|---------|
| Vollständig verwaltet | Nein        | Ja        |
|Sprachunterstützung     |  Python, R, Julia, SQL, C#,<br> Java, Node.js, F#       | Python und R        |
|Betriebssystem     | Ubuntu<br>Windows         |    Ubuntu     |
|Vorkonfigurierte GPU-Option     |  Ja       |    Ja     |
|Option zum Hochskalieren | Ja | Ja |
|SSH-Zugriff    | Ja        |    Ja     |
|RDP-Zugriff    | Ja        |     Nein    |
|Integriert<br>Gehostete Notebooks     |   Nein<br>(zusätzliche Konfigurationsschritte erforderlich)      |      Ja   |
|Integriertes einmaliges Anmelden     | Nein <br>(zusätzliche Konfigurationsschritte erforderlich)         |    Ja     |
|Integrierte Zusammenarbeit     | Nein         | Ja        |
|Vorinstallierte Tools     |  Jupyter (Lab), RStudio Server, VSCode,<br> Visual Studio, PyCharm, Juno,<br>Power BI Desktop, SSMS, <br>Microsoft Office 365, Apache Drill       |     Jupyter (Lab)<br> RStudio Server   |

## <a name="sample-use-cases"></a>Beispiele für Anwendungsfälle

Im Folgenden werden einige gängige Anwendungsfälle für DSVM-Kunden veranschaulicht.

### <a name="short-term-experimentation-and-evaluation"></a>Kurzfristige Experimente und Auswertungen

Mit DSVM können Sie neue Data Science-[Tools](./tools-included.md) testen oder kennenlernen, insbesondere indem Sie einige unserer veröffentlichten [Beispiele und exemplarischen Vorgehensweisen](./dsvm-samples-and-walkthroughs.md) durcharbeiten.

### <a name="deep-learning-with-gpus"></a>Deep Learning mit GPUs

In DSVM können Ihre Trainingsmodelle Deep Learning-Algorithmen auf Hardware verwenden, die auf Grafikprozessoren (Graphics Processing Units, GPUs) basiert. Dank der VM-Skalierungsfunktionen der Azure-Plattform unterstützt Sie DSVM bei der bedarfsgerechten Nutzung von GPU-basierter Hardware in der Cloud. Sie können zu einem GPU-basierten virtuellen Computer wechseln, wenn Sie große Modelle trainieren oder schnelle Berechnungen benötigen, und dabei den gleichen Betriebssystem-Datenträger beibehalten. Für DSVM können Sie beliebige GPU-fähige VM-SKUs der N-Serie auswählen. Beachten Sie, dass GPU-fähige VM-SKUs in kostenlosen Azure-Konten nicht unterstützt werden.

Die Windows-Editionen von DSVM enthalten vorinstallierte GPU-Treiber und -Frameworks sowie GPU-Versionen der Deep Learning-Frameworks. Unter Linux-Editionen ist GPU-basiertes Deep Learning in der DSVM-Instanz mit Ubuntu aktiviert. 

Sie können die Ubuntu- oder Windows-Editionen von DSVM auch auf einem virtuellen Azure-Computer bereitstellen, der nicht auf GPUs basiert. In diesem Fall werden aber alle Deep Learning-Frameworks auf den CPU-Modus zurückgesetzt.

[Informieren Sie sich ausführlicher über die verfügbaren Deep Learning- und KI-Frameworks.](dsvm-tools-deep-learning-frameworks.md)

### <a name="data-science-training-and-education"></a>Data Science-Schulung und -Ausbildung

Ausbilder in Unternehmen und Dozenten, die Data Science-Kurse leiten, stellen in der Regel ein Image eines virtuellen Computers bereit. Durch das Image wird gewährleistet, dass für die Kursteilnehmer eine konsistente Umgebung eingerichtet wird und die Beispiele erwartungsgemäß funktionieren.

DSVM erstellt eine bedarfsgerechte Umgebung mit einem konsistenten Setup, das den Support erleichtert und Inkompatibilitätsprobleme vermeidet. Wenn diese Umgebungen häufig bereitgestellt werden müssen, insbesondere für kürzere Schulungen, bringt dies erhebliche Vorteile.


## <a name="whats-included-on-the-dsvm"></a>Was ist in DSVM enthalten?

Eine vollständige Liste der Tools für Windows- und Linux-DSVM-Instanzen finden Sie [hier](tools-included.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in diesen Artikeln:

+ Windows:
  + [Bereitstellen einer Data Science Virtual Machine für Windows in Azure](provision-vm.md)
  + [Zehn Dinge, die Sie mit Windows Data Science Virtual Machine machen können](vm-do-ten-things.md)

+ Linux:
  + [Bereitstellen der Data Science Virtual Machine für Linux (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Data Science mit einer Linux Data Science Virtual Machine in Azure](linux-dsvm-walkthrough.md)
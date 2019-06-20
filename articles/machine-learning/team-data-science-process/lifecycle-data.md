---
title: Datenerfassung und -auswertung des Team Data Science-Prozesses
description: Die Ziele, Aufgaben und Projektleistungen für die Phase der Datenerfassung und -auswertung Ihrer Data Science-Projekte
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e29f36897dd52fcb09456768a799209a385d74fe
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60303514"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Phase „Datenerfassung und -auswertung“ des Team Data Science-Prozesses

In diesem Artikel werden die Ziele, Aufgaben und Projektleistungen in Zusammenhang mit der Phase der Datenerfassung und -auswertung des Team Data Science-Prozesses (TDSP) behandelt. Dieser Prozess bietet einen empfohlenen Lebenszyklus, mit dem Sie Ihre Data Science-Projekte strukturieren können. Der Lebenszyklus beschreibt die wichtigsten Phasen, die Projekte typischerweise, oft iterativ, durchlaufen:

   1. **Geschäftliche Aspekte**
   2. **Datenerfassung und -auswertung**
   3. **Modellierung**
   4. **Bereitstellung**
   5. **Kundenakzeptanz**

Dies ist eine visuelle Darstellung des TDSP-Lebenszyklus: 

![TDSP-Lebenszyklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Ziele
* Erstellen Sie ein leeres, hochwertiges DataSet mit bekannter Beziehung zu den Zielvariablen. Suchen Sie das DataSet in der entsprechenden Analyseumgebung, damit Sie mit dem Modellieren beginnen können.
* Entwickeln Sie eine Lösungsarchitektur der Datenpipeline, die die Daten regelmäßig aktualisiert und bewertet.

## <a name="how-to-do-it"></a>Vorgehensweise
In dieser Phase werden drei Hauptaufgaben durchgeführt:

   * **Erfassen der Daten** in der Zielanalyseumgebung.
   * **Untersuchen der Daten**, um zu ermitteln, ob die Datenqualität für die Beantwortung der Frage ausreicht. 
   * **Einrichten einer Datenpipeline**, um neue oder regelmäßig aktualisierte Daten bewerten zu können.

### <a name="ingest-the-data"></a>Erfassen der Daten
Richten Sie den Prozess so ein, dass die Daten von den Quellspeicherorten an die Zielspeicherorte verschoben werden, an denen Analysevorgänge wie das Trainieren oder das Erstellen von Vorhersagen ausgeführt werden. Technische Details und Optionen zum Verschieben der Daten mit den verschiedenen Azure-Datendiensten finden Sie unter [Laden von Daten in Speicherumgebungen für Analysen](ingest-data.md). 

### <a name="explore-the-data"></a>Untersuchen der Daten
Bevor Sie Modelle trainieren können, müssen Sie ein gutes Verständnis der Daten entwickeln. DataSets sind in der Praxis häufig überladen oder weisen fehlende Werte oder verschiedene andere Diskrepanzen auf. Sie können die Datenzusammenfassung und -visualisierung verwenden, um die Qualität Ihrer Daten zu überprüfen und die Informationen bereitzustellen, die vor der Modellerstellung zum Verarbeiten der Daten erforderlich sind. Dieser Prozess ist häufig iterativ.

TDSP verfügt über das automatisierte Hilfsprogramm [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), mit dem Daten visualisiert und Berichte zur Datenzusammenfassung visualisiert werden können. Es wird empfohlen, mit IDEAR zu beginnen, um die Daten zu untersuchen. Auf diese Weise können Sie interaktiv ohne Codierung ein erstes Verständnis der Daten entwickeln. Anschließend können Sie benutzerdefinierten Code für die Datenauswertung und -visualisierung schreiben. Eine Anleitung zum Bereinigen der Daten finden Sie unter [Aufgaben zur Vorbereitung von Daten für erweitertes Machine Learning](prepare-data.md).  

Wenn Sie mit der Qualität der bereinigten Daten zufrieden sind, versuchen Sie im nächsten Schritt, die den Daten zugrunde liegenden Muster besser zu verstehen. Dadurch können Sie ein geeignetes Vorhersagemodell für Ihr Ziel auswählen und entwickeln. Suchen Sie nach Belegen für die Beziehungen der Daten mit dem Ziel. Überprüfen Sie, ob ausreichend Daten für die nächsten Modellierungsschritte vorhanden sind. Auch dieser Prozess ist häufig iterativ. Unter Umständen müssen Sie nach neuen Datenquellen mit genaueren bzw. relevanteren Daten suchen, um das DataSet zu erweitern, das im vorherigen Schritt ursprünglich identifiziert wurde. 

### <a name="set-up-a-data-pipeline"></a>Einrichten einer Datenpipeline
Zusätzlich zur ersten Erfassung und Bereinigung der Daten müssen Sie normalerweise einen Prozess einrichten, um neue Daten zu bewerten oder die Daten im Rahmen des ständigen Lernprozesses regelmäßig zu aktualisieren. Dazu richten Sie eine Datenpipeline oder einen Workflow ein. Der Artikel [Verschieben von Daten aus einer lokalen SQL Server-Instanz nach Azure SQL-Datenbank mit Azure Data Factory](move-sql-azure-adf.md) enthält ein Beispiel für das Einrichten einer Pipeline mit [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

In dieser Phase entwickeln Sie eine Lösungsarchitektur der Datenpipeline. Sie entwickeln die Pipeline parallel mit der nächsten Phase des Data Science-Projekts. Abhängig von Ihren geschäftlichen Anforderungen und den Einschränkungen Ihrer vorhandenen Systeme, in die diese Lösung integriert wird, ist die Pipeline: 

   * Batch-basiert
   * Gestreamt oder in Echtzeit 
   * Hybrid 

## <a name="artifacts"></a>Artefakte
Hier sind die Ergebnisse dieser Phase angegeben:

   * [Bericht zur Datenqualität](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): Dieser Bericht enthält Datenzusammenfassungen, Beziehungen zwischen den einzelnen Attributen und Zielen, die Rangfolge der Variablen usw. Mit dem in TDSP enthaltenen Tool [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) können Sie diesen Bericht für alle DataSets in Tabellenform, z.B. als CSV-Datei oder relationale Tabelle, schnell erstellen. 
   * **Lösungsarchitektur**: Dies kann ein Diagramm oder eine Beschreibung Ihrer Datenpipeline sein, die zum Ausführen von Bewertungen oder Vorhersagen für neue Daten nach dem Erstellen eines Modells verwendet wird. Die Pipeline zum erneuten Trainieren Ihres Modells basierend auf den neuen Daten ist ebenfalls enthalten. Speichern Sie das Dokument im Verzeichnis [Project](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project), wenn Sie die Vorlage für die TDSP-Verzeichnisstruktur verwenden.
   * **Prüfpunktentscheidung**: Bevor Sie mit der vollständigen Featureentwicklung und Modellerstellung beginnen, können Sie das Projekt erneut auswerten, um zu ermitteln, ob Daten in ausreichender Menge vorhanden sind. Es kann beispielsweise sein, dass Sie bereit zum Fortfahren sind oder mehr Daten erfassen müssen oder das Projekt beenden müssen, weil für die Beantwortung der Frage keine Daten zur Verfügung stehen.

## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie Links zu jedem Schritt im Lebenszyklus des TDSP:

   1. [Geschäftliche Aspekte](lifecycle-business-understanding.md)
   2. [Datenerfassung und -auswertung](lifecycle-data.md)
   3. [Modellierung](lifecycle-modeling.md)
   4. [Bereitstellung](lifecycle-deployment.md)
   5. [Kundenakzeptanz](lifecycle-acceptance.md)

Vollständige exemplarische Vorgehensweisen, in denen sämtliche Schritte im Prozess für bestimmte Szenarien gezeigt werden, sind ebenfalls verfügbar. Der Artikel mit [exemplarischen Vorgehensweisen](walkthroughs.md) enthält eine Liste der Szenarien mit Links und Kurzbeschreibungen. Die exemplarischen Vorgehensweisen zeigen auch, wie Cloud- und lokale Tools sowie Dienste in einem Workflow oder einer Pipeline zum Erstellen einer intelligenten Anwendung kombiniert werden. 

Beispiele für die Ausführung der Schritte in TDSPs mit Azure Machine Learning Studio finden Sie unter [Verwenden des TDSP mit Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data).

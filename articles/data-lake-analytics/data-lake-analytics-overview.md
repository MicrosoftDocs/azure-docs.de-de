---
title: Azure Data Lake Analytics – Übersicht
description: Mit Data Lake Analytics können Sie umfassende Einblicke in Ihre Clouddaten gewinnen und so Ihr Unternehmen voranbringen.
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: overview
ms.date: 06/23/2017
ms.openlocfilehash: 4a6ef2821080982e2b34108703bd80574443244a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445519"
---
# <a name="what-is-azure-data-lake-analytics"></a>Was ist Azure Data Lake Analytics?

Azure Data Lake Analytics ist ein bedarfsgesteuerter Dienst für Analyseaufträge zum Vereinfachen von Big Data-Vorgängen. Anstatt sich der Bereitstellung, Konfiguration und Optimierung von Hardware zu widmen, schreiben Sie Abfragen, mit denen Sie Ihre Daten transformieren und nützliche Einblicke erhalten. Der Analysedienst ist in der Lage, umgehend Aufträge jeglicher Größenordnung zu verarbeiten. Wählen Sie dazu die jeweils erforderliche Ressourcenkapazität aus. Da Sie nur für die Leistung bezahlen, die während der Ausführung Ihres Auftrags tatsächlich in Anspruch genommen wurde, ist dies eine überaus kosteneffektive Lösung. 

## <a name="azure-data-lake-analytics-recent-update-information"></a>Informationen zu aktuellen Änderungen für Azure Data Lake Analytics

Der Azure Data Lake Analytics-Dienst wird in unregelmäßigen Abständen für einen bestimmten Zweck aktualisiert. Wir bieten mit Komponentenaktualisierungen, Betavorschauversionen für Komponenten usw. weiterhin Unterstützung für diesen Dienst. 

- Allgemeine Informationen zu aktuellen Änderungen finden Sie unter [Neuerungen in Data Lake Analytics](data-lake-analytics-whats-new.md).
- Einzelheiten zu aktuellen Änderungen finden Sie in den [Versionshinweisen zu Azure Data Lake Analytics](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes).

## <a name="dynamic-scaling"></a>Dynamische Skalierung
  
Data Lake Analytics stellt Ressourcen dynamisch bereit und bietet Ihnen die Möglichkeit, Daten im Tera- oder Petabytebereich zu analysieren. Sie bezahlen nur für die Verarbeitungsleistung, die Sie tatsächlich genutzt haben. Außerdem sind keine Codeänderungen notwendig, wenn Sie die Menge an gespeicherten Daten oder die genutzten Computeressourcen erhöhen oder verringern. 

## <a name="develop-faster-debug-and-optimize-smarter-using-familiar-tools"></a>Schnelleres Entwickeln, Debuggen und Optimieren von Code mithilfe vertrauter Tools
  
Data Lake Analytics ist umfassend in Visual Studio integriert. Sie können Ihren Code mit vertrauten Tools ausführen, debuggen und optimieren. Mithilfe von Visualisierungen Ihrer U-SQL-Aufträge können Sie ermitteln, wie Ihr Code bei der Skalierung ausgeführt wird. Anhand dieser Einblicke lassen sich Leistungsengpässe leicht ermitteln und Kosten optimieren.

## <a name="u-sql-simple-and-familiar-powerful-and-extensible"></a>U-SQL: einfach und vertraut, leistungsstark und erweiterbar
  
Data Lake Analytics enthält U-SQL, eine Abfragesprache, bei der die bekannte, einfache deklarative SQL-Sprache mit den Ausdrücken von C# kombiniert wird. Die U-SQL-Sprache verwendet die gleiche verteilte Runtime, auf der auch der interne Data Lake im Exabytebereich von Microsoft beruht. Jetzt können SQL- und .NET-Entwickler zum Verarbeiten und Analysieren ihrer Daten auf die Fähigkeiten und Kenntnisse zurückgreifen, über die sie bereits verfügen.

## <a name="integrates-seamlessly-with-your-it-investments"></a>Nahtlose Integration in Ihre vorhandenen IT-Systeme
  
Data Lake Analytics nutzt Ihre vorhandenen IT-Systeme für Identitäten, Verwaltung und Sicherheit. Dieser Ansatz trägt zur Vereinfachung von Datengovernance und zur problemlosen Erweiterung aktueller Datenanwendungen dar. Data Lake Analytics ist für die Benutzerverwaltung und Berechtigungsvergabe in Active Directory integriert und enthält Überwachungs- und Auditierungsfunktionen.

## <a name="affordable-and-cost-effective"></a>Erschwinglich und kosteneffektiv

Data Lake Analytics ist eine kosteneffektive Lösung für die Ausführung von Big Data-Workloads. Die Bezahlung erfolgt bei Datenverarbeitung pro Auftrag. Sie benötigen weder Hardware noch Lizenzen oder dienstspezifische Supportvereinbarungen. Wenn Aufträge gestartet oder beendet werden, wird das System automatisch zentral hoch- oder herunterskaliert. So bezahlen Sie stets nur für die tatsächlich in Anspruch genommene Ressourcenmenge. [Weitere Informationen zu Kostenkontrolle und -einsparung](https://aka.ms/adlasavemoney).

## <a name="works-with-all-your-azure-data"></a>Funktioniert mit allen Ihren Daten in Azure
  
Data Lake Analytics kann mit Azure Data Lake Storage verwendet werden, um die höchstmögliche Leistung, den höchsten Durchsatz und optimale Parallelisierung zu erzielen, und ist mit Azure Storage-Blobs, Azure SQL-Datenbank und Azure Synapse Analytics kompatibel.


## <a name="next-steps"></a>Nächste Schritte

* Informationen zu den aktuellen Änderungen von Azure Data Lake Analytics finden Sie unter [Neuerungen in Data Lake Analytics](data-lake-analytics-whats-new.md).
* Erste Schritte mit Data Lake Analytics mithilfe [des Azure-Portals](data-lake-analytics-get-started-portal.md) | [von Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [der CLI](data-lake-analytics-get-started-cli.md)
* Verwalten von Azure Data Lake Analytics mithilfe [des Azure-Portals](data-lake-analytics-manage-use-portal.md) | [von Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [von CLI](data-lake-analytics-manage-use-cli.md) | [des Azure .NET-SDKs](data-lake-analytics-manage-use-dotnet-sdk.md) | [von Node.js](data-lake-analytics-manage-use-nodejs.md)
* [Kostenkontrolle und -einsparung mit Data Lake Analytics](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c)

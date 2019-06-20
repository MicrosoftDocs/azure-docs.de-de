---
title: Lokales Debuggen von Azure Data Lake Analytics-Code
description: Erfahren Sie, wie Sie U-SQL-Aufträge mithilfe von Azure Data Lake Tools für Visual Studio auf der lokalen Arbeitsstation debuggen.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 0827311218202de447e5cf27356e00c4da020e94
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61472990"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Lokales Debuggen von Azure Data Lake Analytics-Code

Sie können Azure Data Lake Tools für Visual Studio zum Ausführen und Debuggen von Azure Data Lake Analytics-Code auf der lokalen Arbeitsstation verwenden, so wie dies auch im Azure Data Lake Analytics-Dienst möglich ist.

Erfahren Sie mehr zum [Ausführen von U-SQL-Skripts auf dem lokalen Computer](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Lokales Debuggen von Skripts und C#-Assemblys

Sie können C#-Assemblys debuggen, ohne sie zu senden und beim Azure Data Lake Analytics-Dienst zu registrieren. Sie können sowohl in der CodeBehind-Datei als auch in einem C#-Projekt, auf das verwiesen wird, Breakpoints festlegen.

### <a name="debug-local-code-in-a-code-behind-file"></a>Debuggen von lokalem Code in einer CodeBehind-Datei

1. Legen Sie Breakpoints in der CodeBehind-Datei fest.
2. Drücken Sie **F5**, um das Skript lokal zu debuggen.

> [!NOTE]
   > Das folgende Verfahren funktioniert nur in Visual Studio 2015. In älteren Versionen von Visual Studio müssen Sie eventuell die **PDB**-Dateien manuell hinzufügen.  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Debuggen von lokalem Code in einem referenzierten C#-Projekt

1. Erstellen Sie ein C#-Assemblyprojekt, und erstellen Sie es, um die **DLL**-Ausgabedatei zu generieren.
2. Registrieren Sie die **DLL**-Datei mithilfe einer U-SQL-Anweisung:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Legen Sie Haltepunkte im C#-Code fest.
4. Drücken Sie **F5**, um das Skript mit Verweis auf die C#-**DLL**-Datei lokal zu debuggen.


## <a name="next-steps"></a>Nächste Schritte

- Ein Beispiel für eine komplexere Abfrage finden Sie unter [Analysieren von Websiteprotokollen mit Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Informationen zum Anzeigen von Auftragsdetails finden Sie unter [Verwenden des Auftragsbrowsers und der Auftragsansicht für Azure Data Lake Analytics-Aufträge](data-lake-analytics-data-lake-tools-view-jobs.md).
- Weitere Informationen zur Verwendung der Scheitelpunktausführungsansicht finden Sie unter [Verwenden der Scheitelpunktausführungsansicht in Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

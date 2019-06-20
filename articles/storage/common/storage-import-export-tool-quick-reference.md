---
title: Kurzübersicht über Befehle des Azure Import/Export-Tools für Importaufträge | Microsoft-Dokumentation
description: Befehlsreferenz für Befehle des Azure Import/Export-Tools, die häufig für Importaufträge verwendet werden.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 7d21ab42188d5b8d6cb8c179a28d1b5bee822f05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478418"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Kurzübersicht über häufig verwendete Befehle für Importaufträge

Dieser Artikel enthält eine Kurzübersicht über einige häufig verwendete Befehle. Ausführliche Informationen zur Verwendung finden Sie unter [Vorbereiten von Festplatten für einen Importauftrag](../storage-import-export-tool-preparing-hard-drives-import.md).

## <a name="first-session"></a>Erste Sitzung

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

## <a name="second-session"></a>Zweite Sitzung

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

## <a name="abort-latest-session"></a>Abbruch der aktuellen Sitzung

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

## <a name="resume-latest-interrupted-session"></a>Fortsetzen der aktuellen unterbrochenen Sitzung

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

## <a name="add-drives-to-latest-session"></a>Hinzufügen von Laufwerken zur aktuellen Sitzung

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>Nächste Schritte

* [Beispielworkflow für die Vorbereitung von Festplatten für einen Importauftrag](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)

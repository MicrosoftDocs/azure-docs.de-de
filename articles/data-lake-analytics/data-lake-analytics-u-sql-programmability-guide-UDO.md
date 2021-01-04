---
title: U-SQL-Programmierbarkeitshandbuch für UDOs für Azure Data Lake
description: Hier finden Sie Informationen zur Programmierbarkeit von U-SQL-UDOs, um in Azure Data Lake Analytics gute U-SQL-Skripts zu erstellen.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 02360c68e5e830ceee69075fd5532b126d85bec2
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512448"
---
# <a name="u-sql-user-defined-objects-overview"></a>U-SQL: Benutzerdefinierte Objekte: Übersicht


## <a name="u-sql-user-defined-objects-udo"></a>U-SQL: Benutzerdefinierte Objekte: UDO
Mit U-SQL können Sie angepasste Programmierbarkeitsobjekte – so genannte benutzerdefinierte Objekte (User-Defined Objects ,UDOs) – definieren.

Im Anschluss finden Sie eine Liste mit UDOs in U-SQL:

* Benutzerdefinierte Extraktoren
    * Zeilenweise Extraktion
    * Wird verwendet, um die Datenextraktion aus benutzerdefinierten strukturierten Dateien zu implementieren.

* Benutzerdefinierte Outputter
    * Zeilenweise Ausgabe
    * Wird zur Ausgabe benutzerdefinierter Datentypen oder Dateiformate verwendet.

* Benutzerdefinierte Prozessoren
    * Eine Zeile nehmen und eine Zeile erzeugen
    * Wird verwendet, um die Anzahl der Spalten zu verringern oder eine neue Spalte mit Werten zu erzeugen, die vom vorhandenen Spaltensatz abgeleitet werden.

* Benutzerdefinierte Applier
    * Eine Zeile nehmen und 0 bis n Zeilen erzeugen
    * Wird mit OUTER/CROSS APPLY verwendet.

* Benutzerdefinierte Combiner
    * Kombiniert Rowsets – benutzerdefinierte JOINs.

* Benutzerdefinierte Reducer
    * N Zeilen nehmen und eine Zeile erzeugen
    * Wird verwendet, um die Zeilenanzahl zu verringern.

UDOs werden im U-SQL-Skript in der Regel explizit als Teil der folgenden U-SQL-Anweisungen aufgerufen:

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

> [!NOTE]  
> UDOs können maximal 0,5 GB Arbeitsspeicher beanspruchen.  Diese Beschränkung des Arbeitsspeichers gilt nicht für lokale Ausführungen.

## <a name="next-steps"></a>Nächste Schritte
* [Übersicht über das U-SQL-Programmierbarkeitshandbuch](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL-Programmierbarkeitshandbuch: UDTs und UDAGGs](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
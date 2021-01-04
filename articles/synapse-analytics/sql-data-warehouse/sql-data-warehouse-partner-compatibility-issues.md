---
title: Kompatibilitätsprobleme mit Drittanbieteranwendungen und Azure Synapse Analytics
description: Beschreibt bekannte Probleme, die Anwendungen von Drittanbietern möglicherweise mit Azure Synapse haben können.
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: ''
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: 861b963922d64926266b42a23f85e9dc30fda7a3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95903983"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>Kompatibilitätsprobleme mit Drittanbieteranwendungen und Azure Synapse Analytics

Anwendungen, die für SQL Server erstellt wurden, arbeiten nahtlos mit dedizierten SQL-Pools von Azure Synapse. In manchen Fällen sind jedoch Features und Sprachelemente, die häufig in SQL Server verwendet werden, in Azure Synapse möglicherweise nicht verfügbar, oder sie verhalten sich anders.

In diesem Artikel werden bekannte Probleme aufgelistet, die bei der Verwendung von Drittanbieteranwendungen mit Azure Synapse Analytics auftreten können. 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Tableau-Fehler: „Fehler beim Ausführen einer Transaktion. Es wurde keine entsprechende Transaktion gefunden.“

Ab Version 10.0.11038.0 von dedizierter SQL-Pool in Azure Synapse können einige Tableau-Abfragen, die gespeicherte Prozeduren aufrufen, mit der folgenden Fehlermeldung fehlschlagen: „ **[Microsoft][ODBC Driver 17 for SQL Server][SQL Server]111214; Der Versuch, eine Transaktion abzuschließen, ist fehlgeschlagen. Es wurde keine entsprechende Transaktion gefunden.** “

### <a name="cause"></a>Ursache

Dies ist ein Problem im dedizierten Azure Synapse-SQL-Pool, der durch die Einführung neuer gespeicherter Systemprozeduren verursacht wird, die automatisch von den ODBC- und JDBC-Treibern aufgerufen werden. Eine dieser gespeicherten Systemprozeduren kann bewirken, dass geöffnete Transaktionen abgebrochen werden, wenn ihre Ausführung fehlschlägt. Dieses Problem kann in Abhängigkeit von der Logik der Clientanwendung auftreten.

### <a name="solution"></a>Lösung
Kunden, bei denen dieses Problem auftritt, wenn sie Tableau verwenden, das mit dedizierten SQL-Pools in Azure Synapse verbunden ist, sollten FMTONLY in der SQL-Verbindung auf JA festlegen. Für Tableau Desktop und Tableau Server sollten Sie eine TDC-Datei (Anpassung der Datenquelle für Tableau) verwenden, um sicherzustellen, dass Tableau diesen Parameter an den Treiber übergibt.  

> [!NOTE] 
> Microsoft stellt keine Unterstützung für Drittanbietertools zur Verfügung. Obwohl wir getestet haben, dass diese Lösung mit Tableau Desktop 2020.3.2 funktioniert, sollten Sie diese Problemumgehung nach eigenem Ermessen verwenden.
>

* [Informationen, wie Sie globale Anpassungen mit einer TDC-Datei bei Tableau Desktop durchführen, finden Sie in der Tableau Desktop-Dokumentation.](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [Informationen, wie Sie globale Anpassungen mit einer TDC-Datei bei Tableau Server durchführen, finden Sie unter „Verwenden einer .TDC-Datei mit Tableau Server“.](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

Das folgende Beispiel zeigt eine Tableau TDC-Datei, die den Parameter „FMTONLY=Yes“ an die SQL-Verbindungszeichenfolge übergibt:

```json
<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
Weitere Informationen zur Verwendung von TDC-Dateien erhalten Sie vom Tableau-Support. 

## <a name="see-also"></a>Weitere Informationen

* [T-SQL-Sprachelemente für dedizierten SQL-Pool in Azure Synapse Analytics.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-reference-tsql-language-elements?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [T-SQL-Anweisungen, die vom dedizierten SQL-Pool in Azure Synapse Analytics unterstützt werden.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-reference-tsql-statements)

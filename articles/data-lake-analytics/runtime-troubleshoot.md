---
title: Problembehandlung für U-SQL-Laufzeitfehler in Azure Data Lake Analytics
description: Erfahren Sie, wie Sie die Problembehandlung für U-SQL-Laufzeitfehler durchführen.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.openlocfilehash: 41b7c80c85331f288343351749e6b2e5292b30c6
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241606"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Problembehandlung für U-SQL-Laufzeitfehler aufgrund von Laufzeitänderungen

Mit der Azure Data Lake-U-SQL-Laufzeit, einschließlich Compiler, Optimierer und Auftrags-Manager, wird Ihr U-SQL-Code verarbeitet.

## <a name="choosing-your-u-sql-runtime-version"></a>Auswählen der U-SQL-Laufzeitversion

Wenn Sie U-SQL-Aufträge über Visual Studio, das ADL SDK oder das Azure Data Lake Analytics Portal übermitteln, wird für Ihren Auftrag die aktuell verfügbare Standardlaufzeit verwendet. Es werden regelmäßig neue Versionen der U-SQL-Laufzeit veröffentlicht, die sowohl kleinere Updates als auch Sicherheitsfixes enthalten.

Sie können auch eine benutzerdefinierte Laufzeitversion auswählen. Der Grund dafür kann sein, dass Sie ein neues Update ausprobieren möchten, bei einer älteren Version einer Laufzeit verbleiben müssen oder Ihnen ein Hotfix für ein gemeldetes Problem bereitgestellt wurde, bei dem Sie nicht auf das reguläre neue Update warten können.

> [!CAUTION]
> Wenn Sie eine andere Laufzeit als die Standardversion auswählen, können Ihre U-SQL-Aufträge möglicherweise unterbrochen werden. Verwenden Sie diese anderen Versionen nur zu Testzwecken.

In seltenen Fällen kann der Microsoft-Support eine andere Version einer Laufzeit als Standardversion für Ihr Konto anheften. Stellen Sie sicher, dass Sie diese Anheftung so schnell wie möglich zurücksetzen. Wenn diese Version angeheftet bleibt, läuft sie zu einem späteren Zeitpunkt ab.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>Überwachen der U-SQL-Laufzeitversion Ihrer Aufträge

Sie können den Verlauf der Laufzeitversionen, die von Ihren bisherigen Aufträgen verwendet wurden, im Auftragsverlauf Ihres Kontos über den Auftragsbrowser von Visual Studio oder den Auftragsverlauf im Azure-Portal anzeigen.

1. Gehen Sie im Azure-Portal zu Ihrem Data Lake Analytics-Konto.
2. Wählen Sie **Alle Aufträge anzeigen** aus. Eine Liste mit allen aktiven und kürzlich abgeschlossenen Aufträge im Konto wird angezeigt.
3. Klicken Sie optional auf **Filtern**, um die Aufträge nach den Werten **Zeitbereich**, **Auftragsname** und **Ersteller** zu suchen.
4. Die Laufzeit, die bei den abgeschlossenen Aufträgen verwendet wurde, wird angezeigt.

![Anzeigen der Laufzeitversion eines früheren Auftrags](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

Die verfügbaren Laufzeitversionen ändern sich mit der Zeit. Die Standardlaufzeit wird immer als „Standard“ bezeichnet. Es bleibt mindestens die vorherige Laufzeit für einige Zeit verfügbar. Außerdem werden aus einer Reihe von Gründen auch spezielle Laufzeiten zur Verfügung gestellt. Explizit benannte Laufzeiten weisen im Allgemeinen das folgende Format auf (Kursivschrift wird für variable Teile und [] für optionale Teile verwendet):

release_YYYYMMDD_adl_buildno[_modifier]

Beispielsweise bezeichnet „release_20190318_adl_3394512_2“ die zweite Version des Builds 3394512 des Laufzeitrelease vom 18. März 2019 and „release_20190318_adl_3394512_private“ bezeichnet einen privaten Build desselben Release. Hinweis: Das Datum bezieht sich auf den Zeitpunkt, zu dem der letzte Eincheckvorgang für dieses Release erfolgte, und nicht unbedingt auf das offizielle Veröffentlichungsdatum.


## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Behandeln von Problemen mit der U-SQL-Laufzeitversion

Es können zwei Probleme mit der Laufzeitversion auftreten:

1. Ein Skript oder ein Benutzercode ändert das Verhalten von einer Version zur nächsten. Solche wichtigen Änderungen werden normalerweise im Voraus durch die Veröffentlichung von Versionshinweisen mitgeteilt. Wenn Sie eine solche wichtige Änderung feststellen, melden Sie dieses geänderte Verhalten dem Microsoft-Support (falls noch nicht dokumentiert), und übergeben Sie Ihre Aufträge für die ältere Runtimeversion.

2. Sie haben eine nicht standardmäßige Laufzeit entweder explizit oder implizit verwendet, als diese an Ihr Konto angeheftet wurde, und diese Laufzeit wurde nach einiger Zeit entfernt. Wenn Runtimes fehlen, aktualisieren Sie Ihre Skripts so, dass sie mit der aktuellen Standardlaufzeit ausgeführt werden. Wenn Sie zusätzliche Zeit benötigen, wenden Sie sich an den Microsoft-Support.

## <a name="known-issues"></a>Bekannte Probleme

* Das Verweisen auf die Datei „Newtonsoft.Json“ ab Version 12.0.3 in einem USQL-Skript führt zu folgendem Kompilierungsfehler:

    *„In Ihrem Data Lake Analytics-Konto ausgeführte Aufträge werden wahrscheinlich langsamer ausgeführt oder können nicht erfolgreich abgeschlossen werden. Ein unerwartetes Problem verhindert eine automatische Wiederherstellung dieser Funktionalität für Ihr Azure Data Lake Analytics-Konto. Die Azure Data Lake-Techniker wurden informiert, um das Problem zu untersuchen.“*  

    Die Aufrufliste enthält Folgendes:  
    `System.IndexOutOfRangeException: Index was outside the bounds of the array.`  
    `at Roslyn.Compilers.MetadataReader.PEFile.CustomAttributeTableReader.get_Item(UInt32 rowId)`  
    `...`

    **Lösung**: Verwenden Sie höchstens Version 12.0.2 der Datei „Newtonsoft.Json“.


## <a name="see-also"></a>Weitere Informationen

- [Azure Data Lake Analytics – Übersicht](data-lake-analytics-overview.md)
- [Verwalten von Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-manage-use-portal.md)
- [Überwachen von Aufträgen in Azure Data Lake Analytics über das Azure-Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

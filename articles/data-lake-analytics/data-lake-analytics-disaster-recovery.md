---
title: Leitfaden zur Notfallwiederherstellung für Azure Data Lake Analytics
description: Erfahren Sie, wie Sie die Notfallwiederherstellung für Ihre Azure Data Lake Analytics-Konten planen.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ea1d4020aa9be23b4839690ae0b386d35bce8a23
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66498544"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Leitfaden zur Notfallwiederherstellung für Azure Data Lake Analytics

Azure Data Lake Analytics ist ein bedarfsgesteuerter Dienst für Analyseaufträge zum Vereinfachen von Big Data-Vorgängen. Anstatt sich der Bereitstellung, Konfiguration und Optimierung von Hardware zu widmen, schreiben Sie Abfragen, mit denen Sie Ihre Daten transformieren und nützliche Einblicke erhalten. Der Analysedienst ist in der Lage, umgehend Aufträge jeglicher Größenordnung zu verarbeiten. Wählen Sie dazu die jeweils erforderliche Ressourcenkapazität aus. Da Sie nur für die Leistung bezahlen, die während der Ausführung Ihres Auftrags tatsächlich in Anspruch genommen wurde, ist dies eine überaus kosteneffektive Lösung. In diesem Artikel wird beschrieben, wie Sie Ihre Aufträge vor selten auftretenden, regionsweiten Ausfällen oder dem versehentlichen Löschen schützen.

## <a name="disaster-recovery-guidance"></a>Leitfäden zur Notfallwiederherstellung

Wenn Sie Azure Data Lake Analytics verwenden, ist es wichtig, dass Sie Ihren eigenen Notfallwiederherstellungsplan vorbereiten. Dieser Artikel führt Sie durch den Vorgang zum Erstellen eines Notfallwiederherstellungsplans. Folgende zusätzliche Ressourcen können Ihnen beim Erstellen Ihres eigenen Plans hilfreich sein:
- [Failure and disaster recovery for Azure applications](/azure/architecture/reliability/disaster-recovery) (Wiederherstellung bei einem Fehler oder Notfall für Azure-Anwendungen)
- [Technischer Leitfaden zur Resilienz in Azure](/azure/architecture/reliability)

## <a name="best-practices-and-scenario-guidance"></a>Bewährte Methoden und Szenarioanleitungen

Sie können einen wiederkehrenden U-SQL-Auftrag in einem ADLA-Konto in einer Region ausführen, die U-SQL-Tabellen sowie unstrukturierte Daten liest und schreibt.  Bereiten Sie sich auf einen Notfall vor, indem Sie diese Schritte ausführen:

1. Erstellen Sie ADLA- und ADLS-Konten in der sekundären Region, die während eines Ausfalls verwendet wird.

   > [!NOTE]
   > Da Kontonamen global eindeutig sind, verwenden Sie ein konsistentes Benennungsschema, das anzeigt, welches Konto sekundär ist.

2. Informationen zu unstrukturierten Daten finden Sie im [Leitfaden zur Notfallwiederherstellung für Daten in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md).

3. Erstellen Sie für strukturierte Daten, die in ADLA-Tabellen und -Datenbanken gespeichert sind, Kopien der Metadatenartefakte wie Datenbanken, Tabellen, Tabellenwertfunktionen und Assemblys. Sie müssen diese Artefakte in regelmäßigen Abständen neu synchronisieren, wenn es in der Produktion zu Änderungen kommt. Beispielsweise müssen neu eingefügte Daten in die sekundäre Region repliziert werden, indem die Daten kopiert und in die sekundäre Tabelle eingefügt werden.

   > [!NOTE]
   > Der Gültigkeitsbereich dieser Objektnamen erstreckt sich nur auf das sekundäre Konto, sie sind nicht global eindeutig, weshalb sie dieselben Namen wie im primären Produktionskonto besitzen können.

Während eines Ausfalls müssen Sie Ihre Skripts aktualisieren, damit die Eingabepfade auf den sekundären Endpunkt verweisen. Dann übermitteln die Benutzer ihre Aufträge an das ADLA-Konto in der sekundären Region. Die Ausgabe des Auftrags wird dann in das ADLA- und ADLS-Konto in der sekundären Region geschrieben.

## <a name="next-steps"></a>Nächste Schritte

[Leitfaden zur Notfallwiederherstellung für Daten in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

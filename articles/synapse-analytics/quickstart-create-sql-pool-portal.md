---
title: 'Schnellstart: Erstellen eines dedizierten SQL-Pools mithilfe des Azure-Portals'
description: Erstellen Sie anhand der Schritte in diesem Leitfaden einen neuen dedizierten SQL-Pool über das Azure-Portal.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3537b126adf082c8b00a02d999789d8a32b3f51d
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462014"
---
# <a name="quickstart-create-a-dedicated-sql-pool-using-the-azure-portal"></a>Schnellstart: Erstellen eines dedizierten SQL-Pools mithilfe des Azure-Portals

Azure Synapse Analytics enthält verschiedene Analysemodule, mit denen Sie Ihre Daten erfassen, transformieren, modellieren und analysieren können. Ein dedizierter SQL-Pool bietet T-SQL-basierte Compute- und Speicherfunktionen. Nach der Erstellung eines dedizierten SQL-Pools in Ihrem Synapse-Arbeitsbereich können Daten geladen, modelliert, verarbeitet und für einen schnelleren analytischen Einblick bereitgestellt werden.

In dieser Schnellstartanleitung erfahren Sie, wie Sie über das Azure-Portal einen dedizierten SQL-Pool in einem Synapse-Arbeitsbereich erstellen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- [Synapse-Arbeitsbereich](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navigieren zum Synapse-Arbeitsbereich

1. Navigieren Sie zu dem Synapse-Arbeitsbereich, in dem der dedizierte SQL-Pool erstellt werden soll, indem Sie in die Suchleiste den Dienstnamen (oder direkt den Ressourcennamen) eingeben.
![Suchleiste im Azure-Portal mit der Eingabe „Synapse-Arbeitsbereiche“](media/quickstart-create-sql-pool/create-sql-pool-00a.png) 
1. Geben Sie in der Liste der Arbeitsbereiche den Namen (oder einen Teil des Namens) des zu öffnenden Arbeitsbereichs ein. In diesem Beispiel verwenden wir einen Arbeitsbereich namens **contosoanalytics**.
![Auflistung von Synapse-Arbeitsbereichen, die so gefiltert wurden, dass diejenigen, die den Namen „Contoso“ enthalten, angezeigt werden](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="create-new-dedicated-sql-pool"></a>Erstellen eines neuen dedizierten SQL-Pools

1. Wählen Sie in dem Synapse-Arbeitsbereich, in dem Sie den dedizierten SQL-Pool erstellen möchten, auf der oberen Leiste den Befehl **New dedicated SQL pool** (Neuer dedizierter SQL-Pool) aus.
![Übersicht über den Synapse-Arbeitsbereich mit dem rot umrandeten Befehl zum Erstellen eines neuen dedizierten SQL-Pools](media/quickstart-create-sql-pool/create-sql-pool-portal-01.png)
2. Geben Sie auf der Registerkarte **Grundeinstellungen** die folgenden Informationen ein:

    | Einstellung | Vorgeschlagener Wert | BESCHREIBUNG |
    | :------ | :-------------- | :---------- |
    | **Dedicated SQL pool name** (Name des dedizierten SQL-Pools) | Ein gültiger Name | Name des dedizierten SQL-Pools |
    | **Leistungsstufe** | DW100c | Legen Sie die kleinste Größe fest, um die Kosten für diesen Schnellstart zu senken. |

  
    ![Erstellungsflow für den dedizierten SQL-Pool: Registerkarte „Grundeinstellungen“](media/quickstart-create-sql-pool/create-sql-pool-portal-02.png)

    > [!IMPORTANT]
    > Beachten Sie, dass für die Namen, die für dedizierte SQL-Pools verwendet werden können, bestimmte Einschränkungen gelten. Namen dürfen keine Sonderzeichen enthalten, höchstens 15 Zeichen lang sein und keine reservierten Wörter enthalten und müssen im Arbeitsbereich eindeutig sein.

3. Klicken Sie auf **Weiter: Zusätzliche Einstellungen**.
4. Wählen Sie **Keine** aus, um den dedizierten SQL-Pool ohne Daten bereitzustellen. Übernehmen Sie die ausgewählte Standardsortierung.

    Wenn Sie Ihren dedizierten SQL-Pool von einem Wiederherstellungspunkt wiederherstellen möchten, wählen Sie **Wiederherstellungspunkt** aus. Weitere Informationen zum Ausführen einer Wiederherstellung finden Sie unter [Gewusst wie: Wiederherstellen eines vorhandenen dedizierten SQL-Pools](backuprestore/restore-sql-pool.md)

![Erstellungsflow für den dedizierten SQL-Pool: Registerkarte „Zusätzliche Einstellungen“](media/quickstart-create-sql-pool/create-sql-pool-portal-03.png)

5. Klicken Sie auf **Überprüfen + erstellen**.
6. Vergewissern Sie sich anhand der vorherigen Eingaben, dass die Details korrekt sind. Klicken Sie auf **Erstellen**.
![Erstellungsflow für den dedizierten SQL-Pool: Registerkarte zum Überprüfen der Einstellungen](media/quickstart-create-sql-pool/create-sql-pool-portal-04.png)

7. Der Flow für die Ressourcenbereitstellung wird nun gestartet.
 ![Screenshot: Seite „Ihre Bereitstellung wurde abgeschlossen.“](media/quickstart-create-sql-pool/create-sql-pool-portal-06.png)

8. Wenn Sie nach Abschluss der Bereitstellung zurück zum Arbeitsbereich navigieren, wird ein neuer Eintrag für den neu erstellten dedizierten SQL-Pool angezeigt.
 ![Flow für die SQL-Poolerstellung: Ressourcenbereitstellung](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)


Nach der Erstellung des dedizierten SQL-Pools ist er im Arbeitsbereich zum Laden von Daten, Verarbeiten von Datenströmen, Lesen aus dem Data Lake usw. verfügbar.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Führen Sie die folgenden Schritte aus, um den dedizierten SQL-Pool aus dem Arbeitsbereich zu löschen.
> [!WARNING]
> Wenn Sie einen dedizierten SQL-Pool löschen, werden die Analyse-Engine und die in der Datenbank des gelöschten dedizierten SQL-Pools gespeicherten Daten aus dem Arbeitsbereich entfernt. Sie können keine Verbindung mehr mit dem dedizierten SQL-Pool herstellen, und alle Abfragen, Pipelines und Notebooks, die aus diesem SQL-Pool lesen oder in diesen dedizierten SQL-Pool schreiben, funktionieren nicht mehr.

Führen Sie die folgenden Schritte aus, wenn Sie den dedizierten SQL-Pool löschen möchten:

1. Navigieren Sie auf dem Blatt des Arbeitsbereichs zum Blatt mit den SQL-Pools.
1. Wählen Sie den zu löschenden dedizierten SQL-Pool aus (in diesem Fall **contosowdw**).
1. Klicken Sie nach der Auswahl auf **Löschen**.
1. Bestätigen Sie den Löschvorgang, und klicken Sie auf die Schaltfläche **Löschen**: ![Übersicht über den dedizierten SQL-Pool: Hervorhebung der Löschbestätigung](media/quickstart-create-sql-pool/create-sql-pool-portal-11.png)
1. Wenn der Vorgang erfolgreich abgeschlossen wurde, wird der dedizierte SQL-Pool nicht mehr in den Arbeitsbereichsressourcen aufgeführt.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Apache Spark-Pools in Azure Synapse Analytics mithilfe von Webtools](quickstart-apache-spark-notebook.md).
- Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines serverlosen Apache Spark-Pools über das Azure-Portal](quickstart-create-apache-spark-pool-portal.md).

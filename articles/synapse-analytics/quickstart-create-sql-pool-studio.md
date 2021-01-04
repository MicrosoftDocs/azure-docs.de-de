---
title: 'Schnellstart: Erstellen eines dedizierten SQL-Pools mithilfe von Synapse Studio'
description: Erstellen Sie anhand der Schritte in diesem Leitfaden mithilfe von Synapse Studio einen dedizierten SQL-Pool.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 10/16/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3644891f12a6475ec9cfec51f572df4742481e8f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541843"
---
# <a name="quickstart-create-a-dedicated-sql-pool-using-synapse-studio"></a>Schnellstart: Erstellen eines dedizierten SQL-Pools mithilfe von Synapse Studio

Azure Synapse Analytics enthält verschiedene Analysemodule, mit denen Sie Ihre Daten erfassen, transformieren, modellieren und analysieren können. Ein dedizierter SQL-Pool bietet T-SQL-basierte Compute- und Speicherfunktionen. Nach der Erstellung eines dedizierten SQL-Pools in Ihrem Synapse-Arbeitsbereich können Daten geladen, modelliert, verarbeitet und für einen schnelleren analytischen Einblick bereitgestellt werden.

In dieser Schnellstartanleitung werden die Schritte zum Erstellen eines dedizierten SQL-Pools in einem Synapse-Arbeitsbereich mithilfe von Synapse Studio erläutert.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.


## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- [Synapse-Arbeitsbereich](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navigieren zum Synapse-Arbeitsbereich

1. Navigieren Sie zu dem Synapse-Arbeitsbereich, in dem der dedizierte SQL-Pool erstellt werden soll, indem Sie in die Suchleiste den Dienstnamen (oder direkt den Ressourcennamen) eingeben.

    ![Suchleiste im Azure-Portal mit der Eingabe „Synapse-Arbeitsbereiche“](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. Geben Sie in der Liste der Arbeitsbereiche den Namen (oder einen Teil des Namens) des zu öffnenden Arbeitsbereichs ein. In diesem Beispiel verwenden wir einen Arbeitsbereich namens **contosoanalytics**.

    ![Auflistung von Synapse-Arbeitsbereichen, die so gefiltert wurden, dass diejenigen angezeigt werden, die den Namen „Contoso“ enthalten.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Starten von Synapse Studio

1. Wählen Sie in der Arbeitsbereichsübersicht die **Web-URL für den Arbeitsbereich** aus, um Synapse Studio zu starten.

    ![Synapse-Arbeitsbereichsübersicht im Azure-Portal mit hervorgehobener Web-URL für den Arbeitsbereich](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-dedicated-sql-pool-in-synapse-studio"></a>Erstellen eines dedizierten SQL-Pools in Synapse Studio

1. Navigieren Sie auf der Synapse Studio-Startseite im linken Navigationsbereich zum **Verwaltungshub**, indem Sie das Symbol **Verwalten** auswählen.

    ![Synapse Studio-Startseite mit hervorgehobenem Abschnitt „Verwaltungshub“](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. Sobald Sie sich im Verwaltungshub befinden, navigieren Sie zum Abschnitt **SQL-Pools**, um die aktuelle Liste der SQL-Pools anzuzeigen, die im Arbeitsbereich verfügbar sind.

    ![Synapse Studio-Verwaltungshub mit ausgewählter Navigation für „SQL-Pools“](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. Wählen Sie den Befehl **+ Neu** aus. Dann wird der Assistent zum Erstellen eines neuen SQL-Pools angezeigt. 

    ![Liste der SQL-Pools im Synapse Studio-Verwaltungshub](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. Geben Sie auf der Registerkarte **Grundeinstellungen** die folgenden Informationen ein:

    | Einstellung | Vorgeschlagener Wert | BESCHREIBUNG |
    | :------ | :-------------- | :---------- |
    | **Name des SQL-Pools** | contosoedw | Dies ist der vorgesehene Name für den dedizierten SQL-Pool. |
    | **Leistungsstufe** | DW100c | Legen Sie diese Einstellung auf die kleinste Größe fest, um die Kosten für diesen Schnellstart zu senken. |

    ![Erstellungsflow für SQL-Pools – Registerkarte „Grundeinstellungen“](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > Beachten Sie, dass für die Namen, die für dedizierte SQL-Pools verwendet werden können, bestimmte Einschränkungen gelten. Namen dürfen keine Sonderzeichen enthalten, höchstens 15 Zeichen lang sein und keine reservierten Wörter enthalten und müssen im Arbeitsbereich eindeutig sein.

4. Wählen Sie auf der nächsten Registerkarte, **Zusätzliche Einstellungen**, die Option **Keine** aus, um den SQL-Pool ohne Daten bereitzustellen. Übernehmen Sie die bereits ausgewählte Standardsortierung.

    Wenn Sie Ihren dedizierten SQL-Pool von einem Wiederherstellungspunkt wiederherstellen möchten, wählen Sie **Wiederherstellungspunkt** aus. Weitere Informationen zum Ausführen einer Wiederherstellung finden Sie unter [Gewusst wie: Wiederherstellen eines vorhandenen dedizierten SQL-Pools](backuprestore/restore-sql-pool.md)

    ![Erstellungsflow für den SQL-Pool: Registerkarte „Zusätzliche Einstellungen“](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. Wir fügen vorerst keine Tags hinzu. Wählen Sie also als Nächstes **Überprüfen + erstellen** aus.

1. Vergewissern Sie sich auf der Grundlage der vorherigen Eingaben auf der Registerkarte **Überprüfen + erstellen**, dass die Details korrekt sind, und klicken Sie auf **Erstellen**. 

    ![Flow für die SQL-Poolerstellung: Registerkarte zum Überprüfen der Einstellungen](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. Der Flow für die Ressourcenbereitstellung wird nun gestartet.

1. Wenn Sie nach Abschluss der Bereitstellung zurück zum Arbeitsbereich navigieren, wird ein neuer Eintrag für den neu erstellten SQL-Pool angezeigt.

    ![Flow für die SQL-Poolerstellung: Ressourcenbereitstellung](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. Nach der Erstellung des dedizierten SQL-Pools ist er im Arbeitsbereich zum Laden von Daten, Verarbeiten von Datenströmen, Lesen aus dem Data Lake usw. verfügbar.

## <a name="clean-up-dedicated-sql-pool-using-synapse-studio"></a>Bereinigen eines dedizierten SQL-Pools mithilfe von Synapse Studio    

Führen Sie die folgenden Schritte aus, um den dedizierten SQL-Pool mithilfe von Synapse Studio aus dem Arbeitsbereich zu löschen.
> [!WARNING]
> Wenn Sie einen dedizierten SQL-Pool löschen, wird das Analysemodul aus dem Arbeitsbereich entfernt. Sie können keine Verbindung mehr mit dem Pool herstellen, und alle Abfragen, Pipelines und Skripts, die diesen dedizierten SQL-Pool verwenden, funktionieren nicht mehr.

Wenn Sie den dedizierten SQL-Pool löschen möchten, führen Sie die folgenden Schritte aus:

1. Navigieren Sie in Synapse Studio im Verwaltungshub zu den SQL-Pools.
1. Wählen Sie die Auslassungspunkte in dem zu löschenden dedizierten SQL-Pool (in diesem Fall **contosoedw**) aus, um die Befehle für den dedizierten SQL-Pool anzuzeigen:

    ![Liste der SQL-Pools, wobei der soeben erstellte Pool ausgewählt ist.](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. Klicken Sie auf **Löschen**.
1. Bestätigen Sie den Löschvorgang, und klicken Sie auf die Schaltfläche **Löschen**.
1. Wenn der Vorgang erfolgreich abgeschlossen wurde, wird der dedizierte SQL-Pool nicht mehr in den Arbeitsbereichsressourcen aufgeführt.

## <a name="next-steps"></a>Nächste Schritte
 
- Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Apache Spark-Notebooks](quickstart-apache-spark-notebook.md).
- Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines dedizierten SQL-Pools mithilfe des Azure-Portals](quickstart-create-sql-pool-portal.md).

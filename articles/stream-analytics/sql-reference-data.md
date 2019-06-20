---
title: Verwenden von Verweisdaten aus einer SQL-Datenbank für einen Azure Stream Analytics-Auftrag (Vorschauversion)
description: In diesem Artikel wird beschrieben, wie Sie eine SQL-Datenbank-Instanz als Verweisdateneingabe für einen Azure Stream Analytics-Auftrag im Azure-Portal und in Visual Studio verwenden.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: f0e62c27885e2f6d5097194e1b9d869e167c4a4c
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304967"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job-preview"></a>Verwenden von Verweisdaten aus einer SQL-Datenbank für einen Azure Stream Analytics-Auftrag (Vorschauversion)

Azure Stream Analytics unterstützt Azure SQL-Datenbank als Eingangsquelle für Verweisdaten. Sie können SQL-Datenbank mit den Stream Analytics-Tools als Verweisdaten für Ihren Stream Analytics-Auftrag im Azure-Portal und in Visual Studio verwenden. In diesem Artikel wird veranschaulicht, wie Sie beide Methoden anwenden.

## <a name="azure-portal"></a>Azure-Portal

Fügen Sie mit den folgenden Schritten die Azure SQL-Datenbank-Instanz als Verweiseingabequelle mithilfe des Azure-Portals hinzu:

### <a name="portal-prerequisites"></a>Portalvoraussetzungen

1. Erstellen Sie einen Stream Analytics-Auftrag.

2. Erstellen Sie ein Speicherkonto, das vom Stream Analytics-Auftrag verwendet werden soll.

3. Erstellen Sie Ihre Azure SQL-Datenbank-Instanz mit einem Dataset, das als Verweisdaten vom Stream Analytics-Auftrag verwendet werden soll.

### <a name="define-sql-database-reference-data-input"></a>Definieren der SQL-Datenbank-Verweisdateneingabe

1. Wählen Sie in Ihrem Stream Analytics-Auftrag **Eingaben** unter **Auftragstopologie** aus. Klicken Sie auf **Referenzeingabe hinzufügen**, und wählen Sie **SQL-Datenbank** aus.

   ![Stream Analytics-Auftragseingabe](./media/sql-reference-data/stream-analytics-inputs.png)

2. Füllen Sie die Stream Analytics-Eingabekonfiguration aus. Wählen Sie Datenbanknamen, Servernamen, Benutzernamen und Kennwort aus. Wenn Sie Ihre Verweisdateneingabe regelmäßig aktualisieren möchten, wählen Sie „Ein“, um die Aktualisierungsrate in TT:HH:MM anzugeben. Wenn Sie über große Datasets mit einer kurzen Aktualisierungsrate verfügen, können Sie eine [Deltaabfrage](sql-reference-data.md#delta-query) durchführen.

   ![SQL-Datenbank-Verweiskonfiguration](./media/sql-reference-data/sql-input-config.png)

3. Testen Sie die Momentaufnahmenabfrage im SQL-Abfrage-Editor. Weitere Informationen finden Sie unter [Verwenden des SQL-Abfrage-Editors im Azure-Portal zum Verbinden und Abfragen von Daten](../sql-database/sql-database-connect-query-portal.md).

### <a name="specify-storage-account-in-job-config"></a>Angeben des Speicherkontos in der Auftragskonfiguration

Navigieren Sie zu **Speicherkontoeinstellungen** unter **Konfigurieren**, und wählen Sie **Speicherkonto hinzufügen** aus.

   ![Stream Analytics-Speicherkontoeinstellungen](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Starten des Auftrags

Nachdem Sie andere Eingaben, Ausgaben und die Abfrage konfiguriert haben, können Sie den Stream Analytics-Auftrag starten.

## <a name="tools-for-visual-studio"></a>Tools für Visual Studio

Fügen Sie mit den folgenden Schritten die Azure SQL-Datenbank-Instanz als Verweiseingabequelle mithilfe von Visual Studio hinzu:

### <a name="visual-studio-prerequisites"></a>Voraussetzungen für Visual Studio

1. [Installieren Sie die Stream Analytics-Tools für Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Die folgenden Versionen von Visual Studio werden unterstützt:

   * Visual Studio 2015
   * Visual Studio 2019

2. Machen Sie sich mit dem Schnellstart der [Stream Analytics-Tools für Visual Studio](stream-analytics-quick-create-vs.md) vertraut.

3. Erstellen Sie ein Speicherkonto.

### <a name="create-a-sql-database-table"></a>Erstellen einer SQL-Datenbanktabelle

Verwenden Sie SQL Server Management Studio, um eine Tabelle zum Speichern Ihrer Verweisdaten zu erstellen. Nähere Informationen finden Sie unter [Entwerfen Ihrer ersten Azure SQL-Datenbank mit SSMS](../sql-database/sql-database-design-first-database.md).

Die im folgenden Beispiel verwendete Beispieltabelle wurde aus der folgenden Anweisung erstellt:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Auswählen Ihres Abonnements

1. Wählen Sie in Visual Studio im Menü **Ansicht** den **Server-Explorer** aus.

2. Klicken Sie mit der rechten Maustaste auf **Azure**, wählen Sie **Verbindung mit Microsoft Azure-Abonnement herstellen** aus, und melden Sie sich dann mit Ihrem Azure-Konto an.

### <a name="create-a-stream-analytics-project"></a>Erstellen eines Stream Analytics-Projekts

1. Wählen Sie **Datei > Neues Projekt** aus. 

2. Wählen Sie in der Vorlagenliste links **Stream Analytics** und dann **Azure Stream Analytics Application** (Azure Stream Analytics-Anwendung) aus. 

3. Geben Sie **Name**, **Speicherort** und **Projektmappenname** für Ihr Projekt ein, und klicken Sie auf **OK**.

   ![Neues Stream Analytics-Projekt in Visual Studio](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Definieren der SQL-Datenbank-Verweisdateneingabe

1. Erstellen Sie ein neues Element.

   ![Neue Stream Analytics-Eingabe in Visual Studio](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Doppelklicken Sie auf **Input.JSON** im **Projektmappen-Explorer**.

3. Füllen Sie die **Stream Analytics-Eingabekonfiguration** aus. Wählen Sie den Datenbanknamen, den Servernamen, den Aktualisierungstyp und die Aktualisierungsrate. Geben Sie die Aktualisierungsrate im Format `DD:HH:MM` an.

   ![Stream Analytics-Eingabekonfiguration in Visual Studio](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Wenn Sie „Nur einmal ausführen“ oder „In regelmäßigen Abständen ausführen“ auswählen, wird eine SQL CodeBehind-Datei mit dem Namen **[Eingabealias].snapshot.sql** in dem Projekt unter dem Dateiknoten **Input.JSON** generiert.

   ![CodeBehind-Eingabe in Visual Studio](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Wenn Sie „In regelmäßigen Abständen mit Delta aktualisieren“ auswählen, werden zwei SQL-CodeBehind-Dateien generiert : **[Eingabealias].snapshot.sql** und **[Eingabealias].delta.sql**.

   ![CodeBehind im Projektmappen-Explorer](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Öffnen Sie die SQL-Datei im Editor, und schreiben Sie die SQL-Abfrage.

5. Wenn Sie Visual Studio 2019 verwenden und SQL Server Data Tools installiert haben, können Sie die Abfrage testen, indem Sie auf **Ausführen** klicken. Ein Assistentenfenster wird eingeblendet, das Sie beim Herstellen der Verbindung mit der SQL-Datenbank-Instanz unterstützt, und das Abfrageergebnis wird am unteren Rand des Fensters angezeigt.

### <a name="specify-storage-account"></a>Festlegen eines Speicherkontos

Öffnen Sie **JobConfig.json**, um das Speicherkonto zum Speichern von SQL-Referenzmomentaufnahmen festzulegen.

   ![Stream Analytics-Auftragskonfiguration in Visual Studio](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Lokales Testen und Bereitstellen in Azure

Bevor Sie den Auftrag in Azure bereitstellen, können Sie die Abfragelogik lokal mit Liveeingabedaten testen. Weitere Informationen zu diesem Feature finden Sie unter [Lokales Testen von Livedaten mithilfe von Azure Stream Analytics-Tools für Visual Studio (Vorschauversion)](stream-analytics-live-data-local-testing.md). Klicken Sie nach Abschluss des Testens auf **An Azure übermitteln**. Im Schnellstart [Erstellen eines Stream Analytics-Auftrags mithilfe des Azure Stream Analytics-Tools für Visual Studio](stream-analytics-quick-create-vs.md) erfahren Sie, wie Sie den Auftrag starten.

## <a name="delta-query"></a>Deltaabfrage

Wenn Sie die Deltaabfrage verwenden, werden [temporale Tabellen in der Azure SQL-Datenbank](../sql-database/sql-database-temporal-tables.md) empfohlen.

1. Erstellen Sie eine temporale Tabelle in Azure SQL-Datenbank.
   
   ```SQL 
      CREATE TABLE DeviceTemporal 
      (  
         [DeviceId] int NOT NULL PRIMARY KEY CLUSTERED 
         , [GroupDeviceId] nvarchar(100) NOT NULL
         , [Description] nvarchar(100) NOT NULL 
         , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
         , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
         , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
      )  
      WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.DeviceHistory));  -- DeviceHistory table will be used in Delta query
   ```
2. Erstellen Sie die Momentaufnahmenabfrage. 

   Weisen Sie mit dem **\@snapshotTime**-Parameter die Stream Analytics-Runtime an, das Referenzdataset aus der zur Systemzeit gültigen temporalen SQL-Datenbank-Tabelle abzurufen. Wenn Sie diesen Parameter nicht angeben, riskieren Sie, ein aufgrund von Zeitabweichungen ungenaues Verweisdataset zu erhalten. Ein Beispiel für die vollständige Momentaufnahmenabfrage sehen Sie unten:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Erstellen Sie die Deltaabfrage. 
   
   Diese Abfrage ruft alle Zeilen in der SQL-Datenbank-Instanz ab, die zwischen der Startzeit **\@deltaStartTime** und der Endzeit **\@deltaEndTime** eingefügt oder gelöscht wurden. Die Deltaabfrage muss die gleichen Spalten wie die Momentaufnahmenabfrage zurückgeben, sowie die Spalte  **_operation_** . Diese Spalte definiert, ob die Zeile zwischen **\@deltaStartTime** und **\@deltaEndTime** eingefügt oder gelöscht wird. Die sich ergebenden Zeilen werden mit **1** gekennzeichnet, wenn die Datensätze eingefügt wurden, oder **2**, wenn sie gelöscht wurden. 

   Für Datensätze, die aktualisiert wurden, übernimmt die temporale Tabelle die Buchführung durch Erfassen eines Einfüge- und Löschvorgangs. Die Stream Analytics-Runtime wendet dann die Ergebnisse auf die an die vorhergehende Momentaufnahme gerichtete Deltaabfrage an, um die Verweisdaten auf dem neuesten Stand zu halten. Ein Beispiel der Deltaabfrage wird unten gezeigt:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Beachten Sie, dass die Stream Analytics-Runtime die Momentaufnahmenabfrage neben der Deltaabfrage in regelmäßigen Abständen ausführen kann, um Prüfpunkte zu speichern.

## <a name="test-your-query"></a>Testen Ihrer Abfrage
   Es ist wichtig, dass Sie sicherstellen, dass Ihre Abfrage das erwartete Dataset zurückgibt, das der Stream Analytics-Auftrag als Referenzdaten verwendet. Um Ihre Abfrage zu testen, wechseln Sie zur Eingabe im Auftragstopologieabschnitt des Portals. Sie können dann in der Eingabe Ihrer SQL-Datenbankreferenzeingabe Beispieldaten auswählen. Nachdem das Beispiel verfügbar ist, können Sie die Datei herunterladen und überprüfen, um festzustellen, ob die zurückgegebenen Daten wie erwartet sind. Wenn Sie Ihre Entwicklungs- und Testiterationen optimieren möchten, wird empfohlen, die [Stream Analytics-Tools für Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install) zu verwenden. Sie können auch ein beliebiges anderes Tool Ihrer Wahl verwenden, um zuerst sicherzustellen, dass die Abfrage die richtigen Ergebnisse aus Ihrer Azure SQL-Datenbank zurückgibt, und diese dann in Ihrem Stream Analytics-Auftrag verwenden. 

## <a name="faqs"></a>Häufig gestellte Fragen

**Entstehen mir zusätzliche Kosten, wenn ich die SQL-Verweisdateneingabe in Azure Stream Analytics verwende?**

Es gibt keine zusätzlichen [Kosten pro Streamingeinheit](https://azure.microsoft.com/pricing/details/stream-analytics/) im Stream Analytics-Auftrag. Allerdings muss dem Stream Analytics-Auftrag ein Azure Storage-Konto zugeordnet werden. Der Stream Analytics-Auftrag fragt die SQL-Datenbank-Instanz ab (während des Auftragsstarts und Aktualisierungsintervalls), um das Verweisdataset abzurufen, und speichert diese Momentaufnahme im Speicherkonto. Beim Speichern dieser Momentaufnahmen fallen zusätzliche Gebühren an, die ausführlich in der [Übersicht über die Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/) für das Azure Storage-Konto beschrieben werden.

**Wie erfahre ich, ob die Verweisdaten-Momentaufnahme von der SQL-Datenbank-Instanz abgefragt und im Azure Stream Analytics-Auftrag verwendet wird?**

Es gibt zwei nach dem logischen Namen gefilterte Metriken (unter „Metrics Azure Portal“), die Sie zur Überwachung der Integrität der SQL-Datenbank-Referenzdateneingabe verwenden können.

   * InputEvents: Diese Metrik misst die Anzahl der Datensätze, die aus dem SQL-Datenbank-Verweisdataset geladen werden.
   * InputEventBytes: Diese Metrik misst die Größe der in den Arbeitsspeicher des Stream Analytics-Auftrags geladenen Verweisdaten-Momentaufnahme. 

Mithilfe der Kombination dieser beiden Metriken kann ermittelt werden, ob der Auftrag die SQL-Datenbank abfragt, um das Verweisdataset abzurufen und dann in den Arbeitsspeicher zu laden.

**Benötige ich eine besondere Art der Azure SQL-Datenbank-Instanz?**

Azure Stream Analytics funktioniert mit jeder Art von Azure SQL-Datenbank-Instanz. Allerdings müssen Sie berücksichtigen, dass die für Ihre Verweisdateneingabe festgelegte Aktualisierungsrate sich auf Ihre Abfragelast auswirken kann. Um die Deltaabfrageoption zu verwenden, sollten Sie temporale Tabellen in Azure SQL-Datenbank verwenden.

**Warum speichert Azure Stream Analytics Momentaufnahmen im Azure Storage-Konto?**

Für Stream Analytics wird die Exactly-Once-Ereignisverarbeitung (genau einmal) und die At-Least-Once-Zustellung (mindestens einmal) von Ereignissen garantiert. In Fällen, in denen vorübergehende Probleme sich auf Ihren Auftrag auswirken, ist eine kleine Wiedergabemenge erforderlich, um den Status wiederherzustellen. Um die Wiedergabe zu ermöglichen, müssen diese Momentaufnahmen in einem Azure Storage-Konto gespeichert sein. Weitere Informationen zur Prüfpunktwiedergabe finden Sie unter [Begriffe zu Prüfpunkten und zur Wiedergabe bei Azure Stream Analytics-Aufträgen](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von Referenzdaten für Suchvorgänge in Stream Analytics](stream-analytics-use-reference-data.md)
* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe des Azure Stream Analytics-Tools für Visual Studio](stream-analytics-quick-create-vs.md)
* [Lokales Testen von Livedaten mithilfe von Azure Stream Analytics-Tools für Visual Studio (Vorschauversion)](stream-analytics-live-data-local-testing.md)

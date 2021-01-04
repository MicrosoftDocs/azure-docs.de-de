---
title: Verwenden von Azure Stream Analytics im dedizierten SQL-Pool
description: Tipps für die Verwendung von Azure Stream Analytics beim dedizierten SQL-Pool in Azure Synapse zum Entwickeln von Echtzeitlösungen.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 8fbe546beb1004214e544f8eb160884c0f64ef9e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458225"
---
# <a name="use-azure-stream-analytics-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Verwenden von Azure Stream Analytics beim dediziertem SQL-Pool in Azure Synapse Analytics

Azure Stream Analytics ist ein vollständig verwalteter Dienst, der eine geringe Latenz, hohe Verfügbarkeit und eine skalierbare komplexe Ereignisverarbeitung durch das Streaming von Daten in der Cloud bietet. Die Grundlagen finden Sie unter [Einführung in Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). In [Erste Schritte mit Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) können Sie sich dann mit dem Erstellen einer End-to-End-Lösung mit Stream Analytics vertraut machen.

In diesem Artikel erfahren Sie, wie Sie Ihren dedizierten SQL-Pool als Ausgabesenke für Datenerfassung mit hohem Durchsatz bei Azure Stream Analytics-Aufträgen verwenden.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Stream Analytics-Auftrag. Zum Erstellen eines Azure Stream Analytics-Auftrags führen Sie die folgenden Schritte im Tutorial [Erste Schritte mit Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) aus:  

    1. Erstellen einer Event Hub-Eingabe
    2. Konfigurieren und starten der Ereignisgenerator-Anwendung
    3. Bereitstellen eines Stream Analytics-Auftrags
    4. Festlegen der Auftragseingabe und -abfrage
* Dedizierter SQL-Pool – Führen Sie zum Erstellen eines neuen dedizierten SQL-Pools die Schritte aus, die Sie finden im [Schnellstart: Erstellen eines dedizierten SQL-Pools](../quickstart-create-sql-pool-portal.md).

## <a name="specify-streaming-output-to-point-to-your-dedicated-sql-pool"></a>Festlegen der Streamingausgabe an Ihren dedizierten SQL-Pool

### <a name="step-1"></a>Schritt 1

Navigieren Sie im Azure-Portal zu Ihrem Stream Analytics Auftrag, und klicken Sie im Menü **Auftragstopologie** auf **Ausgaben**.

### <a name="step-2"></a>Schritt 2

Klicken Sie auf die Schaltfläche **Hinzufügen**, und wählen Sie im Dropdownmenü die Option **Azure Synapse Analytics** aus.

![Auswählen von Azure Synapse Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>Schritt 3

Geben Sie die folgenden Werte ein:

* *Ausgabealias:* Geben Sie einen Anzeigenamen für diese Auftragsausgabe ein.
* *Abonnement*:
  * Wenn Ihr dedizierter SQL-Pool im selben Abonnement wie der Stream Analytics-Auftrag enthalten ist, klicken Sie auf ***Select Azure Synapse Analytics from your subscriptions** _ (Azure Synapse Analytics aus Abonnements auswählen).
  _ Wenn Ihr dedizierter SQL-Pool in einem anderen Abonnement enthalten ist, klicken Sie auf „Provide Azure Synapse Analytics settings manually“ (Azure Synapse Analytics-Einstellungen manuell angeben).
* *Datenbank*: Wählen Sie in der Dropdownliste die Zieldatenbank aus.
* *Benutzername*: Geben Sie den Benutzernamen eines Kontos mit Schreibberechtigungen für die Datenbank an.
* *Kennwort*: Geben Sie das Kennwort für das angegebene Benutzerkonto an.
* *Tabelle*: Geben Sie den Namen der Zieltabelle in der Datenbank an.
* Klicken Sie auf die Schaltfläche **Speichern**.

![Ausgefülltes Azure Synapse Analytics-Formular](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>Schritt 4

Bevor Sie einen Test ausführen können, müssen Sie die Tabelle in Ihrem dedizierten SQL-Pool erstellen.  Führen Sie das folgende Skript für die Tabellenerstellung mithilfe von SQL Server Management Studio (SSMS) oder mit Ihrem bevorzugten Abfragetool aus.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>Schritt 5

Klicken Sie im Azure-Portal für Ihren Stream Analytics-Auftrag auf den Auftragsnamen.  Klicken Sie im Bereich _*_Ausgabedetails_*_ auf die Schaltfläche **_Testen_* _.

![Schaltfläche „Testen“ für Ausgabedetails](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png): Wenn die Verbindung mit der Datenbank erfolgreich hergestellt wurde, wird im Portal eine entsprechende Meldung angezeigt.

### <a name="step-6"></a>Schritt 6

Klicken Sie unter _*_Auftragstopologie_*_ auf das Menü _*_Abfrage_*_, und ändern Sie die Abfrage so, dass Daten in die von Ihnen erstellte Streamausgabe eingefügt werden.  Klicken Sie auf die Schaltfläche _*_Ausgewählte Abfrage testen_*_, um die Abfrage zu testen.  Klicken Sie auf die Schaltfläche _*_Abfrage speichern_*_, wenn der Abfragetest erfolgreich war.

![Speichern der Abfrage](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Schritt 7

Starten Sie den Azure Stream Analytics-Auftrag.  Klicken Sie im Menü _*_Übersicht_*_ auf die Schaltfläche _*_Starten_*_.

![Starten des Stream Analytics-Auftrags](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Klicken Sie im Bereich „Auftrag starten“ auf die Schaltfläche _ *_Starten_**.

![Klicken auf „Starten“](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Nächste Schritte

Einen Überblick über die Integration finden Sie unter [Integrieren anderer Dienste](sql-data-warehouse-overview-integrate.md).
Weitere Tipps zur Entwicklung finden Sie unter [Entwurfsentscheidungen und Programmiertechniken für dedizierte SQL-Pools](sql-data-warehouse-overview-develop.md).

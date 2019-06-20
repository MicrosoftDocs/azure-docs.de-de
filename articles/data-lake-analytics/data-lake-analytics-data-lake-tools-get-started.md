---
title: Erste Schritte mit Azure Data Lake Analytics unter Verwendung von Visual Studio
description: Erfahren Sie, wie Sie Data Lake-Tools für Visual Studio installieren und U-SQL-Skripts entwickeln und testen.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/13/2018
ms.openlocfilehash: b463946402eee40d0de0942eeaf37a6f9ea59990
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60510019"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure Data Lake- und Stream Analytics-Tools enthalten Funktionen für zwei Azure-Dienste: Azure Data Lake Analytics und Azure Stream Analytics. Weitere Informationen zu den Azure Stream Analytics-Szenarien finden Sie unter [Installieren der Azure Stream Analytics-Tools für Visual Studio](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md).

In diesem Artikel erfahren Sie, wie Sie Visual Studio verwenden, um Azure Data Lake Analytics-Konten zu erstellen, Aufträge in [U-SQL](data-lake-analytics-u-sql-get-started.md) zu definieren und Aufträge an den Data Lake Analytics-Dienst zu übermitteln. Weitere Informationen zu Data Lake Analytics finden Sie unter [Übersicht über Azure Data Lake Analytics](data-lake-analytics-overview.md).

> [!IMPORTANT]
> Microsoft empfiehlt Ihnen das Upgrade auf Version 2.3.3000.4 (oder höher) der Azure Data Lake Tools für Visual Studio. Ältere Versionen stehen nicht mehr als Download zur Verfügung und sind veraltet. 
> 
> **Was muss ich tun?**
> 
> 1. Überprüfen Sie, ob Sie von Azure Data Lake Tools für Visual Studio eine niedrigere Version als 2.3.3000.4 verwenden. 
> 
>    ![Überprüfen der Toolversion](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
> 
> 2. Falls es sich bei Ihrer Version um eine Version vor 2.3.3000.4 handelt, aktualisieren Sie Ihre Azure Data Lake Tools für Visual Studio über das Download Center: 
>    - [Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Visual Studio 2013 und 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)


## <a name="prerequisites"></a>Voraussetzungen

* **Visual Studio:** Alle Editionen außer Express werden unterstützt.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **Microsoft Azure SDK für .NET**, Version 2.7.1 oder höher.  Führen Sie die Installation mit dem [Webplattform-Installer](https://www.microsoft.com/web/downloads/platform.aspx)durch.
* Ein **Data Lake Analytics**-Konto. Informationen zum Erstellen eines Kontos finden Sie unter [Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Installieren von Azure Data Lake Tools für Visual Studio

Für dieses Tutorial muss Data Lake Tools für Visual Studio installiert sein. Befolgen Sie die [Installationsanweisungen](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Herstellen einer Verbindung mit einem Azure Data Lake Analytics-Konto

1. Öffnen Sie Visual Studio.

2. Öffnen Sie Server-Explorer, indem Sie **Ansicht** > **Server-Explorer** auswählen.

3. Klicken Sie mit der rechten Maustaste auf **Azure**. Wählen Sie dann **Verbindung mit Microsoft Azure-Abonnement herstellen** aus, und befolgen Sie die Anweisungen.

4. Wählen Sie im Server-Explorer **Azure** > **Data Lake Analytics** aus. Es wird eine Liste Ihrer Data Lake Analytics-Konten angezeigt.

## <a name="write-your-first-u-sql-script"></a>Schreiben Ihres ersten U-SQL-Skripts

Der folgende Text ist ein einfaches U-SQL-Skript. Es definiert ein kleines Dataset und schreibt dieses Dataset als Datei mit dem Namen `/data.csv` in die Standardinstanz von Data Lake Store.

```
USE DATABASE master;
USE SCHEMA dbo;
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-data-lake-analytics-job"></a>Übermitteln eines Data Lake Analytics-Auftrags

1. Wählen Sie **Datei** > **Neu** > **Projekt**.

2. Wählen Sie den Typ **U-SQL-Projekt** aus, und klicken Sie auf **OK**. Visual Studio erstellt eine Projektmappe mit der Datei **Script.usql**.

3. Fügen Sie das vorherige Skript im Fenster **Script.usql** ein.

4. Geben Sie in der linken oberen Ecke des Fensters **Script.usql** das Data Lake Analytics-Konto an.

    ![Visual Studio-U-SQL-Projekt senden](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

5. Wählen Sie in der linken oberen Ecke des Fensters **Script.usql** die Option **Senden** aus.

6. Nach der Auftragsübermittlung wird die Registerkarte **Auftragsansicht** mit dem Auftragsstatus geöffnet. Um den aktuellen Auftragsstatus anzuzeigen und den Bildschirm zu aktualisieren, klicken Sie auf **Aktualisieren**.

    ![U-SQL Visual Studio Data Lake Analytics-Diagramm zur Auftragsleistung](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * **Auftragszusammenfassung** enthält die Zusammenfassung des Auftrags.   
   * **Auftragsdiagramm** visualisiert den Fortschritt des Auftrags.
   * **Metadatenvorgänge** zeigt alle Aktionen, die auf den U-SQL-Katalog ausgeführt wurden.
   * **Daten** zeigt alle Eingaben und Ausgaben.
   * Unter **State History** (Statusverlauf) werden die Zeitachse und Statusdetails angezeigt.
   * Unter **AU Analysis** (AU-Analyse) sehen Sie, wie viele AUs im Auftrag verwendet wurden, und Sie können Simulationen verschiedener AU-Zuordnungsstrategien erkunden.
   * **Diagnose** enthält eine erweiterte Analyse für die Optimierung der Ausführung und Leistung des Auftrags.

## <a name="check-job-status"></a>Überprüfen des Auftragsstatus

1. Wählen Sie im Server-Explorer **Azure** > **Data Lake Analytics** aus.

2. Erweitern Sie den Namen des Azure Data Lake Analytics-Kontos.

3. Doppelklicken Sie auf **Aufträge**.

4. Wählen Sie den Auftrag aus, den Sie zuvor gesendet haben.

## <a name="see-the-job-output"></a>Anzeigen der Auftragsausgabe

1. Navigieren Sie im Server-Explorer zu dem Auftrag, den Sie gesendet haben.

2. Klicken Sie auf die Registerkarte **Daten** .

3. Wählen Sie auf der Registerkarte **Auftragsausgaben** die Datei `"/data.csv"` aus.

## <a name="next-steps"></a>Nächste Schritte

* [Testen und Debuggen von U-SQL-Aufträgen mit lokalen Testläufen und dem Azure Data Lake-U-SQL-SDK](data-lake-analytics-data-lake-tools-local-run.md)
* [Lokales Ausführen und lokales Debuggen von U-SQL mit Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Verwenden der Azure Data Lake-Tools für Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)

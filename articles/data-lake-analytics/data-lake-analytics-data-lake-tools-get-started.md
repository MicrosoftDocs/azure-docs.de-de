---
title: Abfragen in Azure Data Lake Analytics – Visual Studio
description: Erfahren Sie, wie Sie Data Lake-Tools für Visual Studio installieren und U-SQL-Skripts entwickeln und testen.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: d3812cb27d2d9ea7a49038f566f8b4cc5d779172
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241691"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure Data Lake- und Stream Analytics-Tools enthalten Funktionen für zwei Azure-Dienste: Azure Data Lake Analytics und Azure Stream Analytics. Weitere Informationen zu den Azure Stream Analytics-Szenarien finden Sie unter [Installieren der Azure Stream Analytics-Tools für Visual Studio](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md).

Dieser Artikel beschreibt, wie Sie Azure Data Lake Analytics-Konten mithilfe von Visual Studio erstellen. Sie können Aufträge in [U-SQL](data-lake-analytics-u-sql-get-started.md) definieren und Aufträge an den Data Lake Analytics-Dienst übermitteln. Weitere Informationen zu Data Lake Analytics finden Sie unter [Übersicht über Azure Data Lake Analytics](data-lake-analytics-overview.md).

> [!IMPORTANT]
> Wir empfehlen Ihnen das Upgrade auf Azure Data Lake Tools für Visual Studio Version 2.3.3000.4 oder höher. Ältere Versionen stehen nicht mehr als Download zur Verfügung und sind veraltet.
>
> 1. Überprüfen Sie, ob Sie von Azure Data Lake Tools für Visual Studio eine niedrigere Version als 2.3.3000.4 verwenden.
>
>    ![Überprüfen der Toolversion](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
>
> 1. Falls es sich bei Ihrer Version um eine Version vor 2.3.3000.4 handelt, aktualisieren Sie Ihre Azure Data Lake Tools für Visual Studio über das Download Center:
>    - [Für Visual Studio 2017 und 2019](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Visual Studio 2013 und 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)

## <a name="prerequisites"></a>Voraussetzungen

* **Visual Studio:** alle Editionen außer Express werden unterstützt.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK für .NET**, Version 2.7.1 oder höher. Führen Sie die Installation mit dem [Webplattform-Installer](https://www.microsoft.com/web/downloads/platform.aspx)durch.
* Ein **Data Lake Analytics**-Konto. Informationen zum Erstellen eines Kontos finden Sie unter [Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Installieren von Azure Data Lake Tools für Visual Studio

Für dieses Tutorial muss Data Lake Tools für Visual Studio installiert sein. Weitere Informationen finden Sie unter [Installieren von Data Lake Tools für Visual Studio](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Herstellen einer Verbindung mit einem Azure Data Lake Analytics-Konto

1. Öffnen Sie Visual Studio.

1. Öffnen Sie **Data Lake Analytics-Explorer**, indem Sie **Ansicht** > **Data Lake Analytics-Explorer** auswählen.

1. Klicken Sie mit der rechten Maustaste auf **Azure**, und wählen Sie dann **Verbindung mit Microsoft Azure-Abonnement herstellen** aus. Befolgen Sie die Anweisungen unter **Bei Ihrem Konto anmelden**.

1. Wählen Sie im **Server-Explorer** **Azure** > **Data Lake Analytics** aus. Es wird eine Liste Ihrer Data Lake Analytics-Konten angezeigt.

## <a name="write-your-first-u-sql-script"></a>Schreiben Ihres ersten U-SQL-Skripts

Der folgende Text ist ein einfaches U-SQL-Skript. Es definiert ein kleines Dataset und schreibt dieses Dataset als Datei mit dem Namen `/data.csv` in die Standardinstanz von Data Lake Store.

```sql
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

1. Klicken Sie in Visual Studio auf **Datei** > **Neu** > **Projekt**.

1. Wählen Sie den Typ **U-SQL-Projekt** und dann **Weiter** aus. Wählen Sie in **Neues Projekt konfigurieren** die Option **Erstellen** aus.

   Visual Studio erstellt eine Projektmappe mit der Datei **Script.usql**.

1. Fügen Sie das Skript aus [Schreiben Ihres ersten U-SQL-Skripts](#write-your-first-u-sql-script) in das Fenster **Script.usql** ein.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Script.usql**, und wählen Sie dann **Skript senden** aus.

1. Wählen Sie unter **Auftrag übermitteln** ihr Data Lake Analytics-Konto aus, und wählen Sie **Übermitteln** aus.

   ![Visual Studio-U-SQL-Projekt senden](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-submit-job-vs2019.png)

Nach der Auftragsübermittlung wird die Registerkarte **Auftragsansicht** mit dem Auftragsstatus geöffnet.

* **Auftragszusammenfassung** enthält die Zusammenfassung des Auftrags.
* **Auftragsdiagramm** visualisiert den Fortschritt des Auftrags.
* **Metadatenvorgänge** zeigt alle Aktionen, die auf den U-SQL-Katalog ausgeführt wurden.
* **Daten** zeigt alle Eingaben und Ausgaben.
* Unter **State History** (Statusverlauf) werden die Zeitachse und Statusdetails angezeigt.
* Unter **AU-Analyse** sehen Sie, wie viele AUs im Auftrag verwendet wurden, und Sie können Simulationen verschiedener AU-Zuordnungsstrategien erkunden.
* **Diagnose** enthält eine erweiterte Analyse für die Optimierung der Ausführung und Leistung des Auftrags.

![U-SQL Visual Studio Data Lake Analytics-Diagramm zur Auftragsleistung](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

Um den aktuellen Auftragsstatus anzuzeigen und den Bildschirm zu aktualisieren, wählen Sie **Aktualisieren** aus.

## <a name="check-job-status"></a>Überprüfen des Auftragsstatus

1. Wählen Sie im **Server-Explorer** **Azure** > **Data Lake Analytics** aus.

1. Erweitern Sie den Namen des Azure Data Lake Analytics-Kontos.

1. Doppelklicken Sie auf **Aufträge**.

1. Wählen Sie den Auftrag aus, den Sie zuvor gesendet haben.

## <a name="see-the-job-output"></a>Anzeigen der Auftragsausgabe

1. Navigieren Sie im **Server-Explorer** zu dem Auftrag, den Sie gesendet haben.

1. Klicken Sie auf die Registerkarte **Daten** .

1. Wählen Sie auf der Registerkarte **Auftragsausgaben** die Datei `"/data.csv"` aus.

## <a name="next-steps"></a>Nächste Schritte

* [Testen und Debuggen von U-SQL-Aufträgen mit lokalen Testläufen und dem Azure Data Lake-U-SQL-SDK](data-lake-analytics-data-lake-tools-local-run.md)
* [Lokales Ausführen und lokales Debuggen von U-SQL mit Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Verwenden der Azure Data Lake-Tools für Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)

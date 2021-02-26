---
title: Laden von Terabytes an Daten in Azure Synapse Analytics
description: Veranschaulicht, wie 1 TB Daten in weniger als 15 Minuten mithilfe von Azure Data Factory in Azure Synapse Analytics geladen werden kann
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5acae7c90efbf178fad199177fa6e0886e497fdf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371208"
---
# <a name="load-1-tb-into-azure-synapse-analytics-under-15-minutes-with-data-factory"></a>Laden von 1 TB Daten in Azure Synapse Analytics in weniger als 15 Minuten mit Data Factory
> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Informationen für die aktuelle Version des Data Factory-Diensts finden Sie unter [Kopieren und Transformieren von Daten in Azure Synapse Analytics (ehemals SQL Data Warehouse) mithilfe von Azure Data Factory](../connector-azure-sql-data-warehouse.md).


[Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) ist eine cloudbasierte Datenbank mit horizontaler Skalierung, mit der sehr große volumen sowohl relationaler als auch nicht relationaler Daten verarbeitet werden können.  Azure Synapse Analytics basiert auf der MPP-Architektur (Massively Parallel Processing), die für Data-Warehouse-Workloads in Großunternehmen optimiert ist.  Es bietet Cloudelastizität mit der Flexibilität, Speicher zu skalieren und unabhängig zu berechnen.

Die ersten Schritte mit Azure Synapse Analytics sind jetzt mithilfe von **Azure Data Factory** einfacher als je zuvor.  Azure Data Factory ist ein vollständig verwalteter cloudbasierter Datenintegrationsdienst, der verwendet werden kann, um Azure Synapse Analytics mit den Daten Ihres bestehenden Systems aufzufüllen. Der Dienst spart Ihnen wertvolle Zeit beim Bewerten von Azure Synapse Analytics und Erstellen von Analyselösungen. Die wichtigsten Vorteile beim Laden von Daten in Azure Synapse Analytics mithilfe von Azure Data Factory sind wie folgt:

* **Mühelose Einrichtung**: Intuitiver Assistent mit fünf Schritten. Keine Skripterstellung erforderlich.
* **Unterstützung für umfangreiche Datenspeicher**: Integrierte Unterstützung für umfangreiche lokale und cloudbasierte Datenspeicher.
* **Sicher und kompatibel**: Daten werden über HTTPS oder ExpressRoute übertragen, und globale Dienste stellen sicher, dass Ihre Daten nie die geografische Grenze verlassen.
* **Beispiellose Leistung mithilfe von PolyBase**: Die Verwendung von PolyBase ist die effizienteste Methode zum Verschieben von Daten in Azure Synapse Analytics. Mit der Funktion „Stagingblob“ sinkt die Ladezeit für alle Arten von Datenspeicher drastisch. Dies gilt nicht für Azure Blob Storage, da dies von Polybase standardmäßig unterstützt wird.

In diesem Artikel wird gezeigt, wie der Assistenten zum Kopieren in Data Factory verwendet wird, um 1 TB Daten aus Azure Blob Storage in Azure Synapse Analytics in unter 15 Minuten mit über 1,2 GBit/s Durchsatz zu laden.

Dieser Artikel enthält detaillierte Anleitungen zum Verschieben von Daten in Azure Synapse Analytics mithilfe des Kopier-Assistenten.

> [!NOTE]
>  Im Artikel [Verschieben von Daten in und aus Azure Synapse Analytics mithilfe von Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) finden Sie allgemeine Informationen zu den Möglichkeiten von Data Factory beim Verschieben von Daten aus/in Azure Synapse Analytics.
>
> Sie können Pipelines auch mithilfe von Visual Studio, PowerShell usw. erstellen. Siehe [Tutorial: Kopieren von Daten aus Blob Storage in SQL-Datenbank mithilfe von Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finden Sie eine kurze exemplarische Vorgehensweise mit detaillierten Anleitungen zur Verwendung der Kopieraktivität in Azure Data Factory.  
>
>

## <a name="prerequisites"></a>Voraussetzungen
* Azure Blob Storage: In diesem Experiment wird Azure Blob Storage (GRS) verwendet, um den TPC-H Testdatensatz zu speichern.  Falls Sie noch kein Azure-Speicherkonto haben, lesen Sie den Artikel [Erstellen eines Speicherkontos](../../storage/common/storage-account-create.md).
* [TPC-H](http://www.tpc.org/tpch/)-Daten: Wir verwenden TPC-H als Testdatensatz.  Verwenden Sie dazu `dbgen` aus dem TPC-H-Toolkit, wodurch Sie den Datensatz erzeugen können.  Sie können entweder Quellcode für `dbgen` aus [TPC Tools](http://www.tpc.org/tpc_documents_current_versions/current_specifications5.asp) herunterladen und selbst kompilieren, oder die kompilierte Binärdatei aus [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TPCHTools) herunterladen.  Führen Sie dbgen.exe mit folgenden Befehlen aus, um eine 1 TB Flatfile für Tabelle `lineitem` zu erzeugen, die auf 10 Dateien verteilt ist:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Kopieren Sie jetzt die generierten Dateien in Azure Blob.  Unter [Verschieben von Daten in das lokale und aus dem lokalen Dateisystem mithilfe von Azure Data Factory](data-factory-onprem-file-system-connector.md) erhalten Sie weitere Informationen, wie Sie dies mithilfe von ADF Copy durchführen.    
* Azure Synapse Analytics: Bei diesem Experiment werden mit 6.000 DWUs erstellte Daten in Azure Synapse Analytics geladen.

    Ausführliche Anweisungen zum Erstellen einer Azure Synapse Analytics-Datenbank finden Sie unter [Erstellen einer Azure Synapse Analytics-Datenbank](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md).  Um die bestmögliche Leistung des Ladevorgangs in Azure Synapse Analytics mithilfe von Polybase zu erreichen, wählen wir die maximale Anzahl von Data Warehouse-Einheiten (DWUs) aus, die in der Leistungseinstellung zulässig ist (6.000).

  > [!NOTE]
  > Wenn Sie aus Azure Blob Storage laden, ist die Leistung beim Laden von Daten direkt proportional zur Anzahl der DWUs, die Sie für Azure Synapse Analytics konfigurieren:
  >
  > Das Laden von 1 TB in 1.000 DWU in Azure Synapse Analytics dauert 87 Minuten (ca. 200 MBit/s Durchsatz). Das Laden von 1 TB in 2.000 DWU in Azure Synapse Analytics dauert 46 Minuten (ca. 380 MBit/s Durchsatz). Das Laden von 1 TB in 6.000 DWU in Azure Synapse Analytics dauert 14 Minuten (ca. 1,2 GBit/s Durchsatz).
  >
  >

    Bewegen Sie den Schieberegler „Leistung“ ganz nach rechts, um einen dedizierten SQL-Pool mit 6.000 DWUs zu erstellen:

    ![Schieberegler „Performance“ (Leistung)](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Bei einer vorhandenen Datenbank, die nicht mit 6.000 DWUs konfiguriert wurde, können Sie mithilfe des Azure-Portals eine Hochskalierung durchführen.  Navigieren Sie zu der Datenbank in Azure-Portal. Dort gibt es eine Schaltfläche **Scale (Skalieren)** im Bereich **Overview (Übersicht)** , wie in der folgenden Abbildung gezeigt:

    ![Schaltfläche „Skalieren“](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Klicken Sie auf die Schaltfläche **Scale** um den folgenden Bereich zu öffnen, bewegen Sie den Schieberegler auf den Höchstwert, und klicken Sie auf die Schaltfläche **Save (Speichern)** .

    ![Scale-Dialogfeld](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    In diesem Experiment werden Daten mithilfe der Ressourcenklasse `xlargerc` in Azure Synapse Analytics geladen.

    Um den bestmögliche Durchsatz zu erreichen, muss mithilfe eines Azure Synapse Analytics-Benutzers, der zur Ressourcenklasse `xlargerc` gehört, ein Kopiervorgang ausgeführt werden.  Erfahren Sie, wie das geht, indem Sie das folgende [Beispiel: Ändern der Ressourcenklasse eines Benutzers](../../synapse-analytics/sql-data-warehouse/resource-classes-for-workload-management.md) ausführen.  
* Erstellen Sie eine Zieltabellenschema in der Datenbank von Azure Synapse Analytics, indem Sie die folgende DDL-Anweisung ausführen:

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
  Nachdem die vorbereitenden Schritte abgeschlossen sind, kann jetzt die Kopieraktivität mithilfe des Kopier-Assistenten ausgeführt werden.

## <a name="launch-copy-wizard"></a>Starten des Kopier-Assistenten
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Klicken Sie links oben auf **Ressource erstellen** und anschließend auf **Intelligence + Analyse** und **Data Factory**.
3. Gehen Sie im Bereich **Neue Data Factory** wie folgt vor:

   1. Geben Sie **LoadIntoSQLDWDataFactory** als **Name** ein.
       Der Name der Azure Data Factory muss global eindeutig sein. Bei Anzeige der Fehlermeldung **Data Factory-Name „LoadIntoSQLDWDataFactory“ ist nicht verfügbar**. Ändern Sie den Namen der Data Factory (z. B. in „IhrnameLoadIntoSQLDWDataFactory“), und wiederholen Sie den Vorgang. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](data-factory-naming-rules.md) .  
   2. Wählen Sie Ihr Azure- **Abonnement** aus.
   3. Führen Sie unter „Ressourcengruppe“ einen der folgenden Schritte aus:
      1. Wählen Sie **Use existing** (Vorhandene verwenden), um eine vorhandene Ressourcengruppe auszuwählen.
      2. Wählen Sie **Neu erstellen**, um einen Namen für eine Ressourcengruppe einzugeben.
   4. Wählen Sie einen **Standort** für die Data Factory aus.
   5. Aktivieren Sie unten auf dem Blatt das Kontrollkästchen **An Dashboard anheften**.  
   6. Klicken Sie auf **Erstellen**.
4. Nach Abschluss der Erstellung wird das Blatt **Data Factory** wie in der folgenden Abbildung dargestellt angezeigt:

   ![Data Factory-Startseite](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Klicken Sie auf der Data Factory-Startseite auf die Kachel **Daten kopieren**, um den **Kopier-Assistenten** zu starten.

   > [!NOTE]
   > Wenn Sie feststellen, dass der Webbrowser bei der Autorisierung hängen bleibt, deaktivieren Sie die Einstellung **Cookies und Websitedaten von Drittanbietern blockieren**, oder lassen Sie die Einstellung aktiviert, und erstellen Sie eine Ausnahme für **login.microsoftonline.com**. Versuchen Sie anschließend erneut, den Assistenten zu starten.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Schritt 1: Konfigurieren des Zeitplans für das Laden von Daten
Der erste Schritt ist das Konfigurieren des Zeitplans für das Laden von Daten.  

Auf der Seite **Eigenschaften**:

1. Geben Sie unter **Aufgabenname** den Namen **CopyFromBlobToAzureSqlDataWarehouse** ein.
2. Wählen Sie die Option **Run once now (Einmalig ausführen)** aus.   
3. Klicken Sie auf **Weiter**.  

    ![Kopier-Assistent – Eigenschaftsseite](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Schritt 2: Konfigurieren der Quelle
In diesem Abschnitt werden die Schritte zum Konfigurieren der Quelle angezeigt: Azure Blob mit den TPC-H-Elementdateien mit 1 TB.

1. Wählen Sie **Azure Blob Storage** als Datenspeiche aus, und klicken Sie auf **Weiter**.

    ![Kopier-Assistent – Seite Quelle auswählen](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Geben Sie die Verbindungsinformationen für das Azure Blob Storage-Konto ein, und klicken Sie auf **Weiter**.

    ![Kopier-Assistent – Quellenverbindungsinformation](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Wählen Sie den **Ordner** mit den TPC-H-Elementdateien und klicken Sie auf **Weiter**.

    ![Kopier-Assistent – Seite Eingabeordner auswählen](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Sobald Sie auf **Weiter** geklickt haben, werden die Dateiformateinstellungen automatisch erkannt.  Stellen Sie sicher, dass das Spaltentrennzeichen als „|“ anstelle des standardmäßigen Kommas „,“ festgelegt ist.  Klicken Sie auf **Weiter** nachdem Sie die Daten in der Vorschau angezeigt haben.

    ![Kopiertool – Dateiformateinstellungen](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Schritt 3: Konfigurieren des Ziels
Dieser Abschnitt beschreibt das Konfigurieren des Ziels (Tabelle `lineitem`) in der Azure Synapse Analytics-Datenbank.

1. Wählen Sie **Azure Synapse Analytics-** als Zielspeicher aus, und klicken Sie auf **Weiter**.

    ![Kopier-Assistent – Ziel-Datenspeicher auswählen](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Geben Sie die Verbindungsinformationen für Azure Synapse Analytics ein.  Stellen Sie sicher, dass Sie den Benutzer angeben, der Mitglied der Rolle `xlargerc` ist (siehe Abschnitt **Voraussetzungen** für detaillierte Anweisungen), und klicken Sie auf **Weiter**.

    ![Kopier-Assistent: Zielverbindungsinformation](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Wählen Sie die Zieltabelle aus, und klicken Sie auf **Weiter**.

    ![Kopier-Assistent – Seite Zuordnung der Tabelle](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Lassen Sie auf der Seite „Schemazuordnung“ die Option „Spaltenzuordnung anwenden“ deaktiviert, und klicken Sie auf **Weiter**.

## <a name="step-4-performance-settings"></a>Schritt 4: Leistungseinstellungen

**Allow polybase (Zulassen von PolyBase)** ist standardmäßig aktiviert.  Klicken Sie auf **Weiter**.

![Kopier-Assistent – Seite Zuordnung des Schemas](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Schritt 5: Bereitstellen und Überwachen der Auslastungsergebnisse
1. Klicken Sie für die Bereitstellung auf **Fertig stellen**.

    ![Kopier-Assistent: Seite 1 der Zusammenfassung](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Nachdem die Bereitstellung abgeschlossen ist, klicken Sie auf `Click here to monitor copy pipeline` um Ausführung und Fortschritt des Kopiervorgangs zu überwachen. Wählen Sie die Kopierpipeline aus, die Sie in der **Activity Windows**-Liste erstellt haben.

    ![Kopier-Assistent: Seite 2 der Zusammenfassung](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Sie können die Details zum Kopiervorgang unter **Activity Window Explorer** im rechten Bereich anzeigen, darunter das Datenvolumen, das aus der Quelle gelesen und in das Ziel geschrieben wurde, die Dauer und den durchschnittlichen Durchsatz der Ausführung.

    Wie Sie im folgenden Screenshot sehen können, dauerte das Kopieren von 1 TB aus Azure Blob Storage in Azure Synapse Analytics 14 Minuten, wobei effektiv ein Durchsatz von 1,22 GBit/s erreicht wurde!

    ![Kopier-Assistent – Kopieren erfolgreich Dialog](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Bewährte Methoden
Hier sind einige bewährte Methoden für die Ausführung Ihrer Azure Synapse Analytics-Datenbank:

* Verwenden Sie eine größere Ressourcenklasse beim Laden in einem GRUPPIERTEN COLUMNSTORE-INDEX.
* Für effektivere Verknüpfungen sollten Sie die Verwendung von Hashverteilung durch Auswahl einer Spalte in Betracht ziehen, statt der standardmäßigen Roundrobin-Verteilung.
* Ziehen Sie für ein schnelleres Seitenladetempo die Verwendung von Heap für vorübergehende Daten in Betracht.
* Erstellen Sie Statistiken, nachdem das Laden von Daten in Azure Synapse Analytics abgeschlossen ist.

Einzelheiten finden Sie unter [Bewährte Methoden für Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md).

## <a name="next-steps"></a>Nächste Schritte
* [Data Factory Kopier-Assistent](data-factory-copy-wizard.md) – Dieser Artikel enthält Details zum Kopier-Assistenten.
* [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) – Dieser Artikel enthält die Messungen zur Leistungsreferenz und das Optimierungshandbuch.
---
title: Laden von Daten in Azure SQL Data Warehouse mit Azure Data Factory | Microsoft-Dokumentation
description: Verwenden von Azure Data Factory zum Kopieren von Daten in Azure SQL Data Warehouse
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: 6a7e0a27d3cda4193a04467d541f851a9e57fa46
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60548788"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Laden von Daten in Azure SQL Data Warehouse mit Azure Data Factory

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) ist eine cloudbasierte Datenbank für das horizontale Hochskalieren, mit der sehr große Datenvolumen verarbeitet werden können, und zwar sowohl relational als auch nicht relational. SQL Data Warehouse basiert auf der MPP-Architektur (Massively Parallel Processing), die für Data Warehouse-Workloads für Unternehmen optimiert ist. Es bietet Cloudelastizität mit der Flexibilität, Speicher zu skalieren und unabhängig zu berechnen.

Die ersten Schritte mit Azure SQL Data Warehouse sind jetzt mithilfe von Azure Data Factory einfacher als je zuvor. Azure Data Factory ist ein vollständig verwalteter, cloudbasierter Datenintegrationsdienst. Mithilfe dieses Diensts können Sie ein SQL Data Warehouse mit Daten aus dem vorhandenen System füllen und Zeit beim Erstellen von Analyselösungen sparen.

Azure Data Factory bietet die folgenden Vorteile beim Laden von Daten in Azure SQL Data Warehouse:

* **Mühelose Einrichtung**: Intuitiver Assistent mit 5 Schritten. Keine Skripterstellung erforderlich.
* **Unterstützung für umfangreiche Datenspeicher**: Integrierte Unterstützung für umfangreiche lokale und cloudbasierte Datenspeicher. Eine ausführliche Liste finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
* **Sicher und kompatibel**: Daten werden über HTTPS oder ExpressRoute übertragen. Globale Dienste stellen sicher, dass Ihre Daten nie die geografische Grenze verlassen.
* **Beispiellose Leistung mithilfe von PolyBase**: PolyBase ist die effizienteste Methode zum Verschieben von Daten in Azure SQL Data Warehouse. Mit der Funktion „Stagingblob“ werden schnelle Ladezeiten für alle Arten von Datenspeicher erreicht. Dies gilt auch für Azure Blob Storage und Data Lake Store. (Azure Blob Storage und Azure Data Lake Store werden standardmäßig von Polybase unterstützt.) Weitere Informationen finden Sie unter [Leistung der Kopieraktivität](copy-activity-performance.md).

In diesem Artikel erfahren Sie, wie Sie das Tool zum Kopieren von Daten in Data Factory zum _Laden von Daten aus der Azure SQL-Datenbank in Azure SQL Data Warehouse_ verwenden. Sie können ähnliche Schritte zum Kopieren von Daten aus anderen Typen von Datenspeichern ausführen.

> [!NOTE]
> Weitere Informationen finden Sie unter [Kopieren von Daten nach und aus Azure SQL Data Warehouse mithilfe von Azure Data Factory](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Azure SQL Data Warehouse: Dieses Data Warehouse enthält die Daten, die aus der SQL-Datenbank kopiert werden. Wenn Sie noch kein Azure SQL Data Warehouse erstellt haben, finden Sie Anweisungen dazu unter [Erstellen eines SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Azure SQL-Datenbank: In diesem Tutorial werden Daten aus einer Azure SQL-Datenbank mit Adventure Works LT-Beispieldaten kopiert. Sie können eine SQL-Datenbank erstellen, indem Sie den Anweisungen unter [Erstellen einer Azure SQL-Datenbank](../sql-database/sql-database-get-started-portal.md) folgen. 
* Azure-Speicherkonto: Azure Storage wird im Massenkopiervorgang als _Staging_blob verwendet. Falls Sie noch nicht über ein Azure-Speicherkonto verfügen, finden Sie Anweisungen dazu unter [Erstellen eines Speicherkontos](../storage/common/storage-quickstart-create-account.md).

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Daten + Analysen** > **Data Factory** aus: 
   
   ![Auswählen von „Data Factory“ im Bereich „Neu“](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Geben Sie auf der Seite **Neue Data Factory** die in der folgenden Abbildung gezeigten Werte für die Felder an:
      
   ![Seite „Neue Data Factory“](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Name**: Geben Sie einen global eindeutigen Namen für die Azure Data Factory ein. Wenn die Fehlermeldung „Data Factory mit dem Namen \"LoadSQLDWDemo\" ist nicht verfügbar“ angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Sie können beispielsweise den Namen _**IhrName**_ **ADFTutorialDataFactory** verwenden. Versuchen Sie erneut, die Data Factory zu erstellen. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](naming-rules.md).
    * **Abonnement**: Wählen Sie Ihr Azure-Abonnement aus, in dem die Data Factory erstellt werden soll. 
    * **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus der Dropdownliste aus, oder wählen Sie die Option **Neu erstellen** aus, und geben Sie dann den Namen einer Ressourcengruppe ein. Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/resource-group-overview.md).  
    * **Version**: Wählen Sie **V2** aus.
    * **Standort**: Wählen Sie den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die von der Data Factory verwendeten Datenspeicher können sich an anderen Standorten bzw. in anderen Regionen befinden. Diese Datenspeicher umfassen Azure Data Lake Store, Azure Storage, Azure SQL-Datenbank usw.

3. Klicken Sie auf **Erstellen**.
4. Nach Abschluss der Erstellung navigieren Sie zu Ihrer Data Factory. Die Startseite **Data Factory** wird wie in der folgenden Abbildung dargestellt angezeigt:
   
   ![Data Factory-Startseite](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Wählen Sie die Kachel **Erstellen und überwachen** aus, um die Datenintegrationsanwendung auf einer separaten Registerkarte zu starten.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Laden von Daten in Azure SQL Data Warehouse

1. Wählen Sie auf der Seite **Erste Schritte** die Kachel **Daten kopieren** aus, um das Tool zum Kopieren von Daten zu starten:

   ![Kachel für das Tool zum Kopieren von Daten](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. Geben Sie auf der Seite **Eigenschaften** im Feld **Aufgabenname** den Namen **CopyFromSQLToSQLDW** ein, und klicken Sie dann auf **Weiter**:

    ![Eigenschaftenseite](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. Führen Sie auf der Seite **Quelldatenspeicher** die folgenden Schritte aus:

    a. Klicken Sie auf **+ Neue Verbindung erstellen**:

    ![Seite „Quelldatenspeicher“](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. Wählen Sie im Katalog **Azure SQL-Datenbank** und dann **Weiter** aus. Sie können in das Suchfeld zum Filtern der Connectors „SQL“ eingeben.

    ![Auswählen der Azure SQL-Datenbank](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Wählen Sie auf der Seite **New Linked Service** (Neuer verknüpfter Dienst) in der Dropdownliste Ihren Server- und Datenbanknamen aus, und geben Sie den Benutzernamen und das Kennwort an. Klicken Sie auf **Verbindung testen**, um die Einstellungen zu überprüfen, und wählen Sie dann **Fertig stellen** aus.
   
    ![Konfigurieren von Azure SQL-Datenbank](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Wählen Sie den neu erstellten verknüpften Dienst als Quelle aus, und klicken Sie auf **Weiter**.

    ![Auswählen des verknüpften Quelldiensts](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. Geben Sie auf der Seite **Tabellen auswählen, aus denen die Daten kopiert werden sollen, oder eine benutzerdefinierte Abfrage verwenden** die Zeichenfolge **SalesLT** ein, um die Tabellen zu filtern. Aktivieren Sie das Kontrollkästchen **(Alles auswählen)** , um alle Tabellen für den Kopiervorgang zu verwenden, und klicken Sie dann auf **Weiter**: 

    ![Auswählen von Quelltabellen](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. Führen Sie auf der Seite **Zieldatenspeicher** die folgenden Schritte aus:

    a. Klicken Sie auf **+ Neue Verbindung erstellen**, um eine Verbindung hinzuzufügen.

    ![Seite „Senkendatenspeicher“](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. Wählen Sie im Katalog **Azure SQL Data Warehouse** und dann **Weiter** aus.

    ![Auswählen von Azure SQL DW](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Wählen Sie auf der Seite **New Linked Service** (Neuer verknüpfter Dienst) in der Dropdownliste Ihren Server- und Datenbanknamen aus, und geben Sie den Benutzernamen und das Kennwort an. Klicken Sie auf **Verbindung testen**, um die Einstellungen zu überprüfen, und wählen Sie dann **Fertig stellen** aus.
   
    ![Konfigurieren von Azure SQL DW](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Wählen Sie den neu erstellten verknüpften Dienst als Senke aus, und klicken Sie auf **Weiter**.

    ![Auswählen des verknüpften Senkendiensts](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. Überprüfen Sie den Inhalt der Seite **Tabellenmapping**, und klicken Sie dann auf **Weiter**. Eine intelligente Tabellenzuordnung wird angezeigt. Die Quelltabellen werden den Zieltabellen auf Grundlage der Tabellennamen zugeordnet. Wenn eine Quelltabelle im Ziel nicht vorhanden ist, wird von Azure Data Factory standardmäßig eine Zieltabelle mit dem gleichen Namen erstellt. Sie können eine Quelltabelle auch einer vorhandenen Zieltabelle zuordnen. 

   > [!NOTE]
   > Das automatische Erstellen der Tabelle für die SQL Data Warehouse-Senke tritt auf, wenn SQL Server oder Azure SQL-Datenbank als Quelle fungieren. Wenn Sie Daten aus einem anderen Quelldatenspeicher kopieren, müssen Sie das Schema vorab in der Azure SQL Data Warehouse-Senke erstellen, bevor Sie den Datenkopiervorgang ausführen.

   ![Seite „Tabellenmapping“](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. Überprüfen Sie den Inhalt der Seite **Schemazuordnung**, und klicken Sie dann auf **Weiter**. Die intelligente Tabellenzuordnung basiert auf dem Spaltennamen. Wenn Sie Data Factory die Tabellen automatisch erstellen lassen, kann eine Datentypkonvertierung erfolgen, wenn zwischen den Quell- und Zielspeichern Inkompatibilitäten vorliegen. Wenn es eine nicht unterstützte Datentypkonvertierung zwischen der Quell- und Zielspalte gibt, wird eine Fehlermeldung neben der entsprechenden Tabelle angezeigt.

    ![Seite „Schemazuordnung“](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. Führen Sie auf der Seite **Einstellungen** die folgenden Schritte aus:

    a. Klicken Sie im Abschnitt **Stagingeinstellungen** auf **+ Neu**, um einen neuen Stagingspeicher zu erstellen. Der Speicher wird für das Staging der Daten verwendet, bevor diese mit PolyBase in SQL Data Warehouse geladen werden. Nach Abschluss des Kopiervorgangs werden die vorläufigen Daten in Azure Storage automatisch bereinigt. 

    ![Konfigurieren des Stagings](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. Wählen Sie auf der Seite **Neuer verknüpfter Dienst** Ihr Speicherkonto und dann **Fertig stellen** aus.
   
    ![Konfigurieren von Azure Storage](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. Deaktivieren Sie im Abschnitt **Erweiterte Einstellungen** die Option **Use type default** (Typstandard verwenden), und wählen Sie dann **Weiter** aus.

    ![Konfigurieren von PolyBase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und klicken Sie dann auf **Weiter**:

    ![Seite „Zusammenfassung“](./media/load-azure-sql-data-warehouse/summary-page.png)
1. Klicken Sie auf der Seite **Bereitstellung** auf **Überwachen**, um die Pipeline (Task) zu überwachen:

    ![Bereitstellungsseite](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. Beachten Sie, dass die Registerkarte **Überwachen** auf der linken Seite automatisch ausgewählt ist. In der Spalte **Aktionen** werden Links zum Anzeigen von Aktivitätsausführungsdetails und zum erneuten Ausführen der Pipeline angezeigt: 

    ![Überwachen der Pipelineausführungen](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. Klicken Sie in der Spalte **Aktionen** auf den Link **Aktivitätsausführungen anzeigen**, um mit der Pipelineausführung verknüpfte Aktivitätsausführungen anzuzeigen. Klicken Sie oben auf den Link **Pipelines**, um zurück zur Ansicht mit den Pipelineausführungen zu wechseln. Klicken Sie zum Aktualisieren der Liste auf **Aktualisieren**. 

    ![Überwachung der Aktivitätsausführungen](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Zum Überwachen der Ausführungsdetails jeder Kopieraktivität wählen Sie in der Aktivitätsüberwachungsansicht unter **Aktionen** den Link **Details** aus. Sie können Details wie die Menge der Daten, die aus der Quelle in die Senke kopiert wurden, den Datendurchsatz, die Ausführungsschritte mit entsprechender Dauer sowie die verwendeten Konfigurationen überwachen:

    ![Überwachen der Details zur Aktivitätsausführung](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den folgenden Artikel, um mehr über die Unterstützung von Azure SQL Data Warehouse zu erfahren: 

> [!div class="nextstepaction"]
>[Azure SQL Data Warehouse-Connector](connector-azure-sql-data-warehouse.md)

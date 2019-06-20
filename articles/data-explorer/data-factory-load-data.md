---
title: Kopieren von Daten aus Azure Data Factory in Azure Data Explorer
description: In diesem Thema wird erläutert, wie Sie Daten mithilfe des Azure Data Factory-Kopiertools in Azure Data Explorer laden (erfassen).
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 64856d53168a7676cf279da2d8675ce81e1985f7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60447873"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>Kopieren von Daten in Azure Data Explorer mithilfe von Azure Data Factory 

Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst für Echtzeitanalysen großer Datenmengen, die aus vielfältigen Quellen wie Anwendungen, Websites und IoT-Geräten gestreamt werden. Erkunden Sie Daten interaktiv, und ermitteln Sie Muster und Anomalien, um Produkte zu optimieren, das Kundenerlebnis zu verbessern, Geräte zu überwachen und den Betrieb auszuweiten. Erkunden Sie neue Fragen, und erhalten Sie Antworten in Minuten. Azure Data Factory ist ein vollständig verwalteter, cloudbasierter Datenintegrationsdienst. Mithilfe dieses Diensts können Sie Ihre Azure Data Explorer-Datenbank mit Daten aus dem vorhandenen System füllen und beim Erstellen von Analyselösungen Zeit sparen.

Azure Data Factory bietet die folgenden Vorteile beim Laden von Daten in Azure Data Explorer:

* **Mühelose Einrichtung**: Intuitiver Assistent mit fünf Schritten. Keine Skripterstellung erforderlich.
* **Unterstützung für umfangreiche Datenspeicher**: Integrierte Unterstützung für umfangreiche lokale und cloudbasierte Datenspeicher. Eine ausführliche Liste finden Sie in der Tabelle [Unterstützte Datenspeicher](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Sicher und kompatibel**: Daten werden über HTTPS oder ExpressRoute übertragen. Globale Dienste stellen sicher, dass Ihre Daten nie die geografische Grenze verlassen.
* **Hohe Leistung**: Bis zu 1GB/s Datenladegeschwindigkeit in Azure Data Explorer. Weitere Informationen finden Sie unter [Leistung der Kopieraktivität](/azure/data-factory/copy-activity-performance).

In diesem Artikel erfahren Sie, wie Sie das Tool zum Kopieren von Daten in Data Factory zum Laden von Daten aus Amazon S3 in Azure Data Explorer verwenden. Sie können ähnliche Schritte zum Kopieren von Daten aus anderen Datenspeichern wie [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage), [Azure SQL-Datenbank](/azure/data-factory/connector-azure-sql-database), [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse), [Google BigQuery](/azure/data-factory/connector-google-bigquery),[Oracle](/azure/data-factory/connector-oracle) und [Dateisystem](/azure/data-factory/connector-file-system) ausführen.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* [Azure Data Explorer-Cluster und Datenbank](create-cluster-database-portal.md)
* Datenquelle.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Klicken Sie auf die Schaltfläche **Ressource erstellen** (+) oben links im Portal > **Analytics** > **Data Factory**.

   ![Erstellen einer neuen Data Factory](media/data-factory-load-data/create-adf.png)

1. Geben Sie auf der Seite **Neue Data Factory** Werte für die folgenden Felder an, und wählen Sie anschließend **Erstellen** aus.

    ![Seite „Neue Data Factory“](media/data-factory-load-data/my-new-data-factory.png)

    **Einstellung**  | **Feldbeschreibung**
    |---|---|
    | **Name** | Geben Sie einen global eindeutigen Namen für die Data Factory ein. Wenn die Fehlermeldung *„Data Factory mit dem Namen \"LoadADXDemo\" ist nicht verfügbar* angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](/azure/data-factory/naming-rules).|
    | **Abonnement** | Wählen Sie Ihr Azure-Abonnement aus, in dem die Data Factory erstellt werden soll. |
    | **Ressourcengruppe** | Wählen Sie **Neu erstellen**, und geben Sie den Namen einer neuen Ressourcengruppe ein. Wählen Sie **Vorhandene verwenden** aus, wenn Sie über eine vorhandene Ressourcengruppe verfügen. |
    | **Version** | Wählen Sie **V2** aus. |
    | **Location** | Wählen Sie den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die von der Data Factory verwendeten Datenspeicher können sich an anderen Standorten bzw. in anderen Regionen befinden. |
    | | |

1. Wählen Sie auf der Symbolleiste die Option „Benachrichtigungen“ aus, um den Erstellungsvorgang zu überwachen. Navigieren Sie nach Abschluss der Erstellung zu Ihrer Data Factory. Die Startseite der **Data Factory** wird geöffnet.

   ![Data Factory-Startseite](media/data-factory-load-data/data-factory-home-page.png)

1. Wählen Sie die Kachel **Erstellen und überwachen** aus, um die Anwendung auf einer separaten Registerkarte zu starten.

## <a name="load-data-into-azure-data-explorer"></a>Laden von Daten in Azure Data Explorer

Daten können aus vielen Typen von [Datenspeichern](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) in Azure Data Explorer geladen werden. In diesem Thema wird das Laden von Daten aus Amazon S3 erläutert.

Es gibt zwei Möglichkeiten, Daten mithilfe von Azure Data Factory in Azure Data Explorer zu laden:

* Azure Data Factory-Benutzeroberfläche – [Registerkarte **Autor**](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [Azure Data Factory-Tool **Daten kopieren**](/azure/data-factory/quickstart-create-data-factory-copy-data-tool), das in diesem Artikel verwendet wird.

### <a name="copy-data-from-amazon-s3-source"></a>Kopieren von Daten aus Amazon S3 (Quelle)

1. Klicken Sie auf der Seite **Erste Schritte** auf die Kachel **Daten kopieren**, um das Tool zum Kopieren von Daten zu starten.

   ![Kachel für das Tool zum Kopieren von Daten](media/data-factory-load-data/copy-data-tool-tile.png)

1. Geben Sie auf der Seite **Eigenschaften** im Feld **Aufgabenname** einen Namen ein, und klicken Sie dann auf **Weiter**.

    ![Kopieren aus Quelleigenschaften](media/data-factory-load-data/copy-from-source.png)

1. Klicken Sie auf der Seite **Quelldatenspeicher** auf **+ Neue Verbindung erstellen**.

    ![Quelldaten – Verbindung erstellen](media/data-factory-load-data/source-create-connection.png)

1. Wählen Sie **Amazon S3** und anschließend **Weiter** aus.

    ![Neuer verknüpfter Dienst](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Führen Sie auf der Seite **Neuer verknüpfter Dienst (Amazon S3)** die folgenden Schritte aus:

    ![Mit Amazon S3 verknüpften Dienst angeben](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * Geben Sie im Feld **Name** den Namen des neuen verknüpften Dienstes an.
    * Wählen Sie im Dropdown den Wert **Connect via integration runtime** (Über Integration Runtime verbinden) aus.
    * Geben Sie den Wert für die **Zugriffsschlüssel-ID** an.
    * Geben Sie den Wert für den **geheimen Zugriffsschlüssel** an.
    * Wählen Sie **Verbindung testen** aus, um die erstellte Verbindung mit dem verknüpften Dienst zu testen.
    * Wählen Sie **Fertig stellen** aus.

1. Auf der Seite **Quelldatenspeicher** wird die neue Verbindung mit AmazonS31 angezeigt. Klicken Sie auf **Weiter**.

   ![Erstellte Verbindung mit Quelldatenspeicher](media/data-factory-load-data/source-data-store-created-connection.png)

1. Auf der Seite für **Eingabedatei oder -ordner auswählen**:

    1. Navigieren Sie zum Ordner bzw. zur Datei, der bzw. die kopiert werden soll. Wählen Sie den Ordner bzw. die Datei aus.
    1. Wählen Sie das Kopierverhalten entsprechend den jeweiligen Anforderungen aus. Lassen Sie die Option **Binary copy** (Binärkopie) deaktiviert.
    1. Klicken Sie auf **Weiter**.

    ![Auswählen der Eingabedatei bzw. des Eingabeordners](media/data-factory-load-data/source-choose-input-file.png)

1. Wählen Sie auf der Seite **file formats settings** (Einstellungen für Dateiformate) die relevanten Einstellungen für die Datei aus, und klicken Sie auf **Weiter**.

   ![Auswählen der Eingabedatei bzw. des Eingabeordners](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Kopieren von Daten in Azure Data Explorer (Ziel)

Der neue verknüpfte Azure Data Explorer-Dienst wird erstellt, um die Daten in die unten angegebene Azure Data Explorer-Zieltabelle (Senke) zu kopieren.

1. Auf der Seite **Zieldatenspeicher** können Sie eine vorhandene Datenspeicherverbindung übernehmen oder einen neuen Datenspeicher angeben, indem Sie auf **+ Neue Verbindung erstellen** klicken.

    ![Seite „Zieldatenspeicher“](media/data-factory-load-data/destination-create-connection.png)

1. Wählen Sie auf der Seite **Neuer verknüpfter Dienst** die Option **Azure Data Explorer** aus, und klicken Sie dann auf **Weiter**.

    ![„Azure Data Explorer – Neuer verknüpfter Dienst“ auswählen](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Führen Sie auf der Seite **Neuer verknüpfter Dienst (Azure Data Explorer)** die folgenden Schritte aus:

    ![ADX – Neuer verknüpfter Dienst](media/data-factory-load-data/adx-new-linked-service.png)

   * Wählen Sie den **Namen** für den neuen verknüpften Azure Data Explorer-Dienst aus.
   * In **Account selection method** (Auswahlmethode für Konten): 
        * Klicken Sie auf das Optionsfeld **From Azure subscription** (Aus Azure-Abonnement), und wählen Sie das Konto für Ihr **Azure-Abonnement** aus. Wählen Sie anschließend Ihren **Cluster** aus. In der Dropdownliste werden nur Cluster aufgeführt, die dem Benutzer gehören.
        * Sie können auch auf das Optionsfeld **Manuell eingeben** klicken und Ihren **Endpunkt** eingeben.
    * Geben Sie den **Mandanten** an.
    * Geben Sie die **Dienstprinzipal-ID** ein.
    * Klicken Sie auf die Schaltfläche **Dienstprinzipalschlüssel**, und geben Sie den **Dienstprinzipalschlüssel** ein.
    * Wählen Sie Ihre **Datenbank** im Dropdownmenü aus. Sie können auch das Kontrollkästchen **Bearbeiten** aktivieren und den Namen Ihrer Datenbank eingeben.
    * Wählen Sie **Verbindung testen** aus, um die erstellte Verbindung mit dem verknüpften Dienst zu testen. Wenn Sie eine Verbindung mit Ihrem Setup herstellen können, wird ein grünes Häkchen (**Verbindung erfolgreich**) angezeigt.
    * Wählen Sie **Fertig stellen** aus, um die Erstellung des verknüpften Diensts abzuschließen.

    > [!NOTE]
    > Der Dienstprinzipal wird von Azure Data Factory für den Zugriff auf den Azure Data Explorer-Dienst verwendet. [Erstellen Sie einen Azure Active Directory (Azure AD)-Dienstprinzipal](/azure/azure-stack/azure-stack-create-service-principals#manage-service-principal-for-azure-ad) als Dienstprinzipal. Verwenden Sie nicht die **Azure Key Vault**-Methode.

1. Der **Zieldatenspeicher** wird geöffnet. Die erstellte Azure Data Explorer-Datenverbindung kann nun verwendet werden. Wählen Sie **Weiter** aus, um die Verbindung zu konfigurieren.

    ![ASX – Zieldatenspeicher](media/data-factory-load-data/destination-data-store.png)

1. Legen Sie unter **Tabellenmapping** den Namen der Zieltabelle fest, und wählen Sie **Weiter** aus.

    ![Ziel-Dataset – Tabellenmapping](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. Auf der Seite **Column mapping** (Spaltenzuordnung):
    * Die erste Zuordnung wird von ADF entsprechend der [ADF-Schemazuordnung](/azure/data-factory/copy-activity-schema-and-type-mapping) durchgeführt.
        * Legen Sie die **Column mappings** (Spaltenzuordnungen) für die Azure Data Factory-Zieltabelle fest. Die Standardzuordnung von der Quelle zur ADF-Zieltabelle wird angezeigt.
        * Deaktivieren Sie die Spalten, die Sie beim Definieren der Spaltenzuordnungen nicht benötigen.
    * Die zweite Zuordnung erfolgt, wenn diese tabellarischen Daten in Azure Data Explorer erfasst werden. Die Zuordnung erfolgt gemäß den [CSV-Zuordnungsregeln](/azure/kusto/management/mappings#csv-mapping). Beachten Sie Folgendes: Selbst wenn die Datenquelle nicht im CSV-Format vorlag, hat ADF die Daten in ein tabellarisches Format umgewandelt. Daher ist die CSV-Zuordnung an diesem Punkt die einzige relevante Zuordnung.
        * Fügen Sie unter **Azure Data Explorer (Kusto) sink properties** (Azure Data Explorer (Kusto)-Senkeneigenschaften) den relevanten **Ingestion mapping name** (Name der Erfassungszuordnung) (optional) hinzu, sodass die Spaltenzuordnung verwendet werden kann.
        * Wenn kein Wert für **Ingestion mapping name** (Name der Erfassungszuordnung) angegeben wird, wird die Zuordnungsreihenfolge „nach Name“ angewendet, die im Abschnitt **Column mappings** (Spaltenzuordnungen) definiert wurde. Wenn die Zuordnung „nach Name“ fehlschlägt, versucht Azure Data Explorer, die Daten mittels Zuordnung „nach Spaltenposition“ zu erfassen (standardmäßig werden Zuordnungen nach Position vorgenommen).
    * Wählen Sie **Weiter** aus.

    ![Ziel-Dataset – Tabellenmapping](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Auf der Seite **Einstellungen** :
    * Legen Sie die relevanten **fault tolerance settings** (Fehlertoleranzeinstellungen) fest.
    * **Leistungseinstellungen**: „Enable Staging“ (Staging aktivieren) ist nicht anwendbar. Unter **Erweiterte Einstellungen** werden Kostenaspekte berücksichtigt. Sofern keine besonderen Anforderungen vorliegen, lassen Sie die Einstellungen unverändert.
    * Klicken Sie auf **Weiter**.

    ![Einstellungen für „Daten kopieren“](media/data-factory-load-data/copy-data-settings.png)

1. Überprüfen Sie unter **Zusammenfassung** die Einstellungen, und klicken Sie dann auf **Weiter**.

    ![Zusammenfassung für „Daten kopieren“](media/data-factory-load-data/copy-data-summary.png)

1. Auf der Seite **Bereitstellung**:
    * Wählen Sie **Überwachung** aus, um zur Registerkarte **Überwachung** zu wechseln und den Status der Pipeline (Fortschritt, Fehler und Datenfluss) zu überwachen.
    * Wählen Sie **Edit Pipeline** (Pipeline bearbeiten) aus, um verknüpfte Dienste, Datasets und Pipelines zu bearbeiten.
    * Wählen Sie **Fertig stellen** aus, um die Aufgabe „Daten kopieren“ abzuschließen.

    ![Bereitstellungsseite](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über den [Azure Data Explorer-Connector](/azure/data-factory/connector-azure-data-explorer) in Azure Data Factory.

* Informieren Sie sich über das Bearbeiten von verknüpften Diensten, Datasets und Pipelines in der [Data Factory-Benutzeroberfläche](/azure/data-factory/quickstart-create-data-factory-portal).

* Erfahren Sie mehr über [Azure Data Explorer-Abfragen](/azure/data-explorer/web-query-data) zum Abfragen von Daten.

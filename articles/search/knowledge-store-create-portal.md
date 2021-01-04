---
title: Erstellen eines Wissensspeichers im Azure-Portal
titleSuffix: Azure Cognitive Search
description: Verwenden Sie den Datenimport-Assistenten, um einen Wissensspeicher zum Speichern angereicherter Inhalte zu erstellen. Stellen Sie von anderen Apps aus eine Verbindung mit einem Wissensspeicher her, um Analysen durchzuführen, oder senden Sie angereicherte Inhalte an Downstreamprozesse.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/17/2020
ms.openlocfilehash: 3225013f09abd326c619b67caf77918889a64859
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94741806"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Schnellstart: Erstellen eines Azure Cognitive Search-Wissensspeichers im Azure-Portal

Der Wissensspeicher ist ein Azure Cognitive Search-Feature, das Ausgaben aus einer Pipeline für Inhaltsverarbeitung zur späteren Analyse oder für die Downstreamverarbeitung speichert. 

Eine Pipeline akzeptiert unstrukturierte Text- und Bildinhalte, wendet über Cognitive Services künstliche Intelligenz (etwa OCR und Verarbeitung natürlicher Sprache) an und gibt neue Strukturen und Informationen aus, die zuvor nicht vorhanden waren. Eines der physischen Artefakte, die durch eine Pipeline erstellt werden, ist ein [Wissensspeicher](knowledge-store-concept-intro.md), auf den Sie über Tools zugreifen können, um den Inhalt zu analysieren und zu untersuchen.

In dieser Schnellstartanleitung werden Dienste und Daten in der Azure-Cloud miteinander kombiniert, um einen Wissensspeicher zu erstellen. Nach Abschluss der Einrichtung wird im Portal der **Datenimport-Assistent** ausgeführt, um alles miteinander zu verknüpfen. Am Ende verfügen Sie neben den ursprünglichen Textinhalten über angereicherte Inhalte, die Sie im Portal mithilfe des [Storage-Explorer](knowledge-store-view-storage-explorer.md) anzeigen können.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).

+ Ein Azure Cognitive Search-Dienst [Erstellen Sie einen Dienst](search-create-service-portal.md), oder suchen Sie in Ihrem aktuellen Abonnement [nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Für diesen Schnellstart können Sie einen kostenlosen Dienst verwenden. 

+ Ein Azure Storage-Konto mit [Blob Storage](../storage/blobs/index.yml).

> [!NOTE]
> In diesem Schnellstart wird außerdem [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) für die KI verwendet. Aufgrund der geringen Workloadgröße wird Cognitive Services im Hintergrund für die kostenlose Verarbeitung von bis zu 20 Transaktionen genutzt. Das bedeutet, dass Sie diese Übung durchführen können, ohne eine zusätzliche Cognitive Services-Ressource erstellen zu müssen.

## <a name="set-up-your-data"></a>Einrichten Ihrer Daten

In den folgenden Schritten richten Sie einen Blobcontainer in Azure Storage ein, um heterogene Inhaltsdateien zu speichern.

1. [Laden Sie „HotelReviews_Free.csv“ herunter.](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D) Bei diesen Daten handelt es sich um gespeicherte Hotelrezensionen (von Kaggle.com) in einer CSV-Datei. Die Daten umfassen 19 Kundenfeedbacks für ein einzelnes Hotel. 

1. [Erstellen Sie ein Azure-Speicherkonto](../storage/common/storage-account-create.md?tabs=azure-portal), oder [suchen Sie nach einem vorhandenen Konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) in Ihrem aktuellen Abonnement. Azure Storage wird sowohl für die zu importierenden Rohinhalte als auch für den resultierenden Wissensspeicher verwendet.

   + Wählen Sie den Kontotyp **StorageV2 (allgemein, Version 2)** aus.

1. Öffnen Sie die Seiten für Blobdienste, und erstellen Sie einen Container mit dem Namen *hotel-reviews*.

1. Klicken Sie auf **Hochladen**.

    ![Hochladen der Daten](media/knowledge-store-create-portal/upload-command-bar.png "Hochladen der Hotelrezensionen")

1. Wählen Sie die Datei **HotelReviews-Free.csv** aus, die Sie im ersten Schritt heruntergeladen haben.

    ![Erstellen des Azure-Blobcontainers](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Erstellen des Azure-Blobcontainers")

1. Bevor Sie die Blob Storage-Seiten schließen, öffnen Sie über einen Link im linken Navigationsbereich die Seite **Zugriffsschlüssel**. Rufen Sie eine Verbindungszeichenfolge für den Datenabruf aus Blob Storage ab. Eine Verbindungszeichenfolge sieht in etwa wie im folgenden Beispiel aus: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Nun können Sie zum **Datenimport**-Assistenten wechseln.

## <a name="run-the-import-data-wizard"></a>Ausführen des Datenimport-Assistenten

1. Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com/) an.

1. [Suchen Sie Ihren Suchdienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/), und klicken Sie auf der Übersichtsseite auf der Befehlsleiste auf **Daten importieren**, um in vier Schritten einen Wissensspeicher zu erstellen.

   ![Befehl zum Importieren von Daten](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Schritt 1: Erstellen einer Datenquelle

1. Wählen Sie unter **Verbindung mit Ihren Daten herstellen** die Option **Azure Blob Storage** sowie das erstellte Konto und den erstellten Container aus. 

1. Geben Sie unter **Name** den Namen `hotel-reviews-ds` ein.

1. Wählen Sie unter **Analysemodus** die Option **Durch Trennzeichen getrennter Text** aus, und aktivieren Sie anschließend das Kontrollkästchen **Erste Zeile enthält Überschrift**. Vergewissern Sie sich, dass unter **Trennzeichen** ein Komma (,) angegeben ist.

1. Fügen Sie in **Verbindungszeichenfolge** die Verbindungszeichenfolge ein, die Sie aus der Seite **Zugriffsschlüssel** in Azure Storage kopiert haben.

1. Geben Sie in **Container** den Namen des Blobontainers ein, der die Daten enthält.

    Ihre Seite sollte in etwa dem folgenden Screenshot entsprechen.

    ![Erstellen eines Datenquellenobjekts](media/knowledge-store-create-portal/hotel-reviews-ds.png "Erstellen eines Datenquellenobjekts")

1. Wechseln Sie zur nächsten Seite.

### <a name="step-2-add-cognitive-skills"></a>Schritt 2: Hinzufügen von kognitiven Qualifikationen

In diesem Schritt des Assistenten erstellen Sie ein Skillset mit Anreicherungen für kognitive Qualifikationen. Die Quelldaten bestehen aus Kundenbewertungen in verschiedenen Sprachen. Zu den Qualifikationen, die für dieses Dataset relevant sind, gehören die Schlüsselwortextraktion, die Stimmungserkennung und die Textübersetzung. Diese Anreicherungen werden in einem späteren Schritt als Azure-Tabellen in einen Wissensspeicher „projiziert“.

1. Erweitern Sie **Cognitive Services-Instanz anfügen**. Standardmäßig ist **Free (begrenzte Anreicherung)** ausgewählt. Da „HotelReviews-Free.csv“ 19 Datensätze enthält und mit der kostenlosen Ressource 20 Transaktionen pro Tag möglich sind, können Sie diese Ressource verwenden.

1. Erweitern Sie **Anreicherungen hinzufügen**.

1. Geben Sie unter **Name des Skillsets** den Namen `hotel-reviews-ss` ein.

1. Wählen Sie unter **Quelldatenfeld** die Option **reviews_text** aus.

1. Wählen Sie unter **Granularitätsebene für Anreicherung** die Option **Seiten (5.000 Zeichenblöcke)** aus.

1. Wählen Sie die folgenden kognitiven Qualifikationen aus:
    + **How to extract key phrases in Text Analytics** (Extrahieren von Schlüsselbegriffen mithilfe der Textanalyse)
    + **Übersetzen von Text**
    + **Stimmung erkennen**

      ![Erstellen eines Skillsets](media/knowledge-store-create-portal/hotel-reviews-ss.png "Erstellen eines Skillsets")

1. Erweitern Sie **Anreicherungen in einem Wissensspeicher speichern (Vorschau)** .

1. Wählen Sie unter **Azure-Tabellenprojektionen** die folgenden Projektionen aus:
    + **Dokumente**
    + **Seiten**
    + **Schlüsselbegriffe**

1. Geben Sie unter **Verbindungszeichenfolge für Speicherkonto** die Verbindungszeichenfolge ein, die Sie in einem früheren Schritt gespeichert haben.

    ![Konfigurieren des Wissensspeichers](media/knowledge-store-create-portal/hotel-reviews-ks.png "Konfigurieren des Wissensspeichers")

1. Optional können Sie eine Power BI-Vorlage herunterladen. Wenn Sie über den Assistenten auf die Vorlage zugreifen, wird die lokale PBIT-Datei angepasst, um die Form der Daten widerzuspiegeln.

1. Wechseln Sie zur nächsten Seite.

### <a name="step-3-configure-the-index"></a>Schritt 3: Konfigurieren des Index

In diesem Schritt des Assistenten konfigurieren Sie einen Index für optionale Volltextsuchabfragen. Der Assistent analysiert Ihre Datenquelle anhand einer Stichprobe, um Felder und Datentypen abzuleiten. Sie müssen lediglich die Attribute für das gewünschte Verhalten auswählen. Mit dem Attribut **Abrufbar** kann der Suchdienst beispielsweise einen Feldwert zurückgeben. Das Attribut **Suchbar** ermöglicht dagegen eine Volltextsuche für das Feld.

1. Geben Sie unter **Indexname** den Namen `hotel-reviews-idx` ein.

1. Übernehmen Sie für Attribute die Standardauswahl: **Abrufbar** und **Durchsuchbar** für die neuen Felder, die von der Pipeline erstellt werden.

    Ihr Index sollte in etwa wie in der folgenden Abbildung aussehen: Aufgrund der Länger der Liste sind in der Abbildung nicht alle Felder zu sehen.

    ![Konfigurieren eines Index](media/knowledge-store-create-portal/hotel-reviews-idx.png "Konfigurieren eines Index")

1. Wechseln Sie zur nächsten Seite.

### <a name="step-4-configure-the-indexer"></a>Schritt 4: Konfigurieren des Indexers

In diesem Schritt des Assistenten konfigurieren Sie einen Indexer, der die Datenquelle, das Skillset und den Index aus den vorherigen Schritten zusammenführt.

1. Geben Sie unter **Name**`hotel-reviews-idxr` ein.

1. Behalten Sie unter **Zeitplan** den Standardwert **Einmalig** bei.

1. Klicken Sie auf **Senden**, um den Indexer auszuführen. In diesem Schritt werden die Datenextraktion und die Indizierung durchgeführt und die kognitiven Qualifikationen angewendet.

## <a name="monitor-status"></a>Überwachen des Status

Die Indizierung kognitiver Qualifikationen dauert länger als die übliche textbasierte Indizierung. Der Assistent sollte die Liste „Indexer“ auf der Übersichtsseite öffnen, damit Sie den Status nachverfolgen können. Zur eigenen Navigation wechseln Sie zur Seite „Übersicht“, und klicken Sie auf **Indexer**.

Im Azure-Portal können Sie auch das Benachrichtigungsaktivitätsprotokoll auf einen klickbaren Statuslink für eine **Azure Cognitive Search-Benachrichtigung** überprüfen. Die Ausführung kann mehrere Minuten dauern.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Daten mithilfe von Cognitive Services angereichert und die Ergebnisse in einen Wissensspeicher projiziert haben, können Sie den Storage-Explorer oder Power BI verwenden, um Ihr angereichertes Dataset zu untersuchen.

Sie können Inhalte im Storage-Explorer anzeigen oder mit Power BI noch einen Schritt weiter gehen und Erkenntnisse mittels Visualisierung gewinnen.

> [!div class="nextstepaction"]
> [Anzeigen mit dem Storage-Explorer](knowledge-store-view-storage-explorer.md)
> [Verbinden mit Power BI](knowledge-store-connect-power-bi.md)

> [!Tip]
> Wenn Sie diese Übung wiederholen oder eine andere exemplarische Vorgehensweise für die KI-Anreicherung ausprobieren möchten, löschen Sie den Indexer *hotel-reviews-idxr*. Durch Löschen des Indexers wird der Zähler für kostenlose Transaktionen pro Tag für die Cognitive Services-Verarbeitung auf Null zurückgesetzt.

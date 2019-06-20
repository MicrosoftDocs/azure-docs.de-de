---
title: Erfassen von Streamingereignissen über das Azure-Portal – Azure Event Hubs | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie über das Azure-Portal die Erfassung von über Azure Event Hubs gestreamten Ereignissen aktivieren.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: shvija
ms.openlocfilehash: 9108c52529319288fba48dbad3c6f8aa6cb5f725
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60822544"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Aktivieren der Erfassung von über Azure Event Hubs gestreamten Ereignissen

Mit Azure [Event Hubs Capture][capture-overview] können Sie die Streamingdaten in Event Hubs automatisch an ein [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)- oder [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)-Konto Ihrer Wahl übermitteln.

Sie können Capture über das [Azure-Portal](https://portal.azure.com) zum Zeitpunkt der Event Hub-Erstellung konfigurieren. Die Daten können entweder in einem Azure [Blob Storage](https://azure.microsoft.com/services/storage/blobs/)-Container oder in einem [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)-Konto erfasst werden.

Weitere Informationen finden Sie in der [Übersicht über Event Hubs Capture][capture-overview].

## <a name="capture-data-to-an-azure-storage-account"></a>Erfassen von Daten in einem Azure Storage-Konto  

Beim Erstellen eines Event Hubs können Sie Capture aktivieren, indem Sie auf dem Portalblatt **Event Hub erstellen** auf die Schaltfläche **Ein** klicken. Anschließend geben Sie ein Speicherkonto und einen Container an, indem Sie im Feld **Capture Provider** (Capture-Anbieter) auf **Azure Storage** klicken. Da Event Hubs Capture die Dienst-zu-Dienst-Authentifizierung mit Speicher verwendet, müssen Sie keine Speicherverbindungszeichenfolge angeben. Die Ressourcenauswahl wählt automatisch den Ressourcen-URI für Ihr Speicherkonto. Wenn Sie Azure Resource Manager verwenden, müssen Sie diesen URI explizit als Zeichenfolge angeben.

Das Standadzeitfenster beträgt fünf Minuten. Der Mindestwert ist 1, der Höchstwert 15. Für das **Größenfenster** gilt ein Bereich von 10 bis 500 MB.

![Zeitfenster für die Erfassung][1]

> [!NOTE]
> Sie können die Ausgabe leerer Dateien, wenn während des Erfassungszeitfensters keine Ereignisse auftreten, aktivieren oder deaktivieren. 

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Erfassen von Daten in einem Azure Data Lake Store-Konto

Wenn Sie Daten in Azure Data Lake Store erfassen möchten, müssen Sie ein Data Lake Store-Konto und einen Event Hub erstellen:

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Erstellen eines Azure Data Lake Store-Kontos und Erstellen von Ordnern

> [!NOTE]
> Die Event Hubs Capture-Funktion unterstützt zurzeit nur Gen 1 von Azure Data Lake Store, nicht Gen 2. 

1. Erstellen Sie gemäß der Anleitung unter [Erste Schritte mit Azure Data Lake Store über das Azure-Portal](../data-lake-store/data-lake-store-get-started-portal.md) ein Data Lake Store Gen 1-Konto.
2. Führen Sie die Anleitungsschritte im Abschnitt [Zuweisen von Berechtigungen für Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) aus, um unter dem Data Lake Store-Konto einen Ordner zu erstellen, in dem Sie die Daten aus Event Hubs erfassen möchten. Sie können für Event Hubs auch Berechtigungen zuweisen, damit diese Daten in Ihr Data Lake Store-Konto schreiben können.  


### <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs

1. Der Event Hub muss dem gleichen Azure-Abonnement angehören wie die Azure Data Lake Store-Instanz, die Sie soeben erstellt haben. Erstellen Sie den Event Hub, indem Sie auf der Portalseite **Event Hub erstellen** unter **Capture** auf die Schaltfläche **Ein** klicken. 
2. Wählen Sie auf der Portalseite **Event Hub erstellen** im Feld **Capture Provider** (Capture-Anbieter) die Option **Azure Data Lake Store** aus.
3. Geben Sie unter **Data Lake Store auswählen** das zuvor erstellte Data Lake Store-Konto und im Feld für den **Data Lake-Pfad** den Pfad zu dem von Ihnen erstellten Datenordner an.

    ![Auswählen des Data Lake Storage-Kontos][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Hinzufügen oder Konfigurieren von Capture für einen bereits vorhandenen Event Hub

Capture kann für vorhandene Event Hubs in Event Hubs-Namespaces konfiguriert werden. Wenn Sie Capture für einen vorhandenen Event Hub aktivieren oder Ihre Capture-Einstellungen ändern möchten, klicken Sie auf den Namespace, um den Bildschirm mit der Übersicht zu laden. Klicken Sie dann auf den Event Hub, den Sie aktivieren bzw. dessen Capture-Einstellung Sie ändern möchten. Klicken Sie abschließend auf der linken Seite der geöffneten Seite auf die Option **Capture**, und bearbeiten Sie die Einstellungen, wie in den folgenden Abbildungen dargestellt:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![Konfigurieren von Azure Blob Storage][2]

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

![Konfigurieren von Azure Data Lake Storage][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Event Hubs Capture finden Sie in der [Übersicht über Event Hubs Capture][capture-overview].
- Sie können Event Hubs Capture auch über Azure Resource Manager-Vorlagen konfigurieren. Weitere Informationen finden Sie unter [Aktivieren von Capture über eine Azure Resource Manager-Vorlage](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Erstellen eines Azure Event Grid-Abonnements mit einem Event Hubs-Namespace als Quelle](store-captured-data-data-warehouse.md)
- [Erste Schritte mit Azure Data Lake Store über das Azure-Portal](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md

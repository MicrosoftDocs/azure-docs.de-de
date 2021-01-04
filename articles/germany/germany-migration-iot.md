---
title: Migrieren von Azure-IoT-Ressourcen von Azure Deutschland zu Azure weltweit
description: Dieser Artikel enthält Informationen zum Migrieren von Azure-IoT-Ressourcen von Azure Deutschland zu Azure weltweit.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 6e480ef9c12bdf425a41f567f1941d3f1a654b08
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018583"
---
# <a name="migrate-iot-resources-to-global-azure"></a>Migrieren von IoT-Ressourcen zu Azure weltweit

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Dieser Artikel enthält Informationen dazu, wie Sie Azure-IoT-Ressourcen von Azure Deutschland zu Azure weltweit migrieren können.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Sie können das Azure Cosmos DB-Datenmigrationstool verwenden, um Daten zu Azure Cosmos DB zu migrieren. Das Azure Cosmos DB-Datenmigrationstool ist eine Open-Source-Lösung, mit der Daten aus verschiedenen Quellen in Azure Cosmos DB importiert werden können.

Das Azure Cosmos DB-Datenmigrationstool steht als GUI-Tool oder als Befehlszeilentool zur Verfügung. Der Quellcode ist im GitHub-Repository [Azure Cosmos DB Data Migration Tool](https://github.com/azure/azure-documentdb-datamigrationtool) verfügbar. Eine [kompilierte Version des Tools](https://www.microsoft.com/download/details.aspx?id=46436) ist im Microsoft Download Center verfügbar.

Um Azure Cosmos DB-Ressourcen zu migrieren, sollten Sie die folgenden Schritte ausführen:

1. Überprüfen Sie die Anforderungen an die Anwendungsbetriebszeit und die Kontokonfigurationen, um den besten Aktionsplan zu bestimmen.
1. Klonen Sie die Kontokonfigurationen aus Azure Deutschland in die neue Region, indem Sie das Datenmigrationstool ausführen.
1. Kann ein Wartungsfenster verwendet werden, kopieren Sie die Daten aus der Quelle in das Ziel, indem Sie das Datenmigrationstool ausführen.
1. Ist es nicht möglich, ein Wartungsfenster zu verwenden, kopieren Sie die Daten aus der Quelle in das Ziel, indem Sie das Tool und dann die folgenden Schritte ausführen:
   1. Verwenden Sie einen konfigurationsgesteuerten Ansatz, um Änderungen an den Lese-/Schreibvorgängen in einer Anwendung vorzunehmen.
   1. Führen Sie eine erstmalige Synchronisierung aus.
   1. Richten Sie eine inkrementelle Synchronisierung ein, und binden Sie den Änderungsfeed ein.
   1. Verknüpfen Sie Lesevorgänge mit dem neuen Konto, und überprüfen Sie die Anwendung.
   1. Beenden Sie Schreibvorgänge in das alte Konto, überprüfen Sie, ob der Änderungsfeed berücksichtigt wird, und verknüpfen Sie dann Schreibvorgänge mit dem neuen Konto.
   1. Beenden Sie das Tool, und löschen Sie das alte Konto.
1. Führen Sie das Tool aus, um zu überprüfen, ob die Daten im alten und im neuen Konto konsistent sind.

Weitere Informationen finden Sie unter:

- Lesen Sie eine [Einführung in Azure Cosmos DB](../cosmos-db/introduction.md).
- Erfahren Sie, wie [Daten in Azure Cosmos DB importiert werden](../cosmos-db/import-data.md).

## <a name="functions"></a>Functions

Ein Migrieren von Azure Functions-Ressourcen von Azure Deutschland zu Azure weltweit wird zurzeit nicht unterstützt. Die empfohlene Vorgehensweise ist, dass Sie eine Resource Manager-Vorlage exportieren, den Standort ändern und dann in der Zielregion erneut bereitstellen.

> [!IMPORTANT]
> Ändern Sie den Standort, Azure Key Vault-Geheimnisse, Zertifikate und andere GUIDs, damit diese konsistent mit der neuen Region sind.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Functions-Tutorials](../azure-functions/index.yml) durcharbeiten.
- Erfahren Sie, wie Sie [Resource Manager-Vorlagen exportieren](../azure-resource-manager/templates/export-template-portal.md), oder lesen Sie die Übersicht zu [Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Lesen Sie die [Übersicht zu Azure Functions](../azure-functions/functions-overview.md).
- Lesen Sie die [Übersicht über Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/).
- Erfahren Sie, wie Sie [eine Vorlage erneut bereitstellen](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="notification-hubs"></a>Notification Hubs

Um Einstellungen von einer Instanz von Azure Notification Hubs zu einer anderen Instanz zu migrieren, exportieren und importieren Sie alle Registrierungstoken und Tags:

1. [Exportieren Sie die vorhandenen Notification Hub-Registrierungen](/previous-versions/azure/azure-services/dn790624(v=azure.100)) in einen Azure Blob Storage-Container.
1. Erstellen Sie einen neuen Notification Hub in der Zielumgebung.
1. [Importieren Sie Ihre Registrierungstoken](/previous-versions/azure/azure-services/dn790624(v=azure.100)) aus Blobspeicher in Ihren neuen Notification Hub.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Notification Hubs-Tutorials](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md) durcharbeiten.
- Lesen Sie die [Übersicht über Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="iot-hub"></a>IoT Hub

Sie können zwar Azure IoT Hub-Instanzen von Azure Deutschland zu Azure weltweit migrieren, eine solche Migration ist aber nicht nahtlos.

> [!NOTE]
> Diese Migration kann dazu führen, dass Ausfallzeiten und Datenverluste für Ihre Azure-IoT-Anwendung auftreten. Alle Telemetriemeldungen, C2D-Befehle und auftragsbezogenen Informationen (Zeitpläne und Verlauf) werden nicht migriert. Sie müssen Ihre Geräte und Back-End-Anwendungen erneut konfigurieren, damit die neuen Verbindungszeichenfolgen verwendet werden.

### <a name="step-1-re-create-the-iot-hub"></a>Schritt 1: Erneutes Erstellen des IoT-Hubs

IoT Hub unterstützt nativ keine Klonvorgänge. Sie können jedoch über den Azure Resource Manager [eine Ressourcengruppe als Vorlage exportieren](../azure-resource-manager/templates/export-template-portal.md), um Ihre IoT Hub-Metadaten zu exportieren. Konfigurierte Routen und andere IoT-Hub-Einstellungen werden in die exportierten Metadaten einbezogen. Stellen Sie die Vorlage dann in Azure weltweit bereit. Möglicherweise finden Sie es einfacher, den IoT-Hub im Azure-Portal erneut zu erstellen, indem Sie sich die Details in der exportierten JSON-Datei ansehen.

### <a name="step-2-migrate-device-identities"></a>Schritt 2: Migrieren von Geräteidentitäten

So migrieren Sie Geräteidentitäten:

1. Verwenden Sie im Quellmandanten in Azure Deutschland die Resource Manager-API [ExportDevices](../iot-hub/iot-hub-bulk-identity-mgmt.md), um alle Geräteidentitäten, Gerätezwillinge und Modulzwillinge (einschließlich der Schlüssel) in einen Speichercontainer zu exportieren. Sie können einen Speichercontainer in Azure Deutschland oder Azure weltweit verwenden. Vergewissern Sie sich, dass der generierte Shared Access Signature-URI ausreichende Berechtigungen hat. 
1. Führen Sie die Resource Manager-API [ImportDevices](../iot-hub/iot-hub-bulk-identity-mgmt.md) aus, um alle Geräteidentitäten aus dem Speichercontainer in den geklonten IoT-Hub in Azure weltweit zu importieren.
1. Konfigurieren Sie Ihre Geräte und Back-End-Anwendungen neu, damit die neuen Verbindungszeichenfolgen verwendet werden. Der Hostname wird von **\*.azure-devices.de** in **\*.azure-devices.com** geändert.  

> [!NOTE]
> Die Stammzertifizierungsstelle ist in Azure Deutschland und Azure weltweit unterschiedlich. Sie müssen dies berücksichtigen, wenn Sie Ihre Geräte und Back-End-Anwendungen, die mit der IoT Hub-Instanz interagieren, neu konfigurieren.

Weitere Informationen finden Sie unter:

- Erfahren Sie, wie Sie [IoT Hub-Identitäten in einem Massenvorgang exportieren](../iot-hub/iot-hub-bulk-identity-mgmt.md#export-devices).
- Erfahren Sie, wie Sie [IoT Hub-Identitäten in einem Massenvorgang importieren](../iot-hub/iot-hub-bulk-identity-mgmt.md#import-devices).
- Lesen Sie die [Übersicht zu IoT Hub](../iot-hub/about-iot-hub.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Tools, Techniken und Empfehlungen zum Migrieren von Ressourcen in den folgenden Dienstkategorien:

- [Compute](./germany-migration-compute.md)
- [Netzwerk](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Datenbanken](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [Integration](./germany-migration-integration.md)
- [Identität](./germany-migration-identity.md)
- [Security](./germany-migration-security.md)
- [Verwaltungstools](./germany-migration-management-tools.md)
- [Medien](./germany-migration-media.md)
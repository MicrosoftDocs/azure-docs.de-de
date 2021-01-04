---
title: Azure-Datenübertragungsoptionen für große Datasets, mittlere bis hohe Netzwerkbandbreite | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Azure-Lösung für die Datenübertragung auswählen, wenn Sie eine mittlere bis hohe Netzwerkbandbreite in Ihrer Umgebung nutzen und planen, große Datasets zu übertragen.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: f1ded1ce87df1cb57960bbc99cd950ed7b3f97ee
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573934"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Datenübertragung für große Datasets mit mittlerer bis hoher Netzwerkbandbreite
 
Dieser Artikel bietet einen Überblick über die Datenübertragungslösungen, wenn Sie in Ihrer Umgebung eine mittlere bis hohe Netzwerkbandbreite nutzen und planen, große Datasets zu übertragen. Der Artikel beschreibt auch die empfohlenen Datenübertragungsoptionen sowie die jeweilige Schlüsselfunktionsmatrix für dieses Szenario.

Eine Übersicht über alle verfügbaren Datenübertragungsoptionen finden Sie unter [Auswählen einer Azure-Datenübertragungslösung](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Beschreibung des Szenarios

Große Datasets beziehen sich auf Datengrößen im TB- bis PB-Bereich. Mittlere bis hohe Netzwerkbandbreite bezieht sich auf 100 MBit/s bis 10 GBit/s.

## <a name="recommended-options"></a>Empfohlene Optionen

Die in diesem Szenario empfohlenen Optionen hängen davon ab, ob Sie eine mittlere Netzwerkbandbreite oder eine hohe Netzwerkbandbreite nutzen.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Mittlere Netzwerkbandbreite (100 MBit/s bis 1 GBit/s)

Bei mittlerer Netzwerkbandbreite müssen Sie die Zeit für die Datenübertragung über das Netzwerk projizieren.

Schätzen Sie anhand der folgenden Tabelle die Zeit, und wählen Sie danach zwischen einer Offlineübertragung oder einer Übertragung über das Netzwerk. Die Tabelle zeigt die voraussichtliche Zeit für die Übertragung von Daten über das Netzwerk für verschiedene verfügbare Netzwerkbandbreiten (bei einer Auslastung von 90 %).  

![Netzwerkübertragung oder Offlineübertragung](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Wenn die Netzwerkübertragung als zu langsam eingestuft wird, sollten Sie ein physisches Gerät verwenden. Die empfohlenen Optionen in diesem Fall sind die Offlineübertragungsgeräte aus der Azure Data Box-Produktfamilie oder Azure Import/Export mit eigenen Datenträgern.

    - **Azure Data Box-Produktfamilie für Offlineübertragungen**: Verwenden Sie von Microsoft bereitgestellte Data Box-Geräte, um große Datenmengen in Azure zu übertragen, wenn Sie aufgrund von Zeit, Netzwerkverfügbarkeit oder Kosten eingeschränkt sind. Kopieren Sie lokale Daten mithilfe von Tools wie Robocopy. Abhängig von der für die Übertragung vorgesehenen Datengröße können Sie zwischen Data Box Disk, Data Box oder Data Box Heavy wählen.
    - **Azure Import/Export**: Verwenden Sie den Azure Import/Export-Dienst, indem Sie Ihre eigenen Festplattenlaufwerke versenden, um große Datenmengen sicher in Azure Blob Storage und Azure Files zu importieren. Sie können diesen Dienst auch zum Übertragen von Daten aus Azure Blob Storage auf Festplattenlaufwerke und zum Versand an lokale Standorte nutzen.

- Wenn die Netzwerkübertragung als sinnvoll erachtet wird, können Sie eines der folgenden Tools verwenden, die unter [Hohe Netzwerkbandbreite](#high-network-bandwidth) beschrieben werden.


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Hohe Netzwerkbandbreite (1 GBit/s bis 100 GBit/s)

Wenn die verfügbare Netzwerkbandbreite hoch ist, verwenden Sie eines der folgenden Tools.

- **AzCopy**: Verwenden Sie dieses Befehlszeilentool, um Daten einfach und schnell in und aus Azure Blobs, Files und Table Storage mit optimaler Leistung zu kopieren. AzCopy unterstützt Nebenläufigkeit und Parallelität sowie die Fortsetzung unterbrochener Kopiervorgänge.
- **Azure Storage REST-APIs/SDKs**: Beim Erstellen einer Anwendung können Sie die Anwendung mithilfe von Azure Storage REST-APIs entwickeln und die in mehreren Sprachen angebotenen Azure SDKs verwenden.
- **Azure Data Box-Produktfamilie für Onlineübertragungen**: Data Box Edge und Data Box Gateway sind Onlinenetzwerkgeräte, die Daten in und aus Azure übertragen können. Verwenden Sie das physische Data Box Edge-Gerät, wenn gleichzeitig eine kontinuierliche Erfassung und Vorverarbeitung der Daten vor dem Hochladen erforderlich ist. Data Box Gateway ist eine virtuelle Version des Geräts mit den gleichen Datenübertragungsfunktionen. In jedem Fall wird die Datenübertragung vom Gerät verwaltet.
- **Azure Data Factory**: Data Factory sollte verwendet werden, um einen Übertragungsvorgang aufzuskalieren, und wenn es Bedarf für Orchestrierung und Überwachungsfunktionen auf Unternehmensebene gibt. Verwenden Sie Data Factory, um regelmäßig Dateien zwischen mehreren Azure-Diensten, lokal oder in einer Kombination aus beidem zu übertragen. Mit Data Factory können Sie datengesteuerte Workflows (sogenannte Pipelines) erstellen und planen, die Daten aus unterschiedlichen Datenspeichern erfassen und die Datenbewegung und Datentransformation automatisieren.

## <a name="comparison-of-key-capabilities"></a>Vergleich der Schlüsselfunktionen

In den folgenden Tabellen werden die Unterschiede bei den Schlüsselfunktionen für die empfohlenen Optionen zusammengefasst.

### <a name="moderate-network-bandwidth"></a>Mittlere Netzwerkbandbreite

Wenn Sie die Offlinedatenübertragung nutzen, verwenden Sie die folgende Tabelle, um die Unterschiede in den Schlüsselfunktionen zu verstehen.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy            |    Importieren/Exportieren                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    **Datengröße**                    |    Bis zu 35 TB                 |    Bis zu 80 TB pro Gerät                       |    Bis zu 800 TB pro Gerät               |    Variable                            |
|    **Datentyp**                    |    Azure-Blobs                  |    Azure-Blobs<br>Azure Files                    |    Azure-Blobs<br>Azure Files            |    Azure-Blobs<br>Azure Files          |
|    **Formfaktor**                  |    5 SSDs pro Auftrag             |    1 x 22,7 kg Gerät in Desktopgröße pro Auftrag    |    1 x ca. 226,8 kg großes Gerät pro Auftrag    |    Bis zu 10 HDDs/SSDs pro Auftrag        |
|    **Für Ersteinrichtung benötigte Zeit**               |    Niedrig <br>(15 Min.)            |    Niedrig bis mittel <br> (< 30 Min.)               |    Moderat<br>(1 bis 2 Stunden)               |    Mittel bis schwierig<br>(variabel) |
|    **Senden von Daten an Azure**           |    Ja                          |    Ja                                           |    Ja                                   |    Ja                                 |
|    **Exportieren von Daten aus Azure**           |    Nein                           |    Nein                                            |    Nein                                    |    Ja                                 |
|    **Verschlüsselung**                   |    AES 128-Bit                  |    AES 256-Bit                                   |    AES 256-Bit                           |    AES 128-Bit                         |
|    **Hardware**                     |     Von Microsoft bereitgestellt          |    Von Microsoft bereitgestellt                            |    Von Microsoft bereitgestellt                    |    Vom Kunden bereitgestellt                   |
|    **Netzwerkschnittstelle**            |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    **Partnerintegration**          |    Einige                         |    [Hoch](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Hoch](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Einige                                |
|    **Versand**                     |    Von Microsoft verwaltet            |    Von Microsoft verwaltet                             |    Von Microsoft verwaltet                     |    Vom Kunden verwaltet                    |
| **Verwendung bei Datenbewegungen**     |Innerhalb einer kommerziellen Grenze|Innerhalb einer kommerziellen Grenze|Innerhalb einer kommerziellen Grenze|Über geografische Grenzen hinweg, z.B. aus den USA in die EU|
|    **Preise**                          |    [Preise](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Preise](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Preise](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Preise](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


Bei Nutzung von Onlinedatenübertragung verwenden Sie die Tabelle im folgenden Abschnitt für hohe Netzwerkbandbreite.

### <a name="high-network-bandwidth"></a>Hohe Netzwerkbandbreite

|                                     |    Tools AzCopy, <br>Azure PowerShell, <br>Azure CLI             |    Azure Storage REST-APIs, SDKs                   |    Data Box Gateway oder Data Box Edge          |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    **Datentyp**              |    Azure-Blobs, Azure Files, Azure Tables    |    Azure-Blobs, Azure Files, Azure Tables    |    Azure-Blobs, Azure Files                           |   Unterstützt mehr als 70 Datenconnectors für Datenspeicher und Formate    |
|    **Formfaktor**            |    Befehlszeilentools                        |    Programmgesteuerte Schnittstelle                    |    Microsoft stellt ein virtuelles <br>oder physisches Gerät bereit     |    Dienst im Azure-Portal                                            |
|    **Einmalige Ersteinrichtung** |    Einfach               |    Moderat                       |    Einfach (< 30 Minuten) bis mittel (1 bis 2 Stunden)            |    Umfangreich                                                          |
|    **Datenvorverarbeitung**          |    Nein                                        |    Nein                                        |    Ja (mit Edge-Compute)                               |    Ja                                                                |
|    **Übertragung aus anderen Clouds**   |    Nein                                        |    Nein                                        |    Nein                                                    |    Ja                                                                |
|    **Benutzertyp**                    |    IT-Profi oder Entwickler                                       |    Entwicklung                                       |    IT-Profi                                                |    IT-Profi                                                             |
|    **Preise**                      |    Kostenlos, Gebühren für Datenerfassung fallen an         |    Kostenlos, Gebühren für Datenerfassung fallen an         |    [Preise](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Preise](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Nächste Schritte

- [Informationen zum Übertragen von Daten mit Import/Export](./storage-import-export-data-to-blobs.md).
- Informationen zum

    - [Übertragen von Daten mit Data Box Disk](../../databox/data-box-disk-quickstart-portal.md).
    - [Übertragen von Daten mit Data Box](../../databox/data-box-quickstart-portal.md).
- [Übertragen von Daten mit AzCopy](./storage-use-azcopy-v10.md).
- Informationen zum
    - [Übertragen von Daten mit Data Box Gateway](../../databox-gateway/data-box-gateway-deploy-add-shares.md)
    - [Transformieren von Daten mit Data Box Edge vor dem Senden an Azure](../../databox-online/azure-stack-edge-deploy-configure-compute.md).
- [Informationen zum Übertragen von Daten mit Azure Data Factory](../../data-factory/quickstart-create-data-factory-portal.md).
- Verwenden der REST-APIs zum Übertragen von Daten

    - [In .NET](/dotnet/api/overview/azure/storage)
    - [In Java](/java/api/overview/azure/storage)
---
title: Azure-Datenübertragungsoptionen für große Datasets mit geringer oder keiner Netzwerkbandbreite | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Azure-Lösung für die Datenübertragung auswählen, wenn Sie nur geringe oder gar keine Netzwerkbandbreite in Ihrer Umgebung nutzen können und planen, große Datasets zu übertragen.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 8afedec224fed55f617340af512485726ff44326
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792938"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Datenübertragung für große Datasets mit geringer oder keiner Netzwerkbandbreite
 
Dieser Artikel bietet einen Überblick über die Datenübertragungslösungen, wenn Sie in Ihrer Umgebung nur eingeschränkte oder gar keine Netzwerkbandbreite nutzen können und planen, große Datasets zu übertragen. Der Artikel beschreibt auch die empfohlenen Datenübertragungsoptionen sowie die jeweilige Schlüsselfunktionsmatrix für dieses Szenario.

Eine Übersicht über alle verfügbaren Datenübertragungsoptionen finden Sie unter [Auswählen einer Azure-Datenübertragungslösung](storage-choose-data-transfer-solution.md).

## <a name="offline-transfer-or-network-transfer"></a>Offlineübertragung oder Netzwerkübertragung

Große Datasets bedeuten, dass Sie Daten in einer Größenordnung von wenigen TB bis wenigen PB haben. Sie verfügen über eingeschränkte oder gar keine Netzwerkbandbreite, und Ihr Netzwerk ist langsam oder unzuverlässig. Außerdem gilt:

- Sie sind durch die Kosten der Netzwerkübertragung durch Ihre Internetdienstanbieter (ISPs) eingeschränkt.
- Im Zusammenhang mit sensiblen Daten können aufgrund von Sicherheits- oder Organisationsrichtlinien keine ausgehenden Verbindungen verwendet werden.

Verwenden Sie in allen oben genannten Fällen ein physisches Gerät, um eine einmalige Massendatenübertragung auszuführen. Wählen Sie zwischen Data Box Disk-, Data Box- und Data Box Heavy-Geräten, die von Microsoft zur Verfügung gestellt werden, und Import/Export mit Ihren eigenen Datenträgern.

Um zu überprüfen, ob ein physisches Gerät die richtige Option ist, verwenden Sie die folgende Tabelle. Sie zeigt die voraussichtliche Zeit für die Übertragung von Daten über das Netzwerk für verschiedene verfügbare Bandbreiten (bei einer Auslastung von 90 %). Wenn Netzwerkübertragung als zu langsam eingestuft wird, sollten Sie ein physisches Gerät verwenden.  

![Netzwerkübertragung oder Offlineübertragung](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Empfohlene Optionen

Die in diesem Szenario verfügbaren Optionen sind Geräte für Azure Data Box-Offlineübertragung oder Azure Import/Export.

- **Azure Data Box-Produktfamilie für Offlineübertragungen** : Verwenden Sie von Microsoft bereitgestellte Data Box-Geräte, um große Datenmengen in Azure zu übertragen, wenn Sie aufgrund von Zeit, Netzwerkverfügbarkeit oder Kosten eingeschränkt sind. Kopieren Sie lokale Daten mithilfe von Tools wie Robocopy. Abhängig von der für die Übertragung vorgesehenen Datengröße können Sie zwischen Data Box Disk, Data Box oder Data Box Heavy wählen.
- **Azure Import/Export** : Verwenden Sie den Azure Import/Export-Dienst, indem Sie Ihre eigenen Festplattenlaufwerke versenden, um große Datenmengen sicher in Azure Blob Storage und Azure Files zu importieren. Sie können diesen Dienst auch zum Übertragen von Daten aus Azure Blob Storage auf Festplattenlaufwerke und zum Versand an lokale Standorte nutzen.

## <a name="comparison-of-key-capabilities"></a>Vergleich der Schlüsselfunktionen

In der folgenden Tabelle werden die Unterschiede der Schlüsselfunktionen zusammengefasst.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy              |    Importieren/Exportieren                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    **Datengröße**                    |    Bis zu 35 TB                 |    Bis zu 80 TB pro Gerät                       |    Bis zu 800 TB pro Gerät               |    Variable                            |
|    **Datentyp**                    |    Azure-Blobs                  |    Azure-Blobs<br>Azure Files                    |    Azure-Blobs<br>Azure Files            |    Azure-Blobs<br>Azure Files          |
|    **Formfaktor**                  |    5 SSDs pro Auftrag             |    1 x 22,7 kg Gerät in Desktopgröße pro Auftrag    |    1 x ca. 226,8 kg großes Gerät pro Auftrag    |    Bis zu 10 HDDs/SSDs pro Auftrag        |
|    **Für Ersteinrichtung benötigte Zeit**           |    Niedrig <br>(15 Min.)            |    Niedrig bis mittel <br> (< 30 Min.)               |    Moderat<br>(1 bis 2 Stunden)               |    Mittel bis schwierig<br>(variabel) |
|    **Senden von Daten an Azure**           |    Ja                          |    Ja                                           |    Ja                                   |    Ja                                 |
|    **Exportieren von Daten aus Azure**       |    Nein                           |    Nein                                            |    Nein                                    |    Ja                                 |
|    **Verschlüsselung**                   |    AES 128-Bit                  |    AES 256-Bit                                   |    AES 256-Bit                           |    AES 128-Bit                         |
|    **Hardware**                     |     Von Microsoft bereitgestellt          |    Von Microsoft bereitgestellt                            |    Von Microsoft bereitgestellt                    |    Vom Kunden bereitgestellt                   |
|    **Netzwerkschnittstelle**            |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    **Partnerintegration**          |    Einige                         |    [Hoch](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Hoch](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Einige                                |
|    **Versand**                     |    Von Microsoft verwaltet            |    Von Microsoft verwaltet                             |    Von Microsoft verwaltet                     |    Vom Kunden verwaltet                    |
| **Verwendung bei Datenbewegungen**     |Innerhalb einer kommerziellen Grenze|Innerhalb einer kommerziellen Grenze|Innerhalb einer kommerziellen Grenze|Über geografische Grenzen hinweg, z.B. aus den USA in die EU|
|    **Preise**                      |    [Preise](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Preise](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Preise](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Preise](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>Nächste Schritte

- Informationen zum

    - [Übertragen von Daten mit Data Box Disk](../../databox/data-box-disk-quickstart-portal.md).
    - [Übertragen von Daten mit Data Box](../../databox/data-box-quickstart-portal.md).
    - [Übertragen von Daten mit Import/Export](./storage-import-export-data-to-blobs.md).
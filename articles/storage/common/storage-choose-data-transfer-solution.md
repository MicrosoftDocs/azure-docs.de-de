---
title: Auswählen einer Azure-Lösung für die Datenübertragung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Azure-Lösung für die Datenübertragung basierend auf Datengrößen und verfügbarer Netzwerkbandbreite in Ihrer Umgebung auswählen.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 56470c08f0ac940dae42821ae61846f1c86d52eb
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479518"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Auswählen einer Azure-Lösung für die Datenübertragung

Dieser Artikel gibt einen Überblick über einige der gängigen Azure-Datenübertragungslösungen. Der Artikel enthält auch Links zu empfohlenen Optionen, abhängig von der Netzwerkbandbreite in Ihrer Umgebung und der Größe der Daten, die Sie übertragen möchten.

## <a name="types-of-data-movement"></a>Typen von Datenverschiebung

Die Datenübertragung kann offline oder über die Netzwerkverbindung erfolgen. Wählen Sie Ihre Lösung abhängig von den folgenden Faktoren aus:

- **Datengröße**: Größe der Daten, die übertragen werden sollen,
- **Übertragungshäufigkeit**: Einmalige oder regelmäßige Datenerfassung und
- **Netzwerk**: Für die Datenübertragung in Ihrer Umgebung verfügbare Bandbreite.

Das Verschieben der Daten kann auf die folgende Weise erfolgen:

- **Offlineübertragung mit versandfähigen Geräten**: Verwenden Sie physische versandfähige Geräte, wenn Sie eine einmalige Offline-Datenmassenübertragung durchführen möchten. Microsoft übersendet Ihnen einen Datenträger oder ein sicheres spezielles Gerät. Alternativ können Sie Ihre eigenen Datenträger erwerben und einsenden. Sie kopieren Daten auf das Gerät und senden es dann an Azure. Dort werden die Daten hochgeladen.  Die verfügbaren Optionen für diesen Fall sind Data Box Disk, Data Box, Data Box Heavy und Import/Export (unter Verwendung eigener Datenträger).

- **Netzwerkübertragung**: Sie übertragen Ihre Daten über Ihre Netzwerkverbindung in Azure. Dies kann auf verschiedene Weise erfolgen.

    - **Grafische Benutzeroberfläche**: Wenn Sie gelegentlich nur wenige Dateien übertragen und die Datenübertragung nicht automatisieren müssen, können Sie ein grafisches Benutzeroberflächentool wie Azure Storage Explorer oder ein webbasiertes Durchsuchungstool im Azure-Portal auswählen.
    - **Übertragung mit Skripts oder programmgesteuerte Übertragung**: Sie können optimierte Softwaretools verwenden, die von uns bereitgestellt werden, oder unsere REST-APIs/SDKs direkt aufrufen. Die verfügbaren skriptfähigen Tools sind AzCopy, Azure PowerShell und die Azure CLI. Verwenden Sie für eine programmgesteuerte Schnittstelle eines der SDKs für .NET, Java, Python, Node/JS, C++, Go, PHP oder Ruby.
    - **Lokale Geräte**: Wir stellen ein physisches oder virtuelles Gerät bereit, das sich in Ihrem Datencenter befindet und die Datenübertragung über das Netzwerk optimiert. Diese Geräte bieten auch einen lokalen Cache für häufig verwendete Dateien. Das physische Gerät ist Data Box Edge, das virtuelle Gerät ist Data Box Gateway. Beide werden in Ihrer lokalen Umgebung dauerhaft ausgeführt und sind über das Netzwerk mit Azure verbunden.
    - **Verwaltete Datenpipeline**: Sie können eine Cloudpipeline einrichten, um regelmäßig Dateien zwischen mehreren Azure-Diensten, lokal oder in einer Kombination aus beidem zu übertragen. Verwenden Sie Azure Data Factory, um Datenpipelines einzurichten und zu verwalten sowie Daten zur Analyse zu verschieben und zu transformieren.

Die folgende Abbildung veranschaulicht die Richtlinien für die Auswahl der verschiedenen Azure-Datenübertragungstools in Abhängigkeit von der für die Übertragung verfügbaren Netzwerkbandbreite, der für die Übertragung vorgesehenen Datengröße und der Häufigkeit der Übertragung.

![Azure-Datenübertragungstools](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**Die Obergrenzen der Offlineübertragungsgeräte (Data Box Disk, Data Box und Data Box Heavy) können durch mehrere Bestellungen eines Gerätetyps erweitert werden.*

## <a name="selecting-a-data-transfer-solution"></a>Auswählen einer Datenübertragungslösung

Beantworten Sie die folgenden Fragen, um die Auswahl einer Datenübertragungslösung zu erleichtern:

- Ist Ihre verfügbare Netzwerkbandbreite begrenzt oder nicht vorhanden, und Sie möchten große Datasets übertragen?
  
    Wenn dies der Fall ist, sehen Sie sich das folgende Szenario an: [Szenario 1: Übertragen großer Datasets mit keiner oder niedriger Netzwerkbandbreite](storage-solution-large-dataset-low-network.md).
- Möchten Sie große Datasets über das Netzwerk übertragen, und nutzen Sie eine mittlere bis hohe Netzwerkbandbreite?

    Wenn dies der Fall ist, sehen Sie sich das folgende Szenario an: [Szenario 2: Übertragen großer Datasets mit mittlerer bis hoher Netzwerkbandbreite](storage-solution-large-dataset-moderate-high-network.md).
- Möchten Sie gelegentlich nur wenige Dateien über das Netzwerk übertragen?

    Wenn dies der Fall ist, untersuchen Sie [Szenario 3: Übertragen kleiner Datasets mit begrenzter bis mittlerer Netzwerkbandbreite](storage-solution-small-dataset-low-moderate-network.md).
- Sie suchen eine zeitnahe Datenübertragung in regelmäßigen Abständen?

    In diesem Fall verwenden Sie die hier beschriebenen skriptbasierten/programmgesteuerten Optionen: [Szenario 4: Periodische Datenübertragungen](storage-solution-periodic-data-transfer.md).
- Suchen Sie nach kontinuierlicher, fortlaufender Datenübertragung?

    In diesem Fall verwenden Sie die Optionen in [Szenario 4: Periodische Datenübertragungen](storage-solution-periodic-data-transfer.md).
 

## <a name="data-transfer-feature-in-azure-portal"></a>Datenübertragungsfunktion im Azure-Portal

Sie können auch zu Ihrem Azure Storage-Konto im Azure-Portal wechseln und die Funktion **Datenübertragung** auswählen. Geben Sie die Netzwerkbandbreite in Ihrer Umgebung, die Größe der zu übertragenden Daten und die Häufigkeit der Datenübertragung an. Es werden Ihnen die optimalen Datenübertragungslösungen angezeigt, die den von Ihnen bereitgestellten Informationen entsprechen. 

## <a name="next-steps"></a>Nächste Schritte

- [Einführung in Azure Storage-Explorer](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [Übersicht über AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10).
- [Verwenden von Azure PowerShell mit Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-powershell-guide-full)
- [Verwenden der Azure CLI mit Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- Sie erhalten Informationen zu folgenden Themen:

    - [Azure Data Box, Azure Data Box Disk und Azure Data Box Heavy für Offlineübertragungen](https://docs.microsoft.com/azure/databox/).
    - [Azure Data Box Gateway und Azure Data Box Edge für Onlineübertragungen](https://docs.microsoft.com/azure/databox-online/).
- [Was ist Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)?
- Verwenden der REST-APIs zum Übertragen von Daten

    - [In .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [In Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)

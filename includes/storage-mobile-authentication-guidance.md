---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: eeea7b1fed0c2a1f805e21b4dec9ec3cad7fc976
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95996809"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Konfigurieren der Anwendung für den Zugriff auf Azure Storage
Es gibt zwei Möglichkeiten, um Ihre Anwendung für den Zugriff auf die Storage-Dienste zu authentifizieren:

* Gemeinsam verwendeter Schlüssel: Verwenden Sie den gemeinsam verwendeten Schlüssel nur für Testzwecke.
* Shared Access Signature (SAS): Verwenden Sie SAS für Produktionsanwendungen.

### <a name="shared-key"></a>Gemeinsam verwendeter Schlüssel
Bei der Authentifizierung mit gemeinsam verwendetem Schlüssel nutzt die Anwendung Ihren Kontonamen und Kontoschlüssel, um auf die Storage-Dienste zuzugreifen. Für eine schnelle Demonstration, wie Sie diese Bibliothek verwenden können, wird in diesen ersten Schritten die Authentifizierung mit gemeinsam verwendetem Schlüssel verwendet.

> [!WARNING] 
> **Nutzen Sie die Authentifizierung mit gemeinsam verwendetem Schlüssel nur für Testzwecke!** Ihr Kontoname und Kontoschlüssel, mit denen Sie vollständigen Lese-/Schreibzugriff auf das zugehörige Storage-Konto erhalten, werden an alle Personen verteilt, die Ihre App herunterladen. Diese Vorgehensweise wird **nicht** empfohlen, da das Risiko besteht, dass Ihr Schlüssel durch nicht vertrauenswürdige Clients beeinträchtigt werden kann.
> 
> 

Wenn Sie die Authentifizierung mit gemeinsam verwendetem Schlüssel nutzen, erstellen Sie eine [Verbindungszeichenfolge](../articles/storage/common/storage-configure-connection-string.md). Die Verbindungszeichenfolge besteht aus:  

* dem **DefaultEndpointsProtocol** – Sie können zwischen HTTP oder HTTPS wählen. Die Verwendung von HTTPS wird jedoch unbedingt empfohlen.
* dem **Kontonamen** – der Name Ihres Storage-Kontos
* dem **Kontoschlüssel** – navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Speicherkonto, und klicken Sie auf das Symbol **Schlüssel**, um diese Informationen zu erhalten.
* (Optional) **EndpointSuffix** – dieser Wert wird für Speicherdienste in Regionen mit anderen Endpunktsuffixen verwendet (etwa Azure China oder Azure Governance).

Hier sehen Sie ein Beispiel einer Verbindungszeichenfolge bei Authentifizierung mit gemeinsam verwendetem Schlüssel:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Shared Access Signatures (SAS)
Die empfohlene Methode für eine mobile Anwendung zum Authentifizieren einer Anforderung durch einen Client bei einem Azure Storage-Dienst ist die Nutzung einer Shared Access Signature (SAS). SAS ermöglicht es Ihnen, dem Client mit einem angegebenen Satz von Berechtigungen für einen bestimmten Zeitraum Zugriff auf eine Ressource zu gewähren.
Als Speicherkontobesitzer müssen Sie eine SAS für Ihre mobilen Clients erstellen. Zum Generieren der SAS sollten Sie wahrscheinlich einen separaten Dienst schreiben, der die SAS generiert, die an Ihre Clients verteilt werden. Zu Testzwecken können Sie mit dem [Microsoft Azure Storage-Explorer](https://storageexplorer.com) oder mit dem [Azure-Portal](https://portal.azure.com) eine SAS erstellen. Bei der Erstellung der SAS können Sie das Zeitintervall angeben, in dem die SAS gültig ist, sowie die Berechtigungen, die die SAS dem Client erteilt.

Das folgende Beispiel zeigt die Verwendung des Microsoft Azure Storage-Explorers zum Erstellen einer SAS.

1. Wenn Sie es nicht bereits getan haben, [installieren Sie den Microsoft Azure Storage-Explorer](https://storageexplorer.com)
2. Stellen Sie eine Verbindung mit Ihrem Abonnement her.
3. Klicken Sie auf das Speicherkonto und auf die Registerkarte „Aktionen“ unten links. Klicken Sie auf „Shared Access Signature abrufen“, um eine „Verbindungszeichenfolge“ für Ihre SAS zu generieren.
4. Hier ist ein Beispiel für eine SAS-Verbindungszeichenfolge, die Lese- und Schreibberechtigungen auf Dienst-, Container- und Objektebene für den Blobdienst des Speicherkontos gewährt.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Wie Sie sehen, machen Sie den Kontoschlüssel in Ihrer Anwendung nicht verfügbar, wenn Sie eine SAS verwenden. Weitere Informationen zu SAS und bewährten Methoden bei der Verwendung von SAS erhalten Sie unter [Shared Access Signatures: Grundlegendes zum SAS-Modell](../articles/storage/common/storage-sas-overview.md).
---
title: Verwalten des Ablaufs von Azure Blob-Speicher
titleSuffix: Azure Content Delivery Network
description: Informationen zu den Optionen für das Festlegen der Gültigkeitsdauer von Blobs beim Azure CDN-Zwischenspeichern.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: how-to
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: 206ff6f888229356743bebb816cf03e4f7a7504b
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778706"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Verwalten des Ablaufs von Azure Blob-Speicher in Azure CDN
> [!div class="op_single_selector"]
> * [Azure-Webinhalt](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 
> 

Der [Blob Storage-Dienst](../storage/common/storage-introduction.md#blob-storage) in Azure Storage ist einer von verschiedenen Azure-basierten Ursprüngen, die in Azure Content Delivery Network (CDN) integriert wurden. Jeder öffentlich zugängliche Blobinhalt kann in Azure CDN zwischengespeichert werden, bis seine Gültigkeitsdauer abläuft. Die Gültigkeitsdauer wird durch den `Cache-Control`-Header in der HTTP-Antwort des Ursprungsservers bestimmt. In diesem Artikel werden verschiedene Methoden beschrieben, mit denen Sie den `Cache-Control`-Header für ein Blob in Azure Storage festlegen können.

Sie können die Cacheeinstellungen auch über das Azure-Portal steuern, indem Sie CDN-Cacheregeln festlegen. Wenn Sie eine Cacheregel erstellen und das Zwischenspeicherverhalten auf **Außerkraftsetzung** oder **Cache umgehen** festlegen, werden die in diesem Artikel erörterten vom Ursprung angegebenen Cacheeinstellungen ignoriert. Weitere Informationen zu allgemeinen Cachekonzepten finden Sie unter [How caching works](cdn-how-caching-works.md) (Funktionsweise von Caching).

> [!TIP]
> Sie haben auch die Möglichkeit, keine Gültigkeitsdauer für ein Blob festzulegen. In diesem Fall wendet Azure CDN automatisch eine Standardgültigkeitsdauer von sieben Tagen an, es sei denn, Sie haben Cacheregeln im Azure-Portal eingerichtet. Diese Standardgültigkeitsdauer gilt nur für die Optimierung allgemeiner Webbereitstellungen. Bei der Optimierung großer Dateien beträgt die Standardgültigkeitsdauer einen Tag, bei der Optimierung für das Medienstreaming beträgt die Standardgültigkeitsdauer ein Jahr.
> 
> Weitere Informationen dazu, wie Azure CDN den Zugriff auf Blobs und andere Dateien beschleunigen kann, finden Sie in der [Übersicht über Azure Content Delivery Network](cdn-overview.md).
> 
> Weitere Informationen zu Azure Blob Storage finden Sie unter [Einführung in Blob Storage](../storage/blobs/storage-blobs-introduction.md).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Festlegen von Cache-Control-Headern mithilfe von CDN-Cacheregeln
Die bevorzugte Methode zum Einrichten des `Cache-Control`-Headers für einen Blob ist die Verwendung von Cacheregeln im Azure-Portal. Weitere Informationen zu CDN-Cacheregeln finden Sie unter [Steuern des Verhaltens beim Zwischenspeichern im Azure Content Delivery Network mit Cacheregeln](cdn-caching-rules.md).

> [!NOTE] 
> Cacheregeln sind nur für die Profile **Azure CDN Standard von Verizon** und **Azure CDN Standard von Akamai** verfügbar. Für Profile vom Typ **Azure CDN Premium von Verizon**  müssen Sie die [Azure CDN-Regel-Engine](./cdn-verizon-premium-rules-engine.md) im **Verwaltungsportal** verwenden, um von einer ähnlichen Funktionalität zu profitieren.

**So navigieren Sie zur Seite mit den CDN-Cacheregeln**

1. Wählen Sie im Azure-Portal ein CDN-Profil und anschließend den Endpunkt für das Blob aus.

2. Wählen Sie im linken Bereich unter „Einstellungen“ die Option **Cacheregeln** aus.

   ![Schaltfläche für CDN-Cacheregeln](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   Die Seite **Cacheregeln** wird geöffnet.

   ![Seite zur CDN-Zwischenspeicherung](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**So legen Sie mit globalen Cacheregeln Cache-Control-Header für einen Blob Storage-Dienst fest**

1. Legen Sie unter **Globale Cacheregeln** die Option **Verhalten für das Zwischenspeichern von Abfragezeichenfolgen** auf **Abfragezeichenfolgen ignorieren** fest. Legen Sie dann das **Verhalten beim Zwischenspeichern** auf **Außerkraftsetzung** fest.
      
2. Geben Sie für **Dauer bis Cacheablauf** einen Wert von 3600 im Feld **Sekunden** ein, oder geben Sie im Feld **Stunden** den Wert 1 an. 

   ![Beispiel für globale CDN-Cacheregeln](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Mit diesen globalen Cacheregeln wird eine Cachedauer von 1 Stunde festgelegt, die sich auf alle Anforderungen an den Endpunkt auswirkt. Der festgelegte Ablauf überschreibt alle `Cache-Control`- oder `Expires`-HTTP-Header, die durch die vom Endpunkt festgelegten Ursprungsserver gesendet werden.   

3. Wählen Sie **Speichern** aus.
 
**So legen Sie mit benutzerdefinierten Cacheregeln Cache-Control-Header für eine Blobdatei fest**

1. Erstellen Sie unter **Benutzerdefinierte Cacheregeln** zwei Übereinstimmungsbedingungen:

     A. Legen Sie die erste **Übereinstimmungsbedingung** auf **Pfad** fest, und geben Sie für den **Übereinstimmungswert**`/blobcontainer1/*` ein. Legen Sie das **Verhalten beim Zwischenspeichern** auf **Außerkraftsetzung** fest, und geben Sie im Feld **Stunden** den Wert 4 ein.

    B. Legen Sie die erste **Übereinstimmungsbedingung** auf **Pfad** fest, und geben Sie für den **Übereinstimmungswert**`/blobcontainer1/blob1.txt` ein. Legen Sie das **Verhalten beim Zwischenspeichern** auf **Außerkraftsetzung** fest, und geben Sie im Feld **Stunden** den Wert 2 ein.

    ![Beispiel für benutzerdefinierte CDN-Cacheregeln](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    Die erste benutzerdefinierte Cacheregel legt eine Cachedauer von vier Stunden für alle Blobdateien im Ordner `/blobcontainer1` auf dem Ursprungsserver fest, der durch Ihren Endpunkt angegeben wird. Die zweite Regel setzt für die Blobdatei `blob1.txt` die erste Regel außer Kraft und legt eine Cachedauer von zwei Stunden fest.

2. Wählen Sie **Speichern** aus.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Festlegen von Cache-Control-Headern unter Verwendung von Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/) ist eine der schnellsten und leistungsstärksten Möglichkeiten zum Verwalten Ihrer Azure-Dienste. Verwenden Sie das `Get-AzStorageBlob`-Cmdlet zum Abrufen eines Verweises auf das Blob, und legen Sie dann die Eigenschaft `.ICloudBlob.Properties.CacheControl` fest. 

Beispiel:

```powershell
# Create a storage context
$context = New-AzStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Sie können auch PowerShell zum [Verwalten Ihrer CDN-Profile und Endpunkte](cdn-manage-powershell.md) verwenden.
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Festlegen von Cache-Control-Headern unter Verwendung von .NET
Um den `Cache-Control`-Header für ein Blob unter Verwendung von .NET-Code anzugeben, verwenden Sie die [Azure Storage-Clientbibliothek für .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md) zum Festlegen der Eigenschaft [CloudBlob.Properties.CacheControl](/dotnet/api/microsoft.azure.storage.blob.blobproperties.cachecontrol).

Beispiel:

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer <container name> = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob <blob name> = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Weitere .NET-Codebespiele finden Sie unter [Azure Blob Storage Samples for .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)(Azure Blob Storage-Beispiele für .NET).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Festlegen von Cache-Control-Headern mithilfe anderer Methoden

### <a name="azure-storage-explorer"></a>Azure Storage-Explorer
Mit dem [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) können Sie Ihre Blob Storage-Ressourcen anzeigen und bearbeiten, darunter beispielsweise Eigenschaften wie *CacheControl* . 

So aktualisieren Sie die *CacheControl* -Eigenschaft eines Blobs mit dem Azure Storage-Explorer:
   1. Wählen Sie ein Blob aus, und klicken Sie dann im Kontextmenü auf **Eigenschaften** . 
   2. Scrollen Sie nach unten zur *CacheControl* -Eigenschaft.
   3. Geben Sie einen Wert ein, und wählen Sie **Speichern** aus.


![Eigenschaften des Azure Storage-Explorers](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Azure-Befehlszeilenschnittstelle
Mithilfe der [Azure-Befehlszeilenschnittstelle](/cli/azure) (CLI) können Sie Azure-Blobressourcen über die Befehlszeile verwalten. Legen Sie zum Festlegen des Cache-Control-Headers beim Hochladen eines Blobs mit der Azure-Befehlszeilenschnittstelle die Eigenschaft *cacheControl* mit dem `-p`-Switch fest. Im folgenden Beispiel wird gezeigt, wie die Gültigkeitsdauer auf eine Stunde (3.600 Sekunden) festgelegt wird:
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>REST-API für Azure-Speicherdienste
Sie können die [REST-API für Azure-Speicherdienste](/rest/api/storageservices/) verwenden, um die Eigenschaft *x-ms-blob-cache-control* mithilfe der folgenden Vorgänge für eine Anforderung explizit festzulegen:
  
   - [Put Blob](/rest/api/storageservices/Put-Blob)
   - [Put Block List](/rest/api/storageservices/Put-Block-List)
   - [Set Blob Properties](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>Testen des Cache-Control-Headers
Sie können die Einstellungen der Gültigkeitsdauer Ihrer Blobs einfach überprüfen. Mithilfe der [Entwicklertools](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) Ihres Browsers können Sie überprüfen, ob Ihr Blob den `Cache-Control`-Antwortheader enthält. Sie können die Antwortheader außerdem über ein Tool wie [wget](https://www.gnu.org/software/wget/), [Postman](https://www.getpostman.com/) oder [Fiddler](https://www.telerik.com/fiddler) untersuchen.

## <a name="next-steps"></a>Nächste Schritte
* [Erfahren Sie, wie Sie den Ablauf von Clouddienstinhalten in Azure CDN verwalten können.](cdn-manage-expiration-of-cloud-service-content.md)
* [Informationen zu Cachekonzepten](cdn-how-caching-works.md)
---
title: Aktivieren der sicheren TLS-Verbindung für den Azure Storage-Client | Microsoft-Dokumentation
description: Informationen zum Aktivieren von TLS 1.2 im Client von Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 218708ffc9a680150d7b6bf559a00ca87054bbe8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65152971"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Aktivieren der sicheren TLS-Verbindung für den Azure Storage-Client

Transport Layer Security (TLS) und Secure Sockets Layer (SSL) sind kryptografische Protokolle, die Kommunikationssicherheit über ein Computernetzwerk bereitstellen. SSL 1.0, 2.0 und 3.0 haben sich als anfällig erwiesen. Sie werden per RFC untersagt. TLS 1.0 wird unsicher durch Verwendung der unsicheren Blockchiffre (DES CBC und RC2 CBC) und Streamchiffre (RC4). Der PCI-Rat hat zudem die Migration zu höheren TLS-Versionen empfohlen. Weitere Details finden Sie unter [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

In Azure Storage wurde die Verwendung von SSL 3.0 seit 2015 beendet, TLS 1.2 wird für öffentliche HTTPS-Endpunkte verwendet, TLS 1.0 und TLS 1.1 werden jedoch aus Gründen der Abwärtskompatibilität weiterhin unterstützt.

Um sichere und konforme Verbindungen mit Azure Storage sicherzustellen, müssen Sie TLS 1.2 oder eine neuere Version auf Clientseite aktivieren, bevor Sie Anforderungen zur Verwendung des Azure Storage-Diensts senden.

## <a name="enable-tls-12-in-net-client"></a>Aktivieren von TLS 1.2 im .NET-Client

Damit der Client die Aushandlung über TLS 1.2 durchführt, müssen das Betriebssystem und die .NET Framework-Version beide TLS 1.2 unterstützen. Weitere Details finden Sie unter [Unterstützung für TLS 1.2](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).

Im folgenden Beispiel wird gezeigt, wie Sie TLS 1.2 in Ihrem .NET-Client aktivieren.

```csharp

    static void EnableTls12()
    {
        // Enable TLS 1.2 before connecting to Azure Storage
        System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

        // Connect to Azure Storage
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName={yourstorageaccount};AccountKey={yourstorageaccountkey};EndpointSuffix=core.windows.net");
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        CloudBlobContainer container = blobClient.GetContainerReference("foo");
        container.CreateIfNotExists();
    }

```

## <a name="enable-tls-12-in-powershell-client"></a>Aktivieren von TLS 1.2 im PowerShell-Client

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)] 

Im folgenden Beispiel wird gezeigt, wie Sie TLS 1.2 in Ihrem PowerShell-Client aktivieren.

```powershell
# Enable TLS 1.2 before connecting to Azure Storage
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

$resourceGroup = "{YourResourceGroupName}"
$storageAccountName = "{YourStorageAccountNme}"
$prefix = "foo"

# Connect to Azure Storage
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccountName
$ctx = $storageAccount.Context
$listOfContainers = Get-AzStorageContainer -Context $ctx -Prefix $prefix
$listOfContainers
```

## <a name="verify-tls-12-connection"></a>Überprüfen der TLS 1.2-Verbindung

Sie können Fiddler verwenden, um zu überprüfen, ob TLS 1.2 tatsächlich verwendet wird. Öffnen Sie Fiddler, um den Netzwerkdatenverkehr auf dem Client zu erfassen, und führen Sie dann das Beispiel oben aus. Anschließend können Sie die TLS-Version in der Verbindung sehen, die mit dem Beispiel hergestellt wird.

Der folgende Screenshot ist ein Beispiel für die Überprüfung.

![Screenshot zur Überprüfung der TLS-Version in Fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Weitere Informationen

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [PCI compliance on TLS](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls) (PCI-Konformität bei TLS)
* [Aktivieren von TLS im Java-Client](https://www.java.com/en/configure_crypto.html)

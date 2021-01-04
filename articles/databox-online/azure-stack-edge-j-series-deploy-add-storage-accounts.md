---
title: Tutorial zum Übertragen von Daten an ein Speicherkonto mit einem Azure Stack Edge Pro-Gerät mit GPU | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie auf einem Azure Stack Edge Pro-Gerät mit GPU lokale und Edge-Speicherkonten hinzufügen und sich damit verbinden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to storage accounts on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: c24970b30e522d593a491194dbbabb0421ad7f4b
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185540"
---
# <a name="tutorial-transfer-data-via-storage-accounts-with-azure-stack-edge-pro-gpu"></a>Tutorial: Übertragen von Daten an Speicherkonten mit einem Azure Stack Edge Pro-Gerät mit GPU 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

In diesem Tutorial erfahren Sie, wie Sie auf Ihrem Azure Stack Edge Pro-Gerät Speicherkonten hinzufügen und eine Verbindung mit diesen herstellen. Nachdem die Speicherkonten hinzugefügt wurden, kann Azure Stack Edge Pro Daten an Azure übertragen.

Dieser Vorgang kann bis zu 30 Minuten dauern.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen von Speicherkonten
> * Verbinden mit dem Speicherkonto

 
## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich, dass Folgendes erfüllt ist, bevor Sie Ihrem Azure Stack Edge Pro-Gerät Speicherkonten hinzufügen:

- Sie haben Ihr physisches Gerät gemäß der Anleitung unter [Installieren von Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md) installiert.

- Sie haben das physische Gerät wie unter [Aktivieren Ihres Azure Stack Edge Pro-Geräts](azure-stack-edge-gpu-deploy-activate.md) beschrieben aktiviert.


## <a name="add-an-edge-storage-account"></a>Hinzufügen eines Edge-Speicherkontos

Führen Sie die folgenden Schritte aus, um ein Edge-Speicherkonto zu erstellen:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]


## <a name="connect-to-the-edge-storage-account"></a>Verbinden mit dem Edge-Speicherkonto

Sie können jetzt über *HTTP* oder *HTTPS* eine Verbindung mit den REST-APIs des Edge-Speichers herstellen.

- *HTTPS* ist die sichere und empfohlene Methode.
- *HTTP* wird zum Herstellen von Verbindungen über vertrauenswürdige Netzwerke verwendet.

## <a name="connect-via-http"></a>Herstellen einer Verbindung über HTTP

Zum Herstellen einer Verbindung mit REST-APIs von Edge-Speicher über HTTP müssen die folgenden Schritte ausgeführt werden:

- Hinzufügen der virtuellen IP-Adresse für Azure-konsistente Dienste und des Blob-Dienstendpunkts zum Remotehost
- Überprüfen der Verbindung 

Die einzelnen Schritte werden in den folgenden Abschnitten beschrieben.

### <a name="add-device-ip-address-and-blob-service-endpoint-to-the-remote-client"></a>Hinzufügen der Geräte-IP-Adresse und des Blob-Dienstendpunkts zum Remoteclient

[!INCLUDE [Add device IP and blob service endpoint to host file](../../includes/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint.md)]


### <a name="verify-connection"></a>Überprüfen der Verbindung

Um die Verbindung zu überprüfen, benötigen Sie in der Regel die folgenden (möglicherweise abweichenden) Informationen, die Sie im vorherigen Schritt erfasst haben:

- Name des Speicherkontos
- Zugriffsschlüssel für das Speicherkonto
- Blob-Dienstendpunkt

Sie verfügen bereits über den Namen des Speicherkontos und den Blob-Dienstendpunkt. Sie können den Zugriffsschlüssel für das Speicherkonto abrufen, indem Sie sich über Azure Resource Manager und einen Azure PowerShell-Client mit dem Gerät verbinden.

Führen Sie die Schritte in [Herstellen einer Verbindung mit dem Gerät über Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md) aus. Sobald Sie sich über Azure Resource Manager bei den APIs des lokalen Geräts angemeldet haben, rufen Sie die Liste der Speicherkonten auf dem Gerät ab. Führen Sie das folgende Cmdlet aus:

`Get-AzureRMStorageAccount`

Bestimmen Sie in der Liste der Speicherkonten auf dem Gerät das Speicherkonto, für das Sie den Zugriffsschlüssel benötigen. Notieren Sie sich den Namen des Speicherkontos und die Ressourcengruppe.

Nachfolgend sehen Sie eine Beispielausgabe:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccount

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime          ProvisioningState EnableHttpsTrafficOnly
------------------ ----------------- -------- -------     ----    ---------- ------------          ----------------- ----------------------
myasetiered1       myasetiered1      DBELocal StandardLRS Storage            11/27/2019 7:10:12 PM Succeeded         False
```

Führen Sie das folgende Cmdlet aus, um den Zugriffsschlüssel abzurufen:

`Get-AzureRmStorageAccountAccessKey`

Nachfolgend sehen Sie eine Beispielausgabe:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasetiered1
Name: myasetiered1

KeyName Value    Permissions                                                                                
------- -----    -----------                                                                                
key1    Jb2brrNjRNmArFcDWvL4ufspJjlo+Nie1uh8Mp4YUOVQNbirA1uxEdHeV8Z0dXbsG7emejFWI9hxyR1T93ZncA==        Full
key2    6VANuHzHcJV04EFeyPiWRsFWnHPkgmX1+a3bt5qOQ2qIzohyskIF/2gfNMqp9rlNC/w+mBqQ2mI42QgoJSmavg==        Full
```

Kopieren und speichern Sie diesen Schlüssel. Sie nutzen diesen Schlüssel, um die Verbindung mithilfe von Azure Storage-Explorer zu überprüfen.

Verwenden Sie zum Überprüfen der erfolgreichen Verbindungsherstellung Storage-Explorer, um das Anfügen an ein externes Speicherkonto durchzuführen. [Laden Sie Storage-Explorer herunter](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409), falls noch nicht geschehen.

[!INCLUDE [Verify connection using Storage Explorer](../../includes/azure-stack-edge-gateway-verify-connection-storage-explorer.md)]


## <a name="connect-via-https"></a>Herstellen einer Verbindung über HTTPS

Zum Herstellen einer Verbindung mit Azure-Blobspeicher-REST-APIs über HTTPS müssen die folgenden Schritte ausgeführt werden:

- Abrufen des Blob-Endpunktzertifikats
- Importieren des Zertifikats auf dem Client- oder Remotehost
- Hinzufügen der Geräte-IP und des Blob-Dienstendpunkts zum Client- oder Remotehost
- Konfigurieren und Überprüfen der Verbindung

Die einzelnen Schritte werden in den folgenden Abschnitten beschrieben.

### <a name="get-certificate"></a>Abrufen des Zertifikats

Der Zugriff auf Blob Storage über HTTPS erfordert ein SSL-Zertifikat für das Gerät. Sie laden außerdem dieses Zertifikat als *PFX*-Datei mit einem angefügten privaten Schlüssel auf Ihr Azure Stack Edge Pro-Gerät hoch. Weitere Informationen zum Erstellen (nur für Test- und Entwicklungszwecke) und Hochladen dieser Zertifikate auf Ihr Azure Stack Edge Pro-Gerät finden Sie unter:

- [Erstellen des Blob-Endpunktzertifikats](azure-stack-edge-j-series-manage-certificates.md#create-certificates-optional)
- [Hochladen des Blob-Endpunktzertifikats](azure-stack-edge-j-series-manage-certificates.md#upload-certificates)
- [Importieren von Zertifikaten auf den Client, der auf das Gerät zugreift](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)

### <a name="import-certificate"></a>Importieren des Zertifikats

Wenn Sie mit Azure Storage-Explorer eine Verbindung mit den Speicherkonten auf dem Gerät herstellen, müssen Sie auch das Zertifikat im PEM-Format in den Storage-Explorer importieren. In der Windows-Umgebung entspricht die Base-64-Codierung *.cer* dem PEM-Format.

Führen Sie die folgenden Schritte aus, um die Zertifikate in Azure Storage-Explorer zu importieren:

1. Stellen Sie sicher, dass Azure Storage-Explorer die Azure Stack-APIs als Ziel verwendet. Wechseln Sie zu **Bearbeiten > Azure Stack-APIs als Ziel verwenden**. Starten Sie bei Aufforderung Storage-Explorer neu, damit die Änderung wirksam wird.

2. Um SSL-Zertifikate zu importieren, wechseln Sie zu **Bearbeiten > SSL-Zertifikate > Zertifikate importieren**.

  
   ![Importieren des Zertifikats in Storage-Explorer](./media/azure-stack-edge-j-series-deploy-add-storage-accounts/import-cert-storage-explorer-1.png) 

3. Navigieren Sie zur Signaturkette und den Blob-Zertifikaten, und stellen Sie bereit. Sowohl die Signaturkette als auch das Blob-Zertifikat müssen im PEM-Format vorliegen, das dem Base64-codierten Format auf Windows-Systemen entspricht. Sie werden benachrichtigt, dass die Zertifikate erfolgreich importiert wurden.


### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Hinzufügen der Geräte-IP-Adresse und des Blob-Dienstendpunkts

Führen Sie dieselben Schritte aus, um beim [Herstellen der Verbindung über *HTTP* die IP-Adresse des Geräts und den Endpunkt des Blobdiensts hinzuzufügen](#add-device-ip-address-and-blob-service-endpoint-to-the-remote-client).

### <a name="configure-and-verify-connection"></a>Konfigurieren und Überprüfen der Verbindung

Befolgen Sie die Schritte zum [Konfigurieren und Überprüfen der Verbindung, die Sie beim Herstellen einer Verbindung über *http*](#verify-connection) verwendet haben. Der einzige Unterschied besteht darin, dass die Option *HTTP verwenden* deaktiviert bleibt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden unter anderem folgende Azure Stack Edge Pro-Themen behandelt:

> [!div class="checklist"]
> * Hinzufügen von Speicherkonten
> * Herstellen einer Verbindung mit einem Speicherkonto

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihre Daten mithilfe von Azure Stack Edge Pro transformieren:

> [!div class="nextstepaction"]
> [Transformieren von Daten mit Azure Stack Edge Pro](./azure-stack-edge-j-series-deploy-configure-compute.md)



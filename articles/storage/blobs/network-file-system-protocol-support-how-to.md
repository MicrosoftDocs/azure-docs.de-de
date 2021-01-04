---
title: Einbinden von Azure Blob Storage mithilfe des NFS 3.0-Protokolls (Vorschau) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Container über eine Azure-VM (virtueller Computer) oder einen lokal ausgeführten Client in Blob Storage mithilfe des NFS 3.0-Protokolls einbinden.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 97b52159684eca9be59ccc711f6d2f19b5eb8d49
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906113"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol-preview"></a>Einbinden von Azure Blob Storage mithilfe des NFS 3.0-Protokolls (Vorschau)

Sie können einen Container über eine Windows- oder Linux-basierte Azure-VM oder ein lokal ausgeführtes Windows- oder Linux-System in Blob Storage mithilfe des NFS 3.0-Protokolls einbinden. Dieser Artikel bietet eine schrittweise Anleitung. Weitere Informationen zur Unterstützung des NFS 3.0-Protokolls in Blob-Speicher finden Sie unter [Unterstützung für Network File System 3.0 (NFS) in Azure Blob Storage (Vorschau)](network-file-system-protocol-support.md).

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>Schritt 1: Registrieren des NFS 3.0-Protokollfeatures in Ihrem Abonnement

1. Öffnen Sie ein PowerShell-Befehlsfenster. 

2. Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

   ```powershell
   Connect-AzAccount
   ```

3. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement fest.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   Ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

4. Registrieren Sie die `AllowNFSV3`-Funktion mit dem folgenden Befehl.

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. Registrieren Sie den Ressourcenanbieter, indem Sie den folgenden Befehl verwenden.
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>Schritt 2: Überprüfen, ob das Feature registriert ist 

Die Registrierungsgenehmigung kann bis zu einer Stunde dauern. Um zu überprüfen, ob die Registrierung abgeschlossen wurde, verwenden Sie die folgenden Befehle.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>Schritt 3: Erstellen eines virtuellen Azure-Netzwerks (VNet)

Ihr Speicherkonto muss in einem VNet sein. Ein VNet ermöglicht Clients eine sichere Verbindung mit Ihrem Speicherkonto. Weitere Informationen zum VNet und wie Sie eins erstellen, finden Sie in der [Dokumentation zu virtuellen Netzwerken](../../virtual-network/index.yml).

> [!NOTE]
> Clients im selben VNet können Container in Ihr Konto einbinden. Sie können auch einen Container von einem Client aus einbinden, der in einem lokalen Netzwerk ausgeführt wird, doch Sie müssen zunächst Ihr lokales Netzwerk mit Ihrem VNet verbinden. Siehe [Unterstützte Netzwerkverbindungen](network-file-system-protocol-support.md#supported-network-connections).

## <a name="step-4-configure-network-security"></a>Schritt 4: Konfigurieren der Netzwerksicherheit

Die einzige Möglichkeit zum Sichern der Daten in Ihrem Konto besteht darin, ein VNet und andere Netzwerksicherheitseinstellungen zu verwenden. Alle anderen Tools, die zum Sichern von Daten verwendet werden, wie Kontoschlüsselautorisierung, Azure Active Directory (AD) und Zugriffssteuerungslisten (Access Control Lists, ACLs), werden in Konten, für die die Unterstützung des NFS 3.0-Protokolls aktiviert ist, noch nicht unterstützt. 

Informationen zum Sichern der Daten in Ihrem Konto finden Sie in den folgenden Empfehlungen: [Netzwerksicherheitsempfehlungen für Blob Storage](security-recommendations.md#networking).

## <a name="step-5-create-and-configure-a-storage-account"></a>Schritt 5: Erstellen und Konfigurieren eines Speicherkontos

Um einen Container mithilfe von NFS 3.0 einzubinden, müssen Sie ein Speicherkonto erstellen, **nachdem** Sie die Funktion in Ihrem Abonnement registriert haben. Sie können keine Konten aktivieren, die vor der Registrierung der Funktion bereits vorhanden waren. 

In der Vorschauversion dieses Features wird das NFS 3.0-Protokoll in [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md)-Konten und Konten vom Typ [Allgemein v2](../common/storage-account-overview.md#general-purpose-v2-accounts) unterstützt.

Wenn Sie das Konto konfigurieren, wählen Sie die folgenden Werte aus:

|Einstellung | Premium-Leistung | Standardleistung  
|----|---|---|
|Ort|Alle verfügbaren Regionen |Eine der folgenden Regionen: „Australien, Osten“, „Südkorea, Mitte“ und „USA, Süden-Mitte“   
|Leistung|Premium| Standard
|Kontoart|BlockBlobStorage| Allgemein v2
|Replikation|Lokal redundanter Speicher (LRS)| Lokal redundanter Speicher (LRS)
|Konnektivitätsmethode|Öffentlicher Endpunkt (ausgewählte Netzwerke) oder privater Endpunkt |Öffentlicher Endpunkt (ausgewählte Netzwerke) oder privater Endpunkt
|Sichere Übertragung erforderlich|Disabled|Disabled
|Hierarchischer Namespace|Aktiviert|Aktiviert
|NFS V3|Aktiviert |Aktiviert 

Sie können die Standardwerte für alle anderen Einstellungen übernehmen. 

## <a name="step-6-create-a-container"></a>Schritt 6: Erstellen eines Containers

Erstellen Sie einen Container in Ihrem Speicherkonto, indem Sie eins dieser Tools oder SDKs verwenden:

|Tools|SDKs|
|---|---|
|[Azure portal](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-blobs.md#create-a-container)|[Java](data-lake-storage-directory-file-acl-java.md#create-a-container)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure-Befehlszeilenschnittstelle](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>Schritt 7: Einbinden des Containers

Erstellen Sie ein Verzeichnis auf Ihrem Windows- oder Linux-System, und binden Sie dann ein Container in das Speicherkonto ein.

### <a name="linux"></a>[Linux](#tab/linux)

1. Erstellen Sie auf einem Linux-System ein Verzeichnis.

   ```
   mkdir -p /mnt/test
   ```

2. Binden Sie einen Container mit dem folgenden Befehl ein.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - Ersetzen Sie den Platzhalter `<storage-account-name>`, der in diesem Befehl vorkommt, durch den Namen Ihres Speicherkontos.  

   - Ersetzen Sie den Platzhalter `<container-name>` durch den Namen Ihres Containers.


### <a name="windows"></a>[Windows](#tab/windows)

1. Öffnen Sie das Dialogfeld **Windows-Features**, und aktivieren Sie dann das Feature **Client für NFS**. 

   ![Feature „Client für NFS“](media/network-file-system-protocol-how-to/client-for-network-files-system-feature.png)

2. Binden Sie einen Container mithilfe des [mount](/windows-server/administration/windows-commands/mount)-Befehls ein.

   ```
   mount -o nolock <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name> *
   ```

   - Ersetzen Sie den Platzhalter `<storage-account-name>`, der in diesem Befehl vorkommt, durch den Namen Ihres Speicherkontos.  

   - Ersetzen Sie den Platzhalter `<container-name>` durch den Namen Ihres Containers.

3. Wenn Sie Schreibberechtigungen benötigen, müssen Sie möglicherweise die Standard-UID und die GID ändern, die von Windows zum Herstellen einer Verbindung mit der Freigabe verwendet werden. Führen Sie dazu die folgenden PowerShell-Befehle als Administrator aus:

   ```
   New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default -Name AnonymousUid -PropertyType DWord -Value 0
   New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default -Name AnonymousGid -PropertyType DWord -Value 0
   ```
   
   - Starten Sie den NFS-Clientdienst neu, oder starten Sie nach dieser Änderung den Server neu.

---

## <a name="resolve-common-issues"></a>Beheben allgemeiner Probleme

|Problem/Fehler | Lösung|
|---|---|
|`Access denied by server while mounting`|Stellen Sie sicher, dass der Client in einem unterstützten Subnetz ausgeführt wird. Siehe [Unterstützte Netzwerkadressen](network-file-system-protocol-support.md#supported-network-connections).|
|`No such file or directory`| Stellen Sie sicher, dass der Container, den Sie einbinden, erstellt wurde, nachdem Sie überprüft haben, ob die Funktion registriert wurde. Anweisungen zum Wechseln in den Wartungsmodus finden Sie im Abschnitt [Schritt 2: Überprüfen, ob das Feature registriert ist](#step-2-verify-that-the-feature-is-registered). Achten Sie außerdem darauf, den Befehl zum Einbinden und die zugehörigen Parameter direkt im Terminal einzugeben. Wenn Sie diesen Befehl oder einen Teil davon aus einer anderen Anwendung kopieren und in das Terminal einfügen, kann dies dazu führen, dass ausgeblendete Zeichen in den eingefügten Informationen diesen Fehler verursachen.|

## <a name="see-also"></a>Weitere Informationen

[Unterstützung für Network File System 3.0 (NFS) in Azure Blob Storage (Vorschau)](network-file-system-protocol-support.md)
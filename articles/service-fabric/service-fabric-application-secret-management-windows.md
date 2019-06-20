---
title: Einrichten eines Verschlüsselungszertifikats und Verschlüsseln von Geheimnissen in Azure Service Fabric-Windows-Clustern | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie ein Verschlüsselungszertifikat einrichten und Geheimnisse in Windows-Clustern verschlüsseln.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: 3d324c54d10433520a73f2bd836c26bd79f1b3bb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60615267"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Einrichten eines Verschlüsselungszertifikats und Verschlüsseln von Geheimnissen in Windows-Clustern
In diesem Artikel wird veranschaulicht, wie Sie ein Verschlüsselungszertifikat einrichten und zum Verschlüsseln von Geheimnissen in Windows-Clustern verwenden. Informationen zu Linux-Clustern finden Sie unter [Einrichten eines Verschlüsselungszertifikats und Verschlüsseln von Geheimnissen in Linux-Clustern][secret-management-linux-specific-link].

[Azure Key Vault][key-vault-get-started] wird als sicherer Speicherort für Zertifikate sowie zum Installieren von Zertifikaten auf Service Fabric-Clustern in Azure verwendet. Wenn die Bereitstellung nicht in Azure erfolgt, muss Key Vault nicht zum Verwalten von Geheimnissen in Service Fabric-Anwendungen eingesetzt werden. Die *Verwendung* von Geheimnissen in einer Anwendung ist jedoch cloudplattformunabhängig, sodass Anwendungen auf einem Cluster bereitgestellt werden können, der an einem beliebigen Standort gehostet wird. 

## <a name="obtain-a-data-encipherment-certificate"></a>Abrufen eines Datenverschlüsselungszertifikats
Ein Datenverschlüsselungszertifikat wird ausschließlich für die Ver- und Entschlüsselung von [Parametern][parameters-link] in der Datei „Settings.xml“ eines Diensts und von [Umgebungsvariablen][environment-variables-link] in der Datei „ServiceManifest.xml“ eines Diensts verwendet. Es wird nicht für die Authentifizierung oder Signierung von Verschlüsselungstext verwendet. Das Zertifikat muss die folgenden Anforderungen erfüllen:

* Das Zertifikat muss einen privaten Schlüssel enthalten.
* Das Zertifikat muss für den Schlüsselaustausch erstellt werden und in eine PFX-Datei (Persönlicher Informationsaustausch) exportiert werden können.
* Zu den wichtigsten Verwendungszwecken des Zertifikatschlüssels muss die Datenverschlüsselung (10) zählen. Der Zertifikatschlüssel darf nicht für die Server- oder Clientauthentifizierung verwendet werden. 
  
  Beim Erstellen eines selbstsignierten Zertifikats mithilfe von PowerShell muss z.B. das Flag `KeyUsage` auf `DataEncipherment` festgelegt werden:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Installieren des Zertifikats in Ihrem Cluster
Dieses Zertifikat muss auf jedem Knoten innerhalb des Clusters installiert werden. Anweisungen zum Einrichten finden Sie unter [Erstellen eines Clusters mithilfe von Azure Resource Manager][service-fabric-cluster-creation-via-arm]. 

## <a name="encrypt-application-secrets"></a>Verschlüsseln von Geheimnissen in Anwendungen
Der folgende PowerShell-Befehl wird zum Verschlüsseln eines geheimen Werts verwendet. Mit diesem Befehl wird nur der Wert verschlüsselt. Der Verschlüsselungstext wird **nicht** verschlüsselt. Zum Erstellen des Chiffretexts für geheime Werte muss das Verschlüsselungszertifikat verwendet werden, das auf Ihrem Cluster installiert ist:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Die resultierende Base64-codierte Zeichenfolge enthält sowohl den geheimen Chiffretext als auch Informationen zu dem Zertifikat, das für die Verschlüsselung verwendet wurde.

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich über das [Angeben von verschlüsselten Geheimnissen in einer Anwendung][secret-management-specify-encrypted-secrets-link].

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application

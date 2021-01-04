---
title: include file
description: include file
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 3509185baa3a9d7be90c1fa4bd8000da4a8a6fe5
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562909"
---
## <a name="prepare-for-akv-integration"></a>Vorbereiten auf die Integration des Azure-Schlüsseltresors
Es müssen mehrere Voraussetzungen erfüllt sein, damit Sie die Azure-Schlüsseltresor-Integration zum Konfigurieren Ihres virtuellen SQL Server-Computers verwenden können: 

1. [Installieren von Azure Powershell](#install)
2. [Erstellen von Azure Active Directory](#register)
3. [Erstellen eines Schlüsseltresors](#createkeyvault)

In den folgenden Abschnitten werden diese erforderlichen Komponenten und die Informationen beschrieben, die Sie ermitteln müssen, um die PowerShell-Cmdlets später ausführen zu können.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a name="install-azure-powershell"></a><a id="install"></a> Installieren von Azure PowerShell
Stellen Sie sicher, dass Sie das aktuelle Azure PowerShell-Modul installiert haben. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="register-an-application-in-your-azure-active-directory"></a><a id="register"></a> Registrieren einer Anwendung in Ihrer Azure Active Directory-Instanz

Zunächst benötigen Sie für Ihr Abonnement [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD). Neben vielen weiteren Vorteilen erhalten Sie hiermit die Möglichkeit, für bestimmte Benutzer und Anwendungen die Berechtigung für Ihren Schlüsseltresor zu vergeben.

Registrieren Sie als Nächstes eine Anwendung für AAD. So erhalten Sie ein Dienstprinzipalkonto, das über Zugriff auf Ihren Schlüsseltresor verfügt, was für Ihre VM erforderlich ist. Im Artikel zu Azure Key Vault finden Sie diese Schritte im Abschnitt [Registrieren einer Anwendung bei Azure Active Directory](../articles/key-vault/general/manage-with-cli2.md#registering-an-application-with-azure-active-directory). Alternativ können Sie sich in [diesem Blogbeitrag](/archive/blogs/kv/azure-key-vault-step-by-step) die Schritte mit Screenshots im Abschnitt **Get an identity for the application** (Abrufen einer Identität für die Anwendung) ansehen. Vor dem Ausführen der Schritte müssen Sie während der Registrierung die folgenden Informationen ermitteln, da sie diese später beim Aktivieren der Azure Key Vault-Integration auf Ihrem virtuellen SQL-Computer benötigen.

* Suchen Sie nach dem Hinzufügen der Anwendung auf dem Blatt **Registrierte App** nach der **Anwendungs-ID** (die auch AAD ClientID oder AppID genannt wird).
    Die Anwendungs-ID wird später dem Parameter **$spName** (Dienstprinzipalname) im PowerShell-Skript zugewiesen, um die Azure Key Vault-Integration zu ermöglichen.

   ![Anwendungs-ID](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Kopieren Sie bei der Schlüsselerstellung das Geheimnis für Ihren Schlüssel, wie im folgenden Screenshot zu sehen. Dieser geheime Schlüssel des Schlüssels wird später dem Parameter **$spSecret** (geheimer Schlüssel des Dienstprinzipals) im PowerShell-Skript zugewiesen.

   ![AAD-Geheimnis](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* Die Anwendungs-ID und das Geheimnis werden auch zum Erstellen von Anmeldeinformationen in SQL Server verwendet.

* Sie müssen diese neue Anwendungs-ID (oder Client-ID) so autorisieren, dass sie über die folgenden Zugriffsberechtigungen verfügt: **get**, **wrapKey**, **unwrapKey**. Hierfür wird das Cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) verwendet. Weitere Informationen finden Sie unter [Azure Key Vault – Übersicht](../articles/key-vault/general/overview.md).

### <a name="create-a-key-vault"></a><a id="createkeyvault"></a> Erstellen einer Key Vault-Instanz
Um den Azure-Schlüsseltresor zum Speichern der Schlüssel zu verwenden, die Sie für die Verschlüsselung auf Ihrem virtuellen Computer nutzen, benötigen Sie Zugriff auf den Schlüsseltresor. Wenn Sie den Schlüsseltresor noch nicht eingerichtet haben, können Sie die Schritte im Artikel [Erste Schritte mit Azure Key Vault](../articles/key-vault/general/overview.md) ausführen. Vor dem Ausführen der Schritte müssen Sie während dieses Setups einige Informationen ermitteln, da sie diese später beim Aktivieren der Azure Key Vault-Integration auf Ihrem virtuellen SQL-Computer benötigen.

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'
```

Achten Sie beim Schritt zum „Erstellen eines Schlüsseltresors“ auf die zurückgegebene **vaultUri**-Eigenschaft. Hierbei handelt es sich um die Schlüsseltresor-URL. Im Beispiel dieses Schritts (siehe unten) lautet der Name des Schlüsseltresors „ContosoKeyVault“, sodass sich für den Schlüsseltresor die URL https://contosokeyvault.vault.azure.net/ ergibt.

Die Schlüsseltresor-URL wird später dem Parameter **$akvURL** im PowerShell-Skript zugewiesen, um die Azure-Schlüsseltresor-Integration zu ermöglichen.

Nach Erstellung des Schlüsseltresors muss diesem ein Schlüssel hinzugefügt werden. Dieser wird später bei der Erstellung eines asymmetrischen Schlüssels in SQL Server verwendet.
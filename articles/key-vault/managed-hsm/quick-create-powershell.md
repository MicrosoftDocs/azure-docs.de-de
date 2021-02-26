---
title: 'Erstellen und Abrufen der Attribute eines verwalteten Schlüssels in Azure Key Vault: Azure PowerShell'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Azure PowerShell einen verwalteten Schlüssel in Azure Key Vault festlegen und den Schlüssel daraus abrufen.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/26/2021
ms.author: mbaldwin
ms.openlocfilehash: 943555e9f7a26530a075aee27dd310d96974e652
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072789"
---
# <a name="quickstart-set-and-retrieve-a-managed-key-from-azure-key-vault-using-powershell"></a>Schnellstart: Festlegen eines Geheimnisses und Abrufen eines verwalteten Schlüssels aus Azure Key Vault mithilfe von PowerShell

In dieser Schnellstartanleitung erstellen Sie mit Azure PowerShell einen Schlüsseltresor in Azure Key Vault. Azure Key Vault ist ein Clouddienst, der als sicherer Geheimnisspeicher fungiert. Dadurch können Schlüssel, Kennwörter, Zertifikate und andere Geheimnisse sicher gespeichert werden. Weitere Informationen zu Key Vault finden Sie in der [Übersicht](../general/overview.md). Mit Azure PowerShell können Azure-Ressourcen mithilfe von Befehlen oder Skripts erstellt und verwaltet werden. Danach speichern Sie einen Schlüssel.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens die Version 1.0.0 des Azure PowerShell-Moduls verwenden. Geben Sie `$PSVersionTable.PSVersion` ein, um die Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Login-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Erstellen Sie mithilfe des Azure PowerShell-Cmdlets [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe namens *myResourceGroup* am Standort *westus*. 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"

## Get your principal ID

To create a managed HSM, you will need your Azure Active Directory principal ID.  To obtain your ID, use the Azure PowerShell [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet, passing your email address to the "UserPrincipalName" parameter:

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName "<your@email.address>"
```

Ihre Prinzipal-ID wird im Format „xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx“ zurückgegeben.

## <a name="create-a-managed-hsm"></a>Erstellen eines verwalteten HSM

Erstellen Sie mithilfe des Azure PowerShell-Cmdlets [New-AzKeyVaultManagedHsm](/powershell/module/az.keyvault/new-azkeyvaultmanagedhsm) ein neues verwaltetes Key Vault-HSM. Dazu müssen einige Informationen angegeben werden:

- Name des verwalteten HSM: Eine Zeichenfolge mit 3 bis 24 Zeichen, die nur Zahlen (0–9), Buchstaben (a–z, A–Z) und Bindestriche (-) enthalten darf.

  > [!Important]
  > Jedes verwaltete HSM muss einen eindeutigen Namen haben. Ersetzen Sie in den folgenden Beispielen „<your-unique-managed-hsm-name>“ durch den Namen Ihres verwalteten HSM.

- Ressourcengruppennamen: **myResourceGroup**
- Standort: **EastUS**
- Ihre Prinzipal-ID: Übergeben Sie die Azure Active Directory-Prinzipal-ID, die Sie im letzten Abschnitt abgerufen haben, an den Parameter „Administrator“. 

```azurepowershell-interactive
New-AzKeyVaultManagedHsm -Name "<your-unique-managed-hsm-name>" -ResourceGroupName "myResourceGroup" -Location "West US" -Administrator "<your-principal-ID>"
```

Die Ausgabe dieses Cmdlets zeigt Eigenschaften der neu erstellten verwalteten HSM. Beachten Sie die beiden folgenden Eigenschaften:

- **Name des verwalteten HSM:** Der Name, den Sie oben für den Parameter „--name“ angegeben haben.
- **Tresor-URI**: In dem Beispiel ist dies „https://&lt;your-unique-managed-hsm-name&gt;.vault.azure.net/“. Anwendungen, die Ihren Tresor über die zugehörige REST-API nutzen, müssen diesen URI verwenden.

An diesem Punkt ist nur Ihr Azure-Konto zum Ausführen von Vorgängen für den neuen Tresor autorisiert.

## <a name="activate-your-managed-hsm"></a>Aktivieren Ihres verwalteten HSM

Bis zur Aktivierung des HSM sind alle Datenebenenbefehle deaktiviert. Sie können keine Schlüssel erstellen oder Rollen zuweisen. Nur die designierten Administratoren, die im Rahmen des Erstellungsbefehls zugewiesen wurden, können das HSM aktivieren. Zum Aktivieren des HSM muss die [Sicherheitsdomäne](security-domain.md) heruntergeladen werden.

Für die HSM-Aktivierung benötigen Sie Folgendes:
- Mindestens drei RSA-Schlüsselpaare (maximal zehn)
- Geben Sie die Mindestanzahl von Schlüsseln an, die zum Entschlüsseln der Sicherheitsdomäne erforderlich sind (Quorum).

Für die HSM-Aktivierung müssen zwischen drei und zehn öffentliche RSA-Schlüssel an das HSM gesendet werden. Das HSM verschlüsselt die Sicherheitsdomäne mit diesen Schlüsseln und sendet sie zurück. Nach erfolgreichem Herunterladen der Sicherheitsdomäne ist Ihr HSM verwendungsbereit. Darüber hinaus müssen Sie ein Quorum angeben. Hierbei handelt es sich um die Mindestanzahl privater Schlüssel, die zum Entschlüsseln der Sicherheitsdomäne erforderlich sind.

Im folgenden Beispiel wird gezeigt, wie Sie mithilfe von `openssl` ([hier](https://slproweb.com/products/Win32OpenSSL.html) für Windows verfügbar) drei selbst signierte Zertifikate generieren.

```console
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Erstellen und speichern Sie die in diesem Schritt generierten RSA-Schlüsselpaare und die generierte Sicherheitsdomänendatei an einem sicheren Ort.

Verwenden Sie das Azure PowerShell-Cmdlet [Export-AzKeyVaultSecurityDomain](/powershell/module/az.keyvault/export-azkeyvaultsecuritydomain), um die Sicherheitsdomäne herunterzuladen und das verwaltete HSM zu aktivieren. Im folgenden Beispiel werden drei RSA-Schlüsselpaare verwendet. (Für diesen Befehl werden nur öffentliche Schlüssel benötigt.) Das Quorum wird auf „2“ festgelegt.

```azurepowershell-interactive
Export-AzKeyVaultSecurityDomain -Name "<your-unique-managed-hsm-name>" -Certificates "cert_0.cer", "cert_1.cer", "cert_2.cer" -OutputPath "MHSMsd.ps.json" -Quorum 2
```

Speichern Sie die Sicherheitsdomänendatei und die RSA-Schlüsselpaare an einem sicheren Ort. Sie werden für die Notfallwiederherstellung sowie für die Erstellung eines weiteren verwalteten HSM mit der gleichen Sicherheitsdomäne benötigt, um die gemeinsame Nutzung von Schlüsseln zu ermöglichen.

Nach erfolgreichem Herunterladen der Sicherheitsdomäne befindet sich Ihr HSM im aktiven Zustand und ist verwendungsbereit.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Key Vault-Instanz erstellt und ein Zertifikat darin gespeichert. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](../general/overview.md)
- Sehen Sie sich die Referenz zu den [Azure PowerShell-Cmdlets für Key Vault](/powershell/module/az.keyvault/) an.
- [Azure Key Vault-Sicherheitsübersicht](../general/security-overview.md)

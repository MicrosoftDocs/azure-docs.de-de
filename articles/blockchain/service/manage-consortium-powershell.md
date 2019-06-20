---
title: Verwalten der Mitglieder in einem Azure Blockchain Service-Konsortium mithilfe von Azure PowerShell
description: Hier erfahren Sie, wie Sie die Mitglieder des Azure Blockchain Service-Konsortiums mithilfe von Azure PowerShell verwalten.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 4bb72bc3fe8b85a8d2aed88e02f5f3150abb6899
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66493665"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-by-using-powershell"></a>Verwalten der Konsortiumsmitglieder in Azure Blockchain Service mithilfe von PowerShell

Sie können PowerShell dazu verwenden, Blockchainkonsortiumsmitglieder Ihres Azure Blockchain-Diensts zu verwalten. Mitglieder mit Administratorrechten können alle Teilnehmer des Blockchainkonsortiums einladen, hinzufügen, entfernen und deren Rollen ändern. Mitglieder mit Benutzerberechtigungen können alle Teilnehmer des Blockchainkonsortiums anzeigen und ihren Mitgliedsanzeigenamen ändern.

## <a name="prerequisites"></a>Voraussetzungen

* Erstellen Sie ein Blockchainmitglied über das [Azure-Portal](create-member.md).
* Weitere Informationen zu Konsortien, Mitgliedern und Knoten finden Sie unter [Azure Blockchain Service-Konsortium](consortium.md).

## <a name="open-azure-cloud-shell"></a>Öffnen von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert.

Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [shell.azure.com/powershell](https://shell.azure.com/powershell) navigieren. Wählen Sie **Kopieren** aus, um die Codeblöcke zu kopieren. Fügen Sie die Blöcke anschließend in Cloud Shell ein, und wählen Sie **Eingabe**, um sie auszuführen.

## <a name="install-the-powershell-module"></a>Installieren des PowerShell-Moduls

Installieren Sie das Paket „Microsoft.AzureBlockchainService.ConsortiumManagement.PS“ aus dem PowerShell-Katalog.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Festlegen der Informationspräferenz

Sie können beim Ausführen der Cmdlets weitere Informationen erhalten, indem Sie die Variable für die Informationspräferenz festlegen. Standardmäßig ist *$InformationPreference* auf *SilentlyContinue* festgelegt.

Um ausführlichere Informationen von Cmdlets zu erhalten, legen Sie die Präferenz in der PowerShell wie folgt fest:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Herstellen einer Web3-Verbindung

Um Konsortiumsmitglieder zu verwalten, müssen Sie eine Web3-Verbindung mit Ihrem Blockchain Service-Mitgliedsendpunkt herstellen. Mithilfe dieses Skripts können Sie globale Variablen für den Aufruf der Cmdlets zur Konsortiumsverwaltung festlegen.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Ersetzen Sie *\<Member account password\>* (Kennwort des Mitgliedskontos) durch das Kennwort des Mitgliedskontos, das Sie beim Erstellen des Mitglieds verwendet haben.

Die weiteren Werte finden Sie im Azure-Portal:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zur **Übersichtsseite** Ihres Azure Blockchain Service-Standardmitglieds.

    ![Mitgliedsübersicht](./media/manage-consortium-powershell/member-overview.png)

    Ersetzen Sie *\<Member account\>* (Mitgliedskonto) und *\<RootContract address\>* (Stammvertragsadresse) durch die Werte aus dem Portal.

1. Wählen Sie für die Endpunktadresse **Transaction nodes** (Transaktionsknoten) und dann den **standardmäßigen Transaktionsknoten** aus. Der Standardknoten weist denselben Namen auf wie das Blockchainmitglied.
1. Wählen Sie **Connection strings** (Verbindungszeichenfolgen).

    ![Verbindungszeichenfolgen](./media/manage-consortium-powershell/connection-strings.png)

    Ersetzen Sie *\<Endpoint address\>* (Endpunktadresse) durch den Wert unter **HTTPS (Access key 1)** (HTTPS (Zugriffsschlüssel 1)) oder **HTTPS (Access key 2)** (HTTPS (Zugriffsschlüssel 2)).

## <a name="manage-the-network-and-smart-contracts"></a>Verwalten von Netzwerk und Smart Contracts

Verwenden Sie die Netzwerk- und Smart Contract-Cmdlets, um eine Verbindung mit den Smart Contracts der Blockchainendpunkte herzustellen, die für die Konsortiumsverwaltung verantwortlich sind.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Verwenden Sie dieses Cmdlet, um eine Verbindung mit den Smart Contracts der Konsortiumsverwaltung herzustellen. Diese Verträge werden zum Verwalten und Durchsetzen von Mitgliedern des Konsortiums verwendet.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parameter | BESCHREIBUNG | Erforderlich |
|-----------|-------------|:--------:|
| RootContractAddress | Stammvertragsadresse der Smart Contracts der Konsortiumsverwaltung | Ja |
| Web3Client | Ein von New-Web3Connection abgerufenes Web3Client-Objekt | Ja |

#### <a name="example"></a>Beispiel

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Verwenden Sie dieses Cmdlet, um ein Objekt zu erstellen, das die Informationen für das Verwaltungskonto eines Remoteknotens enthalten soll.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parameter | BESCHREIBUNG | Erforderlich |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Adresse des Blockchainmitgliedskontos | Ja |
| ManagedAccountPassword | Kennwort der Kontoadresse | Ja |

#### <a name="example"></a>Beispiel

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Verwenden Sie dieses Cmdlet, um eine Verbindung mit dem RPC-Endpunkt eines Transaktionsknotens herzustellen.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parameter | BESCHREIBUNG | Erforderlich |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Adresse des Blockchainmitgliedsendpunkts | Ja |

#### <a name="example"></a>Beispiel

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Verwalten der Konsortiumsmitglieder

Verwenden Sie Cmdlets zur Konsortiumsmitgliedsverwaltung, um Mitglieder innerhalb des Konsortiums zu verwalten. Die verfügbaren Aktionen richten sich nach Ihrer Konsortiumsrolle.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Verwenden Sie dieses Cmdlet, um Mitgliederdetails abzurufen oder Mitglieder des Konsortiums aufzulisten.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parameter | BESCHREIBUNG | Erforderlich |
|-----------|-------------|:--------:|
| NAME | Der Name des Blockchain Service-Mitglieds, zu dem Sie Details abrufen möchten. Wenn Sie einen Namen eingeben, werden die Details zu diesem Mitglied zurückgegeben. Wenn Sie keinen Namen angeben, wird eine Liste aller Konsortiumsmitglieder zurückgegeben. | Nein |
| Mitglieder | Ein von Import-ConsortiumManagementContracts abgerufenes Mitgliederobjekt | Ja |
| Web3Client | Ein von New-Web3Connection abgerufenes Web3Client-Objekt | Ja |

#### <a name="example"></a>Beispiel

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Beispielausgabe

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Verwenden Sie dieses Cmdlet, um ein Blockchainmitglied zu entfernen.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parameter | BESCHREIBUNG | Erforderlich |
|-----------|-------------|:--------:|
| NAME | Der Name des zu entfernenden Mitglieds | Ja |
| Mitglieder | Ein von Import-ConsortiumManagementContracts abgerufenes Mitgliederobjekt | Ja |
| Web3Account | Ein von Import-Web3Account abgerufenes Web3Account-Objekt | Ja |
| Web3Client | Ein von New-Web3Connection abgerufenes Web3Client-Objekt | Ja |

#### <a name="example"></a>Beispiel

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Verwenden Sie dieses Cmdlet, um die Attribute eines Blockchainmitglieds festzulegen, z.B. Anzeigename und Konsortiumsrolle.

Konsortiumsadministratoren können **DisplayName** (Anzeigename) und **Role** (Rolle) für alle Mitglieder festlegen. Konsortiumsmitglieder mit Benutzerrolle können nur ihren eigenen Mitgliedsanzeigenamen ändern.

`Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parameter | BESCHREIBUNG | Erforderlich |
|-----------|-------------|:--------:|
| NAME | Name des Blockchainmitglieds | Ja |
| DisplayName | Neuer Anzeigename | Nein |
| AccountAddress | Kontoadresse | Nein |
| Mitglieder | Ein von Import-ConsortiumManagementContracts abgerufenes Mitgliederobjekt | Ja |
| Web3Account | Ein von Import-Web3Account abgerufenes Web3Account-Objekt | Ja |
| Web3Client |  Ein von New-Web3Connection abgerufenes Web3Client-Objekt| Ja |

#### <a name="example"></a>Beispiel

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Verwalten der Einladungen der Konsortiumsmitglieder

Verwenden Sie die Cmdlets zum Verwalten der Einladung von Konsortiumsmitgliedern, um Einladungen der Konsortiumsmitglieder zu verwalten. Die verfügbaren Aktionen richten sich nach Ihrer Konsortiumsrolle.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Verwenden Sie dieses Cmdlet, um neue Mitglieder zum Konsortium einzuladen.

`New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Parameter | BESCHREIBUNG | Erforderlich |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-Abonnement-ID des Mitglieds, das eingeladen werden soll | Ja |
| Rolle | Die Konsortiumsrolle. Mögliche Werte sind ADMIN oder USER. Bei ADMIN handelt es sich um die Administratorrolle des Konsortiums. Bei USER handelt es sich um die Mitgliederrolle des Konsortiums. | Ja |
| Mitglieder | Ein von Import-ConsortiumManagementContracts abgerufenes Mitgliederobjekt | Ja |
| Web3Account | Ein von Import-Web3Account abgerufenes Web3Account-Objekt | Ja |
| Web3Client | Ein von New-Web3Connection abgerufenes Web3Client-Objekt | Ja |

#### <a name="example"></a>Beispiel

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Verwenden Sie dieses Cmdlet, um den Einladungsstatus eines Konsortiumsmitglieds abzurufen oder aufzulisten.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parameter | BESCHREIBUNG | Erforderlich |
|-----------|-------------|:--------:|
| SubscriptionId | Die Azure-Abonnement-ID des Mitglieds, das eingeladen werden soll. Wenn Sie eine Abonnement-ID angeben, werden die Einladungsdetails zur Abonnement-ID zurückgegeben. Wenn Sie keine Abonnement-ID angeben, wird eine Liste mit allen Mitgliedereinladungen zurückgegeben. | Nein |
| Mitglieder | Ein von Import-ConsortiumManagementContracts abgerufenes Mitgliederobjekt | Ja |
| Web3Client | Ein von New-Web3Connection abgerufenes Web3Client-Objekt | Ja |

#### <a name="example"></a>Beispiel

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Beispielausgabe

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Verwenden Sie dieses Cmdlet, um die Einladung eines Konsortiumsmitglieds zu widerrufen.

`Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>`

| Parameter | BESCHREIBUNG | Erforderlich |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-Abonnement-ID des Mitglieds, dessen Einladung widerrufen werden soll | Ja |
| Mitglieder | Ein von Import-ConsortiumManagementContracts abgerufenes Mitgliederobjekt | Ja |
| Web3Account | Ein von Import-Web3Account abgerufenes Web3Account-Objekt | Ja |
| Web3Client | Ein von New-Web3Connection abgerufenes Web3Client-Objekt | Ja |

#### <a name="example"></a>Beispiel

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Verwenden Sie dieses Cmdlet, um die **Rolle** für eine vorhandene Einladung festzulegen. Nur Konsortiumsadministratoren können Einladungen ändern.

`Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Parameter | BESCHREIBUNG | Erforderlich |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-Abonnement-ID des Mitglieds, das eingeladen werden soll | Ja |
| Rolle | Neue Konsortiumsrolle für die Einladung. Mögliche Werte sind **USER** oder **ADMIN**. | Ja |
| Mitglieder |  Ein von Import-ConsortiumManagementContracts abgerufenes Mitgliederobjekt | Ja |
| Web3Account | Ein von Import-Web3Account abgerufenes Web3Account-Objekt | Ja |
| Web3Client | Ein von New-Web3Connection abgerufenes Web3Client-Objekt | Ja |

#### <a name="example"></a>Beispiel

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Konsortien, Mitgliedern und Knoten finden Sie unter:

> [!div class="nextstepaction"]
> [Azure Blockchain Service consortium (Azure Blockchain-Dienstkonsortium)](consortium.md)

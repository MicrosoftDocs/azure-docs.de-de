---
title: 'Windows Virtual Desktop: PowerShell-Modul – Azure'
description: Installieren und Einrichten des PowerShell-Moduls für Windows Virtual Desktop
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2f01e2b58c997db08ad4427de7eef1ee3760c4a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016810"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>Einrichten des PowerShell-Moduls für Windows Virtual Desktop

>[!IMPORTANT]
>Dieser Inhalt gilt für Windows Virtual Desktop mit Azure Resource Manager-Integration.

Das PowerShell-Modul für Windows Virtual Desktop ist in das Azure PowerShell-Modul integriert. In diesem Artikel erfahren Sie, wie Sie das PowerShell-Modul so einrichten, dass Sie Cmdlets für Windows Virtual Desktop ausführen können.

## <a name="set-up-your-powershell-environment"></a>Einrichten Ihrer PowerShell-Umgebung

Um das Modul zu verwenden, installieren Sie zunächst die [neueste Version von PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core). Windows Virtual Desktop-Cmdlets funktionieren zurzeit nur mit PowerShell Core.

Als Nächstes müssen Sie das DesktopVirtualization-Modul für die Verwendung in der PowerShell-Sitzung installieren.

Führen Sie das folgende PowerShell-Cmdlet im Modus mit erhöhten Rechten aus, um das Modul zu installieren:

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> Wenn dieses Cmdlet nicht funktioniert, versuchen Sie es erneut mit erhöhten Berechtigungen.

Führen Sie als Nächstes das folgende Cmdlet aus, um eine Verbindung mit Azure herzustellen:

```powershell
Connect-AzAccount
```

>[!IMPORTANT]
>Wenn Sie eine Verbindung mit dem US Gov-Portal herstellen, führen Sie stattdessen dieses Cmdlet aus:
> 
> ```powershell
> Connect-AzAccount -EnvironmentName AzureUSGovernment
> ```

Zum Anmelden bei Ihrem Azure-Konto ist ein Code erforderlich, der generiert wird, wenn Sie das Connect-Cmdlet ausführen. Um sich anzumelden, wechseln Sie zu <https://microsoft.com/devicelogin>, geben den Code ein und melden sich dann mit ihren Azure-Administratoranmeldeinformationen an.

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

Dadurch werden Sie direkt bei dem Standardabonnement für Ihre Administratoranmeldeinformationen angemeldet.

## <a name="change-the-default-subscription"></a>Ändern des Standardabonnements

Wenn Sie das Standardabonnement ändern möchten, nachdem Sie sich angemeldet haben, führen Sie das folgende Cmdlet aus:

```powershell
Select-AzSubscription -Subscription <preferredsubscriptionname>
```

Mithilfe des Out-GridView-Cmdlets können Sie auch eine Auswahl aus einer Liste treffen:

```powershell
Get-AzSubscription | Out-GridView -PassThru | Select-AzSubscription
```

Wenn Sie ein neues Abonnement verwenden möchten und auswählen, müssen Sie die ID des Abonnements in den anschließend ausgeführten Cmdlets nicht angeben. Mit dem folgenden Cmdlet wird beispielsweise ein bestimmter Sitzungshost abgerufen, ohne dass die Abonnement-ID erforderlich ist:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

Sie können Abonnements auch pro Cmdlet ändern, indem Sie den gewünschten Abonnementnamen als Parameter hinzufügen. Das nächste Cmdlet ist mit dem vorherigen Beispiel identisch, außer dass die Abonnement-ID als Parameter hinzugefügt wurde, um das vom Cmdlet verwendete Abonnement zu ändern.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>Abrufen von Standorten

Der Location-Parameter ist für alle **New-AzWVD**-Cmdlets obligatorisch, durch die neue Objekte erstellt werden.

Führen Sie das folgende Cmdlet aus, um eine Liste der Standorte abzurufen, die Ihr Abonnement unterstützt:

```powershell
Get-AzLocation
```

Die Ausgabe für **Get-AzLocation** sieht wie folgt aus:

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

Wenn Sie den Standort Ihres Kontos kennen, können Sie diesen in einem Cmdlet verwenden. Mit diesem Cmdlet wird beispielsweise ein Hostpool am Standort „southeastasia“ erstellt:

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihr PowerShell-Modul eingerichtet haben, können Sie Cmdlets ausführen, um verschiedenste Aufgaben in Windows Virtual Desktop auszuführen. Hier einige Einsatzmöglichkeiten für Ihr Modul:

- Erkunden Sie unsere [Windows Virtual Desktop-Tutorials](), um Ihre individuelle Windows Virtual Desktop-Umgebung einzurichten.
- [Erstellen eines Hostpools mit PowerShell](create-host-pools-powershell.md)
- [Konfigurieren der Lastenausgleichsmethode für Windows Virtual Desktop](configure-host-pool-load-balancing.md)
- [Konfigurieren des Zuweisungstyps für den Hostpool mit persönlichen Desktops](configure-host-pool-personal-desktop-assignment-type.md)
- Und viele mehr!

Falls Probleme auftreten, finden Sie Unterstützung in unserem [Artikel zur Problembehandlung in PowerShell](troubleshoot-powershell.md).


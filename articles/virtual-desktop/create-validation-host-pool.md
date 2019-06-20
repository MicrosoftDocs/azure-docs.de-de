---
title: Erstellen eines Hostpools für die Vorschauversion von Windows Virtual Desktop zum Überprüfen von Dienstupdates – Azure
description: Erfahren Sie, wie Sie einen Hostpool für die Überwachung von Dienstupdates erstellen, bevor Updates in der Produktion bereitgestellt werden.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 91dd716a595dd56c14b3be0bf11a7b69eeb470d6
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834120"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Tutorial: Erstellen eines Hostpools zum Überprüfen von Dienstupdates

Hostpools sind eine Sammlung identischer virtueller Computer innerhalb von Mandantenumgebungen mit Windows Virtual Desktop (Vorschauversion). Vor der Bereitstellung von Hostpools in Ihrer Produktionsumgebung empfehlen wir dringend, dass Sie einen Überprüfungshostpool erstellen. Updates werden zuerst auf Überprüfungshostpools angewendet, sodass Sie Dienstupdates überwachen können, bevor Sie sie in Ihrer Produktionsumgebung bereitstellen. Ohne einen Überprüfungshostpool können Sie keine Änderungen erkennen, die Fehler einführen, was zu Ausfallzeiten für Benutzer in der Produktionsumgebung führen könnte.

Um sicherzustellen, dass Ihre Apps mit den neuesten Updates funktionieren, sollte der Überprüfungshostpool Hostpools in Ihrer Produktionsumgebung so ähnlich wie möglich sein. Benutzer sollten so häufig eine Verbindung mit dem Überprüfungshostpool herstellen, wie sie es auch mit dem Produktionshostpool tun. Wenn Sie automatisierte Tests mit Ihrem Hostpool durchführen, sollten Sie auch beim Überprüfungshostpool automatisierte Tests durchführen.

Sie können Probleme beim Überprüfungshostpool entweder mit der [Diagnosefunktion](diagnostics-role-service.md) oder den [Artikeln zur Problembehandlung bei Windows Virtual Desktop](https://docs.microsoft.com/Azure/virtual-desktop/troubleshoot-set-up-overview) debuggen.

>[!NOTE]
> Wir empfehlen, dass Sie den Überprüfungshostpool eingerichtet lassen, um alle zukünftigen Updates zu testen.

Zur Vorbereitung müssen Sie ggf. zunächst das [Windows Virtual Desktop-PowerShell-Modul herunterladen und importieren](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview).

## <a name="create-your-host-pool"></a>Erstellen Ihres Hostpools

Sie können einen Hostpool erstellen, indem Sie die Anweisungen in diesen Artikeln befolgen:
- [Tutorial: Erstellen eines Hostpools mit Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Erstellen eines Hostpools mit einer Azure Resource Manager-Vorlage](create-host-pools-arm-template.md)
- [Erstellen eines Hostpools mit PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definieren Ihres Hostpools als Überprüfungshostpool

Führen Sie die folgenden PowerShell-Cmdlets aus, um den neuen Hostpool als Überprüfungshostpool zu definieren. Ersetzen Sie die Werte in Anführungszeichen durch die für Ihre Sitzung relevanten Werte:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Führen Sie das folgende PowerShell-Cmdlet aus, um zu bestätigen, dass die Überprüfungseigenschaft festgelegt ist. Ersetzen Sie die Werte in Anführungszeichen durch die für Ihre Sitzung relevanten Werte.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Die Ergebnisse des Cmdlets sollte der folgenden Ausgabe ähneln:

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Zeitplan für Updates

In der Vorschauphase kommen Dienstupdates ungefähr monatlich vor. Wenn schwerwiegende Probleme vorliegen, werden kritische Updates in häufigeren Abständen bereitgestellt.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun einen Überprüfungshostpool erstellt haben, können Sie Ihren Überprüfungshostpool optional mit RemoteApps auffüllen. Weitere Informationen zum Verwalten von Apps in Windows Virtual Desktop finden Sie im folgenden Tutorial:

> [!div class="nextstepaction"]
> [Manage app groups for Windows Virtual Desktop Preview](./manage-app-groups.md) (Verwalten von App-Gruppen für Windows Virtual Desktop (Vorschauversion))

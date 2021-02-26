---
title: 'Windows Virtual Desktop (klassisch): PowerShell – Azure'
description: Informationen zum Behandeln von Problemen mit PowerShell, wenn Sie eine Windows Virtual Desktop-Mandantenumgebung (klassisch) einrichten.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 08c9f7a6c5f8fd4a51c464018438bf6e7db119fb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374897"
---
# <a name="windows-virtual-desktop-classic-powershell"></a>Windows Virtual Desktop (klassisch): PowerShell

>[!IMPORTANT]
>Dieser Inhalt gilt für Windows Virtual Desktop (klassisch). Der Dienst unterstützt keine Windows Virtual Desktop-Objekte in Azure Resource Manager. Wenn Sie Windows Virtual Desktop-Objekte in Azure Resource Manager verwalten möchten, helfen Ihnen die Informationen in [diesem Artikel](../troubleshoot-powershell.md) weiter.

Verwenden Sie diesen Artikel, um Fehler und Probleme bei der Verwendung von PowerShell mit Windows Virtual Desktop zu beheben. Weitere Informationen zu PowerShell von Remote Desktop Services finden Sie unter [Windows Virtual Desktop: PowerShell](/powershell/windows-virtual-desktop/overview).

## <a name="provide-feedback"></a>Feedback geben

In der [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) können Sie sich mit dem Produktteam und aktiven Communitymitgliedern über den Windows Virtual Desktop-Dienst austauschen.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Während der Installation von Windows Virtual Desktop verwendete PowerShell-Befehle

Dieser Abschnitt listet PowerShell-Befehle auf, die typischerweise beim Einrichten von Windows Virtual Desktop verwendet werden, und bietet Lösungen für Probleme, die bei der Verwendung dieser Befehle auftreten können.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Error: Befehl Add-RdsAppGroupUser: Der angegebene UserPrincipalName ist bereits einer RemoteApp-App-Gruppe im angegebenen Hostpool zugeordnet.

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Ursache:** Der verwendete Benutzername wurde bereits einer App-Gruppe eines anderen Typs zugewiesen. Benutzer können nicht gleichzeitig einem Remotedesktop und einer Remote-App-Gruppe unter demselben Sitzungshostpool zugewiesen werden.

**Behebung:** Wenn der Benutzer sowohl Remote-Apps als auch Remotedesktops benötigt, erstellen Sie verschiedene Hostpools, oder gewähren Sie dem Benutzer Zugriff auf den Remotedesktop, was die Verwendung einer beliebigen Anwendung auf der Sitzungshost-VM ermöglicht.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Error: Befehl Add-RdsAppGroupUser: Der angegebene UserPrincipalName ist nicht im Azure Active Directory vorhanden, das dem Remote Desktop-Mandanten zugeordnet ist.

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Ursache:** Der durch -UserPrincipalName angegebene Benutzer kann nicht im Azure Active Directory gefunden werden, das an den Windows Virtual Desktop-Mandanten gebunden ist.

**Behebung:** Bestätigen Sie die einzelnen Punkte in der folgenden Liste.

- Der Benutzer ist mit Azure Active Directory synchronisiert.
- Der Benutzer ist nicht mit B2C (Business-to-Consumer) oder B2B (Business-to-Business) Commerce verknüpft.
- Der Windows Virtual Desktop-Mandant ist an das richtige Azure Active Directory gebunden.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Error: Get-RdsDiagnosticActivities: Der Benutzer ist nicht berechtigt, den Verwaltungsdienst abzufragen.

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Ursache:** Parameter -TenantName

**Behebung:** Geben Sie Get-RdsDiagnosticActivities mit -TenantName \<TenantName> aus.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Error: Get-RdsDiagnosticActivities: Der Benutzer ist nicht berechtigt, den Verwaltungsdienst abzufragen.

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Ursache:** Verwendung des Schalters -Deployment.

**Behebung:** Der Schalter -Deployment kann nur von Bereitstellungsadministratoren verwendet werden. Diese Administratoren sind in der Regel Mitglieder des Teams für Remotedesktopdienste/Windows Virtual Desktop. Ersetzen Sie den Schalter -Deployment durch -TenantName \<TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Error: New-RdsRoleAssignment: Der Benutzer ist nicht berechtigt, den Verwaltungsdienst abzufragen.

**Ursache 1:** Das verwendete Konto hat keine Remote Desktop Services-Besitzerberechtigungen für den Mandanten.

**Behebung 1:** Ein Benutzer mit Besitzerberechtigungen für die Remotedesktopdienste muss die Rollenzuweisung ausführen.

**Ursache 2:** Das verwendete Konto hat Besitzerrechte für die Remotedesktopdienste, ist aber nicht Teil des Azure Active Directory des Mandanten oder verfügt nicht über Berechtigungen zum Abfragen des Azure Active Directory, in dem sich der Benutzer befindet.

**Behebung 2:** Ein Benutzer mit Active Directory-Berechtigungen muss die Rollenzuweisung ausführen.

>[!Note]
>New-RdsRoleAssignment kann einem Benutzer keine Berechtigungen erteilen, der nicht in Azure Active Directory (AD) vorhanden ist.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Problembehandlung von Windows Virtual Desktop und die Eskalationspfade finden Sie unter [Überblick über Problembehandlung, Feedback und Support](troubleshoot-set-up-overview-2019.md).
- Informationen zur Problembehandlung beim Erstellen eines Mandanten- und Hostpools in einer Windows Virtual Desktop-Umgebung finden Sie unter [Mandanten- und Hostpoolerstellung](troubleshoot-set-up-issues-2019.md).
- Informationen zur Problembehandlung bei der Konfiguration eines virtuellen Computers (VM) in Windows Virtual Desktop finden Sie unter [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration-2019.md).
- Informationen zur Behebung von Problemen bei Windows Virtual Desktop-Clientverbindungen finden Sie unter [Windows Virtual Desktop – Clientverbindungen](troubleshoot-service-connection-2019.md).
- Informationen zur Behebung von Problemen bei Remotedesktop-Clients finden Sie unter [Problembehandlung für den Remotedesktop-Client](../troubleshoot-client.md).
- Weitere Informationen zum Dienst finden Sie unter [Windows Virtual Desktop-Umgebung](environment-setup-2019.md).
- Ein Tutorial zur Problembehandlung finden Sie unter [Tutorial: Problembehandlung von Bereitstellungen der Resource Manager-Vorlage](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Informationen zur Überwachung von Aktionen finden Sie unter [Überwachen von Vorgängen mit Resource Manager](../../azure-resource-manager/management/view-activity-logs.md).
- Weitere Informationen zu Aktionen zum Bestimmen von Fehlern während der Bereitstellung finden Sie unter [Anzeigen von Bereitstellungsvorgängen mit dem Azure-Portal](../../azure-resource-manager/templates/deployment-history.md).

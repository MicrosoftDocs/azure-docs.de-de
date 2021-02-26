---
title: 'Windows Virtual Desktop: PowerShell – Azure'
description: Informationen zum Behandeln von Problemen mit PowerShell, wenn Sie eine Windows Virtual Desktop-Umgebung einrichten.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cd02ee8ab794858566aeafa96fa78919be3b85a5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367621"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop: PowerShell

>[!IMPORTANT]
>Dieser Inhalt gilt für Windows Virtual Desktop mit Windows Virtual Desktop-Objekten für Azure Resource Manager. Wenn Sie Windows Virtual Desktop (klassisch) ohne Azure Resource Manager-Objekte verwenden, finden Sie weitere Informationen in [diesem Artikel](./virtual-desktop-fall-2019/troubleshoot-powershell-2019.md).

Verwenden Sie diesen Artikel, um Fehler und Probleme bei der Verwendung von PowerShell mit Windows Virtual Desktop zu beheben. Weitere Informationen zu Remote Desktop Services PowerShell finden Sie unter [Windows Virtual Desktop: PowerShell](/powershell/windows-virtual-desktop/overview).

## <a name="provide-feedback"></a>Feedback geben

In der [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) können Sie sich mit dem Produktteam und aktiven Communitymitgliedern über den Windows Virtual Desktop-Dienst austauschen.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Während der Installation von Windows Virtual Desktop verwendete PowerShell-Befehle

Dieser Abschnitt listet PowerShell-Befehle auf, die typischerweise beim Einrichten von Windows Virtual Desktop verwendet werden, und bietet Lösungen für Probleme, die bei der Verwendung dieser Befehle auftreten können.

### <a name="error-new-azroleassignment-the-provided-information-does-not-map-to-an-ad-object-id"></a>Error: New-AzRoleAssignment: Die angegebenen Informationen stimmen mit keiner AD-Objekt-ID überein.

```powershell
New-AzRoleAssignment -SignInName "admins@contoso.com" -RoleDefinitionName "Desktop Virtualization User" -ResourceName "0301HP-DAG" -ResourceGroupName 0301RG -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

**Ursache:** Der durch den Parameter *-SignInName* angegebene Benutzer kann nicht im Azure Active Directory gefunden werden, das an die Windows Virtual Desktop-Umgebung gebunden ist.

**Behebung:** Stellen Sie Folgendes sicher.

- Der Benutzer muss mit Azure Active Directory synchronisiert werden.
- Der Benutzer ist nicht mit B2C (Business-to-Consumer) oder B2B (Business-to-Business) Commerce verknüpft.
- Die Windows Virtual Desktop-Umgebung ist an das richtige Azure Active Directory gebunden.

### <a name="error-new-azroleassignment-the-client-with-object-id-does-not-have-authorization-to-perform-action-over-scope-code-authorizationfailed"></a>Error: New-AzRoleAssignment: „Der Client mit der Objekt-ID hat keine Berechtigung zum Ausführen der Aktion über Bereich (Code: AuthorizationFailed)“

**Ursache 1:** Das verwendete Konto hat keine Besitzerberechtigungen für das Abonnement.

**Behebung 1:** Ein Benutzer mit Besitzerberechtigungen muss die Rollenzuweisung ausführen. Alternativ muss der Benutzer der Rolle „Benutzerzugriffsadministrator“ zugewiesen werden, um einen Benutzer einer Anwendungsgruppe zuzuweisen.

**Ursache 2:** Das verwendete Konto hat Besitzerrechte, ist aber nicht Teil des Azure Active Directory der Umgebung oder verfügt nicht über Berechtigungen zum Abfragen des Azure Active Directory, in dem sich der Benutzer befindet.

**Behebung 2:** Ein Benutzer mit Active Directory-Berechtigungen muss die Rollenzuweisung ausführen.

### <a name="error-new-azwvdhostpool----the-location-is-not-available-for-resource-type"></a>Error: New-AzWvdHostPool: Der Speicherort ist für den Ressourcentyp nicht verfügbar.

```powershell
New-AzWvdHostPool_CreateExpanded: The provided location 'southeastasia' is not available for resource type 'Microsoft.DesktopVirtualization/hostpools'. List of available regions for the resource type is 'eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,centralus'.
```

Ursache: Windows Virtual Desktop unterstützt die Auswahl des Speicherorts von Hostpools, Anwendungsgruppen und Arbeitsbereichen, um Dienstmetadaten an bestimmten Orten zu speichern. Ihre Optionen sind darauf beschränkt, wo diese Funktion verfügbar ist. Dieser Fehler bedeutet, dass das Feature nicht an dem von Ihnen ausgewählten Speicherort verfügbar ist.

Behebung: In der Fehlermeldung wird eine Liste der unterstützten Regionen veröffentlicht. Verwenden Sie stattdessen eine der unterstützten Regionen.

### <a name="error-new-azwvdapplicationgroup-must-be-in-same-location-as-host-pool"></a>Error: New-AzWvdApplicationGroup muss sich am gleichen Speicherort wie der Hostpool befinden

```powershell
New-AzWvdApplicationGroup_CreateExpanded: ActivityId: e5fe6c1d-5f2c-4db9-817d-e423b8b7d168 Error: ApplicationGroup must be in same location as associated HostPool
```

**Ursache:** Der Speicherort stimmt nicht überein. Alle Hostpools, Anwendungsgruppen und Arbeitsbereiche verfügen über einen Speicherort zum Speichern von Dienstmetadaten. Alle Objekte, die Sie erstellen, die einander zugeordnet sind, müssen sich am gleichen Speicherort befinden. Wenn sich ein Hostpool beispielsweise in `eastus` befindet, müssen Sie auch die Anwendungsgruppen in `eastus`erstellen. Wenn Sie einen Arbeitsbereich erstellen, in dem diese Anwendungsgruppen registriert werden, muss sich dieser Arbeitsbereich ebenfalls in `eastus` befinden.

**Behebung:** Rufen Sie den Speicherort ab, in dem der Hostpool erstellt wurde, und weisen Sie dann die zu erstellende Anwendungsgruppe dem gleichen Speicherort zu.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Problembehandlung von Windows Virtual Desktop und die Eskalationspfade finden Sie unter [Überblick über Problembehandlung, Feedback und Support](troubleshoot-set-up-overview.md).
- Informationen zur Problembehandlung beim Einrichten einer Windows Virtual Desktop-Umgebung und von Hostpools finden Sie unter [Umgebungs- und Hostpoolerstellung](troubleshoot-set-up-issues.md).
- Informationen zur Problembehandlung bei der Konfiguration eines virtuellen Computers (VM) in Windows Virtual Desktop finden Sie unter [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration.md).
- Informationen zur Behebung von Problemen bei Windows Virtual Desktop-Clientverbindungen finden Sie unter [Windows Virtual Desktop – Clientverbindungen](troubleshoot-service-connection.md).
- Informationen zur Behebung von Problemen bei Remotedesktop-Clients finden Sie unter [Problembehandlung für den Remotedesktop-Client](troubleshoot-client.md).
- Weitere Informationen zum Dienst finden Sie unter [Windows Virtual Desktop-Umgebung](environment-setup.md).
- Informationen zur Überwachung von Aktionen finden Sie unter [Überwachen von Vorgängen mit Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Weitere Informationen zu Aktionen zum Bestimmen von Fehlern während der Bereitstellung finden Sie unter [Anzeigen von Bereitstellungsvorgängen mit dem Azure-Portal](../azure-resource-manager/templates/deployment-history.md).

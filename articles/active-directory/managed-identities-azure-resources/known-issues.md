---
title: Häufig gestellte Fragen und bekannte Probleme mit verwalteten Identitäten für Azure-Ressourcen
description: Bekannte Probleme mit verwalteten Identitäten für Azure-Ressourcen
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a6797c7bd0c6bd8ce8d3f51b42cb4c2b1338fd6
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65950482"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Häufig gestellte Fragen und bekannte Probleme mit verwalteten Identitäten für Azure-Ressourcen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Häufig gestellte Fragen (FAQs)

> [!NOTE]
> „Verwaltete Identitäten für Azure-Ressourcen“ ist der neue Name für den Dienst, der früher als „Verwaltete Dienstidentität“ (Managed Service Identity, MSI) bezeichnet wurde.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Können verwaltete Identitäten für Azure-Ressourcen mit Azure Cloud Services verwendet werden?

Nein. Es gibt bisher keine Pläne zur Unterstützung von verwalteten Identitäten für Azure-Ressourcen in Azure Cloud Services.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Funktionieren verwaltete Identitäten für Azure-Ressourcen mit ADAL (Active Directory Authentication Library) oder MSAL (Microsoft Authentication Library)?

Nein. Verwaltete Identitäten für Azure-Ressourcen sind noch nicht in ADAL oder MSAL integriert. Ausführliche Informationen zum Anfordern eines Tokens für verwaltete Identitäten für Azure-Ressourcen mithilfe des REST-Endpunkts finden Sie unter [Verwenden verwalteter Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer zum Abrufen eines Zugriffstokens](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Welche Sicherheitsgrenze gilt für verwaltete Identitäten für Azure-Ressourcen?

Bei der Sicherheitsgrenze der Identität handelt es sich um die Ressource, an die sie angefügt ist. Beispielsweise ist die Sicherheitsgrenze für einen virtuellen Computer, für den verwaltete Identitäten für Azure-Ressourcen aktiviert sind, dieser virtuelle Computer. Jeglicher Code, der auf diesem virtuellen Computer ausgeführt wird, kann den Endpunkt für die verwalteten Identitäten für Azure-Ressourcen aufrufen und Token anfordern. Ähnlich verhält es sich mit anderen Ressourcen, die verwaltete Identitäten für Azure-Ressourcen unterstützen.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Wie lautet die Identität, die IMDS standardmäßig verwendet, wenn die Identität nicht in der Anforderung angegeben ist?

- Wenn die vom System zugewiesene verwaltete Identität aktiviert und keine Identität in der Anforderung angegeben ist, verwendet IMDS standardmäßig die vom System zugewiesene verwaltete Identität.
- Wenn die vom System zugewiesene verwaltete Identität nicht aktiviert und nur eine vom Benutzer zugewiesene verwaltete Identität vorhanden ist, verwendet IMDS standardmäßig diese einzige vom Benutzer zugewiesene verwaltete Identität. 
- Wenn die vom System zugewiesene verwaltete Identität nicht aktiviert ist und mehrere vom Benutzer zugewiesene verwaltete Identitäten vorhanden sind, muss in der Anforderung eine verwaltete Identität angegeben werden.

### <a name="should-i-use-the-managed-identities-for-azure-resources-imds-endpoint-or-the-vm-extension-endpoint"></a>Sollte ich den IMDS-Endpunkt der verwalteten Identitäten für Azure-Ressourcen oder den Endpunkt der VM-Erweiterung verwenden?

Bei Verwendung verwalteter Identitäten für Azure-Ressourcen mit virtuellen Computern wird empfohlen, den IMDS-Endpunkt zu verwenden. Der Azure Instance Metadata Service ist ein REST-Endpunkt, der für alle virtuellen IaaS-Computer verfügbar ist, die mit Azure Resource Manager erstellt wurden. 

Zu den Vorteilen der Verwendung von verwalteten Identitäten für Azure-Ressourcen über IMDS gehören:
- Alle für Azure IaaS unterstützten Betriebssysteme können verwaltete Identitäten für Azure-Ressourcen über IMDS verwenden.
- Es ist nicht mehr erforderlich, eine Erweiterung auf Ihrem virtuellen Computer zu installieren, um verwaltete Identitäten für Azure-Ressourcen zu aktivieren. 
- Die von verwalteten Identitäten für Azure-Ressourcen verwendeten Zertifikate sind nicht mehr auf dem virtuellen Computer vorhanden.
- Der Endpunkt ist eine bekannte, nicht routingfähige IP-Adresse, auf die nur innerhalb des virtuellen Computers zugegriffen werden kann.
- Einem einzelnen virtuellen Computer können 1.000 benutzerseitig zugewiesene verwaltete Identitäten zugewiesen werden. 

Die verwalteten Identitäten für die VM-Erweiterung von Azure-Ressourcen sind weiterhin verfügbar, wir entwickeln dafür jedoch keine neuen Funktionen mehr. Es wird empfohlen, auf die Verwendung des IMDS-Endpunkts umzusteigen. 

Einige der Einschränkungen bei der Verwendung des Endpunkts für die VM-Erweiterung:
- Eingeschränkte Unterstützung für Linux-Distributionen: CoreOS Stable, CentOS 7.1, Red Hat 7.2, Ubuntu 15.04, Ubuntu 16.04
- Dem virtuellen Computer können nur 32 verwaltete Identitäten benutzerseitig zugewiesen werden.


Hinweis: Der Support für verwaltete Identitäten für die VM-Erweiterung von Azure-Ressourcen endet im Januar 2019. 

Weitere Informationen zum Azure Instance Metadata Service finden Sie in der [IMDS-Dokumentation](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Werden verwaltete Identitäten automatisch neu erstellt, wenn ich ein Abonnement in ein anderes Verzeichnis verschiebe?

Nein. Wenn Sie ein Abonnement in ein anderes Verzeichnis verschieben, müssen Sie die Identitäten manuell neu erstellen und erneut Rollenzuweisungen in der rollenbasierten Zugriffssteuerung von Azure erteilen.
- Für vom System zugewiesene verwaltete Identitäten: Deaktivieren Sie die Identitäten, und aktivieren Sie sie erneut. 
- Für vom Benutzer zugewiesene verwaltete Identitäten: Löschen Sie die Identitäten, erstellen Sie sie neu, und fügen Sie sie erneut an die erforderlichen Ressourcen an (z. B. VMs).

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Kann ich eine verwaltete Identität verwenden, um auf eine Ressource in einem anderen Verzeichnis/Mandanten zuzugreifen?

Nein. Verwaltete Identitäten unterstützen derzeit keine verzeichnisübergreifenden Szenarien. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Welche Azure RBAC-Berechtigungen sind für eine verwaltete Identität auf einer Ressource erforderlich? 

- Systemseitig zugewiesene verwaltete Identität: Sie benötigen Schreibberechtigungen für die Ressource. Beispiel:  Microsoft.Compute/virtualMachines/write – Oder – Diese Aktion ist in ressourcenspezifischen integrierten Rollen wie [Mitwirkender von virtuellen Computern](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) enthalten.
- Benutzerseitig zugewiesene verwaltete Identität: Sie benötigen Schreibberechtigungen für die Ressource. Beispiel:  Microsoft.Compute/virtualMachines/write. Zusätzlich zu der Rollenzuweisung [Operator für verwaltete Identität](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) für die verwaltete Identität. 

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Wie wird die Erweiterung für verwaltete Identitäten für Azure-Ressourcen neu gestartet?
Unter Windows und bestimmten Versionen von Linux kann das folgende Cmdlet verwendet werden, um die Erweiterung manuell neu zu starten, wenn sie beendet wurde:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Hinweis: 
- Erweiterungsname und -typ für Windows: ManagedIdentityExtensionForWindows
- Erweiterungsname und -typ für Linux: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Bekannte Probleme

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Beim Feature „Automatisierungsskript“ tritt ein Fehler auf, wenn versucht wird, einen Schemaexport für die Erweiterung für verwaltete Identitäten für Azure-Ressourcen durchzuführen.

Wenn verwaltete Identitäten für Azure-Ressourcen auf einem virtuellen Computer aktiviert sind, wird der folgende Fehler bei dem Versuch angezeigt, das Feature „Automatisierungsskript“ für den virtuellen Computer oder seine Ressourcengruppe zu verwenden:

![Exportfehler beim Automatisierungsskript für verwaltete Identitäten für Azure-Ressourcen](./media/msi-known-issues/automation-script-export-error.png)

Das Schema der VM-Erweiterung für verwaltete Identitäten für Azure-Ressourcen (die Einstellung ist für Januar 2019 vorgesehen) kann derzeit nicht in eine Ressourcengruppenvorlage exportiert werden. Die generierte Vorlage weist daher keine Konfigurationsparameter auf, mit denen die verwalteten Identitäten Azure-Ressourcen in der Ressource aktiviert werden können. Diese Abschnitte können anhand der Beispiele in [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe einer Vorlage](qs-configure-template-windows-vm.md) manuell hinzugefügt werden.

Wenn die Schemaexportfunktion für die VM-Erweiterung für verwaltete Identitäten für Azure-Ressourcen (die Einstellung ist für Januar 2019 vorgesehen) verfügbar ist, wird sie in [Exportieren von Ressourcengruppen, die VM-Erweiterungen enthalten](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions) aufgelistet.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Der virtuelle Computer kann nicht gestartet werden, nachdem er aus der Ressourcengruppe oder dem Abonnement verschoben wurde

Wenn Sie einen virtuellen Computer verschieben, der sich im Ausführungsstatus befindet, wird er während des Verschiebevorgangs weiterhin ausgeführt. Wenn der virtuelle Computer nach dem Verschieben allerdings beendet und neu gestartet wird, schlägt der Start fehl. Die Ursache dieses Problems besteht darin, dass der virtuelle Computer den Verweis auf die verwalteten Identitäten für Azure-Ressourcen nicht aktualisiert und weiterhin auf die alte Ressourcengruppe verweist.

**Problemumgehung** 
 
Lösen Sie ein Update auf dem virtuellen Computer aus, damit die richtigen Werte für die verwalteten Identitäten für Azure-Ressourcen abgerufen werden können. Sie können eine VM-Eigenschaftsänderung vornehmen, um den Verweis auf die verwalteten Identitäten für Azure-Ressourcen zu aktualisieren. Beispielsweise können Sie mit dem folgenden Befehl einen neuen Tagwert auf dem virtuellen Computer festlegen:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Mit diesem Befehl wird das neue Tag „fixVM“ mit dem Wert 1 auf dem virtuellen Computer festgelegt. 
 
Durch das Festlegen dieser Eigenschaft wird der virtuelle Computer mit dem richtigen Ressourcen-URI der verwalteten Identitäten für Azure-Ressourcen aktualisiert. Anschließend sollte es möglich sein, den virtuellen Computer zu starten. 
 
Nachdem der virtuelle Computer gestartet wurde, kann das Tag mithilfe des folgenden Befehls entfernt werden:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>Fehlschlagen der Bereitstellung der VM-Erweiterung

Die Bereitstellung der VM-Erweiterung kann aufgrund von Fehlern beim DNS-Lookup fehlschlagen. Starten Sie den virtuellen Computer neu, und wiederholen Sie den Vorgang.
 
> [!NOTE]
> Die VM-Erweiterung wird voraussichtlich im Januar 2019 eingestellt. Es wird empfohlen, dass Sie stattdessen den IMDS-Endpunkt verwenden.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Übertragen eines Abonnements zwischen Azure AD-Verzeichnissen

Verwaltete Identitäten werden nicht aktualisiert, wenn ein Abonnement in ein anderes Verzeichnis verschoben/übertragen wird. Infolgedessen tritt für alle vorhandenen system- oder benutzerseitig zugewiesenen verwalteten Identitäten ein Fehler auf. 

Problemumgehung für verwaltete Identitäten in einem Abonnement, die in ein anderes Verzeichnis verschoben wurden:

 - Für vom System zugewiesene verwaltete Identitäten: Deaktivieren Sie die Identitäten, und aktivieren Sie sie erneut. 
 - Für vom Benutzer zugewiesene verwaltete Identitäten: Löschen Sie die Identitäten, erstellen Sie sie neu, und fügen Sie sie erneut an die erforderlichen Ressourcen an (z. B. VMs).

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Verschieben einer benutzerseitig zugewiesenen verwalteten Identität in eine andere Ressourcengruppe/ein anderes Abonnement

Beim Verschieben einer benutzerseitig zugewiesenen verwalteten Identität in eine andere Ressourcengruppe tritt ein Fehler für die Identität auf. Infolgedessen können Ressourcen (z. B. VMs), die diese Identität verwenden, keine Token für sie anfordern. 

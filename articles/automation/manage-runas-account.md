---
title: Verwalten eines ausführenden Azure Automation-Kontos
description: In diesem Artikel wird beschrieben, wie Sie mit PowerShell oder über das Azure-Portal Ihr ausführendes Konto verwalten.
services: automation
ms.subservice: ''
ms.date: 01/19/2021
ms.topic: conceptual
ms.openlocfilehash: f9e99318c526bb935d0e035fdbf59874249390da
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050938"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Verwalten eines ausführenden Azure Automation-Kontos

Ausführende Konten in Azure Automation ermöglichen die Authentifizierung für die Verwaltung von Ressourcen mit dem Azure Resource Manager- oder dem klassischen Azure-Bereitstellungsmodell mithilfe von Automation-Runbooks und anderen Automation-Features. Dieser Artikel enthält eine Anleitung zur Verwaltung eines ausführenden oder klassischen ausführenden Kontos.

Weitere Informationen zur Azure Automation-Kontoauthentifizierung sowie zu Szenarien der Prozessautomatisierung finden Sie unter [Übersicht über die Automation-Kontoauthentifizierung](automation-security-overview.md).

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Verlängern eines selbstsignierten Zertifikats

Das selbstsignierte Zertifikat, das Sie für das ausführende Konto erstellt haben, läuft ein Jahr nach dem Erstellungsdatum ab. Das Zertifikat muss vor Ablauf Ihres ausführenden Kontos erneuert werden. Sie können es vor dem Ablaufdatum jederzeit erneuern.

Beim Erneuern des selbstsignierten Zertifikats wird das aktuell gültige Zertifikat beibehalten, um sicherzustellen, dass sich keine negativen Auswirkungen auf Runbooks ergeben, die sich in der Warteschlange befinden oder aktiv ausgeführt werden und die mit dem ausführenden Konto authentifiziert werden. Das Zertifikat bleibt bis zum Ablaufdatum gültig.

>[!NOTE]
>Wenn Sie der Meinung sind, dass das ausführende Konto kompromittiert wurde, können Sie das selbstsignierte Zertifikat löschen und neu erstellen.

>[!NOTE]
>Wenn Sie Ihr ausführendes Konto für die Verwendung eines Zertifikats konfiguriert haben, das von Ihrer Unternehmenszertifizierungsstelle ausgestellt wurde, und Sie verwenden die Option zum Erneuern eines selbstsignierten Zertifikats, wird das Unternehmenszertifikat durch ein selbstsigniertes Zertifikat ersetzt.

Verwenden Sie die folgenden Schritte, um das selbstsignierte Zertifikat zu erstellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.

1. Wechseln Sie zu Ihrem Automation-Konto, und wählen Sie im Abschnitt mit den Kontoeinstellungen **Ausführende Konten** aus.

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Eigenschaftenbereich des Automation-Kontos.":::

1. Wählen Sie auf der Eigenschaftsseite **Ausführendes Konto** entweder **Ausführendes Konto** oder **Klassisches ausführendes Konto**, je nachdem, für welches Konto Sie das Zertifikat erneuern müssen.

1. Klicken Sie im Bereich **Eigenschaften** für das ausgewählte Konto auf **Zertifikat erneuern**.

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="Erneuern des Zertifikats für das ausführende Konto.":::

1. Während das Zertifikat erneuert wird, können Sie den Status im Menü unter **Benachrichtigungen** verfolgen.

## <a name="grant-run-as-account-permissions-in-other-subscriptions"></a>Erteilen von Berechtigungen für das ausführende Konto in anderen Abonnements

Azure Automation unterstützt die Verwendung eines einzelnen Automation-Kontos aus einem Abonnement und die Ausführung von Runbooks für Azure Resource Manager-Ressourcen aus mehreren Abonnements. Diese Konfiguration unterstützt nicht das klassische Azure-Bereitstellungsmodell.

Sie weisen dem Dienstprinzipal des ausführenden Kontos die Rolle [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor) im anderen Abonnement oder restriktivere Berechtigungen zu. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure Automation](automation-role-based-access-control.md). Um das ausführende Konto der Rolle im anderen Abonnement zuzuweisen, muss das Benutzerkonto, das diese Aufgabe ausführt, Mitglied der Rolle **Besitzer** in diesem Abonnement sein.

> [!NOTE]
> Diese Konfiguration unterstützt nur mehrere Abonnements einer Organisation unter Verwendung eines gemeinsamen Azure AD-Mandanten.

Ehe Sie die Berechtigungen für das ausführende Konto erteilen, müssen Sie sich zunächst den Anzeigenamen des zuzuweisenden Dienstprinzipals notieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie in Ihrem Automation-Konto unter **Kontoeinstellungen** die Option **Ausführende Konten** aus.
1. Wählen Sie **Ausführendes Azure-Konto** aus.
1. Kopieren oder notieren Sie auf der Seite **Ausführende Azure-Konto** den Wert für **Anzeigename**.

Detaillierte Schritte zum Hinzufügen von Rollenzuweisungen finden Sie in den folgenden Artikeln entsprechend der von Ihnen gewünschten Methode.

* [Hinzufügen der Azure-Rollenzuweisung im Azure-Portal](../role-based-access-control/role-assignments-portal.md)
* [Hinzufügen der Azure-Rollenzuweisung mit Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
* [Hinzufügen der Azure-Rollenzuweisung mit der Azure CLI](../role-based-access-control/role-assignments-cli.md)
* [Hinzufügen der Azure-Rollenzuweisung mit der REST-API](..//role-based-access-control/role-assignments-rest.md)

Nachdem Sie der Rolle das ausführende Konto zugewiesen haben, geben Sie in Ihrem Runbook `Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` an, um den für das Abonnement zu verwendenden Kontext festzulegen. Weitere Informationen finden Sie unter [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

## <a name="limit-run-as-account-permissions"></a>Einschränken der Berechtigungen des ausführenden Kontos

Zum Steuern der Automatisierungsziele für Ressourcen in Azure können Sie das Skript [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8)ausführen. Dieses Skript ändert Ihren vorhandenen Dienstprinzipal für das ausführende Konto, um eine benutzerdefinierte Rollendefinition zu erstellen und zu verwenden. Die Rolle verfügt über Berechtigungen für alle Ressourcen außer [Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>Nachdem Sie das Skript **Update-AutomationRunAsAccountRoleAssignments.ps1** ausgeführt haben, funktionieren Runbooks, die unter Verwendung von ausführenden Konten auf Key Vault zugreifen, nicht mehr. Überprüfen Sie vor dem Ausführen des Skripts Runbooks in Ihrem Konto auf Aufrufe von Azure Key Vault. Um den Zugriff auf Key Vault aus Azure Automation-Runbooks zu aktivieren, müssen Sie [das ausführende Konto den Berechtigungen von Key Vault hinzufügen](#add-permissions-to-key-vault).

Wenn Sie noch weiter einschränken müssen, was der Dienstprinzipal des ausführenden Kontos darf, können Sie dem Element `NotActions` der benutzerdefinierten Rollendefinition weitere Ressourcentypen hinzufügen. Im folgenden Beispiel wird der Zugriff auf `Microsoft.Compute/*` eingeschränkt. Wenn Sie diesen Ressourcentyp zu `NotActions` für die Rollendefinition hinzufügen, kann die Rolle auf keine Compute-Ressource zugreifen. Weitere Informationen zu Rollendefinitionen finden Sie unter [Grundlegendes zu Rollendefinitionen für Azure-Ressourcen](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Sie können bestimmen, ob dem von Ihrem ausführenden Konto verwendeten Dienstprinzipal die Rolle **Mitwirkender** oder eine benutzerdefinierte Rolle zugewiesen wurde.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wechseln Sie zu Ihrem Automation-Konto, und wählen Sie im Abschnitt mit den Kontoeinstellungen **Ausführende Konten** aus.
1. Wählen Sie **Ausführendes Azure-Konto** aus.
1. Wählen Sie **Rolle** aus, um die Rollendefinition zu suchen, die verwendet wird.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Überprüfen der Rolle „Ausführendes Konto“" lightbox="media/manage-runas-account/verify-role-expanded.png":::

Sie können auch die Rollendefinition bestimmen, die von den ausführenden Konten für mehrere Abonnements oder Automation-Konten verwendet wird. Verwenden Sie hierzu das Skript [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) im PowerShell-Katalog.

### <a name="add-permissions-to-key-vault"></a>Hinzufügen von Berechtigungen zu Key Vault

Sie können Azure Automation gestatten, zu überprüfen, ob Key Vault und Ihr Dienstprinzipal des ausführenden Kontos eine benutzerdefinierte Rollendefinition verwenden. Die Voraussetzungen lauten wie folgt:

* Gewähren von Berechtigungen für Key Vault.
* Festlegen der Zugriffsrichtlinie.

Sie können das Skript [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) im PowerShell-Katalog verwenden, um Ihrem ausführenden Konto Berechtigungen für Key Vault zu erteilen. Weitere Einzelheiten zum Festlegen von Berechtigungen für Key Vault finden Sie unter [Zuweisen einer Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-powershell.md).

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Beheben von Fehlkonfigurationsproblemen bei ausführenden Konten

Einige Konfigurationselemente, die für ein ausführendes oder ein klassisches ausführendes Konto erforderlich sind, können eventuell gelöscht oder während der anfänglichen Einrichtung nicht ordnungsgemäß erstellt worden sein. Mögliche Instanzen für Fehlkonfigurationen können sein:

* Zertifikatasset
* Verbindungsasset
* Ausführendes Kontos wurde aus der Rolle „Mitwirkender“ entfernt.
* Dienstprinzipal oder Anwendung in Azure AD

Bei solchen Fehlkonfigurationsinstanzen erkennt das Automation-Konto die Änderungen und zeigt im Eigenschaftenbereich des ausführenden Kontos den Status *Unvollständig* für das Konto an.

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="Unvollständige Konfiguration des ausführenden Kontos.":::

Wenn Sie das ausführende Konto auswählen, wird im Bereich „Eigenschaften“ die folgende Fehlermeldung angezeigt:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Sie können diese Probleme mit ausführenden Konten schnell beheben, indem Sie das ausführende Konto [löschen](delete-run-as-account.md) und [neu erstellen](create-run-as-account.md).

## <a name="next-steps"></a>Nächste Schritte

* [Anwendungsobjekte und Dienstprinzipalobjekte](../active-directory/develop/app-objects-and-service-principals.md)
* [Übersicht über Zertifikate für Azure Cloud Services](../cloud-services/cloud-services-certs-create.md)
* Informationen zum Erstellen oder erneuten Erstellen eines ausführenden Kontos finden Sie unter [Erstellen eines ausführenden Kontos](create-run-as-account.md).
* Wenn Sie ein ausführendes Konto nicht mehr benötigen, finden Sie unter [Löschen eines ausführenden Kontos](delete-run-as-account.md) weitere Informationen.
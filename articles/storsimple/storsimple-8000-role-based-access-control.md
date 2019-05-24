---
title: Verwenden der rollenbasierten Zugriffssteuerung für StorSimple | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die rollenbasierte Zugriffssteuerung (RBAC) von Azure im Kontext von StorSimple verwenden.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: a79753a897a62e194a759c23a9c0acc45c5f36c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66159105"
---
# <a name="role-based-access-control-for-storsimple"></a>Rollenbasierte Zugriffssteuerung für StorSimple

Dieser Artikel enthält eine kurze Beschreibung von Verwendungsmöglichkeiten für die rollenbasierte Zugriffssteuerung (RBAC) von Azure für Ihr StorSimple-Gerät. RBAC ermöglicht eine differenzierte Zugriffsverwaltung für Azure. Mit RBAC können Sie den Zugriff für StorSimple-Benutzer ausschließlich auf die für ihre Arbeit erforderlichen Bereiche beschränken, anstatt jedem Benutzer uneingeschränkten Zugriff zu gewähren. Weitere Informationen zu Grundlagen der Zugriffssteuerung in Azure finden Sie unter [Erste Schritte mit der rollenbasierten Zugriffssteuerung im Azure-Portal](../role-based-access-control/overview.md).

Dieser Artikel gilt für Geräte der StorSimple 8000-Serie, die mit Update 3.0 oder höher im Azure-Portal ausgeführt werden.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-storsimple"></a>RBAC-Rollen für StorSimple

RBAC kann basierend auf Rollen zugewiesen werden. Die Rollen gewähren bestimmte Berechtigungsstufen basierend auf den verfügbaren Ressourcen in der Umgebung. Es gibt zwei Arten von Rollen, aus denen StorSimple-Benutzer auswählen können: integrierte oder benutzerdefinierte Rollen.

* **Integrierte Rollen:** Die integrierten Rollen können Besitzer, Mitwirkende, Leser oder Benutzerzugriffsadministratoren sein. Weitere Informationen finden Sie unter [Integrierte Rollen für die rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/built-in-roles.md).

* **Benutzerdefinierte Rollen:** Wenn die integrierten Rollen nicht Ihren Anforderungen entsprechen, können Sie benutzerdefinierte RBAC-Rollen für StorSimple erstellen. Um eine benutzerdefinierte RBAC-Rolle zu erstellen, erstellen Sie zunächst eine integrierte Rolle, bearbeiten diese und importieren sie wieder in die Umgebung. Sie können die Rolle entweder über Azure PowerShell oder die Azure-Befehlszeilenschnittstelle herunter- und hochladen. Weitere Informationen finden Sie unter [Erstellen integrierter Rollen für die rollenbasierte Zugriffssteuerung](../role-based-access-control/custom-roles.md).

Um die verschiedenen Rollen, die für Benutzer von StorSimple-Geräten verfügbar sind, im Azure-Portal anzuzeigen, wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst, und navigieren Sie dann zu **Zugriffssteuerung (IAM) > Rollen**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Erstellen einer benutzerdefinierten Rolle für StorSimple-Infrastrukturadministratoren

Im folgenden Beispiel beginnen wir mit der integrierten Rolle **Reader**, mit der Benutzer alle Ressourcenbereiche anzeigen, aber diese weder bearbeiten noch neue erstellen dürfen. Wir erweitern diese Rolle, um eine neue benutzerdefinierte Rolle für StorSimple-Infrastrukturadministratoren zu erstellen. Diese Rolle wird Benutzern zugewiesen, die die Infrastruktur für die StorSimple-Geräte verwalten.

1. Führen Sie Windows PowerShell als Administrator aus.

2. Melden Sie sich bei Azure an.

    `Connect-AzAccount`

3. Exportieren Sie die Rolle „Reader“ als eine JSON-Vorlage auf Ihren Computer.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Öffnen Sie die JSON-Datei in Visual Studio. Wie Sie sehen, besteht eine typische RBAC-Rolle aus drei Hauptabschnitten: **Actions**, **NotActions** und **AssignableScopes**.

    Im Abschnitt **Actions** sind alle zulässigen Vorgänge für diese Rolle aufgelistet. Jede Aktion wird von einem Ressourcenanbieter zugewiesen. Verwenden Sie für einen StorSimple-Infrastrukturadministrator den Ressourcenanbieter `Microsoft.StorSimple`.

    Mit PowerShell können Sie alle Ressourcenanbieter anzeigen, die verfügbar und in Ihrem Abonnement registriert sind.

    `Get-AzResourceProvider`

    Sie können auch alle verfügbaren PowerShell-Cmdlets suchen, um die Ressourcenanbieter zu verwalten.

    Im Abschnitt **NotActions** sind alle eingeschränkten Aktionen für eine bestimmte RBAC-Rolle aufgeführt. In diesem Beispiel sind keine Aktionen beschränkt.
    
    Unter **AssignableScopes** sind die Abonnement-IDs aufgeführt. Stellen Sie sicher, dass die RBAC-Rolle die expliziten ID der Abonnements enthält, in denen sie verwendet wird. Wenn nicht die richtige Abonnement-ID angegeben wird, können Sie die Rolle nicht in Ihr Abonnement importieren.

    Bearbeiten Sie die Datei unter Beachtung der oben genannten Informationen.

    ```json
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. Importieren Sie die benutzerdefinierte RBAC-Rolle wieder in die Umgebung.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Diese Rolle sollte jetzt in der Liste der Rollen auf dem Blatt **Zugriffssteuerung** angezeigt werden.

![Anzeigen von RBAC-Rollen](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Weitere Informationen finden Sie unter [Erstellen von benutzerdefinierten Rollen für die rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/custom-roles.md).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Beispielausgabe für die Erstellung einer benutzerdefinierten Rolle mithilfe von PowerShell

```powershell
Connect-AzAccount
```

```Output
Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :
```

```powershell
Get-AzRoleDefinition -Name "Reader"
```

```Output
Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}
```

```powershell
Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"
```

```Output
Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}
```

## <a name="add-users-to-the-custom-role"></a>Hinzufügen von Benutzern zur benutzerdefinierten Rolle

Sie gewähren Zugriff aus der Ressource, der Ressourcengruppe oder dem Abonnement, die bzw. das als Bereich der Rollenzuweisung gilt. Bedenken Sie beim Gewähren von Zugriff, dass der Zugriff, der einem übergeordneten Knoten gewährt wird, von untergeordneten Knoten geerbt wird. Weitere Informationen finden Sie unter [Erste Schritte mit der rollenbasierten Zugriffssteuerung im Azure-Portal](../role-based-access-control/overview.md).

1. Wechseln Sie zu **Zugriffssteuerung (IAM)**. Klicken Sie auf dem Blatt für die Zugriffssteuerung auf **+ Hinzufügen**.

    ![Hinzufügen von Zugriff zur RBAC-Rolle](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Wählen Sie die Rolle aus, die Sie zuweisen möchten. In diesem Fall ist es **StorSimple Infrastructure Administrator**.

3. Wählen Sie den Benutzer, die Gruppe oder die Anwendung als das Element in Ihrem Verzeichnis aus, für das Sie Zugriff gewähren möchten. Sie können das Verzeichnis mit Anzeigenamen, E-Mail-Adressen und Objektbezeichnern durchsuchen.

4. Wählen Sie **Speichern** aus, um die Zuweisung zu erstellen.

    ![Hinzufügen von Berechtigungen zur RBAC-Rolle](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

In der Benachrichtigung **Benutzer hinzufügen** wird der Status verfolgt. Nachdem der Benutzer erfolgreich hinzugefügt wurde, wird die Liste der Benutzer in der Zugriffssteuerung aktualisiert.

## <a name="view-permissions-for-the-custom-role"></a>Anzeigen von Berechtigungen für die benutzerdefinierte Rolle

Nachdem diese Rolle erstellt wurde, können Sie die Berechtigungen dieser Rolle im Azure-Portal anzeigen.

1. Um die Berechtigungen, die dieser Rolle zugeordnet sind, anzuzeigen, wechseln Sie zu **Zugriffssteuerung (IAM) > Rollen > StorSimple Infrastructure Administrator**. Die Liste der Benutzer in dieser Rolle wird angezeigt.

2. Wählen Sie einen StorSimple-Infrastrukturadministrator aus, und klicken Sie auf **Berechtigungen**.

    ![Berechtigungen für die Rolle „StorSimple Infrastructure Administrator“ anzeigen](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Die Berechtigungen, die dieser Rolle zugeordnet sind, werden angezeigt.

    ![Benutzer in der Rolle „StorSimple Infrastructure Administrator“ anzeigen](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das [Zuweisen von benutzerdefinierten Rollen für interne und externe Benutzer](../role-based-access-control/role-assignments-external-users.md).

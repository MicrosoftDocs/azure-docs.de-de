---
title: Gewähren der Berechtigung zum Zugreifen auf einen Azure-Schlüsseltresor für Anwendungen mit Azure RBAC | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Zugriff auf Schlüssel, Geheimnisse und Zertifikate mithilfe der rollenbasierten Zugriffssteuerung in Azure bereitstellen.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: be2fa0fa85da7c0f02567580c50968b7418e4cde
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445182"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control-preview"></a>Gewähren des Zugriffs auf Key Vault-Schlüssel, -Zertifikate und -Geheimnisse mit der rollenbasierten Zugriffssteuerung in Azure (Vorschauversion)

> [!NOTE]
> Der Key Vault-Ressourcenanbieter unterstützt zwei Ressourcentypen: **Tresore** und **verwaltete HSMs**. Die in diesem Artikel beschriebene Zugriffssteuerung gilt nur für **Tresore**. Weitere Informationen zur Zugriffssteuerung für verwaltetes HSM finden Sie unter [Zugriffssteuerung für verwaltetes HSM](../managed-hsm/access-control.md).

Die rollenbasierte Zugriffssteuerung (Azure Role-Based Access Control, Azure RBAC) ist ein Autorisierungssystem, das auf [Azure Resource Manager](../../azure-resource-manager/management/overview.md) basiert und eine präzise Verwaltung des Zugriffs auf Azure-Ressourcen ermöglicht.

Azure RBAC ermöglicht Benutzern das Verwalten von Berechtigungen für Schlüssel, Geheimnisse und Zertifikate. Es bietet einen Ort, an dem alle Berechtigungen für alle Schlüsseltresore verwaltet werden können. 

Das Azure RBAC-Modell bietet die Möglichkeit, Berechtigungen für verschiedene Bereichsebenen festzulegen: Verwaltungsgruppe, Abonnement, Ressourcengruppe oder Einzelressourcen.  Azure RBAC für Key Vault bietet außerdem die Möglichkeit, separate Berechtigungen für einzelne Schlüssel, Geheimnisse und Zertifikate zu verwalten.

Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md).

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>Bewährte Methoden für einzelne Schlüssel, Geheimnisse und Zertifikate

Es wird empfohlen, einen Schlüsseltresor pro Anwendung und Umgebung (Entwicklung, Präproduktion und Produktion) zu verwenden.

Die Berechtigungen für einzelne Schlüssel, Geheimnisse und Zertifikate sollten nur in bestimmten Szenarien verwendet werden:

-   Anwendungen mit mehreren Ebenen, die die Zugriffssteuerung zwischen den Ebenen trennen müssen

-   Freigeben einzelner Geheimnisse zwischen mehreren Anwendungen

Weitere Informationen zu Azure Key Vault-Verwaltungsrichtlinien finden Sie unter:

- [Bewährte Methoden bei Azure Key Vault](best-practices.md)
- [Grenzwerte des Azure Key Vault-Diensts](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations-preview"></a>In Azure integrierte Rollen für Key Vault-Vorgänge auf Datenebene (Vorschau)
> [!NOTE]
> Die Rolle `Key Vault Contributor` ist für Vorgänge auf Verwaltungsebene zur Verwaltung von Schlüsseltresoren gedacht. Sie lässt nicht den Zugriff auf Schlüssel, Geheimnisse und Zertifikate zu.

| Integrierte Rolle | BESCHREIBUNG | id |
| --- | --- | --- |
| Key Vault-Administrator (Vorschau) | Ausführen beliebiger Vorgänge auf Datenebene für einen Schlüsseltresor und alle darin enthaltenen Objekte aus (einschließlich Zertifikate, Schlüssel und Geheimnisse). Kann keine Key Vault-Ressourcen oder Rollenzuweisungen verwalten. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault Certificates Officer (Vorschau) | Ausführen beliebiger Aktionen für die Zertifikate eines Schlüsseltresors mit Ausnahme der Verwaltung von Berechtigungen. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault Crypto Officer (Vorschau)| Ausführen beliebiger Aktionen für die Schlüssel eines Schlüsseltresors mit Ausnahme der Verwaltung von Berechtigungen. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Key Vault Crypto Service Encryption (Vorschau) | Lesen von Metadaten von Schlüsseln und Ausführen von Vorgängen zum Packen/Entpacken. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault Crypto-Benutzer (Vorschau) | Ausführen kryptografischer Vorgänge mithilfe von Schlüsseln. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault Reader (Vorschau)| Lesen von Metadaten von Schlüsseltresoren und deren Zertifikaten, Schlüsseln und Geheimnissen. Sensible Werte, z. B. der Inhalt von Geheimnissen oder Schlüsselmaterial, können nicht gelesen werden. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Key Vault Secrets Officer (Vorschau)| Ausführen beliebiger Aktionen für die Geheimnisse eines Schlüsseltresors mit Ausnahme der Verwaltung von Berechtigungen. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Key Vault Secrets-Benutzer (Vorschau)| Lesen der Inhalte von Geheimnissen. Funktioniert nur für Schlüsseltresore, die das Berechtigungsmodell „Rollenbasierte Azure-Zugriffssteuerung“ verwenden. | 4633458b-17de-408a-b874-0445c86b69e6 |

Weitere Informationen zu den Definitionen von in Azure integrierten Rollen finden Sie unter [In Azure integrierte Rollen](../../role-based-access-control/built-in-roles.md).

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Verwenden von Geheimnis-, Schlüssel- und Zertifikatberechtigungen von Azure RBAC mit Key Vault

Das neue Azure RBAC-Berechtigungsmodell für Key Vault bietet eine Alternative zum Berechtigungsmodell für den Tresor mit Zugriffsrichtlinien. 

### <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um Rollenzuweisungen hinzufügen zu können:

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
- `Microsoft.Authorization/roleAssignments/write`- und `Microsoft.Authorization/roleAssignments/delete`-Berechtigungen, wie z.B. [Benutzerzugriffsadministrator](../../role-based-access-control/built-in-roles.md#user-access-administrator) oder [Besitzer](../../role-based-access-control/built-in-roles.md#owner)

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Aktivieren von Azure RBAC-Berechtigungen für einen Schlüsseltresor

> [!IMPORTANT]
> Wenn Sie das Azure RBAC-Berechtigungsmodell festlegen, werden alle Berechtigungen aus Zugriffsrichtlinien ungültig. Dies kann zu Ausfällen führen, wenn keine äquivalenten Azure-Rollen zugewiesen sind.

1.  Aktivieren von Azure RBAC-Berechtigungen für einen neuen Schlüsseltresor:

    ![Aktivieren von Azure RBAC-Berechtigungen – neuer Tresor](../media/rbac/image-1.png)

2.  Aktivieren von Azure RBAC-Berechtigungen für einen vorhandenen Schlüsseltresor:

    ![Aktivieren von Azure RBAC-Berechtigungen – vorhandener Tresor](../media/rbac/image-2.png)

### <a name="assign-role"></a>Zuweisen einer Rolle

> [!Note]
> Es wird empfohlen, in Skripts die eindeutige Rollen-ID anstelle des Rollennamens zu verwenden. Wird eine Rolle umbenannt, funktionieren somit Ihre Skripts weiterhin. Während der Vorschauphase weist jede Rolle das Suffix „(Vorschau)“ auf, das später entfernt wird. In diesem Dokument wird der Rollenname nur zur besseren Lesbarkeit verwendet.

Azure CLI-Befehl zum Erstellen einer Rollenzuweisung:

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```

Im Azure-Portal ist der Bildschirm „Azure-Rollenzuweisungen“ für alle Ressourcen auf der Registerkarte „Zugriffssteuerung (IAM)“ verfügbar.

![Registerkarte „Rollenzuweisung (IAM)“](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>Rollenzuweisung im Ressourcengruppenbereich

1.  Wechseln Sie zur Schlüsseltresor-Ressourcengruppe.
    ![Rollenzuweisung – Ressourcengruppe](../media/rbac/image-4.png)

2.  Klicken Sie auf „Zugriffssteuerung (IAM)“ \> „Rollenzuweisung hinzufügen“ \> „Hinzufügen“.

3.  Erstellen Sie die Rolle des Key Vault-Lesers („Key Vault Reader (Vorschau)“) für den aktuellen Benutzer.

    ![Hinzufügen einer Rolle – Ressourcengruppe](../media/rbac/image-5.png)

Azure CLI:
```azurecli
az role assignment create --role "Key Vault Reader (preview)" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```

Die obige Rollenzuweisung bietet die Möglichkeit, Key Vault-Objekte in einem Schlüsseltresor aufzulisten.

### <a name="key-vault-scope-role-assignment"></a>Rollenzuweisung im Key Vault-Bereich

1. Wechseln Sie zur Registerkarte „Key Vault“ \> „Zugriffssteuerung (IAM)“.

2. Klicken Sie auf „Rollenzuweisung hinzufügen“ \> „Hinzufügen“.

3. Erstellen Sie die Rolle des Schlüsselgeheimnisbeauftragten („Key Vault Secrets Officer (Vorschau)“) für den aktuellen Benutzer.

    ![Rollenzuweisung – Key Vault](../media/rbac/image-6.png)

 Azure CLI:

```azurecli
az role assignment create --role "Key Vault Secrets Officer (preview)" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```

Nachdem Sie die oben genannte Rollenzuweisung erstellt haben, können Sie Geheimnisse erstellen, aktualisieren und löschen.

4. Erstellen Sie ein neues Geheimnis („Geheimnisse“ \> „+ Generieren/Importieren“) zum Testen der Rollenzuweisung auf Geheimnisebene.

    ![Hinzufügen einer Rolle – Schlüsseltresor](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>Rollenzuweisung im Geheimnisbereich

1. Öffnen Sie eines der zuvor erstellten Geheimnisse, und suchen Sie nach „Übersicht“ und „Zugriffssteuerung (IAM) (Vorschau)“.

2. Klicken Sie auf die Registerkarte „Zugriffssteuerung (IAM) (Vorschau)“.

    ![Rollenzuweisung – Geheimnis](../media/rbac/image-8.png)

3. Erstellen Sie die Rolle des Schlüsselgeheimnisbeauftragten („Key Vault Secrets Officer (Vorschau)“) für den aktuellen Benutzer wie oben für den Schlüsseltresor.

Azure CLI:

```azurecli
az role assignment create --role "Key Vault Secrets Officer (preview)" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```

### <a name="test-and-verify"></a>Testen und Überprüfen

> [!NOTE]
> Browser verwenden Caching, deshalb ist nach dem Entfernen von Rollenzuweisungen eine Seitenaktualisierung erforderlich.<br>
> Warten Sie einige Minuten, bis die Rollenzuweisungen aktualisiert wurden.

1. Überprüfen Sie das Hinzufügen eines neuen Geheimnisses ohne die Rolle „Key Vault Secrets Officer“ auf der Ebene des Schlüsseltresors.

Wechseln Sie zur Registerkarte „Zugriffssteuerung (IAM)“ für den Schlüsseltresor, und entfernen Sie die Rollenzuweisung „Key Vault Secrets Officer (Vorschau)“ für diese Ressource.

![Entfernen der Rollenzuweisung – Key Vault](../media/rbac/image-9.png)

Navigieren Sie zum zuvor erstellten Geheimnis. Alle Eigenschaften des Geheimnisses werden angezeigt.

![Ansicht eines Geheimnisses mit Zugriff](../media/rbac/image-10.png)

Erstellen Sie ein neues Geheimnis („Geheimnisse“ \> „+ Generieren/Importieren“). Folgender Fehler sollte angezeigt werden:

   ![Erstellen eines neuen Geheimnisses](../media/rbac/image-11.png)

2.  Überprüfen Sie die Geheimnisbearbeitung ohne die Rolle „Key Vault Secret Officer“ auf Geheimnisebene.

-   Wechseln Sie zur Registerkarte „Zugriffssteuerung (IAM) (Vorschau)“ für das zuvor erstellte Geheimnis, und entfernen Sie die Rollenzuweisung „Key Vault Secrets Officer (Vorschau)“ für diese Ressource.

-   Navigieren Sie zum zuvor erstellten Geheimnis. Die Eigenschaften des Geheimnisses werden angezeigt.

   ![Ansicht eines Geheimnisses ohne Zugriff](../media/rbac/image-12.png)

3. Überprüfen Sie das Lesen von Geheimnissen ohne die Leser-Rolle auf Schlüsseltresorebene.

-   Wechseln Sie zur Registerkarte „Zugriffssteuerung (IAM)“ für die Schlüsseltresor-Ressourcengruppe, und entfernen Sie die Rollenzuweisung „Key Vault Reader (Vorschau)“.

-   Nach dem Navigieren zur Registerkarte „Geheimnisse“ für den Schlüsseltresor sollte der nachstehende Fehler angezeigt werden:

   ![Registerkarte „Geheimnis“ – Fehler](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>Erstellen von benutzerdefinierten Rollen 

[Befehl „az role definition create“](/cli/azure/role/definition#az-role-definition-create)

**(CLI-Bash-Skript)</br>**
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```

Weitere Informationen zum Erstellen von benutzerdefinierten Rollen finden Sie unter:

[Benutzerdefinierte Azure-Rollen](../../role-based-access-control/custom-roles.md)

## <a name="known-limits-and-performance"></a>Bekannte Grenzwerte und Leistung

-   2\.000 Azure-Rollenzuweisungen pro Abonnement

-   Latenz der Rollenzuweisungen: Bei der aktuellen erwarteten Leistung dauert es bis zu 10 Minuten (600 Sekunden) nach dem Ändern einer Rollenzuweisung, bis dies auf die Rolle angewandt wird.

## <a name="learn-more"></a>Weitere Informationen

- [RBAC in Azure – Übersicht](../../role-based-access-control/overview.md)
- [Tutorial zu benutzerdefinierten Rollen](../../role-based-access-control/tutorial-custom-role-cli.md)
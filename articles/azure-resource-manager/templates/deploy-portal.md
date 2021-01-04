---
title: Bereitstellen von Ressourcen mit dem Azure-Portal
description: Verwenden Sie das Azure-Portal und Azure Resource Manager zum Bereitstellen Ihrer Ressourcen in einer Ressourcengruppe in Ihrem Abonnement.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: b87ac48bbaec7f94d5e75939cf5ec17df0ff2d2f
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92668760"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>Bereitstellen von Ressourcen mit ARM-Vorlagen und dem Azure-Portal

Erfahren Sie, wie Sie Ihre Azure-Ressourcen mit dem [Azure-Portal](https://portal.azure.com) und [Azure Resource Manager-Vorlagen (ARM)](overview.md) bereitstellen. Informationen zum Verwalten von Ressourcen finden Sie unter [Verwalten von Azure-Ressourcen unter Verwendung des Azure-Portals](../management/manage-resources-portal.md).

Zur Bereitstellung von Azure-Ressourcen mit dem Azure-Portal sind normalerweise zwei Schritte notwendig:

- Erstellen Sie eine Ressourcengruppe.
- Bereitstellen von Ressourcen in der Ressourcengruppe

Außerdem können Sie eine angepasste ARM-Vorlage zum Bereitstellen von Azure-Ressourcen erstellen.

In diesem Artikel werden beide Methoden beschrieben.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

1. Wählen Sie zum Erstellen einer neuen Ressourcengruppe im [Azure-Portal](https://portal.azure.com) die Option **Ressourcengruppen** aus.

   ![Auswählen von Ressourcengruppen](./media/deploy-portal/select-resource-groups.png)

1. Wählen Sie unter „Ressourcengruppe“ die Option **Hinzufügen**.

   ![Hinzufügen von Ressourcengruppen](./media/deploy-portal/add-resource-group.png)

1. Wählen Sie die folgenden Eigenschaftswerte aus, bzw. geben Sie sie ein:

    - **Abonnement** : Wählen Sie ein Azure-Abonnement aus.
    - **Ressourcengruppe** : Geben Sie der Ressourcengruppe einen Namen.
    - **Region** : Geben Sie einen Azure-Standort an. An diesem Standort speichert die Ressourcengruppe Metadaten zu den Ressourcen. Aus Compliance-Gründen sollten Sie angeben, wo diese Metadaten gespeichert werden. Im Allgemeinen wird die Angabe eines Standorts empfohlen, an dem sich der Großteil Ihrer Ressourcen befindet. Durch die Verwendung des gleichen Standorts können Sie die Vorlage vereinfachen.

   ![Festlegen von Gruppenwerten](./media/deploy-portal/set-group-properties.png)

1. Klicken Sie auf **Überprüfen + erstellen**.
1. Überprüfen Sie die Werte, und wählen Sie dann **Erstellen** aus.
1. Wählen Sie **Aktualisieren** aus, damit die neue Ressourcengruppe in der Liste erscheint.

## <a name="deploy-resources-to-a-resource-group"></a>Bereitstellen von Ressourcen in einer Ressourcengruppe

Nachdem Sie eine Ressourcengruppe erstellt haben, können Sie über den Marketplace Ressourcen in der Gruppe bereitstellen. Der Marketplace bietet vordefinierte Lösungen für gängige Szenarien.

1. Wählen Sie zum Starten einer Bereitstellung im [Azure-Portal](https://portal.azure.com) die Option **Ressource erstellen** aus.

   ![Neue Ressource](./media/deploy-portal/new-resources.png)

1. Suchen Sie den Typ der Ressource, die Sie bereitstellen möchten. Die Ressourcen sind in Kategorien unterteilt. Falls die gewünschte bereitzustellende Lösung nicht angezeigt wird, können Sie im Marketplace danach suchen. Im folgenden Screenshot ist Ubuntu Server ausgewählt.

   ![Ressourcentyp auswählen](./media/deploy-portal/select-resource-type.png)

1. Je nach ausgewähltem Ressourcentyp müssen Sie einige relevante Eigenschaften festlegen, bevor die Bereitstellung beginnen kann. Für alle Typen müssen Sie eine Zielressourcengruppe auswählen. Die folgende Abbildung zeigt, wie Sie einen virtuellen Linux-Computer erstellen und in der erstellten Ressourcengruppe bereitstellen.

   ![Ressourcengruppe erstellen](./media/deploy-portal/select-existing-group.png)

   Sie können eine Ressourcengruppe auch beim Bereitstellen Ihrer Ressourcen erstellen. Wählen Sie **Neu erstellen** aus, und benennen Sie die Ressourcengruppe.

1. Die Bereitstellung wird gestartet. Die Bereitstellung kann mehrere Minuten dauern. Bei einigen Ressourcen dauert sie länger als bei anderen. Nachdem die Bereitstellung abgeschlossen wurde, wird eine Benachrichtigung angezeigt. Wählen Sie zum Öffnen **Zu Ressource wechseln** aus.

   ![Anzeigen einer Benachrichtigung](./media/deploy-portal/view-notification.png)

1. Nach dem Bereitstellen Ihrer Ressourcen können Sie mit **Hinzufügen** weitere Ressourcen hinzufügen.

   ![Ressource hinzufügen](./media/deploy-portal/add-resource.png)

Obwohl dies nicht angezeigt wurde, wurden die ausgewählten Ressourcen im Portal über eine ARM-Vorlage bereitgestellt. Sie finden die Vorlage im Bereitstellungsverlauf. Weitere Informationen finden Sie unter [Exportieren von Vorlagen nach der Bereitstellung](export-template-portal.md#export-template-after-deployment).

## <a name="deploy-resources-from-custom-template"></a>Bereitstellen von Ressourcen mithilfe einer benutzerdefinierten Vorlage

Wenn Sie eine Bereitstellung ausführen möchten, ohne eine der Vorlagen im Marketplace zu nutzen, können Sie eine angepasste Vorlage erstellen, mit der die Infrastruktur für Ihre Lösung definiert wird. Informationen zum Erstellen von Vorlagen finden Sie unter [Verstehen der Struktur und Syntax von ARM-Vorlagen](template-syntax.md).

> [!NOTE]
> Für die Portalschnittstelle wird das Verweisen auf ein [Geheimnis aus einem Key Vault](key-vault-parameter.md) nicht unterstützt. Verwenden Sie stattdessen [PowerShell](deploy-powershell.md) oder [Azure CLI](deploy-cli.md), um Ihre Vorlage lokal oder über einen externen URI bereitzustellen.

1. Wählen Sie zum Bereitstellen einer benutzerdefinierten Vorlage über das Portal die Option **Ressource erstellen** aus, und suchen Sie nach **Vorlage**. Wählen Sie dann **Vorlagenbereitstellung** aus.

   ![Suchen der Vorlagenbereitstellung](./media/deploy-portal/search-template.png)

1. Klicken Sie auf **Erstellen**.
1. Zum Erstellen einer Vorlage werden verschiedene Optionen angezeigt:

    - **Erstellen einer eigenen Vorlage im Editor:** Erstellen Sie eine eigene Vorlage im Vorlagen-Editor im Portal.
    - **Gängige Vorlagen** : Wählen Sie eine der gängigen Lösungen aus.
    - **Laden einer Schnellstartvorlage von GitHub:** Wählen Sie eine der [Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/) aus.

   ![Anzeigen der Optionen](./media/deploy-portal/see-options.png)

    Dieses Tutorial zeigt Ihnen, wie Sie eine Schnellstartvorlage laden.

1. Geben Sie unter **GitHub-Schnellstartvorlage laden** die Zeichenfolge **„101-storage-account-create“** ein, oder wählen Sie sie aus.

    Sie haben zwei Möglichkeiten:

    - **Vorlage auswählen** : Stellen Sie die Vorlage bereit.
    - **Vorlage bearbeiten** : Bearbeiten Sie die Schnellstartvorlage, bevor Sie sie bereitstellen.

1. Wählen Sie **Vorlage bearbeiten** aus, um sich den Vorlageneditor des Portals anzusehen. Die Vorlage wird in den Editor geladen. Beachten Sie, dass es zwei Parameter gibt: **storageAccountType** und **location**.

   ![Erstellen der Vorlage](./media/deploy-portal/show-json.png)

1. Nehmen Sie eine geringfügige Änderung an der Vorlage vor. Aktualisieren Sie beispielsweise die Variable **storageAccountName** wie folgt:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Wählen Sie **Speichern** aus. Nun wird die Oberfläche für die Vorlagenbereitstellung im Portal angezeigt. Sie sehen außerdem die zwei Parameter, die Sie in der Vorlage definiert haben.
1. Geben Sie die Eigenschaftswerte ein oder wählen Sie sie aus:

    - **Abonnement** : Wählen Sie ein Azure-Abonnement aus.
    - **Ressourcengruppe** : Wählen Sie **Neu erstellen** aus, und geben Sie einen Namen ein.
    - **Standort** : Wählen Sie einen Azure-Standort aus.
    - **Speicherkontotyp** : Verwenden Sie den Standardwert.
    - **Standort** : Verwenden Sie den Standardwert.
    - **Ich stimme den oben genannten Geschäftsbedingungen zu** : Aktivieren Sie dieses Kontrollkästchen.

1. Wählen Sie die Option **Kaufen**.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Anzeigen von Überwachungsprotokollen finden Sie unter [Überwachen von Vorgängen mit Resource Manager](../management/view-activity-logs.md).
- Informationen zum Beheben von Bereitstellungsfehlern finden Sie unter [Anzeigen von Bereitstellungsvorgängen](deployment-history.md).
- Informationen zum Exportieren einer Vorlage aus einer Bereitstellung oder Ressourcengruppe finden Sie unter [Exportieren von ARM-Vorlagen](export-template-portal.md).
- Informationen zum sicheren Rollout Ihres Dienst über mehrere Regionen finden Sie unter [Sichere Bereitstellungsmethoden mit dem Azure-Bereitstellungs-Manager (öffentliche Vorschau)](deployment-manager-overview.md).

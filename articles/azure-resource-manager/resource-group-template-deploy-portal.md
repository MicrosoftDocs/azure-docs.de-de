---
title: Verwenden des Azure-Portals zum Bereitstellen von Azure-Ressourcen | Microsoft Docs
description: Verwenden Sie das Azure-Portal und Azure Resource Manager zum Bereitstellen Ihrer Ressourcen.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: tomfitz
ms.openlocfilehash: 7b28129a3afe9f78d0ef749fa0c7759082c5f758
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60520585"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure-Portal

In diesem Artikel wird veranschaulicht, wie Sie mit dem [Azure-Portal](https://portal.azure.com) und [Azure Resource Manager](resource-group-overview.md) Ihre Azure-Ressourcen bereitstellen. Informationen zum Verwalten von Ressourcen finden Sie unter [Verwalten von Azure-Ressourcen unter Verwendung des Azure-Portals](manage-resources-portal.md).

## <a name="create-resource-group"></a>Ressourcengruppe erstellen

1. Wählen Sie zum Erstellen einer leeren Ressourcengruppe die Option **Ressourcengruppen**.

   ![Auswählen von Ressourcengruppen](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. Wählen Sie unter „Ressourcengruppe“ die Option **Hinzufügen**.

   ![Hinzufügen von Ressourcengruppen](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Geben Sie einen Namen und einen Speicherort an, und wählen Sie ggf. ein Abonnement aus. Sie müssen einen Standort für die Ressourcengruppe angeben, da diese Metadaten zu den Ressourcen speichert. Aus Compliance-Gründen sollten Sie angeben, wo diese Metadaten gespeichert werden. Im Allgemeinen wird die Angabe eines Standorts empfohlen, an dem sich der Großteil Ihrer Ressourcen befindet. Durch die Verwendung des gleichen Standorts können Sie die Vorlage vereinfachen.

   ![Festlegen von Gruppenwerten](./media/resource-group-template-deploy-portal/set-group-properties.png)

   Wenn Sie die Eigenschaften festgelegt haben, klicken Sie auf **Erstellen**.

1. Klicken Sie zum Anzeigen Ihrer neuen Ressourcengruppe auf **Aktualisieren**.

   ![Aktualisieren von Ressourcengruppen](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>Bereitstellen von Ressourcen über den Marketplace

Nachdem Sie eine Ressourcengruppe erstellt haben, können Sie Ressourcen dafür über den Marketplace bereitstellen. Der Marketplace bietet vordefinierte Lösungen für gängige Szenarien.

1. Klicken Sie zum Starten einer Bereitstellung auf **Ressource erstellen**.

   ![Neue Ressource](./media/resource-group-template-deploy-portal/new-resources.png)

1. Suchen Sie den Typ der Ressource, die Sie bereitstellen möchten.

   ![Ressourcentyp auswählen](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Falls die gewünschte bereitzustellende Lösung nicht angezeigt wird, können Sie im Marketplace danach suchen. Beispiel: Wenn Sie eine Wordpress-Lösung suchen, beginnen Sie mit der Eingabe von **Wordpress**, und wählen Sie die gewünschte Option aus.

   ![Durchsuchen von Marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

1. Je nach ausgewähltem Ressourcentyp müssen Sie einige relevante Eigenschaften festlegen, bevor die Bereitstellung beginnen kann. Für alle Typen müssen Sie eine Zielressourcengruppe auswählen. Die folgende Abbildung zeigt, wie Sie eine Web-App erstellen und in der erstellten Ressourcengruppe bereitstellen.

   ![Ressourcengruppe erstellen](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Alternativ können Sie eine Ressourcengruppe beim Bereitstellen Ihrer Ressourcen erstellen. Wählen Sie **Neu erstellen** aus, und benennen Sie die Ressourcengruppe.

   ![Erstellen einer neuen Ressourcengruppe](./media/resource-group-template-deploy-portal/select-new-group.png)

1. Die Bereitstellung wird gestartet. Der Vorgang kann mehrere Minuten dauern. Nachdem die Bereitstellung abgeschlossen wurde, wird eine Benachrichtigung angezeigt.

   ![Anzeigen einer Benachrichtigung](./media/resource-group-template-deploy-portal/view-notification.png)

1. Nach dem Bereitstellen Ihrer Ressourcen können Sie mit **Hinzufügen** weitere Ressourcen hinzufügen.

   ![Ressource hinzufügen](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Bereitstellen von Ressourcen mithilfe einer benutzerdefinierten Vorlage

Wenn Sie eine Bereitstellung ausführen möchten, ohne eine der Vorlagen im Marketplace zu nutzen, können Sie eine angepasste Vorlage erstellen, mit der die Infrastruktur für Ihre Lösung definiert wird. Informationen zum Erstellen von Vorlagen finden Sie unter [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](resource-group-authoring-templates.md).

> [!NOTE]
> Für die Portalschnittstelle wird das Verweisen auf ein [Geheimnis aus einem Key Vault](resource-manager-keyvault-parameter.md) nicht unterstützt. Verwenden Sie stattdessen [PowerShell](resource-group-template-deploy.md) oder [Azure CLI](resource-group-template-deploy-cli.md), um Ihre Vorlage lokal oder über einen externen URI bereitzustellen.

1. Klicken Sie zum Bereitstellen einer benutzerdefinierten Vorlage über das Portal auf **Ressource erstellen**, suchen Sie nach **Vorlagenbereitstellung**, und wählen Sie diese Option aus.

   ![Suchen der Vorlagenbereitstellung](./media/resource-group-template-deploy-portal/search-template.png)

1. Klicken Sie auf **Erstellen**.

   ![Klicken auf „Erstellen“](./media/resource-group-template-deploy-portal/show-template-option.png)

1. Zum Erstellen einer Vorlage werden verschiedene Optionen angezeigt. Wählen Sie **Eigene Vorlage im Editor erstellen**.

   ![Anzeigen der Optionen](./media/resource-group-template-deploy-portal/see-options.png)

1. Sie haben eine leere Vorlage, die zum Anpassen verfügbar ist.

   ![Erstellen der Vorlage](./media/resource-group-template-deploy-portal/blank-template.png)

1. Sie können die JSON-Syntax manuell bearbeiten oder eine vorgefertigte Vorlage aus dem [Katalog mit Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/) auswählen. In diesem Artikel verwenden Sie jedoch die Option **Ressource hinzufügen**.

   ![Bearbeiten der Vorlage](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. Klicken Sie auf **Speicherkonto**, und geben Sie einen Namen ein. Klicken Sie auf **OK**, wenn Sie die Werte eingegeben haben.

   ![Auswählen des Speicherkontos](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. Der Editor fügt automatisch JSON als Ressourcentyp hinzu. Beachten Sie, dass ein Parameter zum Festlegen des Speicherkontotyps enthalten ist. Wählen Sie **Speichern** aus.

   ![Anzeigen der Vorlage](./media/resource-group-template-deploy-portal/show-json.png)

1. Nun haben Sie die Möglichkeit, die in der Vorlage definierten Ressourcen bereitzustellen. Stimmen Sie für die Bereitstellung den Geschäftsbedingungen zu, und klicken Sie auf **Kaufen**.

   ![Bereitstellen der Vorlage](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Bereitstellen von Ressourcen aus einer in Ihrem Konto gespeicherten Vorlage

Über das Portal können Sie eine Vorlage in Ihrem Azure-Konto speichern und später erneut bereitstellen. Weitere Informationen zu Vorlagen finden Sie unter [Erstellen und Bereitstellen Ihrer ersten Azure Resource Manager-Vorlage](resource-manager-create-first-template.md).

1. Klicken Sie zum Suchen der gespeicherten Vorlagen auf **Weitere Dienste**.

   ![Weitere Dienste](./media/resource-group-template-deploy-portal/more-services.png)

1. Suchen Sie nach **Vorlagen**, und wählen Sie diese Option.

   ![Suchen nach Vorlagen](./media/resource-group-template-deploy-portal/find-templates.png)

1. Wählen Sie in der Liste der Vorlagen, die in Ihrem Konto gespeichert sind, diejenige aus, mit der Sie arbeiten möchten.

   ![Gespeicherte Vorlagen](./media/resource-group-template-deploy-portal/saved-templates.png)

1. Wählen Sie **Bereitstellen** aus, um diese gespeicherte Vorlage erneut bereitzustellen.

   ![Bereitstellen der gespeicherten Vorlage](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Anzeigen von Überwachungsprotokollen finden Sie unter [Überwachen von Vorgängen mit Resource Manager](./resource-group-audit.md).
- Informationen zum Beheben von Bereitstellungsfehlern finden Sie unter [Anzeigen von Bereitstellungsvorgängen](./resource-manager-deployment-operations.md).
- Informationen zum Exportieren einer Vorlage aus einer Bereitstellung oder Ressourcengruppe finden Sie unter [Exportieren von Azure Resource Manager-Vorlagen](./manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Informationen zum sicheren Rollout Ihres Dienst über mehrere Regionen finden Sie im Artikel zum [Bereitstellungs-Manager](./deployment-manager-overview.md).

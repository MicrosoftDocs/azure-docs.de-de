---
title: Verwalten von Ressourcen – Azure-Portal
description: Verwenden Sie das Azure-Portal und Azure Resource Manager zum Verwalten Ihrer Ressourcen. Hier wird gezeigt, wie Sie Ressourcen bereitstellen und löschen.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: e2274a551542b06996941b49e7d047baf3e8b3ca
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92894031"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Verwalten von Azure-Ressourcen über das Azure-Portal

Erfahren Sie, wie Sie mit dem [Azure-Portal](https://portal.azure.com) und [Azure Resource Manager](overview.md) Ihre Azure-Ressourcen verwalten. Informationen zum Verwalten von Ressourcengruppen finden Sie unter [Verwalten von Azure-Ressourcengruppen mithilfe des Azure-Portals](manage-resource-groups-portal.md).

Andere Artikel zum Verwalten von Ressourcen:

- [Verwalten von Azure-Ressourcen mithilfe der Azure CLI](manage-resources-cli.md)
- [Verwalten von Azure-Ressourcen mithilfe von Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Bereitstellen von Ressourcen in einer Ressourcengruppe

Nachdem Sie eine Azure Resource Manager-Vorlage erstellt haben, können Sie Ihre Azure-Ressourcen über das Azure-Portal bereitstellen. Eine Anleitung zum Erstellen einer Vorlage finden Sie unter [Schnellstart: Erstellen und Bereitstellen von Azure Resource Manager-Vorlagen über das Azure-Portal](../templates/quickstart-create-templates-use-the-portal.md). Informationen zum Bereitstellen einer Vorlage über das Portal finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure-Portal](../templates/deploy-portal.md).

## <a name="open-resources"></a>Öffnen von Ressourcen

Azure-Ressourcen werden durch Azure-Dienste und Ressourcengruppen organisiert. Das folgenden Verfahren zeigt, wie ein Sie ein Speicherkonto namens **mystorage0207** öffnen. Der virtuelle Computer befindet sich in einer Ressourcengruppe namens **mystorage0207rg**.

So öffnen Sie eine Ressource nach Diensttyp:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im linken Bereich den Azure-Dienst aus. In diesem Fall **Speicherkonten**.  Wenn der Dienst nicht aufgelistet ist, wählen Sie **Alle Dienste** und dann den Diensttyp aus.

    ![Azure Ressource im Portal öffnen](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Wählen Sie die Ressource aus, die Sie öffnen möchten.

    ![Screenshot: Ausgewählte Ressource](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Ein Speicherkonto sieht folgendermaßen aus:

    ![Screenshot: Speicherkonto](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

So öffnen Sie eine Ressource nach Ressourcengruppe:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im linken Bereich **Ressourcengruppen** aus, um die Ressource in der Gruppe aufzulisten.
3. Wählen Sie die Ressource aus, die Sie öffnen möchten. 

## <a name="manage-resources"></a>Ressourcen verwalten

Wenn Sie eine Ressource im Portal anzeigen, sehen Sie die Optionen für die Verwaltung dieser bestimmten Ressource.

![Verwalten von Azure-Ressourcen](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

Der Screenshot zeigt die Verwaltungsoptionen für einen virtuellen Azure-Computer. Sie können Vorgänge wie Starten, Neustarten und Beenden eines virtuellen Computers ausführen.

## <a name="delete-resources"></a>Löschen von Ressourcen

1. Öffnen Sie die Ressource im Portal. Die Schritte finden Sie unter [Öffnen von Ressourcen](#open-resources).
2. Klicken Sie auf **Löschen**. Der folgende Screenshot zeigt die Verwaltungsoptionen für einen virtuellen Computer.

    ![Azure-Ressource löschen](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Geben Sie den Namen der Ressource ein, um den Löschvorgang zu bestätigen, und wählen Sie dann **Löschen** aus.

Weitere Informationen dazu, in welcher Reihenfolge Ressourcenlöschungen in Azure Resource Manager durchgeführt werden, finden Sie unter [Azure Resource Manager: Löschvorgang von Ressourcengruppen](delete-resource-group.md).

## <a name="move-resources"></a>Verschieben von Ressourcen

1. Öffnen Sie die Ressource im Portal. Die Schritte finden Sie unter [Öffnen von Ressourcen](#open-resources).
2. Klicken Sie auf **Verschieben**. Der folgende Screenshot zeigt die Verwaltungsoptionen für ein Speicherkonto.

    ![Azure-Ressource verschieben](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Wählen Sie je nach Bedarf **In eine andere Ressourcengruppe verschieben** oder **In ein anderes Abonnement verschieben** aus.

Weitere Informationen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Sperren von Ressourcen

Das Sperren verhindert, dass andere Benutzer in Ihrer Organisation versehentlich wichtige Ressourcen löschen oder ändern, z. B. ein Azure-Abonnement, eine Ressourcengruppe oder eine Ressource. 

1. Öffnen Sie die Ressource im Portal. Die Schritte finden Sie unter [Öffnen von Ressourcen](#open-resources).
2. Wählen Sie **Sperren** aus. Der folgende Screenshot zeigt die Verwaltungsoptionen für ein Speicherkonto.

    ![Azure-Ressource sperren](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Wählen Sie **Hinzufügen** aus, und geben Sie dann die Eigenschaften der Sperre an.

Weitere Informationen finden Sie unter [Sperren von Ressourcen mit dem Azure-Ressourcen-Manager](lock-resources.md).

## <a name="tag-resources"></a>Markieren von Ressourcen

Das Markieren hilft Ihnen, Ihre Ressourcengruppe und Ressourcen logisch zu organisieren. 

1. Öffnen Sie die Ressource im Portal. Die Schritte finden Sie unter [Öffnen von Ressourcen](#open-resources).
2. Wählen Sie **Tags** aus. Der folgende Screenshot zeigt die Verwaltungsoptionen für ein Speicherkonto.

    ![Azure-Ressource markieren](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Geben Sie die Tageigenschaften ein, und wählen Sie dann **Speichern** aus.

Weitere Informationen finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](tag-resources.md#portal).

## <a name="monitor-resources"></a>Überwachen von Ressourcen

Wenn Sie eine Ressource öffnen, werden im Portal Standarddiagramme und -tabellen zur Überwachung dieses Ressourcentyps angezeigt. Der folgende Screenshot zeigt die Diagramme für einen virtuellen Computer:

![Azure-Ressource überwachen](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Sie können das Stecknadelsymbol in der oberen rechten Ecke der Diagramme auswählen, um das Diagramm an das Dashboard anzuheften. Um sich mit der Verwendung von Dashboards vertraut zu machen, lesen Sie [Erstellen und Freigeben von Dashboards im Azure-Portal](../../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Zugriff auf Ressourcen verwalten

Der Zugriff auf Ressourcen in Azure wird mithilfe der [rollenbasierten Zugriffssteuerung in Azure (Azure RBAC)](../../role-based-access-control/overview.md) verwaltet. Weitere Informationen finden Sie unter [Hinzufügen oder Entfernen von Rollenzuweisungen mithilfe des Azure-Portals](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure Resource Manager finden Sie unter [Übersicht über den Azure Resource Manager](overview.md).
- Informationen zur Resource Manager-Vorlagensyntax finden Sie unter [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](../templates/template-syntax.md).
- Informationen zum Entwickeln von Vorlagen finden Sie in den [Schritt-für-Schritt-Tutorials](../index.yml).
- Informationen zum Anzeigen der Vorlagenschemas für Azure Resource Manager finden Sie in der [Referenz zu Vorlagen](/azure/templates/).

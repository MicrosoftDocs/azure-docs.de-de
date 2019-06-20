---
title: Verwalten von Azure Resource Manager-Gruppen mithilfe des Azure-Portals | Microsoft-Dokumentation
description: Verwenden Sie das Azure-Portal, um Ihre Azure Resource Manager-Gruppen zu verwalten.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: 559c1874c119eabef2c35a954961c1e669df3c06
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65507227"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Verwalten von Azure Resource Manager-Gruppen mithilfe des Azure-Portals

Erfahren Sie , wie Sie mit dem [Azure-Portal](https://portal.azure.com) und [Azure Resource Manager](resource-group-overview.md) Ihre Azure-Ressourcengruppen verwalten. Informationen zum Verwalten von Azure-Ressourcen finden Sie unter [Manage Azure resources by using the Azure portal (Verwalten von Azure-Ressourcen mithilfe des Azure-Portals)](./manage-resources-portal.md).

Weitere Artikel zum Verwalten von Ressourcengruppen:

- [Manage Azure resource groups by using Azure CLI (Verwalten von Azure-Ressourcengruppen mithilfe der Azure-Befehlszeilenschnittstelle)](./manage-resources-cli.md)
- [Manage Azure resource groups by using Azure PowerShell (Verwalten von Azure-Ressourcengruppen mithilfe von Azure PowerShell)](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Was ist eine Ressourcengruppe?

Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Azure-Lösung enthält. Die Ressourcengruppe kann alle Ressourcen für die Lösung oder nur die Ressourcen enthalten, die Sie als Gruppe verwalten möchten. Sie entscheiden in Abhängigkeit davon, was für Ihre Organisation am sinnvollsten ist, wie Sie die Ressourcen den Ressourcengruppen zuordnen möchten. Im Allgemeinen fügen Sie einer Ressourcengruppe Ressourcen hinzu, die den gleichen Lebenszyklus haben, damit Sie diese einfacher als Gruppe bereitstellen, aktualisieren und löschen können.

In der Ressourcengruppe werden Metadaten zu den Ressourcen gespeichert. Wenn Sie einen Standort für die Ressourcengruppe angeben, legen Sie also fest, wo die Metadaten gespeichert werden. Aus Compliance-Gründen müssen Sie unter Umständen sicherstellen, dass Ihre Daten in einer bestimmten Region gespeichert werden.

In der Ressourcengruppe werden Metadaten zu den Ressourcen gespeichert. Wenn Sie einen Standort für die Ressourcengruppe angeben, legen Sie fest, wo die Metadaten gespeichert werden.

## <a name="create-resource-groups"></a>Erstellen von Ressourcengruppe

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Ressourcengruppen** aus.

    ![Ressourcengruppe hinzufügen](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Wählen Sie **Hinzufügen**.
4. Geben Sie die folgenden Werte ein:

   - **Abonnement**: Wählen Sie Ihr Azure-Abonnement. 
   - **Ressourcengruppe**: Geben Sie einen neuen Namen für die Ressourcengruppe ein. 
   - **Region**: Wählen Sie einen Azure-Standort aus, z. B. **USA, Mitte**.

     ![Erstellen einer Ressourcengruppe](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Klicken Sie auf **Bewerten + erstellen**.
6. Klicken Sie auf **Erstellen**. Die Erstellung einer Ressourcengruppe dauert ein paar Sekunden.
7. Klicken Sie oben im Menü auf **Aktualisieren**, um die Liste mit Ressourcengruppen zu aktualisieren. Klicken Sie dann auf die neu erstellte Ressourcengruppe, um sie zu öffnen. Alternativ können Sie die neu erstellte Ressourcengruppe auch öffnen, indem Sie oben auf **Benachrichtigungen** (das Glockensymbol) klicken, und dann **Zu Ressourcengruppe wechseln** auswählen.

    ![Screenshot: Zu Ressourcengruppe wechseln](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Ressourcengruppen auflisten

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wenn Sie eine Auflistung der Ressourcengruppen anzeigen lassen wollen, klicken Sie auf **Ressourcengruppen**.

    ![Ressourcengruppen durchsuchen](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Wählen Sie zum Anpassen der für die Ressourcengruppen angezeigten Informationen die Option **Spalten bearbeiten** aus. Im folgenden Screenshot werden die zusätzlichen Spalten gezeigt, die Sie anzeigen lassen können:

## <a name="open-resource-groups"></a>Ressourcengruppen öffnen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie auf **Ressourcengruppen**.
3. Wählen Sie die Ressourcengruppe aus, die Sie öffnen möchten.

## <a name="delete-resource-groups"></a>Ressourcengruppen löschen

1. Öffnen Sie die Ressourcengruppe, die Sie löschen möchten.  Weitere Informationen finden Sie unter [Ressourcengruppen öffnen](#open-resource-groups).
2. Wählen Sie die Option **Ressourcengruppe löschen**.

    ![Screenshot: Azure-Ressourcengruppe löschen](./media/manage-resource-groups-portal/delete-group.png)

Weitere Informationen dazu, in welcher Reihenfolge Ressourcenlöschungen in Azure Resource Manager durchgeführt werden, finden Sie unter [Azure Resource Manager: Löschvorgang von Ressourcengruppen](./resource-group-delete.md).

## <a name="deploy-resources-to-a-resource-group"></a>Bereitstellen von Ressourcen in einer Ressourcengruppe

Nachdem Sie eine Azure Resource Manager-Vorlage erstellt haben, können Sie Ihre Azure-Ressourcen über das Azure-Portal bereitstellen. Eine Anleitung zum Erstellen einer Vorlage finden Sie unter [Schnellstart: Erstellen und Bereitstellen von Azure Resource Manager-Vorlagen über das Azure-Portal](./resource-manager-quickstart-create-templates-use-the-portal.md). Informationen zum Bereitstellen einer Vorlage über das Portal finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure-Portal](resource-group-template-deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Verschieben in eine andere Ressourcengruppe oder ein anderes Abonnement

Sie können Ressourcen aus einer Ressourcengruppe in eine andere Ressourcengruppe verschieben. Weitere Informationen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Ressourcengruppen sperren

Das Sperren verhindert, dass andere Benutzer in Ihrer Organisation versehentlich wichtige Ressourcen löschen oder ändern, z. B. ein Azure-Abonnement, eine Ressourcengruppe oder eine Ressource. 

1. Öffnen Sie die Ressourcengruppe, die Sie löschen möchten.  Weitere Informationen finden Sie unter [Ressourcengruppen öffnen](#open-resource-groups).
2. Wählen Sie im linken Bereich die Option **Sperren** aus.
3. Klicken Sie auf **Hinzufügen**, um der Ressourcengruppe eine Sperre hinzuzufügen.
4. Geben Sie **Sperrenname**, **Sperrentyp** und **Hinweise** ein. Die Sperrentypen beinhalten **Schreibgeschützt** und **Löschen**.

    ![Screenshot: Sperren einer Azure-Ressourcengruppe](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Weitere Informationen finden Sie unter [Sperren von Ressourcen, um unerwartete Änderungen zu verhindern](./resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Hinzufügen von Tags zu Ressourcengruppen

Sie können Ressourcengruppen und Ressourcen Tags zuordnen, um sie logisch zu organisieren. Weitere Informationen finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](./resource-group-using-tags.md#portal).

## <a name="export-resource-groups-to-templates"></a>Exportieren von Ressourcengruppen in Vorlagen

Informationen zum Exportieren von Vorlagen finden Sie unter [Exportieren von einzelnen und mehreren Ressourcen in eine Vorlage – Portal](export-template-portal.md).

### <a name="fix-export-issues"></a>Beheben von Exportproblemen

Nicht alle Ressourcentypen unterstützen die Funktion zum Exportieren von Vorlagen. Exportprobleme treten nur dann auf, wenn Sie aus einer Ressourcengruppe exportieren anstatt aus Ihrem Bereitstellungsverlauf. Falls die letzte Bereitstellung genau den aktuellen Status der Ressourcengruppe widerspiegelt, sollten Sie die Vorlage nicht aus der Ressourcengruppe, sondern aus dem Bereitstellungsverlauf exportieren. Führen Sie den Export aus einer Ressourcengruppe nur dann durch, wenn Sie Änderungen an der Ressourcengruppe vorgenommen haben, die in nicht einer einzelnen Vorlage definiert sind.

Beheben Sie Exportprobleme, indem Sie der Vorlage die fehlenden Ressourcen manuell hinzufügen. Die Fehlermeldung enthält die Ressourcentypen, die nicht exportiert werden konnten. Suchen Sie den entsprechenden Ressourcentyp in der [Vorlagenreferenz](/azure/templates/). Informationen zum manuellen Hinzufügen eines virtuellen Netzwerkgateways finden Sie beispielsweise unter [Microsoft.Network/virtualNetworkGateways template reference](/azure/templates/microsoft.network/virtualnetworkgateways) (Microsoft.Network/virtualNetworkGateways – Vorlagenreferenz). Der Vorlagenverweis liefert den JSON-Code, um die Ressource zu Ihrer Vorlage hinzuzufügen.

Nachdem Sie das JSON-Format für die Ressource abgerufen haben, müssen Sie die Ressourcenwerte abrufen. Verwenden Sie zum Anzeigen der Werte für die Ressource den GET-Vorgang in der REST-API für den Ressourcentyp. Informationen zum Abrufen der Werte für Ihr Gateway für virtuelle Netzwerke finden Sie beispielsweise unter [Virtual Network Gateways - Get](/rest/api/network-gateway/virtualnetworkgateways/get) (Gateways für virtuelle Netzwerke – Abrufen).

## <a name="manage-access-to-resource-groups"></a>Verwalten des Zugriffs auf Ressourcengruppen

Der Zugriff auf Ressourcen in Azure wird mithilfe der [rollenbasierten Zugriffssteuerung](../role-based-access-control/overview.md) (Role-Based Access Control, RBAC) verwaltet. Weitere Informationen finden Sie unter [Verwalten des Zugriffs mithilfe von RBAC und des Azure-Portals](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure Resource Manager finden Sie unter [Übersicht über den Azure Resource Manager](./resource-group-overview.md).
- Informationen zur Resource Manager-Vorlagensyntax finden Sie unter [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](./resource-group-authoring-templates.md).
- Informationen zum Entwickeln von Vorlagen finden Sie in den [Schritt-für-Schritt-Tutorials](/azure/azure-resource-manager/).
- Informationen zum Anzeigen der Vorlagenschemas für Azure Resource Manager finden Sie in der [Referenz zu Vorlagen](/azure/templates/).
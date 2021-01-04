---
title: Markieren eines virtuellen Computers mithilfe des Azure-Portals
description: Erfahren Sie etwas über das Markieren eines virtuellen Computers mithilfe des Azure-Portals.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 7c3e779c152a967452f86b55f06d38076102085c
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594871"
---
# <a name="tagging-a-vm-using-the-portal"></a>Markieren eines virtuellen Computers über das Portal

In diesem Artikel wird das Hinzufügen von Tags zu einer VM über das Portal beschrieben. Tags sind benutzerdefinierte Schlüssel-Wert-Paare, die direkt auf einer Ressource oder einer Ressourcengruppe platziert werden können. Azure unterstützt derzeit bis zu 50 Tags pro Ressource und Ressourcengruppe. Tags können zum Zeitpunkt der Erstellung auf einer Ressource platziert werden oder zu einer vorhandenen Ressource hinzugefügt werden.


1. Navigieren Sie im Portal zu Ihrer App.
1. Wählen Sie in **Grundlagen** die Option **Klicken Sie hier, um Markierungen hinzuzufügen** aus.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="Screenshot des Abschnitts „Grundlagen“ der VM-Seite.":::

1. Fügen Sie einen Wert für **Name** und **Wert** hinzu, und wählen Sie dann **Speichern** aus.

    :::image type="content" source="media/tag/key-value.png" alt-text="Screenshot der Seite zum Hinzufügen eines Schlüssel-Wert-Paars als Tag.":::



**Nächste Schritte**

- Weitere Informationen zum Markieren Ihrer Azure-Ressourcen finden Sie unter [Übersicht über den Azure Resource Manager](../azure-resource-manager/management/overview.md) und [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md).
- Informationen dazu, wie Tags Ihnen helfen können, die Verwendung Ihrer Azure-Ressourcen zu verwalten, finden Sie unter [Informationen zu Ihrer Rechnung für Microsoft Azure](../cost-management-billing/understand/review-individual-bill.md) und [Gewinnen von Einblicken in den Ressourcenverbrauch unter Microsoft Azure](../cost-management-billing/manage/usage-rate-card-overview.md).
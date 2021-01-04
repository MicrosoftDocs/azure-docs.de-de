---
title: Rollen und Berechtigungen für Azure Data Factory
description: Dieser Artikel beschreibt die Rollen und Berechtigungen, die erforderlich sind, um Data Factorys zu erstellen und mit untergeordneten Ressourcen zu arbeiten.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: dcstwh
ms.author: weetok
manager: anandsub
ms.openlocfilehash: 70be8d8be48f2b1e1cc275c06e4abff09e3e62f6
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498573"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Rollen und Berechtigungen für Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Dieser Artikel beschreibt die Rollen, die zum Erstellen und Verwalten von Azure Data Factory-Ressourcen erforderlich sind, sowie die Berechtigungen, die durch diese Rollen gewährt werden.

## <a name="roles-and-requirements"></a>Rollen und Anforderungen

Damit Sie Data Factory-Instanzen erstellen können, muss das Benutzerkonto, mit dem Sie sich bei Azure anmelden, ein Mitglied der Rolle *Mitwirkender*, *Besitzer* oder ein *Administrator* des Azure-Abonnements sein. Klicken Sie im Azure-Portal in der oberen rechten Ecke auf Ihren Benutzernamen und dann auf **Berechtigungen**, um Ihre Berechtigungen im Abonnement anzuzeigen. Wenn Sie Zugriff auf mehrere Abonnements besitzen, wählen Sie das entsprechende Abonnement aus. 

Für das Erstellen und Verwalten von untergeordneten Ressourcen für Data Factory – z.B. Datasets, verknüpfte Dienste, Pipelines, Trigger und Integration Runtimes – gelten die folgenden Anforderungen:
- Für das Erstellen und Verwalten von untergeordneten Ressourcen im Azure-Portal müssen Sie auf **Ressourcengruppenebene** oder höher Mitglied der Rolle **Mitwirkender von Data Factory** sein.
- Zum Erstellen und Verwalten von untergeordneten Ressourcen mit PowerShell oder dem SDK auf Ressourcenebene oder höher ist die Rolle **Mitwirkender** ausreichend.

Eine Beispielanleitung zum Hinzufügen eines Benutzers zu einer Rolle finden Sie im Artikel [Hinzufügen oder Ändern von Azure-Administratorrollen, die das Abonnement oder die Dienste verwalten](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="set-up-permissions"></a>Einrichten von Berechtigungen

Nachdem Sie eine Data Factory erstellt haben, möchten Sie wahrscheinlich anderen Benutzern das Arbeiten mit dieser Data Factory gestatten. Um anderen Benutzern Zugriff zu gewähren, müssen Sie diese Benutzer zur integrierten Rolle **Data Factory-Mitwirkender** in der **Ressourcengruppe** hinzufügen, die die Data Factory enthält.

### <a name="scope-of-the-data-factory-contributor-role"></a>Geltungsbereich der Rolle „Data Factory-Mitwirkender“

Die Mitgliedschaft in der Rolle **Data Factory-Mitwirkender** ermöglicht Benutzern Folgendes:
- Erstellen, Bearbeiten und Löschen von Data Factorys und untergeordneten Ressourcen – z.B. Datasets, verknüpfte Dienste, Pipelines, Trigger und Integration Runtimes.
- Bereitstellen von Resource Manager-Vorlagen. Die Resource Manager-Bereitstellung ist die Bereitstellungsmethode, die von Data Factory im Azure-Portal verwendet wird.
- Verwalten von App Insights-Warnungen für eine Data Factory.
- Erstellen von Supporttickets.

Weitere Informationen zu dieser Rolle finden Sie unter [Mitwirkender von Data Factory](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Resource Manager-Vorlagenbereitstellung

Die Rolle **Data Factory-Mitwirkender** auf Ressourcengruppenebene oder höher ermöglicht Benutzern die Bereitstellung von Resource Manager-Vorlagen. Daher können Mitglieder dieser Rolle Resource Manager-Vorlagen verwenden, um sowohl Data Factorys also auch deren untergeordnete Ressourcen bereitzustellen, z.B. Datasets, verknüpfte Dienste, Pipelines, Trigger und Integration Runtimes. Die Mitgliedschaft in dieser Rolle ermöglicht den Benutzern nicht die Erstellung anderer Ressourcen.

Berechtigungen für Azure Repos und GitHub sind unabhängig von Data Factory-Berechtigungen. Daher kann ein Benutzer mit Berechtigungen für ein Repository, der nur Mitglied der Rolle „Leser“ ist, untergeordnete Data Factory-Ressourcen bearbeiten und Änderungen an das Repository committen, diese Änderungen aber nicht veröffentlichen.

> [!IMPORTANT]
> Die Resource Manager-Vorlagenbereitstellung mit der Rolle **Data Factory-Mitwirkender** erhöht Ihre Berechtigungen nicht. Ein Beispiel: Wenn Sie eine Vorlage bereitstellen, die einen virtuellen Azure-Computer erstellt, und nicht über die Berechtigung zum Erstellen virtueller Computer verfügen, tritt bei der Bereitstellung ein Autorisierungsfehler auf.

### <a name="custom-scenarios-and-custom-roles"></a>Benutzerdefinierte Szenarien und benutzerdefinierte Rollen

Manchmal müssen Sie verschiedenen Data Factory-Benutzern den Zugriff auf verschiedenen Ebenen gewähren. Beispiel:
- Sie benötigen eine Gruppe, deren Mitglieder nur Berechtigungen für eine bestimmte Data Factory erhalten sollen.
- Möglicherweise benötigen Sie auch eine Gruppe, deren Mitglieder eine Data Factory oder mehrere Factorys überwachen, aber nicht ändern dürfen.

Solche spezifischen Szenarien lassen sich umsetzen, indem Sie benutzerdefinierte Rollen erstellen und diesen Rollen Benutzer zuweisen. Weitere Informationen zu benutzerdefinierten Rollen finden Sie unter [Benutzerdefinierte Rollen in Azure](..//role-based-access-control/custom-roles.md).

Hier finden Sie einige Beispiele, die zeigen, was sich mit benutzerdefinierten Rollen alles erreichen lässt:

- Sie können es einem Benutzer ermöglichen, eine Data Factory in einer Ressourcengruppe im Azure-Portal zu erstellen, zu bearbeiten oder zu löschen.

  Sie können einem Benutzer die integrierte Rolle **Data Factory-Mitwirkender** auf Ressourcengruppenebene zuweisen. Wenn Sie den Zugriff auf jede beliebige Data Factory in einem Abonnement erlauben möchten, weisen Sie die Rolle auf Abonnementebene zu.

- Sie können es einem Benutzer ermöglichen, eine Data Factory anzuzeigen (zu lesen) und zu überwachen, sie aber nicht zu bearbeiten oder zu ändern.

  Sie können einem Benutzer die integrierte Rolle **Leser** in der Data Factory-Ressource zuweisen.

- Sie können es einem Benutzer ermöglichen, die einzelne Data Factory im Azure-Portal zu bearbeiten.

  Dieses Szenario erfordert zwei Rollenzuweisungen.

  1. Weisen Sie die integrierte Rolle **Mitwirkender** auf Data Factory-Ebene zu.
  2. Erstellen Sie eine benutzerdefinierte Rolle mit der Berechtigung **Microsoft.Resources/deployments/** . Sie können einem Benutzer diese benutzerdefinierte Rolle auf Ressourcengruppenebene zuweisen.

- Sie können es einem Benutzer ermöglichen, Verbindungen in einem verknüpften Dienst zu testen oder die Vorschau der Daten in einem Dataset anzuzeigen.

    Erstellen Sie eine benutzerdefinierte Rolle mit Berechtigungen für die folgenden Aktionen: **Microsoft.DataFactory/factories/getFeatureValue/read** und **Microsoft.DataFactory/factories/getDataPlaneAccess/action**. Weisen Sie diese benutzerdefinierte Rolle der Data Factory-Ressource für den Benutzer zu.

- Sie können es einem Benutzer ermöglichen, eine Data Factory über PowerShell oder das SDK zu aktualisieren, nicht aber über das Azure-Portal.

  Sie können einem Benutzer die integrierte Rolle **Mitwirkender** in der Data Factory-Ressource zuweisen. Mit dieser Rolle kann der Benutzer die Ressourcen im Azure-Portal anzeigen, er kann aber nicht auf die Schaltflächen **Veröffentlichen** und **Alle veröffentlichen** zugreifen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Rollen in Azure: [Grundlegendes zu Rollendefinitionen](../role-based-access-control/role-definitions.md)

- Weitere Informationen zur Rolle **Data Factory-Mitwirkender**: [Mitwirkender von Data Factory](../role-based-access-control/built-in-roles.md#data-factory-contributor)

---
title: Erstellen eines neuen Zugriffspakets in der Berechtigungsverwaltung – Azure AD
description: Erfahren Sie, wie Sie ein neues Zugriffspaket für freizugebende Ressourcen in der Azure Active Directory-Berechtigungsverwaltung erstellen.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2ce3b362d02e7acb0a11e6d93b8e94ca8e4d04e
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903529"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Erstellen eines neuen Zugriffspakets in der Azure AD-Berechtigungsverwaltung

Mit einem Zugriffspaket können Sie Ressourcen und Richtlinien einmalig so einrichten, dass der Zugriff für die gesamte Lebensdauer des Zugriffspakets automatisch verwaltet wird. In diesem Artikel wird das Erstellen eines neuen Zugriffspakets beschrieben.

## <a name="overview"></a>Übersicht

Alle Zugriffspakete müssen in einem Container abgelegt werden, der als „Katalog“ bezeichnet wird. In einem Katalog wird definiert, welche Ressourcen Sie Ihrem Zugriffspaket hinzufügen können. Wenn Sie keinen Katalog angeben, wird Ihr Zugriffspaket in den Katalog „Allgemein“ eingefügt. Derzeit können Sie vorhandene Pakete nicht in einen anderen Katalog verschieben.

Wenn Sie ein Zugriffspaket-Manager sind, können Sie keine eigenen Ressourcen zu einem Katalog hinzufügen. Sie können nur die im Katalog verfügbaren Ressourcen verwenden. Wenn Sie einem Katalog Ressourcen hinzufügen müssen, können Sie den Katalogbesitzer darum bitten.

Alle Zugriffspakete müssen mindestens eine Richtlinie aufweisen. Richtlinien geben an, wer das Zugriffspaket anfordern kann. Außerdem sind darin die Genehmigungs- und Lebenszykluseinstellungen festgelegt. Beim Erstellen eines neuen Zugriffspakets können Sie eine anfängliche Richtlinie für in Ihrem Verzeichnis enthaltene Benutzer, nicht in Ihrem Verzeichnis enthaltene Benutzer und nur direkte Administratorzuweisungen erstellen oder die Richtlinie später erstellen.

![Erstellen eines Zugriffspakets](./media/entitlement-management-access-package-create/access-package-create.png)

Nachfolgend sind die grundlegenden Schritte zum Erstellen eines neuen Zugriffspakets aufgeführt.

1. Starten Sie in Identity Governance den Prozess zum Erstellen eines neuen Zugriffspakets.

1. Wählen Sie den Katalog aus, in dem das Zugriffspaket erstellt werden soll.

1. Fügen Sie Ihrem Zugriffspaket Ressourcen aus dem Katalog hinzu.

1. Weisen Sie für jede Ressource Ressourcenrollen zu.

1. Geben Sie Benutzer an, die Zugriff anfordern können.

1. Legen Sie die gewünschten Genehmigungseinstellungen fest.

1. Legen Sie die Lebenszykluseinstellungen fest.

## <a name="start-new-access-package"></a>Starten eines neuen Zugriffspakets

**Erforderliche Rolle:** Globaler Administrator, Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Klicken Sie auf **Azure Active Directory**, und klicken Sie dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Zugriffspakete**.

1. Klicken Sie auf **Neues Zugriffspaket**.
   
    ![Berechtigungsverwaltung im Azure-Portal](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Grundlagen

Auf der Registerkarte **Grundlagen** geben Sie dem Zugriffspaket einen Namen und geben der Katalog an, in dem es erstellt werden soll.

1. Geben Sie einen Anzeigenamen und eine Beschreibung für das Zugriffspaket ein. Benutzer sehen diese Informationen, wenn sie eine Anforderung für das Zugriffspaket senden.

1. Wählen Sie in der Dropdownliste **Katalog** den Katalog aus, in dem das Zugriffspaket erstellt werden soll. Beispielsweise verfügen Sie möglicherweise über einen Katalogbesitzer, der alle Marketingressourcen verwaltet, die angefordert werden können. In diesem Fall können Sie den Marketingkatalog auswählen.

    Es werden nun Kataloge angezeigt, in denen Sie Zugriffspakete erstellen dürfen. Um ein Zugriffspaket in einem vorhandenen Katalog erstellen zu können, müssen Sie ein globaler Administrator, Benutzeradministrator, Katalogbesitzer oder aber Zugriffspaket-Manager für diesen Katalog sein.

    ![Zugriffspaket – Grundlagen](./media/entitlement-management-access-package-create/basics.png)

    Wenn Sie ein globaler Administrator, Benutzeradministrator oder Katalogersteller sind und Ihr Zugriffspaket in einem neuen Katalog erstellen möchten, der nicht aufgeführt ist, klicken Sie auf **Neuen Katalog erstellen**. Geben Sie einen Namen und eine Beschreibung für den Katalog ein, und klicken Sie dann auf **Erstellen**.

    Das Zugriffspaket, das Sie erstellen, und alle darin enthaltenen Ressourcen werden dem neuen Katalog hinzugefügt. Sie können später weitere Katalogbesitzer hinzufügen.

1. Klicken Sie auf **Weiter**.

## <a name="resource-roles"></a>Ressourcenrollen

Auf der Registerkarte **Ressourcenrollen** wählen Sie die Ressourcen aus, die in das Zugriffspaket aufgenommen werden sollen. Benutzern, die das Zugriffspaket anfordern und erhalten, werden alle Ressourcenrollen im Zugriffspaket zugewiesen.

1. Klicken Sie auf den hinzuzufügenden Ressourcentyp (**Gruppen und Teams**, **Apps** oder **SharePoint-Websites**).

1. Wählen Sie in der Liste im angezeigten Auswahlbereich Ressourcen aus.

    ![Zugriffspaket – Ressourcenrollen](./media/entitlement-management-access-package-create/resource-roles.png)

    Wenn Sie das Zugriffspaket im Katalog „Allgemein“ oder einem neuen Katalog erstellen, können Sie beliebige Ressourcen aus dem Verzeichnis in Ihrem Besitz auswählen. Sie müssen mindestens als globaler Administrator, Benutzeradministrator oder Katalogersteller fungieren.

    Wenn Sie das Zugriffspaket in einem vorhandenen Katalog erstellen, können Sie jede bereits im Katalog vorhandene Ressource auswählen, ohne deren Besitzer zu sein.

    Als globaler Administrator, Benutzeradministrator oder Katalogbesitzer haben Sie zusätzlich die Möglichkeit, noch nicht im Katalog enthaltene Ressourcen auszuwählen, deren Besitzer Sie sind. Wenn Sie Ressourcen auswählen, die derzeit nicht im ausgewählten Katalog vorhanden sind, werden diese Ressourcen ebenfalls dem Katalog hinzugefügt, sodass andere Katalogadministratoren Zugriffspakete damit erstellen können. Wenn Sie alle Ressourcen anzeigen möchten, die dem Katalog hinzugefügt werden können, aktivieren Sie das Kontrollkästchen **Alle anzeigen** am oberen Rand des Auswahlbereichs. Wenn Sie nur Ressourcen auswählen möchten, die derzeit im ausgewählten Katalog vorhanden sind, lassen Sie das Kontrollkästchen **Alle anzeigen** deaktiviert (Standardeinstellung).

1. Nachdem Sie die Ressourcen ausgewählt haben, wählen Sie in der Liste **Rolle** die Rolle aus, die Benutzern für die Ressource zugewiesen werden soll.

    ![Zugriffspaket – Ressourcenrollenauswahl](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Klicken Sie auf **Weiter**.

>[!NOTE]
>Sie können einem Katalog und einem Zugriffspaket dynamische Gruppen hinzufügen. Sie können jedoch nur die Rolle „Besitzer“ auswählen, wenn Sie in einem Zugriffspaket eine dynamische Gruppenressource verwalten.

## <a name="requests"></a>Requests

Auf der Registerkarte **Anforderungen** erstellen Sie die erste Richtlinie, um anzugeben, wer das Zugriffspaket anfordern kann, und die Genehmigungseinstellungen festzulegen. Später können Sie weitere Anforderungsrichtlinien erstellen, um zusätzlichen Gruppen von Benutzern mit eigenen Genehmigungseinstellungen das Anfordern des Zugriffspakets zu ermöglichen.

![Zugriffspaket – Registerkarte „Anforderungen“](./media/entitlement-management-access-package-create/requests.png)

Führen Sie die Schritte in einem der folgenden Abschnitte aus, je nachdem, wer in der Lage sein soll, dieses Zugriffspaket anzufordern.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Bewerten + erstellen

Auf der Registerkarte **Bewerten + erstellen** können Sie Ihre Einstellungen überprüfen und nach Validierungsfehlern suchen.

1. Überprüfen der Einstellungen für das Zugriffspaket

    ![Zugriffspaket – Einstellung „Richtlinie aktivieren“](./media/entitlement-management-access-package-create/review-create.png)

1. Klicken Sie auf **Erstellen**, um das Zugriffspaket zu erstellen.

    Das neue Zugriffspaket wird in der Liste der Zugriffspakete aufgeführt.

## <a name="creating-an-access-package-programmatically"></a>Programmgesteuertes Erstellen eines Zugriffspakets

Sie können auch mithilfe von Microsoft Graph ein Zugriffspaket erstellen.  Ein Benutzer in einer passenden Rolle mit einer Anwendung, die über die delegierte `EntitlementManagement.ReadWrite.All`-Berechtigung verfügt, kann die API aufrufen, um

1. [die accessPackageResources im Katalog aufzulisten](/graph/api/accesspackagecatalog-list?tabs=http&view=graph-rest-beta) und [eine accessPackageResourceRequest zu erstellen](/graph/api/accesspackageresourcerequest-post?tabs=http&view=graph-rest-beta), die sich auf alle Ressourcen bezieht, die noch nicht im Katalog enthalten sind.
1. [Auflisten der accessPackageResourceRoles](/graph/api/accesspackage-list-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) für jede accessPackageResource in einem accessPackageCatalog. Diese Rollenliste wird dann zum Auswählen einer Rolle verwendet, wenn anschließend ein accessPackageResourceRoleScope erstellt wird.
1. [Erstellen eines accessPackage](/graph/tutorial-access-package-api?view=graph-rest-beta).
1. [Erstellen einer accessPackageAssignmentPolicy](/graph/api/accesspackageassignmentpolicy-post?tabs=http&view=graph-rest-beta).
1. [Erstellen eines accessPackageResourceRoleScope](/graph/api/accesspackage-post-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) für jede Ressourcenrolle, die im Zugriffspaket benötigt wird.

## <a name="next-steps"></a>Nächste Schritte

- [Teilen des Links zum Anfordern eines Zugriffspakets](entitlement-management-access-package-settings.md)
- [Ändern von Ressourcenrollen für ein Zugriffspaket](entitlement-management-access-package-resources.md)
- [Direktes Zuweisen eines Benutzers zum Zugriffspaket](entitlement-management-access-package-assignments.md)

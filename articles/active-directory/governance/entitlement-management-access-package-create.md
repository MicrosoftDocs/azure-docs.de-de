---
title: Erstellen eines neuen Zugriffspakets in der Azure AD-Berechtigungsverwaltung (Vorschauversion) – Azure Active Directory
description: Erfahren Sie, wie Sie ein neues Zugriffspaket für freizugebende Ressourcen in der Azure Active Directory-Berechtigungsverwaltung (Vorschauversion) erstellen.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 312658bd653d94f3e1a32204b3c5ae4ae290558e
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65832792"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>Erstellen eines neuen Zugriffspakets in der Azure AD-Berechtigungsverwaltung (Vorschauversion)

> [!IMPORTANT]
> Die Berechtigungsverwaltung von Azure Active Directory (Azure AD) befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mit einem Zugriffspaket können Sie Ressourcen und Richtlinien einmalig so einrichten, dass der Zugriff für die gesamte Lebensdauer des Zugriffspakets automatisch verwaltet wird. In diesem Artikel wird das Erstellen eines neuen Zugriffspakets beschrieben.

## <a name="overview"></a>Übersicht

Alle Zugriffspakete müssen in einem Container abgelegt werden, der als „Katalog“ bezeichnet wird. In einem Katalog wird definiert, welche Ressourcen Sie Ihrem Zugriffspaket hinzufügen können. Wenn Sie keinen Katalog angeben, wird Ihr Zugriffspaket in den Katalog „Allgemein“ eingefügt. Derzeit können Sie vorhandene Pakete nicht in einen anderen Katalog verschieben.

Alle Zugriffspakete müssen mindestens eine Richtlinie aufweisen. Richtlinien geben an, wer das Zugriffspaket anfordern kann, sowie Genehmigungs- und Ablaufeinstellungen. Beim Erstellen eines neuen Zugriffspakets können Sie eine anfängliche Richtlinie für in Ihrem Verzeichnis enthaltene Benutzer, nicht in Ihrem Verzeichnis enthaltene Benutzer und nur direkte Administratorzuweisungen erstellen oder die Richtlinie später erstellen.

Im folgenden Diagramm wird der allgemeine Prozess beim Erstellen eines neuen Zugriffspakets veranschaulicht.

![Erstellen eines Zugriffspakets](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>Starten eines neuen Zugriffspakets

**Erforderliche Rolle:** Benutzeradministrator oder Katalogbesitzer

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Klicken Sie auf **Azure Active Directory**, und klicken Sie dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Zugriffspakete**.

    ![Berechtigungsverwaltung im Azure-Portal](./media/entitlement-management-shared/elm-access-packages.png)

1. Klicken Sie auf **Neues Zugriffspaket**.

## <a name="basics"></a>Grundlagen

Auf der Registerkarte **Grundlagen** geben Sie dem Zugriffspaket einen Namen und geben der Katalog an, in dem es erstellt werden soll.

1. Geben Sie einen Anzeigenamen und eine Beschreibung für das Zugriffspaket ein. Benutzer sehen diese Informationen, wenn sie eine Anforderung für das Zugriffspaket senden.

1. Wählen Sie in der Dropdownliste **Katalog** den Katalog aus, in dem das Zugriffspaket erstellt werden soll. Beispielsweise verfügen Sie möglicherweise über einen Katalogbesitzer, der alle Marketingressourcen verwaltet, die angefordert werden können. In diesem Fall können Sie den Marketingkatalog auswählen.

    Es werden nun Kataloge angezeigt, in denen Sie Zugriffspakete erstellen dürfen. Um ein Zugriffspaket in einem vorhandenen Katalog zu erstellen, müssen Sie mindestens Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager sein.

    ![Zugriffspaket – Grundlagen](./media/entitlement-management-access-package-create/basics.png)

    Wenn Sie das Zugriffspaket in einem neuen Katalog erstellen möchten, klicken Sie auf **Neu erstellen**. Geben Sie einen Namen und eine Beschreibung für den Katalog ein, und klicken Sie dann auf **Erstellen**.

    Das Zugriffspaket, das Sie erstellen, und alle darin enthaltenen Ressourcen werden dem neuen Katalog hinzugefügt. Darüber hinaus werden Sie automatisch der erste Besitzer des Katalogs. Sie können weitere Katalogbesitzer hinzufügen.

    Um einen neuen Katalog erstellen zu können, müssen Sie mindestens Benutzeradministrator oder Katalogersteller sein.

1. Klicken Sie auf **Weiter**.

## <a name="resource-roles"></a>Ressourcenrollen

Auf der Registerkarte **Ressourcenrollen** wählen Sie die Ressourcen aus, die in das Zugriffspaket aufgenommen werden sollen.

1. Klicken Sie auf den Ressourcentyp, den Sie hinzufügen möchten (**Gruppen**, **Anwendungen** oder **SharePoint-Websites**).

1. Wählen Sie in der Liste im angezeigten Auswahlbereich Ressourcen aus.

    ![Zugriffspaket – Ressourcenrollen](./media/entitlement-management-access-package-create/resource-roles.png)

    Wenn Sie das Zugriffspaket im Katalog „Allgemein“ oder einem neuen Katalog erstellen, können Sie beliebige Ressourcen aus dem Verzeichnis in Ihrem Besitz auswählen. Sie müssen mindestens Benutzeradministrator oder Katalogersteller sein.

    Wenn Sie das Zugriffspaket in einem vorhandenen Katalog erstellen, können Sie jede bereits im Katalog vorhandene Ressource auswählen, ohne deren Besitzer zu sein.

    Wenn Sie ein Benutzeradministrator oder Katalogbesitzer sind, können Sie als zusätzliche Option im Katalog noch nicht enthaltene Ressourcen in Ihrem Besitz auswählen. Wenn Sie Ressourcen auswählen, die derzeit nicht im ausgewählten Katalog vorhanden sind, werden diese Ressourcen ebenfalls dem Katalog hinzugefügt, sodass andere Katalogadministratoren Zugriffspakete damit erstellen können. Wenn Sie nur Ressourcen auswählen möchten, die derzeit im ausgewählten Katalog vorhanden sind, aktivieren Sie am oberen Rand des Auswahlbereichs das Kontrollkästchen **Nur Elemente im Katalog anzeigen**.

1. Nachdem Sie die Ressourcen ausgewählt haben, wählen Sie in der Liste **Rolle** die Rolle aus, die Benutzern für die Ressource zugewiesen werden soll.

    ![Zugriffspaket – Ressourcenrollenauswahl](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Klicken Sie auf **Weiter**.

## <a name="policy"></a>Richtlinie

Auf der Registerkarte **Richtlinie** erstellen Sie die erste Richtlinie, die angibt, wer das Zugriffspaket anfordern kann, sowie Genehmigungs- und Ablaufeinstellungen. Später können Sie weitere Richtlinien erstellen, um anderen Gruppen von Benutzern das Anfordern des Zugriffspakets zu genehmigen. Diese weisen auch eigene Genehmigungs- und Ablaufeinstellungen auf. Sie können die Richtlinie auch später erstellen.

1. Legen Sie die Option **Erste Richtlinie erstellen** auf **Jetzt** oder **Später** fest.

    ![Zugriffspaket – Richtlinie](./media/entitlement-management-access-package-create/policy.png)

1. Wenn Sie **Später** auswählen, fahren Sie weiter unten mit dem Abschnitt [Bewerten + erstellen](#review--create) fort, um das Zugriffspaket zu erstellen.

1. Wenn Sie **Jetzt** auswählen, führen Sie die Schritte in einem der nachstehenden Abschnitt zu Richtlinien aus.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>Bewerten + erstellen

Auf der Registerkarte **Bewerten + erstellen** können Sie Ihre Einstellungen überprüfen und nach Validierungsfehlern suchen.

1. Überprüfen der Einstellungen für das Zugriffspaket

    ![Zugriffspaket – Richtlinie – Einstellung „Richtlinie aktivieren“](./media/entitlement-management-access-package-create/review-create.png)

1. Klicken Sie auf **Erstellen**, um das Zugriffspaket zu erstellen.

    Das neue Zugriffspaket wird in der Liste der Zugriffspakete aufgeführt.

## <a name="next-steps"></a>Nächste Schritte

- [Bearbeiten und Verwalten eines vorhandenen Zugriffspakets](entitlement-management-access-package-edit.md)
- [Erstellen und Verwalten eines Katalogs](entitlement-management-catalog-create.md)

---
title: Verwalten von Benutzerkonten in Azure API Management | Microsoft Docs
description: Erfahren Sie, wie Sie Benutzer in Azure API Management erstellen und einladen.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: d9cc5da0cd2936c1ac79a308366b8164548cb202
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241720"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Verwalten von Benutzerkonten in Azure API Management

In API Management sind Entwickler die Benutzer der APIs, die Sie mit API Management verfügbar machen. Diese Anleitung beschreibt die Erstellung und Einladung von Entwicklern zur Nutzung Ihrer API und der Produkte, die Sie über die API Management-Instanz bereitgestellt haben. Informationen zum programmgesteuerten Verwalten von Benutzerkonten finden Sie in der Dokumentation zur [Benutzerentität](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user) in der [Referenz zu API Management REST](/rest/api/apimanagement/).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie Aufgaben in diesem Artikel aus: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-developer"></a>Erstellen eines neuen Entwicklers

Um einen neuen Benutzer hinzuzufügen, führen Sie die Schritte in diesem Abschnitt aus:

1. Wählen Sie auf der linken Seite des Bildschirms die Registerkarte **Gruppen** aus.
2. Klicken Sie auf **+ Hinzufügen**.
3. Geben Sie die erforderlichen Informationen für den Benutzer ein.
4. Klicken Sie auf **Hinzufügen**.

    ![Hinzufügen eines neuen Benutzers](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Neu erstellte Entwicklerkonten sind standardmäßig **Aktiv** und werden der Gruppe **Entwickler** zugewiesen. Entwicklerkonten mit dem Status **Aktiv** können sämtliche APIs aufrufen, die sie abonniert haben. Informationen zum Zuordnen des neu erstellten Entwicklers zu weiteren Gruppen finden Sie unter [Zuordnen von Entwicklern zu Gruppen][How to associate groups with developers].

## <a name="invite-developer"></a>Einladen eines Entwicklers
Um einen Entwickler einzuladen, führen Sie die Schritte in diesem Abschnitt aus:

1. Wählen Sie auf der linken Seite des Bildschirms die Registerkarte **Gruppen** aus.
2. Klicken Sie auf **+Einladen**.

Eine Bestätigungsmeldung wird angezeigt. Der neu eingeladene Entwickler wird jedoch erst in der Liste angezeigt, wenn die Einladung angenommen wurde. 

Wenn Sie einen Entwickler einladen, wird eine E-Mail an diesen Entwickler geschickt. Diese E-Mail wird anhand einer Vorlage erstellt und ist konfigurierbar. Weitere Informationen finden Sie unter [Konfigurieren von E-Mail-Vorlagen][Configure email templates].

Nachdem die Einladung angenommen wurde, ist das Konto aktiv.

## <a name="block-developer"></a> Deaktivieren oder erneutes Aktivieren eines Entwicklerkontos

Neu erstellte oder eingeladene Entwicklerkonten sind standardmäßig **Aktiv**. Klicken Sie auf **Blockieren**, um ein Entwicklerkonto zu deaktivieren. Klicken Sie auf **Aktivieren**, um ein blockiertes Entwicklerkonto erneut zu aktivieren. Blockierte Entwicklerkonten haben keinen Zugriff auf das Entwicklerportal und können keine APIs aufrufen. Klicken Sie zum Löschen eines Benutzerkontos auf **Löschen**.

Um einen Benutzer zu blockieren, führen Sie die folgenden Schritte aus.

1. Wählen Sie auf der linken Seite des Bildschirms die Registerkarte **Gruppen** aus.
2. Klicken Sie auf den Benutzer, den Sie blockieren möchten.
3. Klicken Sie auf **Blockieren**.

## <a name="reset-a-user-password"></a>Zurücksetzen des Benutzerkennworts

Informationen zur programmgesteuerten Verwendung von Benutzerkonten finden Sie in der Dokumentation zur Benutzerentität in der Referenz zur [API Management REST-API](/rest/api/apimanagement/). Um das Kennwort eines Benutzerkontos auf einen bestimmten Wert zurückzusetzen, können Sie den Vorgang [Aktualisieren eines Benutzers](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) verwenden und das gewünschte Kennwort angeben.

## <a name="next-steps"> </a>Nächste Schritte
Nachdem Sie das Entwicklerkonto erstellt haben, können Sie das Konto zu Rollen zuweisen und Produkte und APIs abonnieren. Weitere Informationen finden Sie unter [Erstellen und Verwenden von Gruppen][How to create and use groups].

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates

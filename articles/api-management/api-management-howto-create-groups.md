---
title: Verwalten von Entwicklerkonten in Azure API Management mithilfe von Gruppen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Entwicklerkonten in Azure API Management mithilfe von Konten verwalten.
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
ms.openlocfilehash: 5392cf5463dd0b11d1ce53856c8e4e2e788892b0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60658436"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Erstellen und Verwenden von Gruppen für Entwicklerkonten in Azure API Management

In API Management werden Gruppen verwendet, um die Sichtbarkeit von Produkten für Entwickler zu verwalten. Produkte gewähren Sichtbarkeit für Gruppen, und Entwickler können alle Produkte anzeigen und abonnieren, die für die Gruppen sichtbar sind, in denen sie Mitglied sind. 

API Management umfasst folgende unveränderliche Systemgruppen:

* **Administratoren** – Azure-Abonnementadministratoren sind Mitglieder dieser Gruppe. Administratoren verwalten API Management-Dienstinstanzen und erstellen die APIs, Operationen und Produkte, die von den Entwicklern verwendet werden.
* **Entwickler** – Zu dieser Gruppe gehören authentifizierte Benutzer des Entwicklerportals. Entwickler sind die Kunden, die Anwendungen unter Verwendung Ihrer APIs erstellen. Entwickler erhalten Zugriff zum Entwicklerportal und erstellen Anwendungen, die die Operationen einer API aufrufen.
* **Gäste** – Nicht authentifizierte Benutzer wie z. B. potenzielle Kunden, die das Entwicklerportal einer API Management-Instanz besuchen, fallen in diese Gruppe. Sie können diesen Benutzern schreibgeschützten Zugriff gewähren, z.B. um die APIs anzuzeigen, jedoch nicht aufrufen zu können.

Zusätzlich zu diesen Systemgruppen können Administratoren benutzerdefinierte Gruppen erstellen oder [externe Gruppen in zugeordneten Azure Active Directory-Mandanten verwenden][leverage external groups in associated Azure Active Directory tenants]. Benutzerdefinierte und externe Gruppen können gemeinsam mit Systemgruppen verwendet werden, um API-Produkte für Entwickler sichtbar zu machen und ihnen den Zugriff auf die API-Produkte zu ermöglichen. Beispielsweise können Sie eine benutzerdefinierte Gruppe für Entwickler eines spezifischen Partnerunternehmens erstellen und diesen Entwicklern Zugriff auf die APIs über ein Produkt erteilen, das nur die relevanten APIs enthält. Ein Benutzer kann Mitglied von mehr als einer Gruppe sein.

Diese Anleitung beschreibt, wie Administrator einer API Management-Instanz neue Gruppen hinzufügen und diese zu Produkten und Entwicklern zuordnen können.

Zusätzlich zum Erstellen und Verwalten von Gruppen im Herausgeberportal können Sie Ihre Gruppen mithilfe der Entität [Gruppe](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) der API Management-REST-API erstellen und verwalten.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie Aufgaben in diesem Artikel aus: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-group"></a>Erstellen einer Gruppe

In diesem Abschnitt wird gezeigt, wie Sie Ihrem API Management-Konto eine neue Gruppe hinzufügen.

1. Wählen Sie auf der linken Seite des Bildschirms die Registerkarte **Gruppen**.
2. Klicken Sie auf **+Hinzufügen**.
3. Geben Sie einen eindeutigen Namen und eine optionale Beschreibung für die Gruppe ein.
4. Klicken Sie auf **Erstellen**.

    ![Neue Gruppe hinzufügen](./media/api-management-howto-create-groups/groups001.png)

Nachdem die Gruppe erstellt wurde, wird sie der Liste **Gruppen** hinzugefügt. <br/>Klicken Sie auf den Namen der Gruppe und auf **Einstellungen**, um den **Namen** oder die **Beschreibung** zu bearbeiten.<br/>Zum Löschen der Gruppe klicken Sie auf den Namen der Gruppe, und drücken Sie **Löschen**.

Nachdem Sie die Gruppe erstellt haben, können Sie sie zu Produkten und Entwicklern zuordnen.

## <a name="associate-group-product"></a>Zuordnen einer Gruppe zu einem Produkt

1. Wählen Sie auf der linken Seite die Registerkarte **Produkte**.
2. Klicken Sie auf den Namen des gewünschten Produkts.
3. Drücken Sie **Zugriffssteuerung**.
4. Klicken Sie auf **+ Gruppe hinzufügen**.

    ![Zuordnen einer Gruppe zu einem Produkt](./media/api-management-howto-create-groups/groups002.png)
5. Wählen Sie die Gruppe aus, die Sie hinzufügen möchten.

    ![Zuordnen einer Gruppe zu einem Produkt](./media/api-management-howto-create-groups/groups003.png)

    Um eine Gruppe aus dem Produkt zu entfernen, klicken Sie auf **Löschen**.

    ![Löschen einer Gruppe](./media/api-management-howto-create-groups/groups004.png)

Sobald ein Produkt zu einer Gruppe zugeordnet ist, können Entwickler in dieser Gruppe das Produkt anzeigen und abonnieren.

> [!NOTE]
> Informationen zum Hinzufügen von Azure Active Directory-Gruppen finden Sie unter [Autorisieren von Entwicklerkonten mithilfe von Azure Active Directory in Azure API Management](api-management-howto-aad.md).

## <a name="associate-group-developer"></a>Zuordnen von Entwicklern zu Gruppen

In diesem Abschnitt wird gezeigt, wie Gruppen Elemente zugeordnet werden.

1. Wählen Sie auf der linken Seite des Bildschirms die Registerkarte **Gruppen**.
2. Wählen Sie **Mitglieder**.

    ![Hinzufügen eines Mitglieds](./media/api-management-howto-create-groups/groups005.png)
3. Drücken Sie **+ Hinzufügen**, und wählen Sie ein Element aus.

    ![Hinzufügen eines Mitglieds](./media/api-management-howto-create-groups/groups006.png)
4. Drücken Sie **Auswählen**.

Sobald Sie die Zuordnung zwischen Entwickler und Gruppe erstellt haben, können Sie diese auf der Registerkarte **Benutzer** anzeigen.

## <a name="next-steps"> </a>Nächste Schritte

* Sobald ein Entwickler zu einer Gruppe hinzugefügt wurde, können diese die zu dieser Gruppe zugeordneten Produkte anzeigen und abonnieren. Weitere Informationen finden Sie unter [Erstellen und Veröffentlichen eines Produkts in Azure API Management][How create and publish a product in Azure API Management].
* Zusätzlich zum Erstellen und Verwalten von Gruppen im Herausgeberportal können Sie Ihre Gruppen mithilfe der Entität [Gruppe](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) der API Management-REST-API erstellen und verwalten.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md

---
title: 'Tutorial: Erstellen eines Azure Active Directory B2C-Mandanten'
description: In diesem Tutorial erfahren Sie, wie Sie sich auf die Registrierung Ihrer Anwendungen vorbereiten, indem Sie einen Azure Active Directory B2C-Mandanten über das Azure-Portal erstellen.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5229175af618f897caeae7310338cbe51966a48d
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96859591"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Tutorial: Erstellen eines Azure Active Directory B2C-Mandanten

Bevor Ihre Anwendungen mit Azure Active Directory B2C (Azure AD B2C) interagieren können, müssen sie in einem von Ihnen verwalteten Mandanten registriert werden. 

> [!NOTE]
> Sie können bis zu 20 Mandanten pro Abonnement erstellen. Diese Beschränkung schützt vor Bedrohungen für Ihre Ressourcen, etwa vor Denial-of-Service-Angriffen, und wird sowohl im Azure-Portal als auch in der zugrunde liegenden Mandantenerstellungs-API erzwungen. Wenn Sie mehr als 20 Mandanten erstellen müssen, wenden Sie sich an den [Microsoft-Support](support-options.md).

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Erstellen eines Azure AD B2C-Mandanten
> * Verknüpfen Ihres Mandanten mit Ihrem Abonnement
> * Wechseln zu dem Verzeichnis, das Ihren Azure AD B2C-Mandanten enthält
> * Hinzufügen der Azure AD B2C-Ressource als **Favorit** im Azure-Portal

Im nächsten Tutorial erfahren Sie, wie Sie eine Anwendung registrieren.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="create-an-azure-ad-b2c-tenant"></a>Erstellen eines Azure AD B2C-Mandanten

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an. Melden Sie sich mit einem Azure-Konto an, dem mindestens die [Mitwirkender](../role-based-access-control/built-in-roles.md)-Rolle innerhalb des Abonnements oder einer Ressourcengruppe im Abonnement zugewiesen wurde.

1. Wählen Sie das Verzeichnis aus, das Ihr Abonnement enthält.

    Wählen Sie auf der Symbolleiste des Azure-Portals das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihr Abonnement enthält. Dabei handelt es sich um ein anderes Verzeichnis als das, in dem Ihr Azure AD B2C-Mandant enthalten sein wird.

    ![Abonnementmandant, Filter „Verzeichnis und Abonnement“ mit ausgewähltem Abonnementmandanten](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus.

   ![Auswählen der Schaltfläche „Ressource erstellen“](media/tutorial-create-tenant/create-a-resource.png)

1. Suchen Sie nach **Azure Active Directory B2C**, und wählen Sie dann **Erstellen** aus.
2. Wählen Sie **Neuen Azure AD B2C-Mandanten erstellen**.

    ![„Neuen Azure AD B2C-Mandanten erstellen“ im Azure-Portal ausgewählt](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Geben Sie auf der Seite **Verzeichnis erstellen** Folgendes ein:

   - **Organisationsname**: Geben Sie einen Namen für Ihren Azure AD B2C-Mandanten ein.
   - **Name der Anfangsdomäne**: Geben Sie einen Domänennamen für Ihren Azure AD B2C-Mandanten ein.
   - **Land oder Region**: Wählen Sie in der Liste Ihr Land oder Ihre Region aus. Diese Auswahl kann später nicht mehr geändert werden.
   - **Abonnement**: Wählen Sie in der Liste Ihr Abonnement aus.
   - **Ressourcengruppe**: Wählen Sie die Ressourcengruppe aus, die den Mandanten enthalten soll, oder suchen Sie nach der Ressourcengruppe.

    ![Formular „Mandanten erstellen“ mit Beispielwerten im Azure-Portal](media/tutorial-create-tenant/review-and-create-tenant.png)

1. Klicken Sie auf **Überprüfen + erstellen**.
1. Überprüfen Sie Ihre Verzeichniseinstellungen. Klicken Sie anschließend auf **Erstellen**.

Sie können mehrere Azure AD B2C-Mandanten zu Abrechnungszwecken mit einem einzelnen Azure-Abonnement verknüpfen. Zum Verknüpfen eines Mandanten müssen Sie auf dem Azure AD B2C-Mandanten ein Administrator sein, und Ihnen muss unter dem Azure-Abonnement mindestens die Rolle „Mitwirkender“ zugewiesen sein. Weitere Informationen finden Sie unter [Verknüpfen eines Azure AD B2C-Mandanten mit einem Abonnement](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription).

## <a name="select-your-b2c-tenant-directory"></a>Auswählen Ihres B2C-Mandantenverzeichnisses

Wenn Sie mit der Verwendung Ihres neuen Azure AD B2C-Mandanten beginnen möchten, müssen Sie zu dem Verzeichnis wechseln, das den Mandanten enthält.

Wählen Sie im oberen Menü des Azure-Portals den Filter **Verzeichnis und Abonnement** und dann das Verzeichnis mit Ihrem Azure AD B2C-Mandanten aus.

Wenn Ihr neuer Azure B2C-Mandant zunächst nicht in der Liste angezeigt wird, aktualisieren Sie Ihr Browserfenster, und wählen Sie dann erneut im oberen Menü den Filter **Verzeichnis und Abonnement** aus.

![B2C-Mandant mit Verzeichnis im Azure-Portal ausgewählt](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Hinzufügen von Azure AD B2C als Favorit (optional)

Dieser optionale Schritt erleichtert Ihnen die Auswahl Ihres Azure AD B2C-Mandanten in den folgenden Abschnitten und allen nachfolgenden Tutorials.

Statt jedes Mal, wenn Sie mit Ihrem Mandanten arbeiten möchten, in **Alle Dienste** nach *Azure AD B2C* suchen zu müssen, können Sie die Ressource als Favorit markieren. Anschließend können Sie sie im Abschnitt **Favoriten** des Portalmenüs auswählen, um schnell zu Ihrem Azure AD B2C-Mandanten zu navigieren.

Sie müssen diesen Vorgang nur einmal ausführen. Bevor Sie diese Schritte ausführen, müssen Sie in das Verzeichnis mit Ihrem Azure AD B2C-Mandanten gewechselt haben, wie im vorherigen Abschnitt, [Auswählen Ihres B2C-Mandantenverzeichnisses](#select-your-b2c-tenant-directory), beschrieben wurde.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im Menü des Azure-Portals **Alle Dienste** aus.
1. Suchen Sie im Suchfeld **Alle Dienste** nach **Azure AD B2C**, zeigen Sie auf das Suchergebnis, und wählen Sie dann das Sternsymbol in der QuickInfo aus. Im Azure-Portal wird jetzt unter **Favoriten** **Azure AD B2C** angezeigt.
1. Wenn Sie die Position des neuen Favoriten ändern möchten, wählen Sie im Azure-Portalmenü **Azure AD B2C** aus, und ziehen Sie es dann nach oben oder unten an die gewünschte Position.

    ![Azure AD B2C, Menü „Favoriten“, Microsoft Azure-Portal](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines Azure AD B2C-Mandanten
> * Verknüpfen Ihres Mandanten mit Ihrem Abonnement
> * Wechseln zu dem Verzeichnis, das Ihren Azure AD B2C-Mandanten enthält
> * Hinzufügen der Azure AD B2C-Ressource als **Favorit** im Azure-Portal

Als Nächstes erfahren Sie, wie Sie eine Webanwendung in Ihrem neuen Mandanten registrieren.

> [!div class="nextstepaction"]
> [Registrieren Ihrer Anwendungen](tutorial-register-applications.md)
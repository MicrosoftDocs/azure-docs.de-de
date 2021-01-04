---
title: 'Hinzufügen eines Benutzerflows für die Self-Service-Registrierung: Azure AD'
description: Erstellen Sie Benutzerflows für Apps, die von Ihrer Organisation entwickelt werden. Benutzer, die diese App aufrufen, können dann mithilfe der im Benutzerflow konfigurierten Optionen ein Gastkonto erhalten.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f76f4a3e5fc87420c242c693e3c48a91244641e0
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560032"
---
# <a name="add-a-self-service-sign-up-user-flow-to-an-app-preview"></a>Hinzufügen eines Benutzerflows für die Self-Service-Registrierung zu einer App (Vorschau)
> [!NOTE]
> Die Self-Service-Registrierung ist eine öffentliche Previewfunktion von Azure Active Directory. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sie können Benutzerflows für Apps erstellen, die von Ihrer Organisation erstellt werden. Wenn Sie den Benutzerflow einer Anwendung zuordnen, können Sie die Registrierung für diese App aktivieren. Sie können mehrere Anwendungen auswählen, die dem Benutzerflow zugeordnet werden sollen. Nachdem Sie den Benutzerflow den entsprechenden Anwendungen zugewiesen haben, können sich Benutzer, die diese App aufrufen, mit den im Benutzerflow konfigurierten Optionen registrieren und ein Gastkonto erhalten.

> [!NOTE]
> Sie können Benutzerflows mit Apps verknüpfen, die von Ihrer Organisation erstellt wurden. Benutzerflows können nicht für Microsoft-Apps wie SharePoint oder Teams verwendet werden.

## <a name="before-you-begin"></a>Voraussetzungen

### <a name="add-social-identity-providers-optional"></a>Hinzufügen eines sozialen Netzwerks als Identitätsanbieter (optional)

Azure AD ist der Standardidentitätsanbieter für die Self-Service-Registrierung. Das bedeutet, dass Benutzer sich standardmäßig mit einem Azure AD-Konto anmelden können. Soziale Netzwerke als Identitätsanbieter können ebenfalls in diese Registrierungsflows eingeschlossen werden, um Google- und Facebook-Konten zu unterstützen.

- [Hinzufügen von Facebook zu Ihrer Liste der sozialen Netzwerke als Identitätsanbieter](facebook-federation.md)
- [Hinzufügen von Google zu Ihrer Liste der sozialen Netzwerke als Identitätsanbieter](google-federation.md)

> [!NOTE]
> Wenn in der aktuellen Vorschau ein Benutzerflow für die Self-Service-Registrierung mit einer App verknüpft ist und Sie einem Benutzer eine Einladung für diese App senden, kann der Benutzer die Einladung nicht mit einem Gmail-Konto einlösen. Um dieses Problem zu umgehen, kann der Benutzer den Self-Service-Registrierungsprozess durchlaufen. Oder er kann die Einladung einlösen, indem er auf eine andere App zugreift oder das Portal „Meine Apps“ auf https://myapps.microsoft.com verwendet.

### <a name="define-custom-attributes-optional"></a>Definieren von benutzerdefinierten Attributen (optional)

Benutzerattribute sind Werte, die während der Self-Service-Registrierung vom Benutzer erfasst werden. Azure AD verfügt über verschiedene integrierte Attribute, Sie können jedoch benutzerdefinierte Attribute für die Verwendung in Ihrem Benutzerflow erstellen. Außerdem können Sie diese Attribute mit der Microsoft Graph-API lesen und schreiben. Informationen finden Sie unter [Definieren benutzerdefinierter Attribute für Benutzerflows](user-flow-add-custom-attributes.md).

## <a name="enable-self-service-sign-up-for-your-tenant"></a>Aktivieren der Self-Service-Registrierung für Ihren Mandanten

Bevor Sie Ihren Anwendungen einen Benutzerflow für die Self-Service-Registrierung hinzufügen können, müssen Sie das Feature für Ihren Mandanten aktivieren. Nach der Aktivierung stehen im Benutzerflow Steuerelemente zur Verfügung, mit denen Sie den Benutzerflow einer Anwendung zuordnen können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Azure AD-Administrator an.
2. Wählen Sie unter **Azure-Dienste** die Option **Azure Active Directory** aus.
3. Wählen Sie **Benutzereinstellungen** und dann unter **Externe Benutzer** die Option **Externe Einstellungen zur Zusammenarbeit verwalten** aus.
4. Legen Sie den Umschalter **Self-Service-Registrierung von Gästen über Benutzerflows aktivieren (Vorschau)** auf **Ja** fest.

   ![Aktivieren der Self-Service-Registrierung für Gäste](media/self-service-sign-up-user-flow/enable-self-service-sign-up.png)
5. Wählen Sie **Speichern** aus.
## <a name="create-the-user-flow-for-self-service-sign-up"></a>Erstellen des Benutzerflows für die Self-Service-Registrierung

Als Nächstes erstellen Sie den Benutzerflow für die Self-Service-Registrierung und fügen ihn einer Anwendung hinzu.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Azure AD-Administrator an.
2. Wählen Sie unter **Azure-Dienste** die Option **Azure Active Directory** aus.
3. Wählen Sie im Menü auf der linken Seite **Externe Identitäten** aus.
4. Wählen Sie **Benutzerflows (Vorschau)** und dann **Neuer Benutzerflow** aus.

   ![Schaltfläche zum Hinzufügen eines neuen Benutzerflows](media/self-service-sign-up-user-flow/new-user-flow.png)

5. Geben Sie auf der Seite **Erstellen** unter **Name** einen Namen für den Benutzerflow ein. Beachten Sie, dass dem Namen automatisch **B2X_1_** vorangestellt wird.
6. Wählen Sie in der Liste **Identitätsanbieter** mindestens einen Identitätsanbieter aus, den Ihre externen Benutzer für die Anmeldung bei Ihrer Anwendung verwenden können. Standardmäßig ist **Azure Active Directory-Registrierung** ausgewählt. (Lesen Sie die Informationen im Abschnitt [Voraussetzungen](#before-you-begin) weiter oben in diesem Artikel, um zu erfahren, wie Sie Identitätsanbieter hinzufügen.)
7. Wählen Sie unter **Benutzerattribute** die Attribute aus, die Sie vom Benutzer erfassen möchten. Wählen Sie **Mehr anzeigen** aus, um weitere Attribute anzuzeigen. Wählen Sie z.B. **Mehr anzeigen** und dann Attribute und Ansprüche für **Land/Region**, **Anzeigename** und **Postleitzahl** aus. Klicken Sie auf **OK**.

   ![Seite zum Erstellen eines neuen Benutzerflows](media/self-service-sign-up-user-flow/create-user-flow.png)

8. Klicken Sie auf **Erstellen**.
9. Der neue Benutzerflow wird in der Liste **Benutzerflows (Vorschau)** angezeigt. Aktualisieren Sie die Seite bei Bedarf.

## <a name="select-the-layout-of-the-attribute-collection-form"></a>Auswählen des Layouts des Attributesammlungsformulars

Sie können die Reihenfolge auswählen, in der die Attribute auf der Registrierungsseite angezeigt werden. 

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Azure Active Directory** aus.
2. Wählen Sie **Externe Identitäten** und dann **Benutzerflows (Vorschau)** aus.
3. Wählen Sie in der Liste den Benutzerflow für die Self-Service-Registrierung aus.
4. Wählen Sie unter **Anpassen** die Option **Seitenlayouts** aus.
5. Die Attribute, die Sie sammeln möchten, werden aufgelistet. Wenn Sie die Anzeigereihenfolge ändern möchten, wählen Sie ein Attribut aus und dann **Nach oben verschieben**, **Nach unten verschieben**, **Move to the top** (An den Anfang verschieben) oder **Move to the bottom** (Ans Ende verschieben).
6. Wählen Sie **Speichern** aus.

## <a name="add-applications-to-the-self-service-sign-up-user-flow"></a>Hinzufügen von Anwendungen zum Benutzerflow für die Self-Service-Registrierung

Nun können Sie dem Benutzerflow Anwendungen zuordnen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Azure AD-Administrator an.
2. Wählen Sie unter **Azure-Dienste** die Option **Azure Active Directory** aus.
3. Wählen Sie im Menü auf der linken Seite **Externe Identitäten** aus.
4. Wählen Sie unter **Self-Service-Registrierung** die Option **Benutzerflows (Vorschau)** aus.
5. Wählen Sie in der Liste den Benutzerflow für die Self-Service-Registrierung aus.
6. Wählen Sie im Menü auf der linken Seite unter **Verwenden** die Option **Anwendungen** aus.
7. Wählen Sie **Anwendung hinzufügen** aus.

   ![Zuweisen einer Anwendung zum Benutzerflow](media/self-service-sign-up-user-flow/assign-app-to-user-flow.png)

8. Wählen Sie die Anwendung in der Liste aus. Verwenden Sie alternativ das Suchfeld, um die Anwendung zu suchen, und wählen Sie sie dann aus.
9. Klicken Sie auf **Auswählen**.

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen von Google zu Ihrer Liste der sozialen Netzwerke als Identitätsanbieter](google-federation.md)
- [Hinzufügen von Facebook zu Ihrer Liste der sozialen Netzwerke als Identitätsanbieter](facebook-federation.md)
- [Verwenden von API-Connectors zum Anpassen und Erweitern von Benutzerflows über Web-APIs](api-connectors-overview.md)
- [Hinzufügen eines benutzerdefinierten Genehmigungsworkflows zum Benutzerflow](self-service-sign-up-add-approvals.md)

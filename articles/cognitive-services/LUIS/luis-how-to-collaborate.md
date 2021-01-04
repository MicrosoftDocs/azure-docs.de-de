---
title: 'Zusammenarbeit mit anderen Benutzern: LUIS'
titleSuffix: Azure Cognitive Services
description: Ein App-Besitzer kann Mitwirkende zur Erstellungsressource hinzufügen. Diese Mitwirkenden können das Modell ändern und die App trainieren und veröffentlichen.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/08/2020
ms.openlocfilehash: d4bde21dd13b562ffbb51b27ef083ee53685397a
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007964"
---
# <a name="add-contributors-to-your-app"></a>Hinzufügen von Mitwirkenden zu Ihrer App

Ein App-Besitzer kann Mitwirkende zu Apps hinzufügen. Diese Mitwirkenden können das Modell ändern und die App trainieren und veröffentlichen. Sobald Sie Ihr Konto [migriert](luis-migration-authoring.md) haben, werden _Mitwirkende_ für die Erstellungsressource im Azure-Portal verwaltet. Verwenden Sie dazu die Seite **Zugriffssteuerung (IAM)** . Fügen Sie einen Benutzer mit der E-Mail-Adresse des Mitarbeiters und der Rolle _Mitwirkender_ hinzu.

## <a name="add-contributor-to-azure-authoring-resource"></a>Hinzufügen von Mitwirkenden zu einer Azure-Erstellungsressource

Sie haben migriert, wenn Ihre LUIS-Erstellungsumgebung im LUIS-Portal auf der Seite **Verwalten -> Azure-Ressourcen** an eine Erstellungsressource gebunden ist.

1. Suchen Sie im Azure-Portal nach der Language Understanding-Erstellungsressource (LUIS-Ressource). Sie hat den Typ `LUIS.Authoring`.
1. Wählen Sie auf der **Zugriffssteuerung (IAM)**-Seite der Ressource die Option **+Hinzufügen** aus, und wählen Sie dann **Rollenzuweisung hinzufügen** aus.

    ![Im Azure-Portal eine Rollenzuweisung für die Erstellungsressource hinzufügen.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. Wählen Sie im Fenster **Rollenzuweisung hinzufügen** für **Rolle** die Option „Mitwirkender“ aus. Wählen Sie für die Option **Zugriff zuweisen zu** den Eintrag **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** aus. Geben Sie für die Option **Auswählen** die E-Mail-Adresse des Benutzers ein. Ist bekannt, dass der Benutzer mehrere E-Mail-Adressen für dieselbe Domäne hat, müssen Sie das _primäre_ E-Mail-Konto eingeben.

    ![E-Mail-Adresse des Benutzers zur Rolle „Mitwirkender“ für Azure AD hinzufügen](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Wenn Sie die E-Mail-Adresse des Benutzers gefunden haben, wählen Sie das Konto aus, und wählen Sie **Speichern** aus.

    Wenn Sie Probleme mit dieser Rollenzuweisung haben, lesen Sie [Azure-Rollenzuweisungen ](../../role-based-access-control/role-assignments-portal.md) und [Probleme mit RBAC-Rollenzuweisungen](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments).

## <a name="view-the-app-as-a-contributor"></a>Anzeigen der App als Mitwirkender

Nachdem Sie als Mitwirkender hinzugefügt wurden, [melden Sie sich im LUIS-Portal an](sign-in-luis-portal.md).

[!INCLUDE [switch azure directories](includes/switch-azure-directories.md)]

### <a name="users-with-multiple-emails"></a>Benutzer mit mehreren E-Mail-Adressen

Wenn Sie Mitwirkende zu einer LUIS-App hinzufügen, geben Sie die genauen E-Mail-Adressen an. Während ein Einzelbenutzer in Azure Active Directory (Azure AD) mehrere E-Mail-Konten haben kann, die austauschbar verwendet werden, erfordert LUIS, dass sich der Benutzer mit der E-Mail-Adresse anmeldet, die beim Hinzufügen des Mitwirkenden angegeben wurde.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Azure Active Directory-Ressourcen

Wenn Sie in Ihrer Organisation [Azure Active Directory](../../active-directory/index.yml) (Azure AD) verwenden, benötigt LUIS (Language Understanding) die Berechtigung für Informationen über Ihre Benutzer, wenn diese LUIS verwenden möchten. Die von LUIS benötigten Ressourcen sind minimal.

Die detaillierte Beschreibung wird angezeigt, wenn Sie sich mit einem Konto zu registrieren versuchen, für das eine Administratoreinwilligung vorliegt oder keine Administratoreinwilligung erforderlich ist:

* Ermöglicht Ihnen die Anmeldung bei der App mit Ihrem Organisationskonto und lässt die App Ihr Profil lesen. Es erlaubt der App darüber hinaus, grundlegende Firmeninformationen zu lesen. Dies gibt LUIS die Berechtigung, grundlegende Profildaten wie die Benutzer-ID, die E-Mail-Adresse und den Namen zu lesen.
* Es ermöglicht der App, Ihre Daten zu sehen und zu aktualisieren, auch wenn Sie die App aktuell nicht verwenden. Die Berechtigung ist erforderlich, um das Zugriffstoken des Benutzers zu aktualisieren.


### <a name="azure-active-directory-tenant-user"></a>Azure Active Directory-Mandantenbenutzer

LUIS verwendet den Standard-Zustimmungsfluss von Azure Active Directory (Azure AD).

Der Administrator des Mandanten sollte direkt mit dem Benutzer zusammenarbeiten, der den Zugriff auf LUIS in der Azure AD benötigt.

* Im ersten Schritt meldet sich der Benutzer bei LUIS an und sieht das Popupdialogfeld, aus dem er erfährt, dass er eine Administratorgenehmigung benötigt. Der Benutzer setzt sich mit dem Administrator des Mandanten in Verbindung, bevor er fortfährt.
* Im zweiten Schritt meldet sich der Mandantenadministrator bei LUIS und sieht ein Popupdialogfeld zum Zustimmungsfluss. Dies ist das Dialogfeld, das der Administrator benötigt, um dem Benutzer die Genehmigung zu erteilen. Nachdem der Administrator die Berechtigung akzeptiert hat, kann der Benutzer mit LUIS fortfahren. Wenn der Mandantenadministrator sich nicht bei LUIS anmeldet, kann er auf die [Zustimmung](https://account.activedirectory.windowsazure.com/r#/applications) für LUIS zugreifen, wie im folgenden Screenshot gezeigt. Beachten Sie, dass die Liste nach Elementen gefiltert ist, die den Namen `LUIS` enthalten.

![Azure Active Directory-Website mit Berechtigungen nach Anwendung](./media/luis-how-to-collaborate/tenant-permissions.png)

Wenn der Mandantenadministrator nur bestimmten Benutzern die Verwendung von LUIS gestatten möchte, gibt es mehrere mögliche Lösungen:
* Erteilen der „Administratoreinwilligung“ (Einwilligung für alle Benutzer von Azure AD) und anschließendes Festlegen von „Benutzerzuweisung erforderlich“ unter den Eigenschaften der Unternehmensanwendung auf „Ja“ und Zuweisen/Hinzufügen nur der gewünschten Benutzer zur Anwendung. Mit dieser Methode erteilt der Administrator immer noch „Administratoreinwilligung“ für die App, es ist jedoch möglich, die Benutzer zu steuern, die darauf zugreifen können.
* Eine zweite Lösung ist die Verwendung der [Identitäts- und Zugriffsverwaltungs-API von Azure AD in Microsoft Graph](/graph/azuread-identity-access-management-concept-overview), um jedem einzelnen Benutzer die Einwilligung zu erteilen.

Weitere Informationen zu Azure Active Directory-Benutzern und Einwilligung:
* [Einschränken Ihrer App](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) auf eine Gruppe von Benutzern

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, [wie Sie Versionen verwenden](luis-how-to-manage-versions.md), um den Lebenszyklus ihrer App zu steuern.
* Informieren Sie sich über die Konzepte, einschließlich der [Erstellungsressource](luis-how-to-azure-subscription.md#authoring-key) und der [Mitwirkenden](luis-how-to-azure-subscription.md#contributions-from-other-authors) für diese Ressource.
* Erfahren Sie, wie Sie Erstellungs- und Laufzeitressourcen [erstellen](luis-how-to-azure-subscription.md).
* Migrieren Sie zu der neuen [Erstellungsressource](luis-migration-authoring.md).
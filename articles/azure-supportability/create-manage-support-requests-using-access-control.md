---
title: Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure zum Steuern von Zugriffsrechten für die Erstellung und Verwaltung von Supportanfragen | Microsoft-Dokumentation
description: Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure zum Steuern von Zugriffsrechten für die Erstellung und Verwaltung von Supportanfragen
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: azure
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: d98d0637c6d520193b11f4267c59016772ef063a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60809746"
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure zum Steuern von Zugriffsrechten für die Erstellung und Verwaltung von Supportanfragen

Die [rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) ermöglicht eine präzise Zugriffsverwaltung für Azure.
für die Supportanfragenerstellung im Azure-Portal ([portal.azure.com](https://portal.azure.com)) wird mithilfe des RBAC-Modells von Azure definiert, wer zum Erstellen und Verwalten von Supportanfragen berechtigt ist.
Der Zugriff wird gewährt, indem Benutzern, Gruppen und Anwendungen die jeweils geeignete RBAC-Rolle für einen bestimmten Bereich zugewiesen wird. Hierbei kann es sich um ein Abonnement, um eine Ressourcengruppe oder um eine Ressource handeln.

Sehen wir uns ein Beispiel an: Als Ressourcengruppenbesitzer mit Leseberechtigungen für den Abonnementbereich können Sie alle Ressourcen unter der Ressourcengruppe (etwa Websites, virtuelle Computer und Subnetze) verwalten.
Wenn Sie jedoch versuchen, eine Supportanfrage für die VM-Ressource zu erstellen, tritt der folgende Fehler auf:

![Abonnementfehler](./media/create-manage-support-requests-using-access-control/subscription-error.png)

In der Supportanfragenverwaltung benötigen Sie Schreibberechtigungen oder eine Rolle mit der Supportaktion „Microsoft.Support/*“ für den Abonnementbereich, um Supportanfragen erstellen und verwalten zu können.

Im folgenden Artikel erfahren Sie, wie Sie mithilfe der benutzerdefinierten rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure Supportanfragen über das Azure-Portal erstellen und verwalten.

## <a name="getting-started"></a>Erste Schritte

Im obigen Beispiel könnten Sie eine Supportanfrage für Ihre Ressource erstellen, wenn der Abonnementbesitzer Ihnen eine benutzerdefinierte RBAC-Rolle für das Abonnement zuweist.
[Benutzerdefinierte RBAC-Rollen](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) können mithilfe von Azure PowerShell, über die Azure-Befehlszeilenschnittstelle (Command-Line Interface, CLI) und mithilfe der REST-API erstellt werden.

Mit der Actions-Eigenschaft einer benutzerdefinierten Rolle werden die Azure-Vorgänge angegeben, auf die mit der Rolle Zugriff gewährt wird.
Zur Erstellung einer benutzerdefinierten Rolle für die Verwaltung von Supportanfragen muss die Rolle über die Aktion „Microsoft.Support/*“ verfügen.

Hier sehen Sie ein Beispiel für eine benutzerdefinierte Rolle zur Erstellung und Verwaltung von Supportanfragen.
Wir haben diese benutzerdefinierte Rolle „Support Request Contributor“ (Mitwirkender bei Supportanfragen) genannt, und unter diesem Namen wird sie in diesem Artikel auch verwendet.

``` Json
{
    "Name":  "Support Request Contributor",
    "Id":  "1f2aad59-39b0-41da-b052-2fb070bd7942",
    "IsCustom":  true,
    "Description":  "Lets you create and manage support tickets.",
    "Actions":  [
                    "Microsoft.Support/*"
                ],
    "NotActions":  [
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

In den Schritten in [diesem Video](https://www.youtube.com/watch?v=-PaBaDmfwKI) erfahren Sie, wie Sie eine benutzerdefinierte Rolle für Ihr Abonnement erstellen.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Erstellen und Verwalten von Supportanfragen im Azure-Portal

Angenommen, Sie sind der Besitzer des Abonnements „Visual Studio MSDN Subscription“.
Ihr Kollege Joe ist Ressourcenbesitzer für einige der Ressourcengruppen in diesem Abonnement und verfügt über Leseberechtigungen für das Abonnement.
Sie möchten Ihrem Kollegen Joe Zugriff gewähren, damit er Supporttickets für die Ressourcen unter diesem Abonnement erstellen und verwalten kann.

1. Hierzu müssen Sie zunächst zu dem Abonnement navigieren. Unter „Einstellungen“ wird eine Liste mit Benutzern angezeigt. Klicken Sie auf den Benutzer „Joe“, der über Lesezugriff für das Abonnement verfügt, und weisen Sie ihm eine neue benutzerdefinierte Rolle zu.

    ![Hinzufügen einer Rolle](./media/create-manage-support-requests-using-access-control/add-role.png)

2. Klicken Sie auf dem Blatt „Benutzer“ auf „Hinzufügen“. Wählen Sie in der Liste mit den Rollen die benutzerdefinierte Rolle „Support Request Contributor“ aus.

    ![Hinzufügen der Rolle „Support Request Contributor“](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. Klicken Sie nach dem Auswählen des Rollennamens auf „Benutzer hinzufügen“, und geben Sie Joes E-Mail-Anmeldeinformationen ein. Klicken Sie auf „Auswählen“.

    ![Hinzufügen von Benutzern](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Klicken Sie auf „OK“, um den Vorgang fortzusetzen.

    ![Zugriff hinzufügen](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Der Benutzer mit der neu hinzugefügten benutzerdefinierten Rolle „Support Request Contributor“ wird nun unter dem Abonnement angezeigt, für das Sie als Besitzer fungieren.

    ![Benutzer hinzugefügt](./media/create-manage-support-requests-using-access-control/user-added.png)

    Wenn sich Joe beim Portal anmeldet, wird ihm das Abonnement angezeigt, dem er hinzugefügt wurde.

7. Joe klickt auf dem Blatt „Hilfe und Support“ auf „Neue Supportanfrage“ und kann Supportanfragen für „Visual Studio Ultimate mit MSDN“ erstellen.

    ![Neue Supportanfrage](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Wenn Joe auf „Alle Supportanfragen“ klickt, wird ihm eine Liste mit Supportanfragen angezeigt, die für dieses Abonnement erstellt wurden. ![Ansicht mit Falldetails](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Entziehen des Zugriffs auf Supportanfragen im Azure-Portal

Einem Benutzer kann nicht nur Zugriff zum Erstellen und Verwalten von Supportanfragen gewährt werden, der Zugriff kann dem Benutzer auch wieder entzogen werden.
Wenn Sie einem Benutzer die Fähigkeit zum Erstellen und Verwalten von Supportanfragen entziehen möchten, navigieren Sie zum Abonnement, klicken Sie auf „Einstellungen“, und klicken Sie anschließend auf den Benutzer (in diesem Fall: Joe).
Klicken Sie mit der rechten Maustaste auf den Rollennamen „Support Request Contributor“, und klicken Sie anschließend auf „Entfernen“.

![Entziehen des Supportanfragenzugriffs](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Wenn sich Joe nun beim Portal anmeldet und versucht, eine Supportanfrage zu erstellen, tritt der folgende Fehler auf:

![Abonnementfehler 2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Wenn Joe auf „Alle Supportanfragen“ klickt, werden ihm keine Supportanfragen angezeigt.

![Ansicht mit Falldetails 2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)

---
title: 'Schnellstart: Zuweisen von Benutzern zu einer App, die Azure Active Directory als Identitätsanbieter nutzt'
description: In dieser Schnellstartanleitung wird erläutert, wie Sie Benutzern die Verwendung einer App ermöglichen, für die Sie Azure AD als Identitätsanbieter eingerichtet haben.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/01/2020
ms.author: kenwith
ms.openlocfilehash: 53dd2d15565149c3a9888ba063a6194ae033d8e0
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258370"
---
# <a name="quickstart-assign-users-to-an-app-that-is-using-azure-ad-as-an-identity-provider"></a>Schnellstart: Zuweisen von Benutzern zu einer App, die Azure AD als Identitätsanbieter nutzt

In der vorherigen Schnellstartanleitung haben Sie die Eigenschaften für eine App konfiguriert. Beim Festlegen der Eigenschaften haben Sie die Umgebung für zugewiesene und nicht zugewiesene Benutzer konfiguriert. In dieser Schnellstartanleitung wird das Zuweisen von Benutzern zur App erläutert.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um Benutzer einer App zuzuweisen, die Sie Ihrem Azure AD-Mandanten hinzugefügt haben:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.
- Optional: Gehen Sie den Schnellstart [Anzeigen Ihrer Apps](view-applications-portal.md) durch.
- Optional: Gehen Sie den Schnellstart [Hinzufügen einer App](add-application-portal.md) durch.
- Optional: Gehen Sie den Schnellstart [Konfigurieren einer App](add-application-portal-configure.md) durch.

>[!IMPORTANT]
>Verwenden Sie zum Testen der in dieser Schnellstartanleitung aufgeführten Schritte keine Produktionsumgebung.

## <a name="assign-users-to-an-app"></a>Zuweisen von Benutzern zu einer App
1. Wählen Sie im Azure AD-Portal die Option **Unternehmensanwendungen** aus. Suchen Sie dann nach der zu konfigurierenden Anwendung, und wählen Sie diese aus.
2. Wählen Sie im Navigationsmenü auf der linken Seite **Benutzer und Gruppen** aus.
   > [!NOTE]
   > Für einige der Microsoft 365-Apps muss PowerShell verwendet werden. 
3. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**.
4. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
5. Wählen Sie den Benutzer oder die Gruppe aus, den bzw. die Sie der Anwendung zuweisen möchten. Sie können auch mit der Eingabe des Benutzer- oder des Gruppennamens im Suchfeld beginnen. Sie können mehrere Benutzer und Gruppen auswählen. Ihre Auswahl wird unter **Ausgewählte Elemente** angezeigt.
    > [!IMPORTANT]
    > Wenn Sie einer Anwendung eine Gruppe zuweisen, haben nur die Benutzer in der Gruppe Zugriff. Die Zuweisung wird nicht an geschachtelte Gruppen weitergegeben.

    > [!NOTE]
    > Für die gruppenbasierte Zuweisung ist die Azure Active Directory Premium P1- oder P2-Edition erforderlich. Die gruppenbasierte Zuweisung wird nur für Sicherheitsgruppen unterstützt. Geschachtelte Gruppenmitgliedschaften und Microsoft 365-Gruppen werden aktuell nicht unterstützt. Weitere Informationen zu Lizenzierungsanforderungen für die in diesem Artikel erläuterten Features finden Sie in der [Preisübersicht für Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory). 
6. Wählen Sie abschließend **Auswählen** aus.
   ![Zuweisen eines Benutzers oder einer Gruppe zur App](./media/assign-user-or-group-access-portal/assign-users.png)
7. Wählen Sie im Bereich **Benutzer und Gruppen** mindestens einen Benutzer oder eine Gruppe aus der Liste aus, und wählen Sie dann am unteren Rand des Bereichs die Schaltfläche **Auswählen** aus.
8. Falls von der Anwendung unterstützt, können Sie dem Benutzer oder der Gruppe eine Rolle zuweisen. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Rolle auswählen** aus. Wählen Sie dann im Bereich **Rolle auswählen** eine Rolle aus, die den ausgewählten Benutzern oder Gruppen zugeordnet werden soll. Wählen Sie anschließend unten im Bereich **OK** aus. 
    > [!NOTE]
    > Wenn die Anwendung die Rollenauswahl nicht unterstützt, wird die Standardzugriffsrolle zugewiesen. In diesem Fall verwaltet die Anwendung die Zugriffsebene von Benutzern.
9. Wählen Sie im Bereich **Zuweisung hinzufügen** unten die Schaltfläche **Zuweisen** aus.

Sie können die Zuweisung von Benutzern oder Gruppen mit demselben Verfahren aufheben. Wählen Sie den gewünschten Benutzer oder die gewünschte Gruppe und dann die Schaltfläche **Entfernen** aus. Für einige der Microsoft 365- und Office 365-Apps muss PowerShell verwendet werden. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Schnellstartanleitung abgeschlossen haben, sollten Sie die App löschen. Dies dient zur Bereinigung des Testmandanten. Das Löschen der App wird in der letzten Schnellstartanleitung dieser Reihe behandelt. Informationen finden Sie unter [Schnellstart: Löschen einer Anwendung von Ihrem Azure AD-Mandanten (Azure Active Directory)](delete-application-portal.md).

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie einmaliges Anmelden für eine App einrichten:
> [!div class="nextstepaction"]
> [Einrichten des einmaligen SAML-basierten Anmeldens](add-application-portal-setup-sso.md)

oder

> [!div class="nextstepaction"]
> [Einrichten des einmaligen OIDC-basierten Anmeldens](add-application-portal-setup-oidc-sso.md)

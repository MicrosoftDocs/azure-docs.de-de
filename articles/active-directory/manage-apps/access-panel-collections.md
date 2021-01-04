---
title: Erstellen von Sammlungen für das Portal „Meine Apps“ in Azure Active Directory | Microsoft-Dokumentation
description: Verwenden Sie „Meine Apps“-Sammlungen, um die Seiten von „Meine Apps“ anzupassen und die „Meine Apps“-Umgebung für Ihre Endbenutzer zu vereinfachen. Fassen Sie Anwendungen in Gruppen mit separaten Registerkarten zusammen.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 967ac34ddb736f58f66639181cdf10e4aaa8ffab
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656784"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Erstellen von Sammlungen im Portal „Meine Apps“

Ihre Benutzer können im Portal „Meine Apps“ die cloudbasierten Anwendungen, auf die sie zugreifen können, anzeigen und starten. Standardmäßig werden alle Anwendungen, auf die ein Benutzer zugreifen kann, auf einer einzigen Seite aufgelistet. Wenn Sie über eine Azure AD Premium P1- oder P2-Lizenz verfügen, können Sie Sammlungen einrichten, um diese Seite für Ihre Benutzer übersichtlicher zu gestalten. Mit einer Sammlung können Sie verwandte Anwendungen gruppieren (z. B. nach Auftragsrolle, Aufgabe oder Projekt) und auf separaten Registerkarten anzeigen. Eine Sammlung wendet im Wesentlichen einen Filter auf die Anwendungen an, auf die ein Benutzer bereits zugreifen kann. Dem Benutzer werden somit nur die ihm zugewiesenen Anwendungen in der Sammlung angezeigt.

> [!NOTE]
> In diesem Artikel wird beschrieben, wie ein Administrator Sammlungen aktivieren und erstellen kann. Informationen für Endbenutzer zur Verwendung des Portals „Meine Apps“ und der Sammlungen finden Sie unter [Zugreifen auf und Verwenden von Sammlungen](../user-help/my-applications-portal-workspaces.md).

## <a name="enable-the-latest-my-apps-features"></a>Aktivieren der neuesten Meine Apps-Features

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als Benutzeradministrator oder globaler Administrator an.

2. Navigieren Sie zu **Azure Active Directory** > **Benutzereinstellungen**.

3. Wählen Sie unter **Vorschauen für Benutzerfeatures** die Option **Vorschaueinstellungen für Benutzerfeatures verwalten** aus.

4. Wählen Sie unter **Benutzer können Previewfunktionen für „Meine Apps“ verwenden**, eine der folgenden Optionen aus:
   * **Ausgewählt**: Aktiviert die Features für eine bestimmte Gruppe. Verwenden Sie die Option **Eine Gruppe auswählen**, um die Gruppe auszuwählen, für die Sie die Features aktivieren möchten.  
   * **Alle**: Aktiviert die Features für alle Benutzer.

> [!NOTE]
> Benutzer können das Portal „Meine Apps“ über den Link `https://myapps.microsoft.com` oder den angepassten Link für Ihre Organisation (z.B. `https://myapps.microsoft.com/contoso.com`) öffnen. Nachdem Sie die neue Benutzeroberfläche „Meine Apps“ aktiviert haben, wird oben auf der Seite „Meine Apps“ das Banner **Eine aktualisierte Funktion "Meine Apps" ist verfügbar** angezeigt, und Benutzer können die Option zum **Testen** auswählen, um die neue Benutzeroberfläche anzuzeigen. Wenn die neue Benutzeroberfläche nicht mehr verwendet werden soll, können Benutzer oben auf der Seite auf dem Banner **Neue Oberfläche verlassen** die Option **Ja** auswählen.

## <a name="create-a-collection"></a>Erstellen einer Sammlung

Zum Erstellen einer Sammlung müssen Sie über eine Azure AD Premium P1- oder P2-Lizenz verfügen.

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als Administrator mit einer Azure AD Premium P1- oder P2-Lizenz an.

2. Wechseln Sie zu **Azure Active Directory** > **Unternehmensanwendungen**.

3. Wählen Sie unter **Verwalten** die Option **Sammlungen** aus.

4. Wählen Sie **Neue Sammlung** aus. Geben Sie auf der Seite **Neue Sammlung** einen **Namen** für die Sammlung ein (es wird empfohlen, nicht „Sammlung“ im Namen zu verwenden). Geben Sie dann eine **Beschreibung** ein.

   ![Seite „Neue Sammlung“](media/acces-panel-collections/new-collection.png)

5. Wählen Sie die Registerkarte **Anwendungen** aus. Wählen Sie **+ Anwendung hinzufügen** und dann auf der Seite **Anwendungen hinzufügen** alle Anwendungen aus, die Sie der Sammlung hinzufügen möchten, oder verwenden Sie das Feld **Suchen**, um nach Anwendungen zu suchen.

   ![Hinzufügen einer Anwendung zur Sammlung](media/acces-panel-collections/add-applications.png)

6. Wenn Sie das Hinzufügen von Anwendungen beendet haben, wählen Sie **Hinzufügen** aus. Die Liste der ausgewählten Anwendungen wird angezeigt. Mithilfe der Aufwärtspfeile können Sie die Reihenfolge der Anwendungen in der Liste ändern. Wenn Sie eine Anwendung nach unten verschieben oder aus der Sammlung löschen möchten, wählen Sie das Menü **Mehr** ( **...** ) aus.

7. Wählen Sie die Registerkarte **Besitzer** aus. Wählen Sie **+ Benutzer und Gruppen hinzufügen** aus, und wählen Sie dann auf der Seite **Benutzer und Gruppen hinzufügen** die Benutzer oder Gruppen aus, denen Sie den Besitz zuweisen möchten. Wenn Sie mit der Auswahl der Benutzer und Gruppen fertig sind, wählen Sie **Auswählen** aus.

9. Wählen Sie die Registerkarte **Benutzer und Gruppen**. Wählen Sie **+ Benutzer und Gruppen hinzufügen** aus, und wählen Sie dann auf der Seite **Benutzer und Gruppen hinzufügen** die Benutzer oder Gruppen aus, denen Sie die Sammlung zuweisen möchten. Oder verwenden Sie das Feld **Suchen**, um nach Benutzern oder Gruppen zu suchen. Wenn Sie mit der Auswahl der Benutzer und Gruppen fertig sind, wählen Sie **Auswählen** aus.

   ![Hinzufügen von Benutzern und Gruppen](media/acces-panel-collections/add-users-and-groups.png)

11. Klicken Sie auf **Überprüfen + erstellen**. Die Eigenschaften für die neue Sammlung werden angezeigt.


## <a name="view-audit-logs"></a>Anzeigen von Überwachungsprotokollen

In den Überwachungsprotokollen werden Vorgänge für „Meine Apps“-Sammlungen aufgezeichnet, einschließlich dem Erstellen von Sammlungen und Endbenutzeraktionen. Die folgenden Ereignisse werden von „Meine Apps“ generiert:

* Erstellen der Sammlung
* Sammlung bearbeiten
* Sammlung löschen
* Anwendung starten (Endbenutzer)
* Self-Service-Hinzufügung von Anwendungen (Endbenutzer)
* Self-Service-Löschung von Anwendungen (Endbenutzer)

Sie können im [Azure-Portal](https://portal.azure.com) auf die Überwachungsprotokolle zugreifen. Wählen Sie dazu **Azure Active Directory** > **Unternehmensanwendungen** > **Überwachungsprotokolle** im Abschnitt „Aktivität“ aus. Wählen Sie **Meine Apps** für **Dienst** aus.

## <a name="get-support-for-my-account-pages"></a>Anfordern von Support für die Seite(n) „Mein Konto“

Benutzer können auf der Seite „Meine Apps“ die Optionen **Mein Konto** > **Mein Konto anzeigen** auswählen, um ihre Kontoeinstellungen zu öffnen. Auf der Azure AD-Seite **Mein Konto** können Benutzer ihre Sicherheitsinformationen, Geräte, Kennwörter und vieles mehr verwalten. Die Benutzer können auch auf die Einstellungen ihres Office-Kontos zugreifen.

Falls Sie eine Supportanfrage für ein Problem mit der Azure AD-Kontoseite oder der Office-Kontoseite senden müssen, führen Sie die folgenden Schritte aus, damit Ihre Anfrage ordnungsgemäß weitergeleitet wird: 

* Bei Problemen mit der **Azure AD-Seite „Mein Konto“** öffnen Sie eine Supportanfrage im Azure-Portal. Wechseln Sie zu **Azure-Portal** > **Azure Active Directory** > **Neue Supportanfrage**.

* Bei Problemen mit der **Office-Seite „Mein Konto“** öffnen Sie eine Supportanfrage im Microsoft 365 Admin Center. Wechseln Sie zu **Microsoft 365 Admin Center** > **Support**. 

## <a name="next-steps"></a>Nächste Schritte
[Endbenutzerumgebungen für Anwendungen in Azure Active Directory](end-user-experiences.md)
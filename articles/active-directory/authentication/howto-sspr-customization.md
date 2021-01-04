---
title: Anpassen der Self-Service-Kennwortzurücksetzung – Azure Active Directory
description: Es wird beschrieben, wie Sie die Benutzeranzeige und die Umgebungsoptionen für die Self-Service-Kennwortzurücksetzung in Azure AD anpassen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3766db0d5070f15216ac612353e2c25737ad092a
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741625"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Anpassen der Benutzeroberfläche für die Self-Service-Kennwortzurücksetzung von Azure Active Directory

Mit der Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) in Azure Active Directory (Azure AD) können Benutzer ihr Kennwort ohne Beteiligung eines Administrators oder des Helpdesks ändern oder zurücksetzen. Wenn das Konto eines Benutzers gesperrt ist oder dieser sein Kennwort vergessen hat, kann er die Schritte zum Entsperren ausführen und anschließend weiterarbeiten. Dies führt zu weniger Anrufen beim Helpdesk und Produktivitätsverlusten, wenn sich ein Benutzer nicht an seinem Gerät oder einer Anwendung anmelden kann.

Zur Verbesserung der SSPR-Umgebung für Benutzer können Sie das Erscheinungsbild der Seite für die Kennwortzurücksetzung, von E-Mail-Benachrichtigungen oder der Anmeldeseiten anpassen. Mit diesen Anpassungsoptionen können Sie für Benutzer verdeutlichen, dass sie sich am richtigen Ort befinden und sicher sein können, dass sie auf Unternehmensressourcen zugreifen.
    
In diesem Artikel wird veranschaulicht, wie Sie den SSPR-E-Mail-Link für Benutzer, das Unternehmensbranding und den Link für die AD FS-Anmeldeseite anpassen.

## <a name="customize-the-contact-your-administrator-link"></a>Anpassen des Links „Wenden Sie sich an Ihren Administrator“

Um für Benutzer den Zugang zu Hilfe zur Self-Service-Kennwortzurücksetzung zu erleichtern, wird im Portal für die Kennwortzurücksetzung der Link „Wenden Sie sich an Ihren Administrator“ angezeigt. Wenn ein Benutzer auf diesen Link klickt, gibt es zwei Möglichkeiten:

* Falls dieser Link für die Kontaktaufnahme im Standardzustand belassen wurde, wird eine E-Mail an Ihre Administratoren gesendet, in der um Hilfe beim Ändern des Benutzerkennworts gebeten wird. Hier ist ein Beispiel für diese E-Mail angegeben:

    ![Beispiel für eine E-Mail an den Administrator mit der Bitte um Zurücksetzung](./media/howto-sspr-customization/sspr-contact-admin.png)

* Falls das Verhalten angepasst wurde, wird der Benutzer an eine vom Administrator angegebene Webseite oder E-Mail-Adresse weitergeleitet, um Hilfe bereitzustellen.
    * Für die Anpassung empfehlen wir Ihnen die Verwendung eines Verfahrens, mit dem Benutzer in Bezug auf den Support bereits vertraut sind.

    > [!WARNING]
    > Wenn Sie diese Einstellung mit einer E-Mail-Adresse und einem Konto anpassen, für die bzw. das eine Kennwortzurücksetzung erforderlich ist, können die Benutzer möglicherweise keine Unterstützung anfordern.

### <a name="default-email-behavior"></a>Standardverhalten für E-Mail

Die Standard-E-Mail für die Kontaktaufnahme wird in der folgenden Reihenfolge an die Empfänger gesendet:

1. Wenn die Rolle *Heldeskadministrator* oder *Kennwortadministrator* zugewiesen ist, werden die Administratoren mit dieser Rolle benachrichtigt.
1. Falls kein Helpdesk- oder Kennwortadministrator zugewiesen ist, werden die Administratoren mit der Rolle *Benutzeradministrator* benachrichtigt.
1. Wenn keine der obigen Rollen zugewiesen ist, werden die *globalen Administratoren* benachrichtigt.

In jedem Fall werden bis zu 100 Empfänger benachrichtigt.

Weitere Informationen zu den verschiedenen Administratorrollen und wie sie zugewiesen werden können finden Sie unter [Zuweisen von Administratorrollen in Azure Active Directory](../roles/permissions-reference.md).

### <a name="disable-contact-your-administrator-emails"></a>Deaktivieren von „Wenden Sie sich an Ihren Administrator“-E-Mails

Wenn Ihre Organisation nicht möchte, dass Administratoren über Anforderungen zur Kennwortzurücksetzung benachrichtigt werden, können die folgenden Konfigurationsoptionen verwendet werden:

* Passen Sie den Helpdesklink an, um eine Web-URL oder eine mailto:-Adresse bereitzustellen, die Benutzer verwenden können, um Unterstützung zu erhalten. Diese Option finden Sie unter **Zurücksetzen des Kennworts** > **Anpassung** > **Benutzerdefinierte Helpdesk-E-Mail oder -URL**.
* Aktivieren Sie die Self-Service-Kennwortzurücksetzung für alle Benutzer. Diese Option finden Sie unter **Zurücksetzen des Kennworts** > **Eigenschaften**. Wenn Sie nicht möchten, dass Benutzer ihre eigenen Kennwörter zurücksetzen, können Sie den Zugriff auf eine leere Gruppe beschränken. *Dies ist nicht zu empfehlen.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>Anpassen der Anmeldeseite und des Zugriffsbereichs

Sie können die Anmeldeseite anpassen, indem Sie beispielsweise ein Logo hinzufügen, das zusammen mit dem Bild für Ihr Unternehmensbranding angezeigt wird. Weitere Informationen zur Konfiguration des Unternehmensbrandings finden Sie unter [Hinzufügen eines Unternehmensbrandings zur Anmeldeseite in Azure AD](../fundamentals/customize-branding.md).

Die von Ihnen ausgewählten Grafiken werden in den folgenden Situationen angezeigt:

* Nachdem ein Benutzer seinen Benutzernamen eingegeben hat
* Wenn der Benutzer auf die angepasste URL zugreift:
   * Durch Übergeben des `whr`-Parameters an die Seite zum Zurücksetzen des Kennworts, z. B. `https://login.microsoftonline.com/?whr=contoso.com`
   * Durch Übergeben des `username`-Parameters an die Seite zum Zurücksetzen des Kennworts, z. B. `https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>Verzeichnisname

Sie können den Organisationsnamen im Portal und in den automatisierten Nachrichten ändern, um die Benutzerfreundlichkeit zu erhöhen. Navigieren Sie zu **Azure Active Directory** > **Eigenschaften**, um das Attribut für den Verzeichnisnamen im Azure-Portal zu ändern. Diese Option zum Einfügen eines benutzerfreundlichen Organisationsnamens verfügt bei automatisierten E-Mails über die größte Sichtbarkeit. Dies ist in den folgenden Beispielen dargestellt:

* Anzeigename in einer E-Mail, z. B. *Microsoft im Namen von CONTOSO – Demo*.
* Betreffzeile einer E-Mail, z. B. *E-Mail-Prüfnummer für CONTOSO-Demokonto*.

## <a name="customize-the-ad-fs-sign-in-page"></a>Anpassen der AD FS-Anmeldeseite

Wenn Sie Active Directory-Verbunddienste (AD FS) für Ereignisse von Benutzeranmeldungen verwenden, können Sie der Anmeldeseite einen Link hinzufügen. Lesen Sie hierzu die Anleitung unter [Hinzufügen einer Beschreibung zur Anmeldeseite](/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Geben Sie für Benutzer einen Link zu der Seite an, über die sie auf den SSPR-Workflow zugreifen können, z. B. *https://passwordreset.microsoftonline.com* . Um der AD FS-Anmeldeseite einen Link hinzuzufügen, verwenden Sie den folgenden Befehl auf dem AD FS-Server:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Nutzung von SSPR in Ihrer Umgebung finden Sie unter [Berichterstellungsoptionen für die Kennwortverwaltung von Azure AD](howto-sspr-reporting.md).

Falls Sie oder Ihre Benutzer Probleme mit SSPR haben, helfen Ihnen die Informationen unter [Behandeln von Problemen mit der Self-Service-Kennwortzurücksetzung](./troubleshoot-sspr.md) weiter.
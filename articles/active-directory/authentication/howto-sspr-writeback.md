---
title: 'Gewusst wie: Konfigurieren von Kennwortrückschreiben für Azure AD SSPR – Azure Active Directory'
description: Verwenden von Azure AD und Azure AD Connect zum Rückschreiben von Kennwörtern in ein lokales Verzeichnis
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17a2661883dd069e8cb719672f6b92442f1a8a0a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60357500"
---
# <a name="how-to-configure-password-writeback"></a>Gewusst wie: Konfigurieren von Kennwortrückschreiben

Für die folgenden Schritte wird davon ausgegangen, dass Sie Azure AD Connect bereits in Ihrer Umgebung konfiguriert haben, indem Sie die [Express](../hybrid/how-to-connect-install-express.md)- oder [benutzerdefinierten](../hybrid/how-to-connect-install-custom.md) Einstellungen verwendet haben.

1. Um das Kennwortrückschreiben zu konfigurieren und zu aktivieren, melden Sie sich bei Ihrem Azure AD Connect-Server an, und starten Sie den **Azure AD Connect**-Konfigurations-Assistenten.
2. Wählen Sie auf der Seite **Willkommen** die Option **Konfigurieren** aus.
3. Wählen Sie auf der Seite **Weitere Aufgaben** die Option **Synchronisierungsoptionen anpassen** aus, und wählen Sie dann **Weiter** aus.
4. Geben Sie auf der Seite **Mit Azure AD verbinden** die Anmeldeinformationen eines globalen Administrators ein, und wählen Sie dann **Weiter** aus.
5. Wählen Sie auf den Seiten **Verzeichnisse verbinden** und **Domänen-/OE-Filterung** die Schaltfläche **Weiter** aus.
6. Aktivieren Sie auf der Seite **Optionale Features** das Kontrollkästchen neben **Kennwortrückschreiben**, und wählen Sie **Weiter** aus.
   ![Aktivieren des Kennwortrückschreibens in Azure AD Connect][Writeback]
7. Wählen Sie auf der Seite **Bereit zur Konfiguration** die Option **Konfigurieren** aus, und warten Sie, bis der Vorgang abgeschlossen ist.
8. Wenn Sie sehen, dass die Konfiguration beendet ist, wählen Sie **Beenden** aus.

Informationen zu allgemeinen Aufgaben zur Problembehandlung im Zusammenhang mit dem Kennwortrückschreiben finden Sie im Artikel zur Problembehandlung im Abschnitt [Problembehandlung: Kennwortrückschreiben](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback).

> [!WARNING]
> Das Kennwortrückschreiben funktioniert nicht mehr für Kunden, die Azure AD Connect-Versionen bis 1.0.8641.0 verwenden, wenn der [Azure Access Control Service (ACS) am 7. November 2018 eingestellt wird](../develop/active-directory-acs-migration.md). Azure AD Connect-Versionen bis 1.0.8641.0 lassen ab diesem Zeitpunkt kein Kennwortrückschreiben mehr zu, da sie für diese Funktionalität von ACS abhängen.
>
> Um eine Dienstunterbrechung zu vermeiden, führen Sie bei früheren Versionen von Azure AD Connect ein Upgrade auf eine neuere Version durch. Weitere Informationen finden Sie unter [Azure AD Connect: Aktualisieren von einer früheren Version auf die aktuelle Version](../hybrid/how-to-upgrade-previous-version.md).
>

## <a name="licensing-requirements-for-password-writeback"></a>Lizenzierungsanforderungen für das Kennwortrückschreiben

**Self-Service-Kennwortzurücksetzung/-änderung/-entsperrung mit lokalem Rückschreiben ist eine Premium-Funktion von Azure AD**. Weitere Informationen zur Lizenzierung finden Sie auf der [Preiswebsite für Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Damit Kennwortrückschreiben verwendet werden kann, muss in Ihrem Mandanten eine der folgenden Lizenzen zugewiesen sein:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 oder A3
* Enterprise Mobility + Security E5 oder A5
* Microsoft 365 E3 oder A3
* Microsoft 365 E5 oder A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Eigenständige Office 365-Lizenzierungspläne *bieten keine Unterstützung für Self-Service-Kennwortzurücksetzung/-änderung/-entsperrung mit lokalem Rückschreiben* und erfordern, dass Sie einen der obigen Pläne haben, damit diese Funktion verwendbar ist.
>

## <a name="active-directory-permissions"></a>Active Directory-Berechtigungen

Für das in Azure AD Connect angegebene Konto müssen die folgenden Elemente festgelegt sein, wenn SSPR möglich sein soll:

* **Zurücksetzen des Kennworts** 
* **Kennwort ändern** 
* **Schreibberechtigungen** auf `lockoutTime`
* **Schreibberechtigungen** auf `pwdLastSet`
* **Erweiterte Rechte** für eines der folgenden Elemente:
   * Das Stammobjekt von *jeder Domäne* in dieser Gesamtstruktur
   * Der Benutzerorganisationseinheiten, für die SSPR möglich sein soll

Wenn Sie nicht sicher sind, auf welches Konto sich das beschriebene Konto bezieht, öffnen Sie die Azure Active Directory Connect-Konfigurationsoberfläche, und wählen Sie die Option **Aktuelle Konfiguration anzeigen** aus. Das Konto, dem Sie Berechtigungen hinzufügen müssen, wird unter **Synchronisierte Verzeichnisse** angezeigt.

Wenn Sie diese Berechtigungen festlegen, kann das MA-Dienstkonto für jede Gesamtstruktur Kennwörter im Namen der Benutzerkonten innerhalb dieser Gesamtstruktur verwalten. 

> [!IMPORTANT]
> Wenn Sie diese Berechtigungen nicht gewähren, erhalten die Benutzer – obwohl das Zurückschreiben scheinbar ordnungsgemäß konfiguriert ist – Fehler bei dem Versuch, ihre lokalen Kennwörter über die Cloud zu verwalten.
>

> [!NOTE]
> Es kann bis zu einer Stunde oder länger dauern, bis diese Berechtigungen an alle Objekte in Ihrem Verzeichnis repliziert wurden.
>

Um die entsprechenden Berechtigungen für das Kennwortrückschreiben einzurichten, führen Sie die folgenden Schritte aus:

1. Öffnen Sie „Active Directory-Benutzer und -Computer“ mit einem Konto, das geeignete Berechtigungen für die Domänenverwaltung hat.
2. Vergewissern Sie sich, dass im Menü **Ansicht** die Option **Erweiterte Features** aktiviert ist.
3. Klicken Sie im linken Bereich mit der rechten Maustaste auf das Objekt, das den Stamm der Domäne repräsentiert, und wählen Sie **Eigenschaften** > **Sicherheit** > **Erweitert** aus.
4. Wählen Sie auf der Registerkarte **Berechtigungen** die Option **Hinzufügen** aus.
5. Wählen Sie das Konto aus, dem Berechtigungen zugewiesen werden sollen (vom Azure AD Connect-Setup).
6. Wählen Sie in der Dropdownliste **Gilt für** den Eintrag **Nachfolgerbenutzerobjekte** aus.
7. Aktivieren Sie unter **Berechtigungen** die Kontrollkästchen für folgende Optionen:
    * **Kennwort ändern**
    * **Zurücksetzen des Kennworts**
8. Aktivieren Sie unter **Eigenschaften** die Kontrollkästchen für folgende Optionen:
    * **lockoutTime schreiben**
    * **pwdLastSet schreiben**
9. Wählen Sie auf **Übernehmen/OK** aus, um die Änderungen zu übernehmen und alle geöffneten Dialogfelder zu schließen.

## <a name="next-steps"></a>Nächste Schritte

[Was ist die Kennwortrückschreibung?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Aktivieren des Kennwortrückschreibens in Azure AD Connect"

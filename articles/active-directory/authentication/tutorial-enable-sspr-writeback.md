---
title: Aktivieren des Azure Active Directory-Kennwortrückschreibens
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure AD Connect das Rückschreiben von Azure AD-Self-Service-Kennwortzurücksetzungen aktivieren, um Änderungen mit einer lokalen Active Directory Domain Services-Umgebung zu synchronisieren.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 444ca19732921b336cae32a9b1eb5755a08e4bd3
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028052"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>Tutorial: Aktivieren des Rückschreibens von Azure Active Directory-Self-Service-Kennzurücksetzungen in eine lokale Umgebung

Mit der Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) in Azure Active Directory (Azure AD) können Benutzer über einen Webbrowser ihre Kennwörter aktualisieren oder ihre Konten entsperren. In einer Hybridumgebung, in der Azure AD mit einer lokalen Active Directory Domain Services-Umgebung (AD DS) verbunden ist, kann dieses Szenario dazu führen, dass sich Kennwörter zwischen den beiden Verzeichnissen unterscheiden.

Das Kennwortrückschreiben kann genutzt werden, um Kennwortänderungen in Azure AD mit Ihrer lokalen AD DS-Umgebung zu synchronisieren. Azure AD Connect bietet einen sicheren Mechanismus zum Zurücksenden dieser Kennwortänderungen in ein vorhandenes lokales Verzeichnis aus Azure AD.

> [!IMPORTANT]
> In diesem Tutorial wird erläutert, wie ein Administrator die Self-Service-Kennwortzurücksetzung wieder in einer lokalen Umgebung aktiviert. Wenn Sie bereits als Endbenutzer für die Self-Service-Kennwortzurücksetzung registriert sind und wieder zu Ihrem Konto zurückkehren müssen, gehen Sie zu https://aka.ms/sspr.
>
> Wenn Ihr IT-Team die Möglichkeit zum Zurücksetzen Ihres eigenen Kennworts nicht aktiviert hat, wenden Sie sich an den Helpdesk, um weitere Unterstützung zu erhalten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren der erforderlichen Berechtigungen für das Kennwortrückschreiben
> * Aktivieren der Option für das Kennwortrückschreiben in Azure AD Connect
> * Aktivieren des Kennwortrückschreibens in Azure AD-SSPR

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Einen funktionierenden Azure AD-Mandanten mit mindestens einer aktivierten Azure AD Premium P1- oder -Testlizenz.
    * Erstellen Sie ggf. [ein kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
    * Weitere Informationen finden Sie unter [Lizenzanforderungen für Azure AD-Self-Service-Kennwortzurücksetzung](concept-sspr-licensing.md).
* Ein Konto mit Berechtigungen vom Typ *Globaler Administrator*.
* Für Self-Service-Kennwortzurücksetzung konfigurierte Azure AD-Instanz
    * Absolvieren Sie ggf. das [vorherige Tutorial zum Aktivieren der Self-Service-Kennwortzurücksetzung von Azure AD](tutorial-enable-sspr.md).
* Eine vorhandene lokale AD DS-Umgebung, die mit einer aktuellen Version von Azure AD Connect konfiguriert ist
    * Konfigurieren Sie ggf. Azure AD Connect unter Verwendung der [Express-Einstellungen](../hybrid/how-to-connect-install-express.md) oder der [benutzerdefinierten Einstellungen](../hybrid/how-to-connect-install-custom.md).
    * Damit Sie Kennwortrückschreiben verwenden können, müssen Ihre Domänencontroller unter Windows Server 2012 oder höher ausgeführt werden.

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Konfigurieren der Kontoberechtigungen für Azure AD Connect

Mithilfe von Azure AD Connect können Sie Benutzer, Gruppen und Anmeldeinformationen zwischen einer lokalen AD DS-Umgebung und Azure AD synchronisieren. In der Regel installieren Sie Azure AD Connect auf einem Computer mit mindestens Windows Server 2012, der in die lokale AD DS-Domäne eingebunden ist.

Damit Sie das SSPR-Rückschreiben ordnungsgemäß verwenden können, müssen für das in Azure AD Connect angegebene Konto die entsprechenden Berechtigungen und Optionen festgelegt sein. Wenn Sie nicht sicher sind, welches Konto derzeit verwendet wird, öffnen Sie Azure AD Connect, und wählen Sie die Option **Aktuelle Konfiguration anzeigen** aus. Das Konto, dem Sie Berechtigungen hinzufügen müssen, wird unter **Synchronisierte Verzeichnisse** angezeigt. Für das Konto müssen die folgenden Berechtigungen und Optionen festgelegt werden:

* **Zurücksetzen des Kennworts**
* **Schreibberechtigungen** auf `lockoutTime`
* **Schreibberechtigungen** auf `pwdLastSet`
* **Erweiterte Rechte** für „Abgelaufenes Kennwort wiederherstellen“ für das Stammobjekt *jeder Domäne* in dieser Gesamtstruktur, sofern noch nicht festgelegt.

Wenn Sie diese Berechtigungen nicht zuweisen, ist das Rückschreiben scheinbar ordnungsgemäß konfiguriert, Benutzer erhalten jedoch Fehler bei dem Versuch, ihre lokalen Kennwörter über die Cloud zu verwalten. Berechtigungen müssen für **Dieses und alle untergeordneten Objekte** angewendet werden, damit „Abgelaufenes Kennwort wiederherstellen“ angezeigt wird.  

> [!TIP]
>
> Wenn Kennwörter für einige Benutzerkonten nicht in das lokale Verzeichnis zurückgeschrieben werden, stellen Sie sicher, dass die Vererbung für das Konto in der lokalen AD DS-Umgebung nicht deaktiviert ist. Schreibberechtigungen für Kennwörter müssen auf Nachfolgerobjekte angewendet werden, damit die Funktion ordnungsgemäß funktioniert.

Um die entsprechenden Berechtigungen für das Kennwortrückschreiben einzurichten, führen Sie die folgenden Schritte aus:

1. Öffnen Sie in Ihrer lokalen AD DS-Umgebung **Active Directory-Benutzer und -Computer** mit einem Konto, das geeignete *Domänenadministratorberechtigungen* hat.
1. Vergewissern Sie sich, dass im Menü **Ansicht** die Option **Erweiterte Features** aktiviert ist.
1. Klicken Sie im linken Bereich mit der rechten Maustaste auf das Objekt, das den Stamm der Domäne repräsentiert, und wählen Sie **Eigenschaften** > **Sicherheit** > **Erweitert** aus.
1. Wählen Sie auf der Registerkarte **Berechtigungen** die Option **Hinzufügen** aus.
1. Wählen Sie unter **Prinzipal** das Konto aus, auf das die Berechtigungen angewendet werden sollen (das von Azure AD Connect verwendete Konto).
1. Wählen Sie in der Dropdownliste **Gilt für** den Eintrag **Nachfolgerbenutzerobjekte** aus.
1. Aktivieren Sie unter *Berechtigungen* das Kontrollkästchen für die folgende Option:
    * **Zurücksetzen des Kennworts**
1. Aktivieren Sie unter *Eigenschaften* die Kontrollkästchen für folgende Optionen. Scrollen Sie durch die Liste, um zu diesen Optionen zu gelangen. Möglicherweise sind sie bereits standardmäßig festgelegt:
    * **lockoutTime schreiben**
    * **pwdLastSet schreiben**

    [ ![Festlegen der entsprechenden Berechtigungen unter „Active Directory-Benutzer und -Computer“ für das von Azure AD Connect verwendete Konto](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png) ](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. Wählen Sie abschließend **Übernehmen/OK** aus, um die Änderungen zu übernehmen und alle geöffneten Dialogfelder zu schließen.

Wenn Sie Berechtigungen aktualisieren, kann es bis zu einer Stunde oder länger dauern, bis diese Berechtigungen an alle Objekte in Ihrem Verzeichnis repliziert wurden.

Kennwortrichtlinien in der lokalen AD DS-Umgebung verhindern unter Umständen, dass Kennwortzurücksetzungen ordnungsgemäß verarbeitet werden. Damit Kennwortrückschreiben möglichst effizient funktioniert, muss die Gruppenrichtlinie für *Minimales Kennwortalter* auf „0“ festgelegt werden. Diese Einstellung finden Sie in `gpedit.msc` unter **Computerkonfiguration > Richtlinien > Windows-Einstellungen > Sicherheitseinstellungen > Kontorichtlinien**.

Warten Sie beim Aktualisieren der Gruppenrichtlinie, bis die aktualisierte Richtlinie repliziert wurde, oder verwenden Sie den Befehl `gpupdate /force`.

> [!Note]
> Damit Kennwörter sofort geändert werden können, muss für das Kennwortrückschreiben die Einstellung „0“ festgelegt werden. Wenn sich Benutzer aber an die lokalen Richtlinien halten und *Minimales Kennwortalter* auf einen höheren Wert als „0“ festgelegt ist, funktioniert das Kennwortrückschreiben auch nach dem Auswerten der lokalen Richtlinien noch.

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Aktivieren des Kennwortrückschreibens in Azure AD Connect

Eine Konfigurationsoption in Azure AD Connect ist das Kennwortrückschreiben. Wenn diese Option aktiviert ist, führen Kennwortänderungsereignisse dazu, dass Azure AD Connect die aktualisierten Anmeldeinformationen wieder mit der lokalen AD DS-Umgebung synchronisiert.

Aktivieren Sie zum Aktivieren der SSPR zunächst die Rückschreiboption in Azure AD Connect. Führen Sie auf dem Azure AD Connect-Server die folgenden Schritte aus:

1. Melden Sie sich bei Ihrem Azure AD Connect-Server an, und starten Sie den **Azure AD Connect**-Konfigurations-Assistenten.
1. Wählen Sie auf der Seite **Willkommen** die Option **Konfigurieren** aus.
1. Wählen Sie auf der Seite **Weitere Aufgaben** die Option **Synchronisierungsoptionen anpassen** aus, und wählen Sie dann **Weiter** aus.
1. Geben Sie auf der Seite **Mit Azure AD verbinden** die Anmeldeinformationen eines globalen Administrators für Ihren Azure-Mandanten ein, und wählen Sie dann **Weiter** aus.
1. Wählen Sie auf den Seiten **Verzeichnisse verbinden** und **Domänen-/OE-Filterung** die Schaltfläche **Weiter** aus.
1. Aktivieren Sie auf der Seite **Optionale Features** das Kontrollkästchen neben **Kennwortrückschreiben**, und wählen Sie **Weiter** aus.

    ![Konfigurieren von Azure AD Connect für das Kennwortrückschreiben](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. Wählen Sie auf der Seite **Bereit zur Konfiguration** die Option **Konfigurieren** aus, und warten Sie, bis der Vorgang abgeschlossen ist.
1. Wenn Sie sehen, dass die Konfiguration beendet ist, wählen Sie **Beenden** aus.

## <a name="enable-password-writeback-for-sspr"></a>Aktivieren des Kennwortrückschreibens für Self-Service-Kennwortzurücksetzung

Wenn das Kennwortrückschreiben in Azure AD Connect aktiviert ist, konfigurieren Sie die Azure AD-Self-Service-Kennwortzurücksetzung für das Rückschreiben. Wenn Sie SSPR für die Nutzung des Kennwortrückschreibens aktivieren, wird für Benutzer, die ihr Kennwort ändern oder zurücksetzen, dieses aktualisierte Kennwort ebenfalls wieder mit der lokalen AD DS-Umgebung synchronisiert.

Führen Sie zum Aktivieren des Kennwortrückschreibens in SSPR die folgenden Schritte aus:

1. Melden Sie sich mit dem globalen Administratorkonto am [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie die Option aus. Wählen Sie anschließend **Kennwortzurücksetzung** und dann **Lokale Integration** aus.
1. Legen Sie die Option **Kennwörter in Ihr lokales Verzeichnis zurückschreiben?** auf *Ja* fest.
1. Legen Sie die Option **Benutzern das Entsperren von Konten ohne Zurücksetzen des Kennworts erlauben?** auf *Ja* fest.

    ![Aktivieren der Azure AD-Self-Service-Kennwortzurücksetzung für das Kennwortrückschreiben](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. Wählen Sie **Speichern** aus, wenn Sie so weit sind.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die im Rahmen dieses Tutorials konfigurierte Funktionalität für SSPR-Rückschreiben nicht mehr nutzen möchten, gehen Sie wie folgt vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie die Option aus. Wählen Sie anschließend **Kennwortzurücksetzung** und dann **Lokale Integration** aus.
1. Legen Sie die Option **Kennwörter in Ihr lokales Verzeichnis zurückschreiben?** auf *Nein* fest.
1. Legen Sie die Option **Benutzern das Entsperren von Konten ohne Zurücksetzen des Kennworts erlauben?** auf *Nein* fest.

Wenn Sie keine Kennwortfunktionalität mehr nutzen möchten, führen Sie auf dem Azure AD Connect-Server die folgenden Schritte aus:

1. Melden Sie sich bei Ihrem Azure AD Connect-Server an, und starten Sie den **Azure AD Connect**-Konfigurations-Assistenten.
1. Wählen Sie auf der Seite **Willkommen** die Option **Konfigurieren** aus.
1. Wählen Sie auf der Seite **Weitere Aufgaben** die Option **Synchronisierungsoptionen anpassen** aus, und wählen Sie dann **Weiter** aus.
1. Geben Sie auf der Seite **Mit Azure AD verbinden** die Anmeldeinformationen eines globalen Administrators für Ihren Azure-Mandanten ein, und wählen Sie dann **Weiter** aus.
1. Wählen Sie auf den Seiten **Verzeichnisse verbinden** und **Domänen-/OE-Filterung** die Schaltfläche **Weiter** aus.
1. Deaktivieren Sie auf der Seite **Optionale Features** das Kontrollkästchen neben **Kennwortrückschreiben**, und wählen Sie **Weiter** aus.
1. Wählen Sie auf der Seite **Bereit zur Konfiguration** die Option **Konfigurieren** aus, und warten Sie, bis der Vorgang abgeschlossen ist.
1. Wenn Sie sehen, dass die Konfiguration beendet ist, wählen Sie **Beenden** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie das Azure AD-SSPR-Rückschreiben für eine lokale AD DS-Umgebung aktiviert. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Konfigurieren der erforderlichen Berechtigungen für das Kennwortrückschreiben
> * Aktivieren der Option für das Kennwortrückschreiben in Azure AD Connect
> * Aktivieren des Kennwortrückschreibens in Azure AD-SSPR

> [!div class="nextstepaction"]
> [Tutorial: Use risk events to trigger Multi-Factor Authentication and password changes](tutorial-risk-based-sspr-mfa.md) (Tutorial: Auslösen von Multi-Factor Authentication und Kennwortänderungen mithilfe von Risikoereignissen)

---
title: Konfigurieren des einmaligen Anmeldens – Azure Active Directory | Microsoft-Dokumentation
description: In diesem Tutorial wird das Azure-Portal verwendet, um SAML-basiertes einmaliges Anmelden (Single Sign-On, SSO) für eine Anwendung mit Azure Active Directory (Azure AD) zu konfigurieren.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa18bc637ec31a1f83b5cab090e008715c5e0c2a
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825014"
---
# <a name="tutorial-configure-saml-based-single-sign-on-for-an-application-with-azure-active-directory"></a>Tutorial: Konfigurieren des SAML-basierten einmaligen Anmeldens für eine Anwendung mit Azure Active Directory

In diesem Tutorial wird das [Azure-Portal](https://portal.azure.com) verwendet, um SAML-basiertes einmaliges Anmelden für eine Anwendung mit Azure Active Directory (Azure AD) zu konfigurieren. Verwenden Sie dieses Tutorial, wenn kein [anwendungsspezifisches Tutorial](../saas-apps/tutorial-list.md) verfügbar ist. 

In diesem Tutorial verwenden Sie das Azure-Portal zu folgenden Zwecken:

> [!div class="checklist"]
> * Auswählen des Modus für SAML-basiertes einmaliges Anmelden
> * Konfigurieren von anwendungsspezifischen Domänen und URLs
> * Konfigurieren von Benutzerattributen
> * Erstellen eines SAML-Signaturzertifikats
> * Zuweisen von Benutzern zur Anwendung
> * Konfigurieren der Anwendung für SAML-basiertes einmaliges Anmelden
> * Testen der SAML-Einstellungen

## <a name="before-you-begin"></a>Voraussetzungen

1. Wenn die Anwendung Ihrem Azure AD-Mandanten nicht hinzugefügt wurde, finden Sie unter [Schnellstart: Hinzufügen einer Anwendung zum Azure AD-Mandanten](add-application-portal.md) weitere Informationen.

2. Bitten Sie den Anwendungshersteller um die unter [Konfigurieren grundlegender SAML-Optionen](#configure-basic-saml-options) beschriebenen Informationen.

3. Verwenden Sie zum Testen der Schritte in diesem Tutorial keine Produktionsumgebung. Wenn Sie keine solche Umgebung besitzen, können Sie eine [einmonatige Testversion anfordern](https://azure.microsoft.com/pricing/free-trial/).

4. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Cloudanwendungsadministrator oder Anwendungsadministrator für Ihren Azure AD-Mandanten an.

## <a name="select-a-single-sign-on-mode"></a>Auswählen eines Modus für einmaliges Anmelden

Nach dem Hinzufügen einer Anwendung zum Azure AD-Mandanten können Sie einmaliges Anmelden für die Anwendung konfigurieren.

So öffnen Sie die Einstellungen für einmaliges Anmelden:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsbereich **Azure Active Directory** aus. 

2. Klicken Sie im eingeblendeten **Azure Active Directory**-Navigationsbereich unter **Verwalten** auf **Unternehmensanwendungen** . Eine zufällige Stichprobe von Anwendungen in Ihrem Azure AD-Mandanten wird angezeigt. 

3. Wählen Sie im Menü **Anwendungstyp** die Option **Alle Anwendungen** und dann **Übernehmen** aus.

4. Geben Sie den Namen der Anwendung ein, für die Sie einmaliges Anmelden konfigurieren möchten. Sie können beispielsweise **GitHub-test** eingeben, um die im Schnellstart zum [Hinzufügen einer Anwendung](add-application-portal.md) hinzugefügte Anwendung zu konfigurieren.  

     ![Screenshot mit der Suchleiste der Anwendung](media/configure-single-sign-on-portal/azure-portal-application-search.png)

5. Wählen Sie die Anwendung, für die Sie einmaliges Anmelden konfigurieren möchten.

6. Wählen Sie im Abschnitt **Verwalten** die Option **Einmaliges Anmelden** aus. 

7. Wählen Sie **SAML** aus, um einmaliges Anmelden zu konfigurieren. Die Seite **Einmaliges Anmelden (SSO) mit SAML einrichten – Vorschau** wird angezeigt.

## <a name="configure-basic-saml-options"></a>Konfigurieren grundlegender SAML-Optionen

So konfigurieren Sie die Domäne und URLs:

1. Wenden Sie sich an den Anwendungshersteller, um die richtigen Informationen für die folgenden Einstellungen zu erhalten:

    | Konfigurationseinstellung | Vom Dienstanbieter initiiert | Vom Identitätsanbieter initiiert | BESCHREIBUNG |
    |:--|:--|:--|:--|
    | Bezeichner (Entitäts-ID) | Für einige Apps erforderlich | Für einige Apps erforderlich | Er identifiziert eindeutig die Anwendung, für die einmaliges Anmelden konfiguriert wird. Azure AD sendet den Bezeichner als Audience-Parameter des SAML-Tokens an die Anwendung. Von der Anwendung wird erwartet, dass sie diesen Parameter überprüft. Dieser Wert ist auch als Entitäts-ID in SAML-Metadaten enthalten, die von der Anwendung bereitgestellt werden.|
    | Antwort-URL | Optional | Erforderlich | Gibt an, ob die Anwendung den Empfang des SAML-Tokens erwartet. Die Antwort-URL wird auch als „Assertionsverbraucherdienst-URL“ (Assertion Consumer Service, ACS) bezeichnet. |
    | Anmelde-URL | Erforderlich | Nicht angeben | Wenn ein Benutzer diese URL öffnet, wird er vom Dienstanbieter zur Authentifizierung und Anmeldung an Azure AD umgeleitet. Azure AD verwendet die URL, um die Anwendung über Office 365 oder den Azure AD-Zugriffsbereich zu starten. Wird keine URL angegeben, verwendet Azure AD den Identitätsanbieter, um einmaliges Anmelden zu aktivieren, wenn ein Benutzer die Anwendung startet.|
    | Relayzustand | Optional | Optional | Mit dieser Option wird die Anwendung darüber informiert, wohin der Benutzer nach der Authentifizierung umgeleitet werden soll. In der Regel ist der Wert eine für die Anwendung gültige URL. Einige Anwendungen verwenden dieses Feld jedoch anders. Weitere Informationen erhalten Sie vom Anwendungshersteller.
    | Abmelde-URL | Optional | Optional | Wird verwendet, um die SAML-Abmeldeantworten an die Anwendung zurückzusenden.


2. Um die grundlegenden SAML-Konfigurationsoptionen zu bearbeiten, klicken Sie rechts oben im Abschnitt **Grundlegende SAML-Konfiguration** auf das Symbol **Bearbeiten** (Stift).

     ![Konfigurieren von Zertifikaten](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

3. Geben Sie in die entsprechenden Felder auf der Seite die Informationen ein, die der Anwendungsanbieter in Schritt 1 bereitgestellt hat.

4. Klicken Sie am oberen Rand der Seite auf **Speichern**.

## <a name="configure-user-attributes-and-claims"></a>Konfigurieren von Benutzerattributen und Ansprüchen 

Sie können steuern, welche Informationen Azure AD bei der Anmeldung eines Benutzers im SAML-Token an die Anwendung sendet. Sie steuern diese Informationen, indem Sie Benutzerattribute konfigurieren. Sie können beispielsweise Azure AD so konfigurieren, dass es den Namen, die E-Mail-Adresse und die Mitarbeiter-ID des Benutzers an die Anwendung sendet, wenn sich ein Benutzer anmeldet. 

Diese Attribute können erforderlich oder optional sein, damit einmaliges Anmelden ordnungsgemäß funktioniert. Weitere Informationen finden Sie im [anwendungsspezifischen Tutorial](../saas-apps/tutorial-list.md), oder wenden Sie sich an den Anwendungshersteller, um diese Informationen zu erhalten.

1. Klicken Sie zum Bearbeiten von Benutzerattributen und Ansprüchen rechts oben im Abschnitt **Benutzerattribute und Ansprüche** auf das Symbol **Bearbeiten** (Stift).

   Der **Wert für Namensbezeichner** wird auf den Standardwert *benutzer.prinzipalname* festgelegt. Mit der Benutzer-ID wird jeder Benutzer in der Anwendung eindeutig identifiziert. Beispiel: Ist die E-Mail-Adresse sowohl der Benutzername als auch der eindeutige Bezeichner, legen Sie den Wert auf *user.mail* fest.

2. Um den **Wert für Namensbezeichner** zu ändern, klicken Sie für das Feld **Wert für Namensbezeichner** auf das Symbol **Bearbeiten** (Stift). Nehmen Sie bei Bedarf die entsprechenden Änderungen am Format und an der Quelle des Bezeichners vor. Speichern Sie die Änderungen, wenn Sie fertig sind. Weitere Informationen zum Anpassen von Ansprüchen finden Sie im Artikel [Anpassen ausgestellter Ansprüche im SAML-Token für Unternehmensanwendungen](../develop/active-directory-saml-claims-customization.md).

3. Klicken Sie zum Hinzufügen eines Anspruchs oben auf der Seite auf **Neuen Anspruch hinzufügen**. Geben Sie den **Namen** ein, und wählen Sie die entsprechende Quelle. Wenn Sie die Quelle **Attribut** auswählen, müssen Sie das **Quellattribut** auswählen, das Sie verwenden möchten. Wenn Sie die Quelle **Übersetzung** auswählen, müssen Sie die gewünschte **Transformation** und **Parameter 1** auswählen.

4. Wählen Sie **Speichern** aus. Der neue Anspruch wird in der Tabelle angezeigt.
 
## <a name="generate-a-saml-signing-certificate"></a>Generieren eines SAML-Signaturzertifikats

Azure AD verwendet ein Zertifikat zum Signieren der SAML-Token, die an die Anwendung gesendet werden. 

1. Um ein neues Zertifikat zu generieren, klicken Sie rechts oben im Abschnitt **SAML-Signaturzertifikat** auf das Symbol **Bearbeiten** (Stift).

2. Wählen Sie im Abschnitt **SAML-Signaturzertifikat** die Option **Neues Zertifikat** aus.

3. Legen Sie in der angezeigten neuen Zertifikatzeile das **Ablaufdatum** fest. Weitere Informationen zu verfügbaren Konfigurationsoptionen finden Sie im Artikel [Erweiterte Optionen für die Zertifikatsignatur](certificate-signing-options.md).

4. Klicken Sie oben im Abschnitt **SAML-Signaturzertifikat** auf **Speichern**. 

## <a name="assign-users-to-the-application"></a>Zuweisen von Benutzern zur Anwendung

Es empfiehlt sich, einmaliges Anmelden für mehrere Benutzer oder Gruppen zu testen, bevor Sie die Anwendung in Ihrer Organisation einführen.

> [!NOTE]
>
> Diese Schritte führen Sie im Portal im Konfigurationsabschnitt **Benutzer und Gruppen** aus. Wenn Sie fertig sind, müssen Sie zurück zum Abschnitt **Einmaliges Anmelden** navigieren, um das Tutorial abzuschließen.

So weisen Sie einen Benutzer oder eine Gruppe zur Anwendung zu:

1. Öffnen Sie die Anwendung im Portal, sofern sie noch nicht geöffnet ist.
2. Klicken Sie im linken Navigationsbereich für die Anwendung auf **Benutzer und Gruppen**.
3. Klicken Sie auf **Benutzer hinzufügen**.
4. Klicken Sie im Abschnitt **Zuweisung hinzufügen** auf **Benutzer und Gruppen**.
5. Um einen bestimmten Benutzer zu suchen, geben Sie den Benutzernamen in das Feld **Mitglied auswählen oder externen Benutzer einladen** ein. Wählen Sie dann das Profilbild oder Logo des Benutzers aus, und klicken Sie auf **Auswählen**. 
6. Wählen Sie im Abschnitt **Zuweisung hinzufügen** auf **Zuweisen**. Anschließend wird der ausgewählte Benutzer in der Liste **Benutzer und Gruppen** angezeigt.

## <a name="set-up-the-application-to-use-azure-ad"></a>Einrichten der Anwendung für die Verwendung von Azure AD

Sie sind fast fertig.  Im letzten Schritt müssen Sie die Anwendung so einrichten, dass Azure AD als SAML-Identitätsanbieter verwendet wird. 

1. Scrollen Sie nach unten zum Abschnitt **<applicationName> einrichten**. In diesem Tutorial heißt dieser Abschnitt **Set up GitHub-test**. 
2. Kopieren Sie den Wert aus jeder Zeile dieses Abschnitts. Fügen Sie die einzelnen Werte in der entsprechenden Zeile im Abschnitt **Grundlegende SAML-Konfiguration** ein. Kopieren Sie beispielsweise den Wert **Anmelde-URL** aus dem Abschnitt **GitHub-test einrichten**, und fügen Sie ihn in das Feld **Anmelde-URL** im Abschnitt **Grundlegende SAML-Konfiguration** ein usw.
3. Wenn Sie alle Werte in die entsprechenden Felder eingefügt haben, klicken Sie auf **Speichern**.

## <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

Sie können Ihre Einstellungen jetzt testen.  

1. Öffnen Sie die SSO-Einstellungen für Ihre Anwendung. 
2. Scrollen Sie zum Abschnitt **Einmaliges Anmelden mit <applicationName> überprüfen**. In diesem Tutorial heißt dieser Abschnitt **GitHub-test einrichten**.
3. Klicken Sie auf **Test**. Die Testoptionen werden angezeigt.
4. Klicken Sie auf **Als aktueller Benutzer anmelden**. Dadurch können Sie zunächst überprüfen, ob einmaliges Anmelden für Sie als Administrator funktioniert.

Wenn ein Fehler vorliegt, wird eine Fehlermeldung angezeigt. Führen Sie die folgenden Schritte aus:

1. Kopieren Sie die Details, und fügen Sie sie ins Feld **Wie äußert sich der Fehler?** ein.

    ![Leitfaden zur Problemlösung abrufen](media/configure-single-sign-on-portal/error-guidance.png)

2. Klicken Sie auf **Leitfaden zur Problemlösung abrufen**. Die Grundursache und Informationen zur Problemlösung werden angezeigt.  In diesem Beispiel wurde der Benutzer nicht der Anwendung zugewiesen.

3. Lesen Sie den Leitfaden zur Problemlösung, und beheben Sie nach Möglichkeit das Problem.

4. Führen Sie den Test erneut aus, bis der Vorgang erfolgreich abgeschlossen wird.

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie die Einstellungen für einmaliges Anmelden für eine Anwendung konfiguriert. Nach Abschluss der Konfiguration haben Sie der Anwendung einen Benutzer zugewiesen und die Anwendung zur Verwendung des SAML-basierten einmaligen Anmeldens konfiguriert. Nach Ausführung dieser Aufgaben haben Sie überprüft, ob SAML-basiertes einmaliges Anmelden ordnungsgemäß funktioniert.

Sie haben folgende Schritte ausgeführt:
> [!div class="checklist"]
> * Sie haben SAML als SSO-Modus ausgewählt.
> * Sie haben den Anwendungshersteller kontaktiert, um die Domäne und die URLs zu konfigurieren.
> * Sie haben Benutzerattribute konfiguriert.
> * Sie haben ein SAML-Signaturzertifikat erstellt.
> * Sie haben der Anwendung manuell Benutzer oder Gruppen zugewiesen.
> * Sie haben die Anwendung zur Verwendung von Azure AD als SAML-Identitätsanbieter konfiguriert.
> * Sie haben das SAML-basierte einmalige Anmelden getestet.

Verwenden Sie die automatische Benutzerbereitstellung, wenn Sie die Anwendung für weitere Benutzer in der Organisation einführen möchten.

> [!div class="nextstepaction"]
> [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps im Azure-Portal](configure-automatic-user-provisioning-portal.md)



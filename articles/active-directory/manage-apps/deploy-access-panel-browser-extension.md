---
title: Bereitstellen der Azure-Zugriffsbereichserweiterung für IE mithilfe eines Gruppenrichtlinienobjekts | Microsoft-Dokumentation
description: So stellen Sie das Internet Explorer-Add-On für das Portal "Meine Apps" mithilfe von Gruppenrichtlinien bereit
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b6f069489738e9dceeee350a36aa2b45715a314
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825027"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>How to Deploy the Access Panel Extension for Internet Explorer using Group Policy (Bereitstellen der Zugriffsbereichserweiterung für Internet Explorer mit der Gruppenrichtlinie; in englischer Sprache)
In diesem Tutorial wird erläutert, wie Sie mithilfe von Gruppenrichtlinien die Zugriffsbereichserweiterung für Internet Explorer per Remotezugriff auf den Computern Ihrer Benutzer installieren. Diese Erweiterung ist für Benutzer von Internet Explorer erforderlich, die sich bei Apps anmelden müssen, die mit der [kennwortbasierten einmaligen Anmeldung](what-is-single-sign-on.md#password-based-sso)konfiguriert wurden.

Administratoren wird empfohlen, die Bereitstellung dieser Erweiterung zu automatisieren. Andernfalls müssen Benutzer die Erweiterung selbst herunterladen und installieren. Diese Vorgehensweise ist jedoch fehleranfällig; zudem sind Administratorberechtigungen erforderlich. In diesem Tutorial wird eine Methode zum Automatisieren von Softwarebereitstellungen mithilfe von Gruppenrichtlinien erläutert. [Weitere Informationen zu Gruppenrichtlinien](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Die Zugriffsbereichserweiterung ist auch für [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) und [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998) verfügbar. Bei diesen Browser sind für die Installation keine Administratorberechtigungen erforderlich.

## <a name="prerequisites"></a>Voraussetzungen
* Sie haben [Active Directory-Domänendienste](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)eingerichtet und die Computer Ihrer Benutzer Ihrer Domäne hinzugefügt.
* Zum Bearbeiten des Gruppenrichtlinienobjekts benötigen Sie die Berechtigung „Einstellungen bearbeiten“. Standardmäßig verfügen Mitglieder der folgenden Sicherheitsgruppen über diese Berechtigung: Domänenadministratoren, Organisationsadministratoren und Ersteller/Besitzer von Gruppenrichtlinien. [Weitere Informationen.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Schritt 1: Erstellen des Verteilungspunkts
Zunächst müssen Sie das Installationspaket an einem Speicherort im Netzwerk ablegen, auf den über die Computer zugegriffen werden kann, auf denen Sie die Erweiterung per Remotezugriff installieren möchten. Gehen Sie dazu folgendermaßen vor:

1. Melden Sie sich beim Server als Administrator an.
2. Wechseln Sie im Fenster **Server-Manager** zu **Dateien und Speicherdienste**.
   
    ![Dateien und Speicherdienste öffnen](./media/deploy-access-panel-browser-extension/files-services.png)
3. Klicken Sie auf die Registerkarte **Freigaben** . Klicken Sie dann auf **Aufgaben** > **Neue Freigabe**.
   
    ![Dateien und Speicherdienste öffnen](./media/deploy-access-panel-browser-extension/shares.png)
4. Schließen Sie den **Assistenten für neue Freigaben** , und legen Sie Berechtigungen fest, um sicherzustellen, dass von den Computern Ihrer Benutzer aus darauf zugegriffen werden kann. [Weitere Informationen zu Freigaben](https://technet.microsoft.com/library/cc753175.aspx)
5. Laden Sie das folgende Microsoft Windows Installer-Paket (MSI-Datei) herunter: [Access Panel Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
6. Kopieren Sie das Installationspaket an den gewünschten Speicherort auf der Freigabe.
   
    ![Kopieren Sie die MSI-Datei in die Freigabe.](./media/deploy-access-panel-browser-extension/copy-package.png)
7. Stellen Sie sicher, dass von den Clientcomputern aus auf das Installationspaket auf der Freigabe zugegriffen werden kann. 

## <a name="step-2-create-the-group-policy-object"></a>Schritt 2: Erstellen des Gruppenrichtlinienobjekts
1. Melden Sie sich bei dem Server mit den Active Directory-Domänendiensten (Active Directory Domain Services, AD DS) an.
2. Navigieren Sie im Server-Manager zu **Extras** > **Gruppenrichtlinienverwaltung**.
   
    ![Navigieren Sie zu „Tools“ > „Gruppenrichtlinienverwaltung“.](./media/deploy-access-panel-browser-extension/tools-gpm.png)
3. Zeigen Sie im linken Bereich des Fensters **Gruppenrichtlinienverwaltung** die Hierarchie Ihrer Organisationseinheiten (OE) an, und legen Sie fest, in welchem Umfang die Gruppenrichtlinie angewendet werden soll. Sie können beispielsweise eine kleine Organisationseinheit auswählen, die einigen wenigen Benutzern zu Testzwecken bereitgestellt wird, oder eine Organisationseinheit der obersten Ebene festlegen, die im gesamten Unternehmen bereitgestellt wird.
   
   > [!NOTE]
   > Wenn Sie Organisationseinheiten erstellen oder bearbeiten möchten, wechseln Sie zurück zum Server-Manager, und wählen Sie **Extras** > **Active Directory-Benutzer und -Computer** aus.
   > 
   > 
4. Nachdem Sie eine Organisationseinheit ausgewählt haben, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Gruppenrichtlinienobjekt hier erstellen und verknüpfen** aus.
   
    ![Neues Gruppenrichtlinienobjekt erstellen](./media/deploy-access-panel-browser-extension/create-gpo.png)
5. Geben Sie in der Eingabeaufforderung **Neues Gruppenrichtlinienobjekt** einen Namen für das neue Gruppenrichtlinienobjekt ein.
   
    ![Neues Gruppenrichtlinienobjekt benennen](./media/deploy-access-panel-browser-extension/name-gpo.png)
6. Klicken Sie mit der rechten Maustaste auf das Gruppenrichtlinienobjekt, das Sie gerade erstellt haben, und wählen Sie **Bearbeiten** aus.
   
    ![Neues Gruppenrichtlinienobjekt bearbeiten](./media/deploy-access-panel-browser-extension/edit-gpo.png)

## <a name="step-3-assign-the-installation-package"></a>Schritt 3: Zuweisen des Installationspakets
1. Legen Sie fest, ob Sie die Erweiterung basierend auf der **Computerkonfiguration** oder basierend auf der **Benutzerkonfiguration** bereitstellen möchten. Bei Verwendung der [Computerkonfiguration](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx) wird die Erweiterung auf dem Computer installiert, und zwar unabhängig davon, welche Benutzer sich bei ihm anmelden. Bei Verwendung der [Benutzerkonfiguration](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx) wird die Erweiterung für die Benutzer installiert, und zwar unabhängig davon, bei welchem Computer sie sich anmelden.
2. Folgen Sie abhängig vom gewählten Konfigurationstyp im linken Bereich des Fensters **Gruppenrichtlinienverwaltungs-Editor** einem der folgenden Ordnerpfade:
   
   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`
3. Klicken Sie mit der rechten Maustaste auf **Softwareinstallation**, und wählen Sie dann **Neu** > **Paket** aus.
   
    ![Neues Paket für die Softwareinstallation erstellen](./media/deploy-access-panel-browser-extension/new-package.png)
4. Wechseln Sie zum freigegebenen Ordner, der das Installerpaket aus [Schritt 1: Erstellen des Verteilungspunkts](#step-1-create-the-distribution-point) enthält, wählen Sie die MSI-Datei aus, und klicken Sie auf **Öffnen**.
   
   > [!IMPORTANT]
   > Wenn sich die Freigabe auf demselben Server befindet, vergewissern Sie sich, dass Sie nicht über den lokalen Dateipfad, sondern über den Netzwerkdateipfad auf die MSI-Datei zugreifen.
   > 
   > 
   
    ![Wählen Sie das Installationspaket im freigegebenen Ordner.](./media/deploy-access-panel-browser-extension/select-package.png)
5. Wählen Sie in der Eingabeaufforderung **Software bereitstellen** als Bereitstellungsmethode die Option **Zugewiesen** aus. Klicken Sie dann auf **OK**.
   
    ![Wählen Sie „Zugewiesen“, und klicken Sie dann auf „OK“.](./media/deploy-access-panel-browser-extension/deployment-method.png)

Die Erweiterung wird nun für die ausgewählte Organisationseinheit bereitgestellt. [Weitere Informationen zur Gruppenrichtlinien-Softwareinstallation](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Schritt 4: Automatisches Aktivieren der Erweiterung für Internet Explorer
Zusätzlich zur Ausführung des Installationsprogramms muss jede Erweiterung für Internet Explorer vor ihrer Verwendung explizit aktiviert werden. Gehen Sie folgendermaßen vor, um die Zugriffsbereichserweiterung mithilfe von Gruppenrichtlinien zu aktivieren:

1. Navigieren Sie im Fenster **Gruppenrichtlinienverwaltungs-Editor** zu einem der folgenden Ordnerpfade, je nachdem, welchen Konfigurationstyp Sie in [Schritt 3: Zuweisen des Installationspakets](#step-3-assign-the-installation-package) ausgewählt haben:
   
   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
2. Klicken Sie mit der rechten Maustaste auf **Add-On-Liste**, und wählen Sie **Bearbeiten** aus.
    ![Bearbeiten Sie die Add-On-Liste.](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)
3. Wählen Sie im Fenster **Add-On-Liste** die Option **Aktiviert** aus. Klicken Sie im Abschnitt **Optionen** auf **Anzeigen...**.
   
    ![Klicken Sie auf „Aktivieren“ und anschließend auf „Anzeigen...“.](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)
4. Führen Sie im Fenster **Inhalt anzeigen** die folgenden Schritte aus:
   
   1. Kopieren Sie für die erste Spalte (Feld **Wertname**) die folgende Klassen-ID: `{030E9A3F-7B18-4122-9A60-B87235E4F59E}`.
   2. Geben Sie für die zweite Spalte (Feld **Wert**) den folgenden Wert ein: `1`.
   3. Klicken Sie auf **OK**, um das Fenster **Inhalt anzeigen** zu schließen.
      
      ![Füllen Sie die Werte wie oben angegeben aus.](./media/deploy-access-panel-browser-extension/show-contents.png)
5. Klicken Sie auf **OK**, um die Änderungen zu übernehmen und das Fenster **Add-On-Liste** zu schließen.

Die Erweiterung sollte jetzt für die Computer in der ausgewählten Organisationseinheit aktiviert sein. [Erfahren Sie mehr über die Verwendung von Gruppenrichtlinien zum Aktivieren oder Deaktivieren von Add-Ons für Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Schritt 5 (Optional): Deaktivieren der Frage nach der Kennwortspeicherung
Wenn sich Benutzer unter Verwendung der Zugriffsbereichserweiterung bei Websites anmelden, kann Internet Explorer fragen, ob der Benutzer sein Kennwort speichern möchte.

![Kennwortaufforderung](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Falls diese Frage nicht angezeigt werden soll, führen Sie die folgenden Schritte aus, um das Speichern von Kennwörtern durch AutoVervollständigen zu unterbinden:

1. Navigieren Sie im **Gruppenrichtlinienverwaltungs-Editor** zum unten angegebenen Pfad. Diese Konfigurationseinstellung ist nur unter **Benutzerkonfiguration** verfügbar.
   
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
2. Suchen Sie die Einstellung **AutoVervollständigen für Benutzernamen und Kennwörter in Formularen aktivieren**.
   
   > [!NOTE]
   > In älteren Versionen von Active Directory heißt diese Einstellung unter Umständen noch **Kennwörter in AutoVervollständigen können nicht gespeichert werden**. Die Konfiguration für diese Einstellung unterscheidet sich von der in diesem Tutorial beschriebenen Einstellung.
   > 
   > 
   
    ![Diese finden Sie in den Benutzereinstellungen.](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)
3. Klicken Sie mit der rechten Maustaste auf die oben genannte Einstellung, und wählen Sie **Bearbeiten** aus.
4. Wählen Sie im Fenster **AutoVervollständigen für Benutzernamen und Kennwörter in Formularen aktivieren** die Option **Deaktiviert** aus.
   
    ![Wählen Sie „Deaktivieren“ aus.](./media/deploy-access-panel-browser-extension/disable-passwords.png)
5. Klicken Sie auf **OK** , um die Änderungen zu übernehmen und das Fenster zu schließen.

Daraufhin können Benutzer ihre Anmeldeinformationen nicht mehr speichern oder mithilfe von AutoVervollständigen auf zuvor gespeicherte Anmeldeinformationen zugreifen. Für andere Arten von Formularfeldern (etwa Suchfelder) kann AutoVervollständigen dagegen weiter verwendet werden.

> [!WARNING]
> Wenn diese Richtlinie aktiviert wird, nachdem Benutzer bereits Anmeldeinformationen gespeichert haben, werden diese *nicht* gelöscht.
> 
> 

## <a name="step-6-testing-the-deployment"></a>Schritt 6: Testen der Bereitstellung
Gehen Sie folgendermaßen vor, um zu überprüfen, ob die Erweiterungsbereitstellung erfolgreich war:

1. Wenn Sie für die Bereitstellung die Option **Computerkonfiguration**verwendet haben, melden Sie sich bei einem Clientcomputer an, der zu der in [Schritt 2: Erstellen des Gruppenrichtlinienobjekts](#step-2-create-the-group-policy-object) ausgewählten Organisationseinheit gehört. Falls die Bereitstellung mit **Benutzerkonfiguration**erfolgt ist, melden Sie sich als Benutzer an, der zu dieser Organisationseinheit gehört.
2. Möglicherweise werden die Gruppenrichtlinienänderungen auf dem Computer erst durch mehrmaliges Anmelden vollständig aktualisiert. Öffnen Sie zum Erzwingen der Aktualisierung ein Fenster für die **Eingabeaufforderung**, und führen Sie den folgenden Befehl aus: `gpupdate /force`.
3. Der Computer muss neu gestartet werden, damit die Installation ausgeführt werden kann. Das Starten kann während der Installation der Erweiterung erheblich länger dauern als gewöhnlich.
4. Öffnen Sie nach dem Neustart **Internet Explorer**. Klicken Sie in der rechten oberen Ecke des Fensters auf **Extras** (das Zahnradsymbol), und wählen Sie dann **Add-Ons verwalten** aus.
   
    ![Klicken Sie auf „Extras“ > „Add-Ons verwalten“.](./media/deploy-access-panel-browser-extension/manage-add-ons.png)
5. Überprüfen Sie im Fenster **Add-Ons verwalten**, ob die **Zugriffsbereichserweiterung** installiert und ihr **Status** auf **Aktiviert** festgelegt wurde.
   
    ![Stellen Sie sicher, dass die Zugriffsbereichserweiterung installiert und aktiviert wurde.](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="related-articles"></a>Verwandte Artikel
* [Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory](what-is-single-sign-on.md)
* [Problembehandlung in der Zugriffsbereichserweiterung für Internet Explorer](manage-access-panel-browser-extension.md)


---
title: 'Tutorial: Azure Active Directory-Integration mit Rackspace SSO | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Rackspace SSO konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 36b398be-2f7e-4ce8-9031-53587299bc4a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: fd420ea3fc4faae7fe4510a72204d71acaa3549a
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65890789"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Tutorial: Azure Active Directory-Integration mit Rackspace SSO

In diesem Tutorial erfahren Sie, wie Sie Rackspace SSO in Azure Active Directory (Azure AD) integrieren.
Die Integration von Rackspace SSO in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Rackspace SSO hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Rackspace SSO anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Rackspace SSO konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Rackspace SSO-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Rackspace SSO unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-rackspace-sso-from-the-gallery"></a>Hinzufügen von Rackspace SSO aus dem Katalog

Zum Konfigurieren der Integration von Rackspace SSO in Azure AD müssen Sie Rackspace SSO aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Rackspace SSO aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Rackspace SSO** ein, wählen Sie im Ergebnisbereich **Rackspace SSO** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Rackspace SSO in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Rackspace SSO mithilfe eines Testbenutzers namens **Britta Simon**.
Bei der Verwendung des einmaligen Anmeldens mit Rackspace werden die Rackspace-Benutzer bei der ersten Anmeldung am Rackspace-Portal automatisch erstellt. 

Zum Konfigurieren und Testen des einmaligen Anmeldens bei Rackspace SSO über Azure AD müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Rackspace SSO](#configure-rackspace-sso-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Einrichten der Attributzuordnung in der Rackspace-Systemsteuerung](#set-up-attribute-mapping-in-the-rackspace-control-panel)**, um Azure AD-Benutzern Rackspace Rollen zuzuweisen.
1. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Rackspace SSO die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Rackspace SSO** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Laden Sie im Abschnitt **Grundlegende SAML-Konfiguration** die **Dienstanbieter-Metadatendatei** hoch, die Sie von der [URL](https://login.rackspace.com/federate/sp.xml) herunterladen können, und führen Sie die folgenden Schritte aus:

    a. Klicken Sie auf **Metadatendatei hochladen**.

    ![image](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    ![image](common/browse-upload-metadata.png)

    c. Sobald die Metadatendatei erfolgreich hochgeladen wurde, werden die erforderlichen URLs automatisch aufgefüllt.

    d. Geben Sie im Textfeld **Anmelde-URL** eine URL ein: `https://login.rackspace.com/federate/`.

    ![SSO-Informationen zur Domäne und zu den URLs für Rackspace SSO](common/sp-signonurl.png)   

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

Diese Datei wird in Rackspace hochgeladen, um die erforderlichen Konfigurationseinstellungen für den Identitätsverbund auszufüllen.

### <a name="configure-rackspace-sso-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Rackspace SSO

Konfigurieren des einmaligen Anmeldens aufseiten von **Rackspace SSO**:

1. Beachten Sie die Dokumentation zum [Hinzufügen eines Identitätsanbieters zur Systemsteuerung](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/).
1. Sie führt Sie durch die Schritte zum:
    1. Erstellen eines neuen Identitätsanbieters
    1. Festlegen einer E-Mail-Domäne, die Benutzer bei der Anmeldung zur Identifizierung Ihres Unternehmens verwenden
    1. Hochladen der zuvor aus der Azure-Systemsteuerung heruntergeladen **Verbundmetadaten-XML**.

Dadurch werden die grundlegenden erforderlichen SSO-Einstellungen für die Verbindung von Azure und Rackspace ordnungsgemäß konfiguriert.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen `brittasimon@yourcompanydomain.extension` ein. Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Rackspace SSO gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Rackspace SSO** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Rackspace SSO** aus.

    ![Link „Rackspace SSO“ in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Einrichten der Attributzuordnung in der Rackspace-Systemsteuerung

Rackspace verwendet eine **Richtlinie für Attributzuordnung**, um Ihren Benutzern mit einmaliger Anmeldung Rackspace-Rollen und -Gruppen zuzuweisen. Die **Richtlinie für Attributzuordnung** übersetzt Azure AD-SAML-Ansprüche in die für Rackspace erforderlichen Benutzerkonfigurationsfelder. Weitere Informationen finden Sie in der Rackspace-Dokumentation zu den [Grundlagen der Attributzuordnung](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/). Einige Überlegungen:

* Wenn Sie mithilfe von Azure AD-Gruppen unterschiedliche Rackspace-Zugriffsebenen zuweisen möchten, müssen Sie den Gruppenanspruch in den Azure-Einstellungen für einmaliges Anmelden für **Rackspace SSO** aktivieren. Die **Richtlinie für Attributzuordnung** wird dann verwendet, um diese Gruppen den gewünschten Rackspace-Rollen und -Gruppen zuzuordnen:

    ![Die Gruppenanspruch-Einstellungen](common/sso-groups-claim.png)

* Azure AD sendet standardmäßig die UID von Azure AD-Gruppen im SAML-Anspruch anstelle des Namens der Gruppe. Wenn Sie jedoch Ihre lokale Active Directory-Instanz mit Azure AD synchronisieren, haben Sie die Möglichkeit, die tatsächlichen Namen der Gruppen zu senden:

    ![Die Einstellungen für den Gruppenanspruchsnamen](common/sso-groups-claims-names.png)

Die **Beispielrichtlinie für Attributzuordnung** unten veranschaulicht Folgendes:
1. Festlegen des Rackspace-Benutzernamens auf den SAML-Anspruch `user.name`. Jeder Anspruch kann verwendet werden, am häufigsten wird er jedoch auf ein Feld mit der E-Mail-Adresse des Benutzers festgelegt.
1. Festlegen der Rackspace-Rollen `admin` und `billing:admin` für einen Benutzer durch Zuordnen einer Azure AD-Gruppe anhand des Gruppennamens oder der Gruppen-UID. Eine *Ersetzung* von `"{0}"` im Feld `roles` wird verwendet, und die Ergebnisse werden durch die Ergebnisse der `remote`-Regelausdrücke ersetzt.
1. Verwenden der *Standardersetzung* `"{D}"`, damit Rackspace zusätzliche SAML-Felder abrufen kann, indem im SAML-Austausch nach standardmäßigen und bekannten SAML-Ansprüchen gesucht wird.

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> Achten Sie darauf, bei der Bearbeitung Ihrer Richtliniendatei einen Texteditor zu verwenden, der die YAML-Syntax überprüft.

Weitere Beispiele finden Sie in der [Rackspace-Dokumentation zu den Grundlagen der Attributzuordnung](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/).

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Rackspace SSO“ klicken, sollten Sie automatisch bei der Rackspace SSO-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Sie können auch die Schaltfläche **Überprüfen** in den Einstellungen für einmaliges Anmelden für **Rackspace SSO** verwenden:

   ![Schaltfläche „Überprüfen“ für SSO](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


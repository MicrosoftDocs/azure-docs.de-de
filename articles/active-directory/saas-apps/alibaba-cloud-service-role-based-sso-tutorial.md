---
title: 'Tutorial: Azure Active Directory-Integration mit Alibaba Cloud Service (rollenbasiertes SSO) | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Alibaba Cloud Service (rollenbasiertes SSO) konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d4b154b8ce55c381f1398c696bc439067dccfab
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65899748"
---
# <a name="tutorial-azure-active-directory-integration-with-alibaba-cloud-service-role-based-sso"></a>Tutorial: Azure Active Directory-Integration mit Alibaba Cloud Service (rollenbasiertes SSO)

In diesem Tutorial erfahren Sie, wie Sie Alibaba Cloud Service (rollenbasiertes SSO) in Azure Active Directory (Azure AD) integrieren.
Die Integration von Alibaba Cloud Service (rollenbasiertes SSO) in Azure AD hat folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Alibaba Cloud Service (rollenbasiertes SSO) haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Alibaba Cloud Service anzumelden (rollenbasiertes einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Alibaba Cloud Service (rollenbasiertes SSO) konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* SSO-fähiges Alibaba Cloud Service-Konto (rollenbasiertes SSO)

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Alibaba Cloud Service (rollenbasiertes SSO) unterstützt **IDP**-initiiertes SSO.

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Hinzufügen von Alibaba Cloud Service (rollenbasiertes SSO) über den Katalog

Um die Integration von Alibaba Cloud Service (rollenbasiertes SSO) in Azure AD zu konfigurieren, müssen Sie Alibaba Cloud Service (rollenbasiertes SSO) über den Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um Alibaba Cloud Service (rollenbasiertes SSO) über den Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Alibaba Cloud Service (rollenbasiertes SSO)** ein, wählen Sie im Ergebnisbereich **Alibaba Cloud Service (rollenbasiertes SSO)** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Alibaba Cloud Service (rollenbasiertes SSO) in der Ergebnisliste](common/search-new-app.png)

5. Klicken Sie auf der Seite **Alibaba Cloud Service (rollenbasiertes SSO)**  im linken Navigationsbereich auf **Eigenschaften**, kopieren die **Objekt-ID**, und speichern Sie sie auf Ihrem Computer für die spätere Verwendung.

    ![Eigenschaftenkonfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)
    
## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Alibaba Cloud Service (rollenbasiertes SSO) mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Alibaba Cloud Service (rollenbasiertes SSO) eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Alibaba Cloud Service (rollenbasiertes SSO) zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des rollenbasierten SSOs in Alibaba Cloud Service](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** , um Ihre Benutzer verwenden dieses Feature zu aktivieren.
2. **[Konfigurieren des einmaligen Anmeldens für Alibaba Cloud Service (rollenbasiertes SSO)](#configure-alibaba-cloud-service-role-based-sso-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Testbenutzers für Alibaba Cloud Service (rollenbasiertes SSO)](#create-alibaba-cloud-service-role-based-sso-test-user)** , um in Alibaba Cloud Service (rollenbasiertes SSO) eine Entsprechung von Britta Simon zu erhalten, die mit ihrer Benutzerdarstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Alibaba Cloud Service (rollenbasiertes SSO) zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Alibaba Cloud Service (rollenbasiertes SSO)** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Dienstanbieter-Metadatendatei** verfügen:

    >[!NOTE]
    >Sie erhalten die Metadaten des Dienstanbieters über diese [URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml).

    a. Klicken Sie auf **Metadatendatei hochladen**.

    ![image](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    ![image](common/browse-upload-metadata.png)

    c. Nach dem erfolgreichen Upload der Metadatendatei werden die Werte **Bezeichner** und **Antwort-URL** im Abschnitt „Alibaba Cloud Service (rollenbasiertes SSO)“ automatisch aufgefüllt:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Sollten die Werte **Bezeichner** und **Antwort-URL** nicht automatisch aufgefüllt werden, geben Sie die erforderlichen Werte manuell ein.

5. Die Alibaba Cloud Service-Anwendung (rollenbasiertes SSO) erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![image](common/edit-attribute.png)

6. Darüber hinaus wird von der Alibaba Cloud Service-Anwendung (rollenbasiertes SSO) erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die folgenden Schritte aus, um das SAML-Tokenattribut wie in der folgenden Tabelle gezeigt hinzuzufügen:

    | NAME | Namespace | Quellattribut|
    | ---------------| ------------| --------------- |
    | Rolle | https:\//www.aliyun.com/SAML-Role/Attribute | user.assignedroles |
    | RoleSessionName | https:\//www.aliyun.com/SAML-Role/Attribute | user.userprincipalname |

    > [!NOTE]
    > Klicken Sie bitte [hier](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management), um herauszufinden, wie Sie die **Rolle** in Azure AD konfigurieren.

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **Speichern**.

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

8. Kopieren Sie im Abschnitt **Alibaba Cloud Service (rollenbasiertes SSO) einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Konfigurieren des rollenbasierten SSOs in Alibaba Cloud Service

1. Melden Sie sich mit Konto1 bei der Alibaba Cloud [RAM-Konsole](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) an.

2. Wählen Sie im linken Navigationsbereich die Option **SSO** aus.

3. Klicken Sie auf der Registerkarte **Rollenbasiertes SSO** auf **IdP erstellen**.

4. Geben Sie auf der angezeigten Seite `AAD` in das Feld „IdP-Name“ ein, geben Sie eine Beschreibung in das Feld **Notiz** ein, klicken Sie auf **Hochladen**, um die zuvor heruntergeladene Verbundmetadatendatei hochzuladen, und klicken Sie auf **OK**.

5. Nachdem der IdP erfolgreich erstellt wurde, klicken Sie auf **RAM-Rolle erstellen**.

6. Geben Sie im Feld **Name der RAM Rolle** `AADrole` ein, wählen Sie `AAD` aus der Dropdownliste **IdP auswählen** aus, und klicken Sie auf „OK“.

    >[!NOTE]
    >Sie können der Rolle bei Bedarf die Berechtigung erteilen. Nach dem Erstellen des IdPs und der entsprechenden Rolle empfehlen wir, die ARNs des IdPs und der Rolle zur späteren Verwendung zu speichern. Die ARNs finden Sie auf der Seite mit den IdP-Informationen und auf der Seite mit den Rolleninformationen.

7. Ordnen Sie die Alibaba Cloud RAM-Rolle (AADrole) dem Azure AD-Benutzer (u2) zu: Um die RAM-Rolle dem Azure AD-Benutzer zuzuordnen, müssen Sie eine Rolle in Azure AD erstellen, indem Sie diesen Schritten folgen:

    a. Melden Sie sich beim [Azure AD Graph-Tester](https://developer.microsoft.com/graph/graph-explorer?spm=a2c63.p38356.879954.9.7d904e167h6Yg9) an.

    b. Klicken Sie auf **Berechtigungen ändern** , um die erforderlichen Berechtigungen zum Erstellen einer Rolle zu erhalten.

    ![Graph-Konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Wählen Sie die folgenden Berechtigungen aus der Liste aus, und klicken Sie auf **Berechtigungen ändern**, wie in der folgenden Abbildung dargestellt.

    ![Graph-Konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Nach dem Zuweisen der Berechtigungen melden Sie sich wieder beim Graph-Tester an.

    d. Wählen Sie auf die Graph-Tester Seite aus der ersten Dropdownliste **GET** und aus der zweiten Dropdownliste **beta** aus. Geben Sie dann `https://graph.microsoft.com/beta/servicePrincipals` in das Feld neben den Dropdownlisten ein, und klicken Sie auf **Abfrage ausführen**.

    ![Graph-Konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Wenn Sie mehrere Verzeichnisse verwenden, können Sie `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` in das Feld der Abfrage eingeben.

    e. Extrahieren Sie im Abschnitt **Antwortvorschau** die appRoles-Eigenschaft aus „Dienstprinzipal“ für die zukünftige Verwendung.

    ![Graph-Konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Die appRoles-Eigenschaft finden Sie durch Eingabe von `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` in das Feld der Abfrage. Beachten Sie, dass `objectID` die Objekt-ID ist, die Sie auf der Azure AD-Seite **Eigenschaften** kopiert haben.

    f. Wechseln Sie zurück zum Graph-Tester, ändern Sie die Methode von **GET** zu **PATCH**, fügen Sie den folgenden Inhalt in den Abschnitt **Anforderungstext** ein und klicken Sie auf **Abfrage ausführen**:
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > `value` gibt die ARNs des IdPs und die in der RAM-Konsole erstellte Rolle an. Hier können Sie bei Bedarf mehrere Rollen hinzufügen. Azure AD sendet den Wert dieser Rollen als Anspruchswert in der SAML-Antwort. Neue Rollen können Sie jedoch nur nach dem `msiam_access`-Teil für den Patchvorgang hinzufügen. Um den Erstellungsprozess zu optimieren, empfehlen wir Ihnen, einen ID-Generator, wie beispielsweise den GUID-Generator, zu verwenden, um IDs in Echtzeit zu generieren.

    g. Nachdem der Dienstprinzipal mit der erforderlichen Rolle gepatcht wurde, fügen Sie die Rolle mit dem Azure AD-Benutzer (u2) hinzu, indem Sie die Schritte im Abschnitt **Zuweisen des Azure AD-Testbenutzers** des Tutorials ausführen.

### <a name="configure-alibaba-cloud-service-role-based-sso-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Alibaba Cloud Service (rollenbasiertes SSO)

Um das einmalige Anmelden aufseiten von **Alibaba Cloud Service (rollenbasiertes SSO)** zu konfigurieren, müssen Sie die heruntergeladene **Verbundmetadaten-XML** und die entsprechenden kopierten URLs aus dem Azure-Portal an das [Supportteam von Alibaba Cloud Service (rollenbasiertes SSO)](https://www.aliyun.com/service/) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Alibaba Cloud Service (rollenbasiertes SSO) gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Alibaba Cloud Service (rollenbasiertes SSO)** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Alibaba Cloud Service (rollenbasiertes SSO)** aus.

    ![Link „Alibaba Cloud Service (rollenbasiertes SSO)“ in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie auf der Registerkarte **Benutzer und Gruppen** „u2“ aus der Benutzerliste aus, und klicken Sie auf **Auswählen**. Klicken Sie dann auf **Zuweisen**.

    ![Testkonfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

6. Zeigen Sie die zugewiesene Rolle an, und testen Sie „Alibaba Cloud Service (rollenbasiertes SSO)“.

    ![Testkonfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Nach der Zuordnung des Benutzers (u2) wird die erstellte Rolle automatisch an den Benutzer angefügt. Wenn Sie mehrere Rollen erstellt haben, müssen Sie nach Bedarf die geeignete Rolle an den Benutzer anfügen. Wenn Sie rollenbasiertes SSO von Azure AD für mehrere Alibaba Cloud-Konten implementieren möchten, wiederholen Sie die vorherigen Schritte.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Erstellen eines Testbenutzers für Alibaba Cloud Service (rollenbasiertes SSO)

In diesem Abschnitt erstellen Sie in Alibaba Cloud Service (rollenbasiertes SSO) einen Benutzer namens Britta Simon. Lassen Sie sich beim Hinzufügen der Benutzer zur Plattform für Alibaba Cloud Service (rollenbasiertes SSO) ggf. vom [Supportteam für Alibaba Cloud Service (rollenbasiertes SSO)](https://www.aliyun.com/service/) unterstützen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

Nach Abschluss die vorherigen Konfigurationen testen Sie Alibaba Cloud Service (rollenbasiertes SSO) mit folgenden Schritten:

1. Wählen Sie im Azure-Portal auf der Seite **Alibaba Cloud Service (rollenbasiertes SSO)** die Option **Einmaliges Anmelden** aus, und klicken Sie auf **Testen**.

    ![Testkonfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Klicken Sie auf **Als aktueller Benutzer anmelden**.

    ![Testkonfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Wählen Sie auf der Kontoauswahlseite „u2“ aus.

    ![Testkonfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. Die folgende Seite wird angezeigt und zeigt an, dass das rollenbasierte SSO erfolgreich ausgeführt wurde.

    ![Testkonfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


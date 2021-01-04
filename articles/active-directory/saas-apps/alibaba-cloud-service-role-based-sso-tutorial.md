---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Alibaba Cloud Service (rollenbasiertes SSO) | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Alibaba Cloud Service (rollenbasiertes SSO) konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: jeedes
ms.openlocfilehash: 256685e035659cd0fac7fa5577d99f43f4761ea2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180579"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Alibaba Cloud Service (rollenbasiertes SSO)

In diesem Tutorial erfahren Sie, wie Sie Alibaba Cloud Service (rollenbasiertes SSO) in Azure Active Directory (Azure AD) integrieren. Die Integration von Alibaba Cloud Service (rollenbasiertes SSO) in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Alibaba Cloud Service (rollenbasiertes SSO) hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihrem Azure AD-Konto automatisch bei Alibaba Cloud Service (rollenbasiertes SSO) anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Alibaba Cloud Service-Abonnement (rollenbasiertes SSO), für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Alibaba Cloud Service (rollenbasiertes SSO) unterstützt **IDP**-initiiertes SSO.

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Hinzufügen von Alibaba Cloud Service (rollenbasiertes SSO) über den Katalog

Um die Integration von Alibaba Cloud Service (rollenbasiertes SSO) in Azure AD zu konfigurieren, müssen Sie Alibaba Cloud Service (rollenbasiertes SSO) über den Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Alibaba Cloud Service (rollenbasiertes SSO)** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Alibaba Cloud Service (rollenbasiertes SSO)** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.
5. Klicken Sie auf der Seite **Alibaba Cloud Service (rollenbasiertes SSO)**  im linken Navigationsbereich auf **Eigenschaften**, kopieren die **Objekt-ID**, und speichern Sie sie auf Ihrem Computer für die spätere Verwendung.

    ![Eigenschaftenkonfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-sso-for-alibaba-cloud-service-role-based-sso"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Alibaba Cloud Service (rollenbasiertes SSO)

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Alibaba Cloud Service (rollenbasiertes SSO) mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Alibaba Cloud Service (rollenbasiertes SSO) eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Alibaba Cloud Service (rollenbasiertes SSO) zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
2. **[Konfigurieren des rollenbasierten SSOs in Alibaba Cloud Service](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** , um Ihre Benutzer verwenden dieses Feature zu aktivieren.
    1. **[Konfigurieren des einmaligen Anmeldens für Alibaba Cloud Service (rollenbasiertes SSO)](#configure-alibaba-cloud-service-role-based-sso-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Testbenutzers für Alibaba Cloud Service (rollenbasiertes SSO)](#create-alibaba-cloud-service-role-based-sso-test-user)** , um in Alibaba Cloud Service (rollenbasiertes SSO) eine Entsprechung von Britta Simon zu erhalten, die mit ihrer Benutzerdarstellung in Azure AD verknüpft ist.
3. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Alibaba Cloud Service (rollenbasiertes SSO)** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Dienstanbieter-Metadatendatei** verfügen:

    a. Klicken Sie auf **Metadatendatei hochladen**.

    b. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    
    >[!NOTE]
    >1. Laden Sie für die internationale Alibaba Cloud-Website die Dienstanbietermetadaten unter [diesem Link](https://signin.alibabacloud.com/saml-role/sp-metadata.xml) herunter.
    > 1. Verwenden Sie [diesen Link](https://signin.aliyun.com/saml-role/sp-metadata.xml), um die Dienstanbietermetadaten für die Alibaba Cloud Service-Website (CN) herunterzuladen.

    c. Nach dem erfolgreichen Upload der Metadatendatei werden die Werte **Bezeichner** und **Antwort-URL** im Abschnitt „Alibaba Cloud Service (rollenbasiertes SSO)“ automatisch aufgefüllt:

    > [!Note]
    > Sollten die Werte **Bezeichner** und **Antwort-URL** nicht automatisch aufgefüllt werden, geben Sie die erforderlichen Werte manuell ein.

1. Für Alibaba Cloud Service (rollenbasiertes SSO) müssen Rollen in Azure AD konfiguriert werden. Der Rollenanspruch ist vorkonfiguriert und muss nicht von Ihnen konfiguriert werden, Sie müssen ihn aber dennoch in Azure AD erstellen. Informationen dazu finden Sie in [diesem Artikel](../develop/active-directory-enterprise-app-role-management.md).

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Alibaba Cloud Service (rollenbasiertes SSO) einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Alibaba Cloud Service (rollenbasiertes SSO) gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Alibaba Cloud Service (rollenbasiertes SSO)** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie auf der Registerkarte **Benutzer und Gruppen** „u2“ aus der Benutzerliste aus, und klicken Sie auf **Auswählen**. Klicken Sie dann auf **Zuweisen**.

    ![Zuweisen des Azure AD-Testbenutzers 1](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. Zeigen Sie die zugewiesene Rolle an, und testen Sie „Alibaba Cloud Service (rollenbasiertes SSO)“.

    ![Zuweisen des Azure AD-Testbenutzers 2](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Nach der Zuordnung des Benutzers (u2) wird die erstellte Rolle automatisch an den Benutzer angefügt. Wenn Sie mehrere Rollen erstellt haben, müssen Sie nach Bedarf die geeignete Rolle an den Benutzer anfügen. Wenn Sie rollenbasiertes SSO von Azure AD für mehrere Alibaba Cloud-Konten implementieren möchten, wiederholen Sie die vorherigen Schritte.

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Konfigurieren des rollenbasierten SSOs in Alibaba Cloud Service

1. Melden Sie sich mit Konto1 bei der Alibaba Cloud [RAM-Konsole](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) an.

2. Wählen Sie im linken Navigationsbereich die Option **SSO** aus.

3. Klicken Sie auf der Registerkarte **Rollenbasiertes SSO** auf **IdP erstellen**.

4. Geben Sie auf der angezeigten Seite `AAD` in das Feld „IdP-Name“ ein, geben Sie eine Beschreibung in das Feld **Notiz** ein, klicken Sie auf **Hochladen**, um die zuvor heruntergeladene Verbundmetadatendatei hochzuladen, und klicken Sie auf **OK**.

5. Nachdem der IdP erfolgreich erstellt wurde, klicken Sie auf **RAM-Rolle erstellen**.

6. Geben Sie im Feld **Name der RAM Rolle**`AADrole` ein, wählen Sie `AAD` aus der Dropdownliste **IdP auswählen** aus, und klicken Sie auf „OK“.

    >[!NOTE]
    >Sie können der Rolle bei Bedarf die Berechtigung erteilen. Nach dem Erstellen des IdPs und der entsprechenden Rolle empfehlen wir, die ARNs des IdPs und der Rolle zur späteren Verwendung zu speichern. Die ARNs finden Sie auf der Seite mit den IdP-Informationen und auf der Seite mit den Rolleninformationen.

7. Ordnen Sie die Alibaba Cloud RAM-Rolle (AADrole) dem Azure AD-Benutzer (u2) zu: Um die RAM-Rolle dem Azure AD-Benutzer zuzuordnen, müssen Sie eine Rolle in Azure AD erstellen, indem Sie diesen Schritten folgen:

    a. Melden Sie sich beim [Microsoft Graph-Tester](https://developer.microsoft.com/graph/graph-explorer) an.

    b. Klicken Sie auf **Berechtigungen ändern** , um die erforderlichen Berechtigungen zum Erstellen einer Rolle zu erhalten.

    ![Graph-Konfiguration 1](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Wählen Sie die folgenden Berechtigungen aus der Liste aus, und klicken Sie auf **Berechtigungen ändern**, wie in der folgenden Abbildung dargestellt.

    ![Graph-Konfiguration 2](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Nach dem Zuweisen der Berechtigungen melden Sie sich wieder beim Graph-Tester an.

    d. Wählen Sie auf die Graph-Tester Seite aus der ersten Dropdownliste **GET** und aus der zweiten Dropdownliste **beta** aus. Geben Sie dann `https://graph.microsoft.com/beta/servicePrincipals` in das Feld neben den Dropdownlisten ein, und klicken Sie auf **Abfrage ausführen**.

    ![Graph-Konfiguration 3](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Wenn Sie mehrere Verzeichnisse verwenden, können Sie `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` in das Feld der Abfrage eingeben.

    e. Extrahieren Sie im Abschnitt **Antwortvorschau** die appRoles-Eigenschaft aus „Dienstprinzipal“ für die zukünftige Verwendung.

    ![Graph-Konfiguration 4](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

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

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Konfigurieren des einmaligen Anmeldens für Alibaba Cloud Service (rollenbasiertes SSO)

Um das einmalige Anmelden aufseiten von **Alibaba Cloud Service (rollenbasiertes SSO)** zu konfigurieren, müssen Sie die heruntergeladene **Verbundmetadaten-XML** und die entsprechenden kopierten URLs aus dem Azure-Portal an das [Supportteam von Alibaba Cloud Service (rollenbasiertes SSO)](https://www.aliyun.com/service/) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Erstellen eines Testbenutzers für Alibaba Cloud Service (rollenbasiertes SSO)

In diesem Abschnitt erstellen Sie in Alibaba Cloud Service (rollenbasiertes SSO) einen Benutzer namens Britta Simon. Lassen Sie sich beim Hinzufügen der Benutzer zur Plattform für Alibaba Cloud Service (rollenbasiertes SSO) ggf. vom [Supportteam für Alibaba Cloud Service (rollenbasiertes SSO)](https://www.aliyun.com/service/) unterstützen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

Nach Abschluss die vorherigen Konfigurationen testen Sie Alibaba Cloud Service (rollenbasiertes SSO) mit folgenden Schritten:

1. Wählen Sie im Azure-Portal auf der Seite **Alibaba Cloud Service (rollenbasiertes SSO)** die Option **Einmaliges Anmelden** aus, und klicken Sie auf **Testen**.

    ![Testkonfiguration 1](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Klicken Sie auf **Als aktueller Benutzer anmelden**.

    ![Testkonfiguration 2](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Wählen Sie auf der Kontoauswahlseite „u2“ aus.

    ![Testkonfiguration 3](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. Die folgende Seite wird angezeigt und zeigt an, dass das rollenbasierte SSO erfolgreich ausgeführt wurde.

    ![Testkonfiguration 4](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren des einmaligen Anmeldens von Alibaba Cloud Service (rollenbasiertes SSO) können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
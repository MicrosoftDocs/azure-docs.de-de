---
title: 'Tutorial: Azure Active Directory-Integration von Amazon Web Services (AWS) zum Verbinden mehrerer Konten | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure AD und Amazon Web Services (AWS) konfigurieren (altes Tutorial).
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
ms.openlocfilehash: 246eba24f42baacf264c1c6d39ea63a51c62c51f
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457434"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws-legacy-tutorial"></a>Tutorial: Azure Active Directory-Integration mit Amazon Web Services (AWS) (altes Tutorial)

In diesem Tutorial erfahren Sie, wie Sie Azure Active Directory (Azure AD) mit Amazon Web Services (AWS) integrieren (altes Tutorial).

Die Integration von Amazon Web Services (AWS) in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer auf Amazon Web Services (AWS) Zugriff hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch für Amazon Web Services (AWS) anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

![Amazon Web Services (AWS) in der Ergebnisliste](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Beachten Sie bitte, dass das Verbinden einer AWS-App mit allen Ihren AWS-Konten nicht der von uns empfohlene Ansatz ist. Stattdessen empfehlen wir Ihnen die Verwendung [dieses](./amazon-web-service-tutorial.md) Ansatzes, um mehrere Instanzen von AWS-Konten mit mehreren Instanzen von AWS-Apps in Azure AD zu konfigurieren. Sie sollten diese Vorgehensweise nur anwenden, wenn Sie über wenige AWS-Konten und -Rollen verfügen. Dieses Modell ist nicht skalierbar, wenn die AWS-Konten und -Rollen innerhalb dieser Konten anwachsen. Bei diesem Ansatz wird nicht die AWS-Rollenimportfunktion über die Azure AD-Benutzerbereitstellung verwendet, sodass Sie die Rollen manuell hinzufügen/aktualisieren/löschen müssen. Weitere Einschränkungen bei diesem Ansatz finden Sie in den Details unten.

**Beachten Sie bitte, dass wir aus folgenden Gründen nicht empfehlen, diesen Ansatz zu verwenden:**

* Sie müssen den Microsoft Graph-Tester-Ansatz verwenden, um der App alle Rollen zuzuweisen. Wir empfehlen nicht, den Ansatz mittels Manifestdatei zu verwenden.

* Kunden haben uns berichtet, dass nach dem Hinzufügen von ca. 1.200 App-Rollen für eine einzelne AWS-App jeder Vorgang mit der App begann, Fehler im Zusammenhang mit der Größe auszulösen. Es gibt ein festes Limit für die Größe des Anwendungsobjekts.

* Sie müssen die Rolle manuell aktualisieren, wenn die Rollen in einem der Konten hinzugefügt werden, wobei es sich um ein Ersetzen, leider nicht um ein Anfügen handelt. Außerdem, wenn Ihre Konten mehr werden, wird hieraus eine n x n-Beziehung zwischen Konten und Rollen.

* Alle AWS-Konten verwenden dann dieselbe Verbundmetadaten-XML-Datei, und zum Zeitpunkt des Zertifikatrollovers müssen Sie diese riesige Aufgabe bewältigen, um das Zertifikat für alle AWS-Konten gleichzeitig zu aktualisieren.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Amazon Web Services (AWS) konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein SSO-fähiges AWS-Abonnement (Amazon Web Services)

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie eine [einmonatige Testversion anfordern](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Amazon Web Services (AWS) unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Hinzufügen von Amazon Web Services (AWS) aus dem Katalog

Zum Konfigurieren der Integration von Amazon Web Services (AWS) in Azure AD müssen Sie Amazon Web Services (AWS) aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Amazon Web Services (AWS)** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich die Option **Amazon Web Services (AWS)** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

1. Nachdem die Anwendung hinzugefügt wurde, wechseln Sie zur Seite **Eigenschaften** , und kopieren Sie die **Objekt-ID**.

    ![ObjectID](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurieren und Testen des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Amazon Web Services (AWS) basierend auf einer Testbenutzerin mit dem Namen Britta Simon.

Damit das einmalige Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Amazon Web Services (AWS) als Gegenstück zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Amazon Web Services (AWS) muss eine Linkbeziehung eingerichtet werden.

Weisen Sie in Amazon Web Services (AWS) den Wert für **Benutzername** in Azure AD als Wert für **Benutzername** zu, um eine Linkbeziehung herzustellen.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD bei Amazon Web Services (AWS) zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Amazon Web Services (AWS)](#configure-amazon-web-services-aws-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer Amazon Web Services-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Amazon Web Services (AWS) die folgenden Schritte aus:**

1. Wählen Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Amazon Web Services (AWS)** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Im Abschnitt **Basic SAML Configuration** (SAML-Basiskonfiguration) muss der Benutzer keine Schritte ausführen, weil die App bereits mit Azure integriert ist, und lediglich auf **Speichern** klicken.

5. Die AWS-Anwendung (Amazon Web Services) erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute und Ansprüche** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten** , um das Dialogfeld **Benutzerattribute und Ansprüche** zu öffnen.

    ![Screenshot der Benutzerattribute, auf dem das Bearbeitungssteuerelement hervorgehoben ist](common/edit-attribute.png)

6. Konfigurieren Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** das SAML-Tokenattribut wie in der obigen Abbildung gezeigt, und führen Sie die folgenden Schritte aus:

    | Name  | Quellattribut  | Namespace |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Role            | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration             | „Geben Sie einen Wert zwischen 900 Sekunden (15 Minuten) und 43.200 Sekunden (12 Stunden) an“ |  `https://aws.amazon.com/SAML/Attributes` |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen** , um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![Screenshot von „Benutzeransprüche“, auf dem „Neuen Anspruch hinzufügen“ und „Speichern“ hervorgehoben sind](common/new-save-attribute.png)

    ![Screenshot der Seite „Benutzeransprüche verwalten“ zum Eingeben der in diesem Schritt beschriebenen Werte](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Geben Sie im Textfeld **Namespace** den für die betreffende Zeile angezeigten Namespacewert ein.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um die **Verbundmetadaten-XML** herunterzuladen, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Amazon Web Services (AWS)

1. Melden Sie sich in einem anderen Browserfenster bei der Unternehmenswebsite von Amazon Web Services (AWS) als Administrator an.

1. Klicken Sie auf **AWS Home**.

    ![Einmaliges Anmelden konfigurieren – Startseite][11]

1. Klicken Sie auf **Identitäts- und Zugriffsverwaltung**.

    ![Einmaliges Anmelden konfigurieren – Identität][12]

1. Klicken Sie auf **Identitätsanbieter** , und klicken Sie dann auf **Anbieter erstellen**.

    ![Einmaliges Anmelden konfigurieren – Anbieter][13]

1. Führen Sie auf der Dialogfeldseite **Anbieter konfigurieren** die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren – Dialogfeld][14]

    a. Wählen Sie für **Anbietertyp** die Option **SAML** aus.

    b. Geben Sie im Textfeld **Anbietername** einen Anbieternamen ein (zum Beispiel: *WAAD* ).

    c. Klicken Sie auf **Datei auswählen** , um die aus dem Azure-Portal heruntergeladene **Metadatendatei** hochzuladen.

    d. Klicken Sie auf **Nächster Schritt**.

1. Klicken Sie auf der Dialogfeldseite **Anbieterinformationen überprüfen** auf **Erstellen**.

    ![Einmaliges Anmelden konfigurieren – Überprüfen][15]

1. Klicken Sie auf **Rollen** , und klicken Sie dann auf **Rolle erstellen**.

    ![Einmaliges Anmelden konfigurieren – Rollen][16]

    > [!NOTE]
    > Der Rollen-ARN und der SAML-Anbieter-ARN für eine zu importierende Rolle dürfen zusammen maximal 240 Zeichen lang sein.

1. Führen Sie auf der Seite **Rolle erstellen** die folgenden Schritte aus:  

    ![Einmaliges Anmelden konfigurieren – Vertrauensstellung][19]

    a. Wählen Sie **SAML 2.0-Verbund** unter **Typ der vertrauenswürdige Entität auswählen**.

    b. Wählen Sie im **Abschnitt zum Auswählen eines SAML 2.0-Anbieters** den **SAML-Anbieter** aus, den Sie zuvor erstellt haben (Beispiel: *WAAD* ).

    c. Wählen Sie **Programmgesteuerten und AWS Management Console-Zugriff zulassen** aus.
  
    d. Klicken Sie auf **Weiter: Permissions** (Weiter: Berechtigungen).

1. Suchen Sie in der Suchleiste nach **Administrator Access** (Administratorzugriff), aktivieren Sie das Kontrollkästchen für **AdministratorAccess** , und klicken Sie auf **Weiter: Tags**.

    ![Screenshot: „AdministratorAccess“ als Richtlinienname ausgewählt.](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. Führen Sie im Abschnitt **Add tags (optional)** (Tags hinzufügen (optional)) die folgenden Schritte aus:

    ![Hinzufügen von Tags](./media/aws-multi-accounts-tutorial/config2.png)

    a. Geben Sie in das Textfeld **Key** (Schlüssel) den Namen des Schlüssels ein, z. B.: Azureadtest.

    b. Geben Sie in das Textfeld **Value (optional)** (Wert (optional)) den Schlüsselwert im folgenden Format ein: `accountname-aws-admin`. Dabei sollte der gesamte Kontoname kleingeschrieben werden.

    c. Klicken Sie auf **Next: Review** (Weiter: Überprüfen).

1. Führen Sie im Dialogfeld **Überprüfung** die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren – Überprüfung][34]

    a. Geben Sie in das Textfeld **Role name** (Rollenname) den Wert im folgenden Format ein: `accountname-aws-admin`.

    b. Geben Sie in das Textfeld **Role description** (Rollenbeschreibung) den Wert ein, den Sie auch für den Rollennamen verwendet haben.

    c. Klicken Sie auf **Rolle erstellen**.

    d. Erstellen Sie so viele Rollen, wie Sie benötigen, und ordnen Sie diese dem Identitätsanbieter zu.

    > [!NOTE]
    > Erstellen Sie auf ähnliche Weise weitere Rollen wie Kontoname-Finanzen-Admin, Kontoname-Benutzer-mit-Lesezugriff, Kontoname-DevOps-Benutzer und Kontoname-TPM-Benutzer mit verschiedenen zugewiesenen Richtlinien. Diese Rollenrichtlinien können zwar später nach Bedarf im AWS-Konto auch noch geändert werden, aber es ist immer besser, in AWS kontenübergreifend für Rollen immer dieselben Richtlinien beizubehalten.

1. Notieren Sie sich die Konto-ID für dieses AWS-Konto, die Sie entweder in den EC2-Eigenschaften oder, wie unten hervorgehoben, im IAM-Dashboard finden:

    ![Dieser Screenshot zeigt, wo die Konto-ID im AWS-Fenster angezeigt wird.](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Melden Sie sich nun beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu **Gruppen**.

1. Erstellen Sie neue Gruppen mit den gleichen Namen wie die IAM-Rollen, die Sie vorhin erstellt haben, und notieren Sie sich die **Objekt-IDs** für diese neuen Gruppen.

    ![Administratorzugriff1 auswählen](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Melden Sie sich vom aktuellen AWS-Konto ab, und melden Sie sich mit einem anderen Konto an, für das Sie das einmalige Anmelden mit Azure AD konfigurieren möchten.

1. Nach der Erstellung aller Rollen in den Konten werden sie in der Liste **Rollen** für diese Konten angezeigt.

    ![Screenshot: Rollenliste mit Name, Beschreibung und vertrauenswürdigen Entitäten der Rollen.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

1. Wir müssen alle Rollen-ARN und vertrauenswürdigen Entitäten für alle Rollen in allen Konten erfassen, die der Azure AD-Anwendung manuell zugeordnet werden müssen.

1. Klicken Sie auf die Rollen, um die Werte **Rollen-ARN** und **Vertrauenswürdige Entitäten** zu kopieren. Sie benötigen diese Werte für alle Rollen, die Sie in Azure AD erstellen müssen.

    ![Rollen-Einrichtung2](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Führen Sie den obigen Schritt für alle Rollen in allen Konten aus, und speichern Sie alle im Format **Rollen-ARN, vertrauenswürdige Entitäten** in Editor.

1. Öffnen Sie den [Microsoft Graph-Tester](https://developer.microsoft.com/graph/graph-explorer) in einem anderen Fenster.

    a. Melden Sie sich bei der Microsoft Graph-Tester-Website mit den Anmeldeinformationen des globalen Administrators/Co-Admins für Ihren Mandanten an.

    b. Sie müssen über ausreichende Berechtigungen zum Erstellen der Rollen verfügen. Klicken Sie auf **Berechtigungen ändern** , um die erforderlichen Berechtigungen zu erhalten.

    ![Dialogfeld1 „Microsoft Graph-Explorer“](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Wählen Sie in der Liste die folgenden Berechtigungen aus (sofern Sie noch nicht darüber verfügen), und klicken Sie auf „Berechtigungen ändern“. 

    ![Dialogfeld2 „Microsoft Graph-Explorer“](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Darin werden Sie aufgefordert, sich erneut anzumelden und die Zustimmungserklärung zu akzeptieren. Nachdem Sie die Zustimmungserklärung akzeptiert haben, werden Sie erneut bei Microsoft Graph-Tester angemeldet.

    e. Ändern Sie die Dropdownliste für die Version in **Beta**. Verwenden Sie die folgende Abfrage, um alle Dienstprinzipale aus Ihrem Mandanten abzurufen:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Wenn Sie mehrere Verzeichnisse verwenden, können Sie das folgende Muster nutzen, das Ihre primäre Domäne enthält: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`.

    ![Dialogfeld3 „Microsoft Graph-Explorer“](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)
  
    f. Entnehmen Sie der Liste der abgerufenen Dienstprinzipale den Namen, den Sie ändern möchten. Sie können auch mit STRG+F die Anwendung in den aufgeführten Dienstprinzipalen suchen. Sie können die folgende Abfrage mit der **Objekt-ID des Dienstprinzipals** verwenden, die Sie auf der Seite mit den Azure AD-Eigenschaften kopiert haben, um zum entsprechenden Dienstprinzipal zu gelangen.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Dialogfeld4 „Microsoft Graph-Explorer“](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Extrahieren Sie die appRoles-Eigenschaft aus dem Dienstprinzipalobjekt.

    ![Dialogfeld5 „Microsoft Graph-Explorer“](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Jetzt müssen Sie neue Rollen für Ihre Anwendung generieren. 

    i. Der nachfolgende JSON-Code ist ein Beispiel für ein appRoles-Objekt. Erstellen Sie ein ähnliches Objekt, um die gewünschten Rollen für Ihre Anwendung hinzuzufügen.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Neue Rollen können nur nach der Rolle **msiam_access** für den Patchvorgang hinzugefügt werden. Darüber hinaus können Sie je nach Bedarf Ihrer Organisation beliebig viele Rollen hinzufügen. Azure AD sendet den **Wert** dieser Rollen als Anspruchswert in der SAML-Antwort.

    j. Wechseln Sie wieder zu Microsoft Graph-Tester, und ändern Sie die Methode von **GET** in **PATCH**. Patchen Sie das Dienstprinzipalobjekt so, dass es über die gewünschten Rollen verfügt, indem Sie die appRoles-Eigenschaft entsprechend der oben im Beispiel gezeigten Eigenschaft aktualisieren. Klicken Sie auf **Abfrage ausführen** , um den Patchvorgang auszuführen. In einer Erfolgsmeldung wird die Erstellung der Rolle für Ihre Amazon Web Services-Anwendung bestätigt.

    ![Dialogfeld6 „Microsoft Graph-Explorer“](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Nachdem der Dienstprinzipal mit weiteren Rollen gepatcht wurde, können Sie den jeweiligen Rollen Benutzer/Gruppen zuweisen. Wechseln Sie zu diesem Zweck zum Portal, und navigieren Sie zur Amazon Web Services-Anwendung. Klicken Sie oben auf die Registerkarte **Benutzer und Gruppen**.

1. Es empfiehlt sich, neue Gruppen für jede AWS-Rolle zu erstellen, damit Sie die jeweilige Rolle in dieser Gruppe zuweisen können. Beachten Sie, dass es sich hierbei um eine 1: 1-Zuordnung handelt, bei der einer Gruppe eine Rolle zugeordnet ist. Anschließend können Sie die Mitglieder hinzufügen, die dieser Gruppe angehören.

1. Nachdem die Gruppen erstellt wurden, können Sie die Gruppe auswählen und der Anwendung zuweisen.

    ![Einmaliges Anmelden konfigurieren, Hinzufügen1](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Geschachtelte Gruppen werden beim Zuweisen von Gruppen nicht unterstützt.

1. Um der Gruppe die Rolle zuzuweisen, wählen Sie die Rolle aus, und klicken Sie unten auf der Seite auf die Schaltfläche **Zuweisen**.

    ![Einmaliges Anmelden konfigurieren, Hinzufügen2](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Beachten Sie, dass Sie die Sitzung im Azure-Portal aktualisieren müssen, damit neue Rollen angezeigt werden.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Amazon Web Services (AWS)“ klicken, sollte die Amazon Web Services-Anwendungsseite mit der Option zum Auswählen der Rolle angezeigt werden.

![Einmaliges Anmelden testen1](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Sie können auch die SAML-Antwort überprüfen, um die als Ansprüche übergebenen Rollen anzuzeigen.

![Einmaliges Anmelden testen2](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Amazon Web Services (AWS) können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
---
title: 'Tutorial: Konfigurieren des SAP SuccessFactors-Rückschreibens in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das Rückschreiben von Attributen aus Azure AD in SAP SuccessFactors konfigurieren.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: 3260787dec4ae26cd6ef7cc3bd562f39db8e3655
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526974"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors"></a>Tutorial: Konfigurieren des Rückschreibens von Attributen aus Azure AD in SAP SuccessFactors
In diesem Tutorial werden die Schritte zum Rückschreiben von Attributen aus Azure AD in SuccessFactors Employee Central beschrieben. 

## <a name="overview"></a>Übersicht

Sie können die SAP Success Factors Writeback-App so konfigurieren, dass bestimmte Attribute aus Azure Active Directory in SAP SuccessFactors Employee Central geschrieben werden. Die Bereitstellungs-App SuccessFactors Writeback unterstützt das Zuweisen von Werten zu den folgenden Employee Central-Attributen:

* Geschäftliche E-Mail-Adresse
* Username
* Geschäftliche Telefonnummer (einschließlich Landeskennzahl, Ortsvorwahl, Rufnummer und Durchwahl)
* Primäres Flag für die geschäftliche Telefonnummer
* Mobiltelefonnummer (einschließlich Landeskennzahl, Ortsvorwahl, Rufnummer)
* Primäres Flag für die Mobiltelefonnummer 
* Benutzerattribute „custom01“ bis „custom15“
* Attribut „loginmethod“

> [!NOTE]
> Diese App weist keine Abhängigkeit von den Integrations-Apps für die eingehende Benutzerbereitstellung von SuccessFactors auf. Sie können sie unabhängig von der Bereitstellungs-App [SuccessFactors to on-premises AD](sap-successfactors-inbound-provisioning-tutorial.md) bzw. der Bereitstellungs-App [SuccessFactors to Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) konfigurieren.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Für wen ist diese Benutzerbereitstellungslösung am besten geeignet?

Diese Lösung zur Benutzerbereitstellung für das SuccessFactors-Rückschreiben ist ideal geeignet für:

* Organisationen, die Microsoft 365 verwenden und von der IT-Abteilung verwaltete autoritative Attribute (z. B. E-Mail-Adresse, Telefonnummer, Benutzername) in SuccessFactors Employee Central zurückschreiben möchten.

## <a name="configuring-successfactors-for-the-integration"></a>Konfigurieren von SuccessFactors für die Integration

Die SuccessFactors-Bereitstellungsconnectors setzen zum Aufrufen der OData-APIs von Employee Central Anmeldeinformationen eines SuccessFactors-Kontos mit den passenden Berechtigungen voraus. In diesem Abschnitt werden die Schritte zum Erstellen des Dienstkontos in SuccessFactors und zum Erteilen der benötigten Berechtigungen beschrieben. 

* [Erstellen/Ermitteln des API-Benutzerkontos in SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Erstellen einer API-Berechtigungsrolle](#create-an-api-permissions-role)
* [Erstellen einer Berechtigungsgruppe für den API-Benutzer](#create-a-permission-group-for-the-api-user)
* [Erteilen der Berechtigungsrolle für die Berechtigungsgruppe](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Erstellen/Ermitteln des API-Benutzerkontos in SuccessFactors
Wenden Sie sich an das Administratorteam von SuccessFactors oder Ihren Implementierungspartner, um ein Benutzerkonto in SuccessFactors zu erstellen oder zu bestimmen, das zum Aufrufen der OData-APIs verwendet wird. Die Anmeldeinformationen (Benutzername und Kennwort) dieses Kontos sind für das Konfigurieren der Bereitstellungs-Apps in Azure AD erforderlich. 

### <a name="create-an-api-permissions-role"></a>Erstellen einer API-Berechtigungsrolle

1. Melden Sie sich bei SAP SuccessFactors mit einem Benutzerkonto an, das Zugriff auf das Admin Center hat.
1. Suchen Sie nach *Manage Permission Roles* (Berechtigungsrollen verwalten), und wählen Sie dann in den Suchergebnissen **Manage Permission Roles** (Berechtigungsrollen verwalten) aus.

   ![Verwalten von Berechtigungsrollen](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)

1. Klicken Sie in der Liste „Permission Role“ (Berechtigungsrolle) auf **Create New** (Neue erstellen).

   > [!div class="mx-imgBorder"]
   > ![Erstellen einer neuen Berechtigungsrolle](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)

1. Fügen Sie einen **Rollennamen** und eine **Beschreibung** für die neue Berechtigungsrolle hinzu. Der Name und die Beschreibung sollten darauf hindeuten, dass die Rolle für Berechtigungen zur API-Verwendung vorgesehen ist.

   > [!div class="mx-imgBorder"]
   > ![Details zur Berechtigungsrolle](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)

1. Klicken Sie unter den Berechtigungseinstellungen auf **Permission...** (Berechtigung), scrollen Sie in der Berechtigungsliste nach unten, und klicken Sie auf **Manage Integration Tools** (Integrationstools verwalten). Aktivieren Sie das Kontrollkästchen für **Allow Admin to Access to OData API through Basic Authentication** (Administratoren den Zugriff auf OData-API über die Standardauthentifizierung erlauben).

   > [!div class="mx-imgBorder"]
   > ![Verwalten von Integrationstools](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)

1. Scrollen Sie in demselben Feld nach unten, und wählen Sie **Employee Central API** aus. Fügen Sie wie unten gezeigt Berechtigungen für das Lesen und Bearbeiten über die OData-API hinzu. Wählen Sie die Bearbeitungsoption aus, wenn Sie dasselbe Konto für das Rückschreiben in ein SuccessFactors-Szenario verwenden möchten. 

   > [!div class="mx-imgBorder"]
   > ![Lese-/Schreibberechtigungen](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Klicken Sie auf **Done** (Fertig). Klicken Sie auf **Änderungen speichern**.

### <a name="create-a-permission-group-for-the-api-user"></a>Erstellen einer Berechtigungsgruppe für den API-Benutzer

1. Suchen Sie im SuccessFactors Admin Center nach *Manage Permission Groups* (Berechtigungsgruppen verwalten), und wählen Sie in den Suchergebnissen dann **Manage Permission Groups** (Berechtigungsgruppen verwalten) aus.

   > [!div class="mx-imgBorder"]
   > ![Verwalten von Berechtigungsgruppen](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)

1. Klicken Sie im Fenster „Manage Permission Groups“ (Berechtigungsgruppen verwalten) auf **Create New** (Neue erstellen).

   > [!div class="mx-imgBorder"]
   > ![Hinzufügen einer neuen Gruppe](./media/sap-successfactors-inbound-provisioning/create-new-group.png)

1. Fügen Sie einen Gruppennamen für die neue Gruppe hinzu. Im Gruppennamen sollte angegeben werden, dass die Gruppe für API-Benutzer vorgesehen ist.

   > [!div class="mx-imgBorder"]
   > ![Name der Berechtigungsgruppe](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)

1. Fügen Sie der Gruppe Mitglieder hinzu. Beispielsweise können Sie im Dropdownmenü „People Pool“ (Personenpool) die Option **Username** (Benutzername) auswählen und dann den Benutzernamen des API-Kontos eingeben, das für die Integration verwendet werden soll. 

   > [!div class="mx-imgBorder"]
   > ![Add group members](./media/sap-successfactors-inbound-provisioning/add-group-members.png) (Hinzufügen von Gruppenmitgliedern)

1. Klicken Sie auf **Done** (Fertig), um die Erstellung der Berechtigungsgruppe abzuschließen.

### <a name="grant-permission-role-to-the-permission-group"></a>Erteilen der Berechtigungsrolle für die Berechtigungsgruppe

1. Suchen Sie im SuccessFactors Admin Center nach *Manage Permission Roles* (Berechtigungsrollen verwalten), und wählen Sie in den Suchergebnissen dann **Manage Permission Roles** (Berechtigungsrollen verwalten) aus.
1. Wählen Sie in der Liste **Permission Role** (Berechtigungsrolle) die Rolle aus, die Sie für die Berechtigungen zur API-Verwendung erstellt haben.
1. Klicken Sie unter **Grant this role to...** (Rolle erteilen für) auf die Schaltfläche **Add...** (Hinzufügen).
1. Wählen Sie im Dropdownmenü die Option **Permission Group...** (Berechtigungsgruppe) aus, und klicken Sie dann auf **Select...** (Auswählen), um das Fenster „Groups“ (Gruppen) zu öffnen. Darin können Sie die oben erstellte Gruppe suchen und auswählen. 

   > [!div class="mx-imgBorder"]
   > ![Hinzufügen der Berechtigungsgruppe](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. Überprüfen Sie die erteilte Berechtigungsrolle für die Berechtigungsgruppe. 
   > [!div class="mx-imgBorder"]
   > ![Details zu Berechtigungsrolle und -gruppe](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. Klicken Sie auf **Änderungen speichern**.

## <a name="preparing-for-successfactors-writeback"></a>Vorbereitungen für SuccessFactors Writeback

Die Bereitstellungs-App SuccessFactors Writeback verwendet bestimmte *Codewerte* für das Festlegen von E-Mail-Adressen und Telefonnummern in Employee Central. Diese *Codewerte* werden in der Attributzuordnungstabelle als konstante Werte festgelegt und sind für jede SuccessFactors-Instanz anders. Dieser Abschnitt enthält die Schritte zum Erfassen dieser *Codewerte*.

   > [!NOTE]
   > Wenden Sie sich an Ihren SuccessFactors-Administrator, um die Schritte in diesem Abschnitt auszuführen. 

### <a name="identify-email-and-phone-number-picklist-names"></a>Identifizieren der Namen für die Auswahllisten „E-Mail-Adresse“ und „Telefonnummer“ 

In SAP SuccessFactors umfasst eine *Auswahlliste* konfigurierbare Optionen, aus denen Benutzer eine Auswahl treffen können. Die unterschiedlichen Typen von E-Mail-Adressen und Telefonnummern (z. B. geschäftlich, privat usw.) werden mithilfe einer Auswahlliste dargestellt. In diesem Schritt identifizieren Sie die Auswahllisten, die in Ihrem SuccessFactors-Mandanten für die Speicherung von Werten für E-Mail-Adressen und Telefonnummern konfiguriert sind. 
 
1. Suchen Sie im SuccessFactors Admin Center nach *Manage business configuration*. 

   > [!div class="mx-imgBorder"]
   > ![Manage business configuration](./media/sap-successfactors-inbound-provisioning/manage-business-config.png)

1. Wählen Sie unter **HRIS Elements** die Option **emailInfo** aus, und klicken Sie neben dem Feld **email-type** auf *Details*.

   > [!div class="mx-imgBorder"]
   > ![Abrufen von E-Mail-Informationen](./media/sap-successfactors-inbound-provisioning/get-email-info.png)

1. Notieren Sie sich auf der Detailseite **email-type** den Namen der Auswahlliste, die diesem Feld zugeordnet ist. Standardmäßig ist dies **ecEmailType**. Dies kann jedoch in Ihrem Mandanten anders sein. 

   > [!div class="mx-imgBorder"]
   > ![Ermitteln der E-Mail-Auswahlliste](./media/sap-successfactors-inbound-provisioning/identify-email-picklist.png)

1. Wählen Sie unter **HRIS Elements** die Option **phoneInfo** aus, und klicken Sie neben dem Feld **phone-type** auf *Details*.

   > [!div class="mx-imgBorder"]
   > ![Abrufen von Telefoninformationen](./media/sap-successfactors-inbound-provisioning/get-phone-info.png)

1. Notieren Sie sich auf der Detailseite **phone-type** den Namen der Auswahlliste, die diesem Feld zugeordnet ist. Standardmäßig ist dies **ecPhoneType**. Dies kann jedoch in Ihrem Mandanten anders sein. 

   > [!div class="mx-imgBorder"]
   > ![Ermitteln der Telefonauswahlliste](./media/sap-successfactors-inbound-provisioning/identify-phone-picklist.png)

### <a name="retrieve-constant-value-for-emailtype"></a>Abrufen des konstanten Werts für „emailType“

1. Suchen und öffnen Sie im SuccessFactors Admin Center das *Picklist Center*. 
1. Verwenden Sie den Namen der im vorherigen Abschnitt notierten E-Mail-Auswahlliste (z. B. ecEmailType), um die E-Mail-Auswahlliste zu finden. 

   > [!div class="mx-imgBorder"]
   > ![Suchen der E-Mail-Typ-Auswahlliste](./media/sap-successfactors-inbound-provisioning/find-email-type-picklist.png)

1. Öffnen Sie die aktive E-Mail-Auswahlliste. 

   > [!div class="mx-imgBorder"]
   > ![Öffnen der aktiven E-Mail-Typ-Auswahlliste](./media/sap-successfactors-inbound-provisioning/open-active-email-type-picklist.png)

1. Wählen Sie auf der Seite der Auswahlliste für den E-Mail-Typ den Typ *Business*.

   > [!div class="mx-imgBorder"]
   > ![Auswählen des E-Mail-Typs „Business“](./media/sap-successfactors-inbound-provisioning/select-business-email-type.png)

1. Notieren Sie sich die **Option ID**, die dem E-Mail-Typ *Business* zugeordnet ist. Sie verwenden diesen Code mit *emailType* in der Attributzuordnungstabelle.

   > [!div class="mx-imgBorder"]
   > ![Abrufen des Codes für den E-Mail-Typ](./media/sap-successfactors-inbound-provisioning/get-email-type-code.png)

   > [!NOTE]
   > Entfernen Sie das Komma, wenn Sie den Wert kopieren. Wenn z. B. der Wert von **Option ID** *8,448* lautet, legen Sie *emailType* in Azure AD auf die Konstante *8448* (ohne Komma) fest. 

### <a name="retrieve-constant-value-for-phonetype"></a>Abrufen des konstanten Werts für „phoneType“

1. Suchen und öffnen Sie im SuccessFactors Admin Center das *Picklist Center*. 
1. Verwenden Sie den Namen der im vorherigen Abschnitt notierten Telefonauswahlliste, um die Telefonauswahlliste zu finden. 

   > [!div class="mx-imgBorder"]
   > ![Suchen der Telefontyp-Auswahlliste](./media/sap-successfactors-inbound-provisioning/find-phone-type-picklist.png)

1. Öffnen Sie die aktive Telefonauswahlliste. 

   > [!div class="mx-imgBorder"]
   > ![Öffnen der aktiven Telefontyp-Auswahlliste](./media/sap-successfactors-inbound-provisioning/open-active-phone-type-picklist.png)

1. Überprüfen Sie auf der Seite der Telefontyp-Auswahlliste die verschiedenen Telefontypen, die unter **Picklist Values** aufgeführt sind.

   > [!div class="mx-imgBorder"]
   > ![Überprüfen der Telefontypen](./media/sap-successfactors-inbound-provisioning/review-phone-types.png)

1. Notieren Sie sich die **Option ID**, die dem Telefontyp *Business* zugeordnet ist. Sie verwenden diesen Code mit *businessPhoneType* in der Attributzuordnungstabelle.

   > [!div class="mx-imgBorder"]
   > ![Abrufen des Codes für den Telefontyp „Business“](./media/sap-successfactors-inbound-provisioning/get-business-phone-code.png)

1. Notieren Sie sich die **Option ID**, die dem Telefontyp *Cell* zugeordnet ist. Sie verwenden diesen Code mit *cellPhoneType* in der Attributzuordnungstabelle.

   > [!div class="mx-imgBorder"]
   > ![Abrufen des Codes für den Telefontyp „Cell“](./media/sap-successfactors-inbound-provisioning/get-cell-phone-code.png)

   > [!NOTE]
   > Entfernen Sie das Komma, wenn Sie den Wert kopieren. Wenn z. B. der Wert von **Option ID** *10,606* lautet, legen Sie *cellPhoneType* in Azure AD auf die Konstante *10606* (ohne Komma) fest. 


## <a name="configuring-successfactors-writeback-app"></a>Konfigurieren der SuccessFactors Writeback-App

Dieser Abschnitt enthält die folgenden Schritte: 

* [Hinzufügen der Bereitstellungsconnector-App und Konfigurieren der Konnektivität mit SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Konfigurieren von Attributzuordnungen](#part-2-configure-attribute-mappings)
* [Aktivieren und Starten der Benutzerbereitstellung](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Teil 1: Hinzufügen der Bereitstellungsconnector-App und Konfigurieren der Konnektivität mit SuccessFactors

**Konfigurieren Sie das SuccessFactors-Rückschreiben wie folgt:**

1. Besuchen Sie <https://portal.azure.com>.

2. Wählen Sie auf der linken Navigationsleiste **Azure Active Directory** aus.

3. Klicken Sie auf **Unternehmensanwendungen** und dann auf **Alle Anwendungen**.

4. Klicken Sie auf **Anwendung hinzufügen**, und wählen Sie die Kategorie **Alle** aus.

5. Suchen Sie nach **SuccessFactors Writeback** (SuccessFactors-Rückschreiben), und fügen Sie die App aus dem Katalog hinzu.

6. Sobald die App hinzugefügt wurde und der Bildschirm mit den App-Details angezeigt wird, wählen Sie **Bereitstellung** aus.

7. Ändern Sie den **Modus** **Bereitstellung** in **Automatisch**.

8. Vervollständigen Sie den Abschnitt **Administratoranmeldeinformationen** wie folgt:

   * **Administratorbenutzername**: Geben Sie den Benutzernamen des Benutzerkontos für die SuccessFactors-API ein, und fügen Sie die Unternehmens-ID an. Verwenden Sie das Format **Benutzername\@Unternehmens-ID**.

   * **Administratorkennwort**: Geben Sie das Kennwort des Benutzerkontos für die SuccessFactors-API ein. 

   * **Mandanten-URL**: Geben Sie den Namen des Dienstendpunkts für die SuccessFactors-OData-API ein. Geben Sie nur den Hostnamen des Servers ohne „http“ oder „https“ ein. Dieser Wert sollte folgendermaßen aussehen: `api4.successfactors.com`.

   * **Benachrichtigungs-E-Mail**: Geben Sie Ihre E-Mail-Adresse ein, und aktivieren Sie das Kontrollkästchen „E-Mail senden, wenn Fehler auftritt“.
    > [!NOTE]
    > Der Azure AD-Bereitstellungsdienst sendet eine E-Mail-Benachrichtigung, wenn der Bereitstellungsauftrag in den Zustand [Quarantäne](../app-provisioning/application-provisioning-quarantine-status.md) wechselt.

   * Klicken Sie auf die Schaltfläche **Verbindung testen**. Wenn der Verbindungstest erfolgreich ist, klicken Sie oben auf die Schaltfläche **Speichern**. Falls nicht, sollten Sie überprüfen, ob die SuccessFactors-Anmeldeinformationen und die URL gültig sind.
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Nachdem die Anmeldeinformationen erfolgreich gespeichert wurden, wird im Abschnitt **Zuordnungen** die Standardzuordnung angezeigt. Aktualisieren Sie die Seite, wenn die Attributzuordnungen nicht angezeigt werden.  

### <a name="part-2-configure-attribute-mappings"></a>Teil 2: Konfigurieren von Attributzuordnungen

In diesem Abschnitt konfigurieren Sie den Fluss von Benutzerdaten aus SuccessFactors in Active Directory.

1. Klicken Sie auf der Registerkarte „Bereitstellung“ unter **Zuordnungen** auf **Azure Active Directory-Benutzer bereitstellen**.

1. Im Feld **Quellobjektbereich** können Sie auswählen, für welche Benutzergruppen in Azure AD das Rückschreiben durchgeführt werden soll, indem Sie verschiedene attributbasierte Filter definieren. Die Standardoption ist „Alle Benutzer in Azure AD“. 
   > [!TIP]
   > Wenn Sie die Bereitstellungs-App zum ersten Mal konfigurieren, müssen Sie Ihre Attributzuordnungen und Ausdrücke testen und überprüfen, um sicherzustellen, dass sie damit das gewünschte Ergebnis erzielen. Microsoft empfiehlt, die Bereichsfilter unter **Quellobjektbereich** zu verwenden, um Ihre Zuordnungen mit einigen Testbenutzern von Azure AD zu testen. Sobald Sie sich vergewissert haben, dass die Zuordnungen funktionieren, können Sie den Filter entweder entfernen oder schrittweise erweitern, um mehr Benutzer einzubinden.

1. Für das Feld **Zielobjektaktionen** wird nur der Vorgang **Aktualisieren** unterstützt.

1. In der Zuordnungstabelle können Sie im Abschnitt **Attributzuordnungen** die folgenden Azure Active Directory-Attribute zu SuccessFactors zuordnen. In der folgenden Tabelle finden Sie Hinweise, wie Attribute zum Rückschreiben zugeordnet werden können. 

   | \# | Azure AD-Attribut | SuccessFactors-Attribut | Bemerkungen |
   |--|--|--|--|
   | 1 | employeeId | personIdExternal | Standardmäßig ist dieses Attribut der übereinstimmende Kennzeichner. Anstelle von „employeeId“ können Sie jedes andere Azure AD-Attribut verwenden, das in SuccessFactors dem Wert „personIdExternal“ entspricht und entsprechend gespeichert wird.    |
   | 2 | mail | email | Ordnet die Attributquelle für „email“ zu. Zu Testzwecken können Sie „userPrincipalName“ zu „email“ zuordnen. |
   | 3 | 8448 | emailType | Dieser konstante Wert ist der SuccessFactors-ID-Wert, der mit der geschäftlichen E-Mail-Adresse verknüpft ist. Aktualisieren Sie diesen Wert, und passen Sie ihn an Ihre SuccessFactors-Umgebung an. Die Schritte zum Festlegen dieses Werts sind im Abschnitt [Abrufen des konstanten Werts für „emailType“](#retrieve-constant-value-for-emailtype) beschrieben. |
   | 4 | true | emailIsPrimary | Verwenden Sie dieses Attribut, um die geschäftliche E-Mail-Adresse in SuccessFactors als primäre Adresse festzulegen. Wenn die geschäftliche E-Mail-Adresse nicht als primäre Adresse verwendet werden soll, setzen Sie dieses Flag auf „ false“. |
   | 5 | userPrincipalName | [custom01 – custom15] | Mit der Option **Neue Zuordnung hinzufügen** können Sie optional „userPrincipalName“ oder jedes andere Azure AD-Attribut in ein benutzerdefiniertes Attribut schreiben, das im SuccessFactors-Benutzerobjekt verfügbar ist.  |
   | 6 | On Prem SamAccountName | username | Mit der Option **Neue Zuordnung hinzufügen** können Sie optional das lokale Attribut „samAccountName“ zum SuccessFactors-Attribut „username“ hinzufügen. Nutzen Sie die Informationen unter [Azure AD Connect Sync: Verzeichniserweiterungen](../hybrid/how-to-connect-sync-feature-directory-extensions.md), um „samAccountName“ mit Azure AD zu synchronisieren. Der Wert wird in der Dropdownliste für die Quelle als *extension_yourTenantGUID_samAccountName* angezeigt. |
   | 7 | SSO | loginMethod | Wenn der SuccessFactors-Mandant für das [partielle einmalige Anmelden](https://apps.support.sap.com/sap/support/knowledge/en/2320766) eingerichtet ist, können Sie über „Neue Zuordnung hinzufügen“ das Attribut „loginMethod“ optional auf den konstanten Wert „SSO“ oder „KENNWORT“ festlegen. |
   | 8 | telephoneNumber | businessPhoneNumber | Verwenden Sie diese Zuordnung, um *telephoneNumber* von Azure AD an die geschäftliche Telefonnummer von SuccessFactors zu übergeben. |
   | 9 | 10605 | businessPhoneType | Dieser konstante Wert ist der SuccessFactors-ID-Wert, der mit der geschäftlichen Telefonnummer verknüpft ist. Aktualisieren Sie diesen Wert, und passen Sie ihn an Ihre SuccessFactors-Umgebung an. Die Schritte zum Festlegen dieses Werts sind im Abschnitt [Abrufen des konstanten Werts für „phoneType“](#retrieve-constant-value-for-phonetype) beschrieben. |
   | 10 | true | businessPhoneIsPrimary | Verwenden Sie dieses Attribut, um das primäre Flag für die geschäftliche Telefonnummer festzulegen. Gültige Werte sind „true“ oder „false“. |
   | 11 | mobile | cellPhoneNumber | Verwenden Sie diese Zuordnung, um *telephoneNumber* von Azure AD an die geschäftliche Telefonnummer von SuccessFactors zu übergeben. |
   | 12 | 10606 | cellPhoneType | Dieser konstante Wert ist der SuccessFactors-ID-Wert, der mit dem Mobiltelefon verknüpft ist. Aktualisieren Sie diesen Wert, und passen Sie ihn an Ihre SuccessFactors-Umgebung an. Die Schritte zum Festlegen dieses Werts sind im Abschnitt [Abrufen des konstanten Werts für „phoneType“](#retrieve-constant-value-for-phonetype) beschrieben. |
   | 13 | false | cellPhoneIsPrimary | Verwenden Sie dieses Attribut, um das primäre Flag für die Mobiltelefonnummer festzulegen. Gültige Werte sind „true“ oder „false“. |
 
1. Validieren und überprüfen Sie Ihre Attributzuordnungen. 
 
    >[!div class="mx-imgBorder"]
    >![Writeback-Attributzuordnung](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. Klicken Sie auf **Speichern**, um die Zuordnungen zu speichern. Als Nächstes aktualisieren Sie die JSON Path-API-Ausdrücke, um die phoneType-Codes in Ihrer SuccessFactors-Instanz zu verwenden. 
1. Wählen Sie **Erweiterte Optionen anzeigen** aus. 

    >[!div class="mx-imgBorder"]
    >![Erweiterte Optionen anzeigen](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. Klicken Sie auf **Attributliste für SuccessFactors bearbeiten**. 

   > [!NOTE] 
   > Wenn die Option **Attributliste für SuccessFactors bearbeiten** im Azure-Portal nicht angezeigt wird, können Sie über die URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* auf die Seite zugreifen. 

1. In der Ansicht werden in der Spalte **API-Ausdruck** die JSON Path-Ausdrücke angezeigt, die vom Connector verwendet werden. 
1. Aktualisieren Sie die JSON Path-Ausdrücke für geschäftliche Telefonnummer und Mobiltelefonnummer so, dass der ID-Wert (*businessPhoneType* und *cellPhoneType*) angezeigt wird, der Ihrer Umgebung entspricht. 

    >[!div class="mx-imgBorder"]
    >![JSON Path-Änderung für Telefon](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. Klicken Sie auf **Speichern**, um die Zuordnungen zu speichern.

## <a name="enable-and-launch-user-provisioning"></a>Aktivieren und Starten der Benutzerbereitstellung

Nachdem die Konfiguration der SuccessFactors-Bereitstellungs-App abgeschlossen ist, können Sie den Bereitstellungsdienst im Azure-Portal aktivieren.

> [!TIP]
> Beim Aktivieren des Bereitstellungsdiensts werden Bereitstellungsvorgänge für alle Benutzer im Bereich initiiert. Wenn Fehler in der Zuordnung oder Datenprobleme auftreten, kann der Bereitstellungsauftrag fehlschlagen und in den Quarantänezustand wechseln. Um dies zu vermeiden, empfehlen wir, den Filter **Quellobjektbereich** zu konfigurieren und Ihre Attributzuordnungen mit einigen Testbenutzern zu testen, bevor Sie die vollständige Synchronisierung für alle Benutzer starten. Sobald Sie sich vergewissert haben, dass die Zuordnungen funktionieren und Sie die gewünschten Ergebnisse erhalten, können Sie den Filter entweder entfernen oder schrittweise erweitern, um mehr Benutzer einzubinden.

1. Legen Sie auf der Registerkarte **Bereitstellung** die Einstellung **Bereitstellungsstatus** auf **Ein** fest.

1. Wählen Sie **Bereich** aus. Sie können eine der folgenden Optionen auswählen: 
   * **Alle Benutzer und Gruppen synchronisieren**: Wählen Sie diese Option aus, falls Sie planen, zugeordnete Attribute aller Benutzer aus Azure AD in SuccessFactors zurückzuschreiben. Dies unterliegt den Bereichsregeln, die unter **Zuordnungen** -> **Quellobjektbereich** definiert wurden. 
   * **Nur zugewiesene Benutzer und Gruppen synchronisieren**: Wählen Sie diese Option aus, falls Sie planen, das Rückschreiben nur für zugeordnete Attribute von Benutzern durchzuführen, die Sie dieser Anwendung zugewiesen haben (Menüoption unter **Anwendung** -> **Verwalten** -> **Benutzer und Gruppen**). Diese Benutzer unterliegen auch den Bereichsregeln, die unter **Zuordnungen** -> **Quellobjektbereich** definiert sind.

   > [!div class="mx-imgBorder"]
   > ![Rückschreibe-Bereich auswählen](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > Die Bereitstellungs-App für das SuccessFactors-Rückschreiben unterstützt keine „Gruppenzuweisung“. Nur die „Benutzerzuweisung“ wird unterstützt. 

1. Klicken Sie auf **Speichern**.

1. Dieser Vorgang startet die erste Synchronisierung, die abhängig von der Anzahl von Benutzern im Azure AD-Mandanten und vom für den Vorgang definierten Bereich mehrere Stunden dauern kann. Sie können die Statusanzeige überprüfen, um den Fortschritt des Synchronisierungszyklus zu verfolgen. 

1. Im Azure-Portal können Sie sich auf der Registerkarte **Bereitstellungsprotokolle** jederzeit ansehen, welche Aktionen der Bereitstellungsdienst ausgeführt hat. In den Bereitstellungsprotokollen werden alle einzelnen Synchronisierungsereignisse aufgelistet, die vom Bereitstellungsdienst durchgeführt werden. 

1. Nach Abschluss der ersten Synchronisierung wird auf der Registerkarte **Bereitstellung** ein Überwachungszusammenfassungsbericht ausgegeben:

   > [!div class="mx-imgBorder"]
   > ![Statusanzeige für die Bereitstellung](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>Unterstützte Szenarien, bekannte Probleme und Begrenzungen

Lesen Sie hierzu den Abschnitt [Writeback-Szenarien](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios) im Referenzhandbuch zur Integration von SAP SuccessFactors. 

## <a name="next-steps"></a>Nächste Schritte

* [Studieren Sie aufmerksam das Referenzhandbuch zur Integration von Azure AD und SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md)
* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
* [Weitere Informationen zum Konfigurieren des einmaligen Anmeldens zwischen SuccessFactors und Azure Active Directory](successfactors-tutorial.md)
* [Erfahren Sie, wie Sie andere SaaS-Anwendungen in Azure Active Directory integrieren.](tutorial-list.md)
* [Weitere Informationen zum Exportieren und Importieren von Bereitstellungskonfigurationen](../app-provisioning/export-import-provisioning-configuration.md)
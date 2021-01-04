---
title: 'Tutorial: Konfigurieren des Workday-Rückschreibens in Azure Active Directory | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie das Rückschreiben von Attributen aus Azure AD für Workday konfigurieren.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: c65fddcc90b25f70759fb038a72dad0facfa99a9
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359730"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-workday"></a>Tutorial: Konfigurieren des Rückschreibens von Attributen aus Azure AD für Workday
In diesem Tutorial werden die Schritte beschrieben, die Sie ausführen müssen, um Attribute aus Azure AD für Workday zurückzuschreiben. Die Bereitstellungs-App für das Workday-Rückschreiben unterstützt das Zuweisen von Werten zu den folgenden Workday-Attributen:
* Geschäftliche E-Mail-Adresse 
* Workday-Benutzername
* Geschäftliche Festnetztelefonnummer (einschließlich Landeskennzahl, Vorwahl, Nummer und Durchwahl)
* Primäres Flag für die geschäftliche Festnetztelefonnummer
* Geschäftliche Mobilfunknummer (einschließlich Landeskennzahl, Vorwahl, Nummer)
* Primäres Flag für die geschäftliche Mobilfunknummer

## <a name="overview"></a>Übersicht

Nachdem Sie die Bereitstellungsintegration in eingehender Richtung entweder mit der Bereitstellungs-App vom Typ [Von Workday zu lokaler AD-Instanz](workday-inbound-tutorial.md) oder [Von Workday zu Azure AD](workday-inbound-cloud-only-tutorial.md) eingerichtet haben, können Sie optional die Workday-Rückschreibe-App konfigurieren, um Kontaktinformationen, wie die geschäftliche E-Mail-Adresse und die Telefonnummer für Workday zurückzuschreiben. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Für wen ist diese Benutzerbereitstellungslösung am besten geeignet?

Diese Benutzerbereitstellungslösung für das Workday-Rückschreiben eignet sich ideal für:

* Organisationen, die Microsoft 365 nutzen und von der IT-Abteilung verwaltete autoritative Attribute (z. B. die E-Mail-Adresse, Benutzername und Telefonnummer) für Workday zurückschreiben möchten.

## <a name="configure-integration-system-user-in-workday"></a>Konfigurieren eines Integrationssystembenutzers in Workday

Im Abschnitt [Konfigurieren des Integrationssystembenutzers](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) finden Sie Informationen zum Erstellen eines Benutzerkontos für das Workday-Integrationssystem mit Berechtigungen zum Abrufen von Beschäftigtendaten. 

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Konfigurieren der Azure AD-Attributzurückschreibung in Workday

Befolgen Sie diese Anweisungen, um die Zurückschreibung der E-Mail-Adressen und Benutzernamen der Benutzer aus Azure Active Directory in Workday zu konfigurieren.

* [Hinzufügen der Writeback-Connector-App und Herstellen der Verbindung mit Workday](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Konfigurieren von Writeback-Attributzuordnungen](#part-2-configure-writeback-attribute-mappings)
* [Aktivieren und Starten der Benutzerbereitstellung](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Teil 1: Hinzufügen der Writeback-Connector-App und Herstellen der Verbindung mit Workday

**So konfigurieren Sie den Workday Writeback-Connector**

1. Gehe zu <https://portal.azure.com>.

2. Suchen Sie im Azure-Portal nach **Azure Active Directory** , und wählen Sie es aus.

3. Klicken Sie auf **Unternehmensanwendungen** und dann auf **Alle Anwendungen**.

4. Klicken Sie auf **Anwendung hinzufügen** , und wählen Sie dann die Kategorie **Alle** aus.

5. Suchen Sie nach **Workday Writeback** , und fügen Sie die App aus dem Katalog hinzu.

6. Sobald die App hinzugefügt wurde und der Bildschirm mit den App-Details angezeigt wird, wählen Sie **Bereitstellung** aus.

7. Ändern Sie den **Bereitstellungsmodus** in **Automatisch**.

8. Vervollständigen Sie den Abschnitt **Administratoranmeldeinformationen** wie folgt:

   * **Administratorbenutzername** : Geben Sie den Benutzernamen des Workday-Systemintegrationskontos mit angefügtem Mandantendomänennamen ein. Sollte etwa so aussehen: *benutzername\@contoso4*

   * **Administratorkennwort** : Geben Sie das Kennwort des Workday-Systemintegrationskontos ein.

   * **Mandanten-URL** : Geben Sie die URL des Workday-Webdienstendpunkts für Ihren Mandanten ein. Dieser Wert sollte wie folgt lauten: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources`. Dabei wird *contoso4* durch den Namen Ihres Mandanten und *wd3-impl* (bei Bedarf) durch die ordnungsgemäße Umgebungszeichenfolge ersetzt.

   * **Benachrichtigungs-E-Mail** : Geben Sie Ihre E-Mail-Adresse ein, und aktivieren Sie das Kontrollkästchen „E-Mail senden, wenn Fehler auftritt“.

   * Klicken Sie auf die Schaltfläche **Verbindung testen**. Wenn der Verbindungstest erfolgreich ist, klicken Sie oben auf die Schaltfläche **Speichern**. Falls nicht, überprüfen Sie, ob die Workday-URL und -Anmeldeinformationen in Workday gültig sind.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Teil 2: Konfigurieren von Writeback-Attributzuordnungen

In diesem Abschnitt werden Sie konfigurieren, wie die Writebackattribute von Azure AD zu Workday übertragen werden. 

1. Klicken Sie auf der Registerkarte „Bereitstellung“ unter **Zuordnungen** auf den Namen der Zuordnung.

2. Im Feld **Quellobjektbereich** können Sie optional filtern, welche Gruppen von Benutzern in Azure Active Directory Teil des Rückschreibens sein sollen. Die Standardoption ist „Alle Benutzer in Azure AD“.

3. Aktualisieren Sie im Abschnitt **Attributzuordnungen** die entsprechende ID, um das Attribut in Azure Active Directory anzugeben, in dem die Mitarbeiter-ID von Workday gespeichert ist. Eine gängige Methode für den Abgleich ist das Synchronisieren der Mitarbeiter-ID von Workday mit „extensionAttribute“ 1-15 in Azure AD und das anschließende Verwenden dieses Attributs in Azure AD, um die Benutzer wieder mit Workday abzugleichen.

4. In der Regel ordnen Sie das Azure AD-Attribut *userPrincipalName* dem Workday-Attribut *UserID* und das Azure AD-Attribut *mail* dem Workday-Attribut *EmailAddress* zu. 

     >[!div class="mx-imgBorder"]
     >![Azure portal](./media/workday-inbound-tutorial/workday-writeback-mapping.png)

5. Verwenden Sie den unten aufgeführten Leitfaden, um die Werte von Telefonnummernattributen aus Azure AD Workday zuzuordnen. 

     | Workday-Attribut Telefon | Erwarteter Wert | Leitfaden für die Zuordnung |
     |-------------------------|----------------|------------------|
     | WorkphoneLandlineIsPrimary | TRUE/FALSE | Konstante oder Ausdruckszuordnung, deren Ausgabe den Zeichenfolgenwert „true“ oder „false“ hat. |
     | WorkphoneLandlineCountryCodeName | [Aus drei Buchstaben bestehende ISO 3166-1-Landeskennzahl](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Konstante oder Ausdruckszuordnung, deren Ausgabe eine aus drei Buchstaben bestehende Landeskennzahl ist. |
     | WorkphoneLandlineCountryCodeNumber | [Internationale Landesvorwahl](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Konstante oder Ausdruckszuordnung, deren Ausgabe eine gültige Landeskennzahl (ohne das + Zeichen) ist. |
     | WorkphoneLandlineNumber | Vollständige Telefonnummer einschließlich der Vorwahl | Wird dem Attribut *telephoneNumber* zugeordnet. Verwenden Sie RegEx, um Leerzeichen, Klammern und Landeskennzahl zu entfernen. Ein Beispiel sehen Sie unten. |
     | WorkphoneLandlineExtension | Durchwahlnummer | Wenn *telephoneNumber* eine Durchwahl enthält, verwenden Sie RegEx, um den Wert zu extrahieren. |
     | WorkphoneMobileIsPrimary | TRUE/FALSE | Konstante Zuordnung oder Ausdruckszuordnung, deren Ausgabe den Zeichenfolgenwert „true“ oder „false“ hat. |
     | WorkphoneMobileCountryCodeName | [Aus drei Buchstaben bestehende ISO 3166-1-Landeskennzahl](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Konstante oder Ausdruckszuordnung, deren Ausgabe eine aus drei Buchstaben bestehende Landeskennzahl ist. |
     | WorkphoneMobileCountryCodeNumber | [Internationale Landesvorwahl](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Konstante oder Ausdruckszuordnung, deren Ausgabe eine gültige Landeskennzahl (ohne das + Zeichen) ist. |
     | WorkphoneMobileNumber | Vollständige Telefonnummer einschließlich der Vorwahl | Wird dem Attribut *mobile* zugeordnet. Verwenden Sie RegEx, um Leerzeichen, Klammern und Landeskennzahl zu entfernen. Ein Beispiel sehen Sie unten. |

     > [!NOTE]
     > Wenn Sie den Change_Work_Contact-Workday-Webdienst aufrufen, sendet Azure AD die folgenden konstanten Werte: <br>
     > * **Communication_Usage_Type_ID** wird auf die konstante Zeichenfolge „WORK“ festgelegt. <br>
     > * **Phone_Device_Type_ID** wird für Mobiltelefonnummern auf die konstante Zeichenfolge „Mobile“ und für Festnetztelefonnummern auf „Landline“ festgelegt. <br>
     > 
     > Wenn Ihr Workday-Mandant verschiedene Type_IDs verwendet, werden Fehler beim Rückschreiben auftreten. Um derartige Fehler zu vermeiden, können Sie den Workday-Task **Verweis-IDs verwalten** verwenden und die Type_IDs aktualisieren, damit diese mit den von Azure AD verwendeten Werten übereinstimmen. <br>
     >  

     **Verweise auf RegEx-Ausdrücke – Beispiel 1**

     Verwenden Sie den unten stehenden regulären Ausdruck, wenn die Telefonnummer in Azure AD unter Verwendung des für die Self Service-Kennwortzurücksetzung (SSPR) erforderlichen Formats festgelegt wird. <br>
     Beispiel: Wenn der Wert der Telefonnummer + 1 1112223333 ist, wird der RegEx-Ausdruck 1112223333 ausgegeben.

     ```C#
     Replace([telephoneNumber], , "\\+(?<isdCode>\\d* )(?<phoneNumber>\\d{10})", , "${phoneNumber}", , )
     ```

     **Verweise auf RegEx-Ausdrücke – Beispiel 2**

     Verwenden Sie den unten stehenden regulären Ausdruck, wenn die Telefonnummer in Azure AD unter Verwendung des Formats (XXX) XXX-XXXX festgelegt wird. <br>
     Beispiel: Wenn der Wert der Telefonnummer (111) 222-3333 ist, wird der RegEx-Ausdruck 1112223333 ausgegeben.

     ```C#
     Replace([mobile], , "[()\\s-]+", , "", , )
     ```

6. Klicken Sie oben im Abschnitt „Attributzuordnung“ auf **Speichern** , um Ihre Zuordnungen zu speichern.

## <a name="enable-and-launch-user-provisioning"></a>Aktivieren und Starten der Benutzerbereitstellung

Nachdem die Konfiguration der Workday-Bereitstellungs-App abgeschlossen ist, können Sie den Bereitstellungsdienst im Azure-Portal aktivieren.

> [!TIP]
> Beim Aktivieren des Bereitstellungsdiensts werden Bereitstellungsvorgänge für alle Benutzer im Bereich initiiert. Wenn Fehler in der Zuordnung oder Workday-Datenprobleme auftreten, kann der Bereitstellungsauftrag fehlschlagen und in den Quarantänezustand wechseln. Um dies zu vermeiden, empfehlen wir, den Filter **Quellobjektbereich** zu konfigurieren und Ihre Attributzuordnungen mit einigen Testbenutzern zu testen, bevor Sie die vollständige Synchronisierung für alle Benutzer starten. Sobald Sie sich vergewissert haben, dass die Zuordnungen funktionieren und Sie die gewünschten Ergebnisse erhalten, können Sie den Filter entweder entfernen oder schrittweise erweitern, um mehr Benutzer einzubinden.

1. Legen Sie auf der Registerkarte **Bereitstellung** die Einstellung **Bereitstellungsstatus** auf **Ein** fest.

1. Wählen Sie in der Dropdownliste **Bereich** die Option **Alle Benutzer und Gruppen synchronisieren** aus. Mit dieser Option schreibt die Rückschreibe-App zugeordnete Attribute aller Benutzer aus Azure AD in Workday zurück. Dies unterliegt den Bereichsregeln, die unter **Zuordnungen** -> **Quellobjektbereich** definiert wurden. 

   > [!div class="mx-imgBorder"]
   > ![Rückschreibe-Bereich auswählen](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > Die Bereitstellungs-App für Workday-Rückschreiben unterstützt nicht die Option **Nur zugewiesene Benutzer und Gruppen synchronisieren**.
 

2. Klicken Sie auf **Speichern**.

3. Dieser Vorgang startet die erste Synchronisierung, die abhängig von der Anzahl der Benutzer im Quellverzeichnis eine variable Anzahl von Stunden dauern kann. Sie können die Statusanzeige überprüfen, um den Fortschritt des Synchronisierungszyklus zu verfolgen. 

4. Im Azure-Portal können Sie sich auf der Registerkarte **Bereitstellungsprotokolle** jederzeit ansehen, welche Aktionen der Bereitstellungsdienst ausgeführt hat. In den Überwachungsprotokollen werden alle einzelnen vom Bereitstellungsdienst ausgeführten Synchronisierungsereignisse aufgelistet, z. B. welche Benutzer aus der Quelle importiert und in die Zielanwendung exportiert werden.  

5. Nach Abschluss der ersten Synchronisierung wird auf der Registerkarte **Bereitstellung** ein Zusammenfassungsbericht ausgegeben.

     > [!div class="mx-imgBorder"]
     > ![Statusanzeige für die Bereitstellung](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="known-issues-and-limitations"></a>Bekannte Probleme und Einschränkungen

* Die Rückschreibe-App verwendet einen vordefinierten Wert für die Parameter **Communication_Usage_Type_ID** und **Phone_Device_Type_ID**. Wenn Ihr Workday-Mandant einen anderen Wert für diese Attribute verwendet, kann der Rückschreibvorgang nicht ausgeführt werden. Eine vorgeschlagene Problemumgehung ist die Aktualisierung der „Type_IDs“ in Workday. 
* Wenn die Rückschreibe-App so konfiguriert wurde, dass sekundäre Telefonnummern aktualisiert werden, wird die vorhandene sekundäre Telefonnummer in Workday nicht ersetzt. Stattdessen wird dem Worker-Datensatz eine weitere sekundäre Telefonnummer hinzugefügt. Für dieses Verhalten gibt es keine Problemumgehung. 


## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
* [Lesen Sie, wie Sie das einmalige Anmelden zwischen Workday und Azure Active Directory konfigurieren.](workday-tutorial.md)
* [Erfahren Sie, wie Sie andere SaaS-Anwendungen in Azure Active Directory integrieren.](tutorial-list.md)
* [Weitere Informationen zum Exportieren und Importieren von Bereitstellungskonfigurationen](../app-provisioning/export-import-provisioning-configuration.md)


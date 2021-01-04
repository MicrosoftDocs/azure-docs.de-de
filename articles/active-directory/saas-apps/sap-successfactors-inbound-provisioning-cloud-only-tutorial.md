---
title: 'Tutorial: Konfigurieren der eingehenden Bereitstellung von SuccessFactors in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie die eingehende Bereitstellung von SuccessFactors in Azure AD konfigurieren.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.openlocfilehash: a62943c1a808424ded1a5e46ed115cda332bf7d5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020754"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning"></a>Tutorial: Konfigurieren der Benutzerbereitstellung von SAP SuccessFactors zu Azure AD
In diesem Tutorial werden die Schritte vorgestellt, die Sie zum Bereitstellen von Mitarbeiterdaten aus SuccessFactors Employee Central in Azure Active Directory ausführen müssen, wobei einige E-Mail-Adressen optional in SuccessFactors zurückgeschrieben werden. 

>[!NOTE]
>Verwenden Sie dieses Tutorial, wenn die Benutzer, die Sie aus SuccessFactors bereitstellen möchten, ausschließlich Cloudbenutzer sind und kein lokales Active Directory-Konto benötigen. Wenn die Benutzer nur ein lokales AD-Konto oder ein AD- und ein Azure AD-Konto benötigen, lesen Sie das Tutorial zum [Konfigurieren der Benutzerbereitstellung aus SAP SuccessFactors in Active Directory](sap-successfactors-inbound-provisioning-tutorial.md#overview). 

## <a name="overview"></a>Übersicht

Der [Azure Active Directory-Benutzerbereitstellungsdienst](../app-provisioning/user-provisioning.md) ist mit [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) integriert, um den Identitätslebenszyklus von Benutzern zu verwalten. 

Die vom Azure AD-Benutzerbereitstellungsdienst unterstützten SuccessFactors-Workflows zur Benutzerbereitstellung ermöglichen die Automatisierung der folgenden Szenarien im Personalwesen und bei der Verwaltung des Identity Lifecycle Managements:

* **Einstellung neuer Mitarbeiter:** Wenn in SuccessFactors ein neuer Mitarbeiter hinzugefügt wird, wird in Azure Active Directory und optional in Microsoft 365 sowie in [anderen, von Azure AD unterstützten SaaS-Anwendungen](../app-provisioning/user-provisioning.md) automatisch ein Benutzerkonto erstellt, und die E-Mail-Adresse wird in SuccessFactors zurückgeschrieben.

* **Aktualisierung von Mitarbeiterattributen und -profilen:** Wenn in SuccessFactors ein Mitarbeiterdatensatz aktualisiert wird (beispielsweise der Name, Titel oder Vorgesetzte), wird das entsprechende Benutzerkonto in Azure Active Directory und optional in Microsoft 365 und in [anderen, von Azure AD unterstützten SaaS-Anwendungen](../app-provisioning/user-provisioning.md) automatisch aktualisiert.

* **Kündigung von Mitarbeitern:** Wenn einem Mitarbeiter in SuccessFactors gekündigt wird, wird das entsprechende Benutzerkonto in Azure Active Directory und optional in Microsoft 365 und in [anderen, von Azure AD unterstützten SaaS-Anwendungen](../app-provisioning/user-provisioning.md) automatisch deaktiviert.

* **Wiedereinstellung von Mitarbeitern:** Wenn ein Mitarbeiter in SuccessFactors erneut eingestellt wird, kann sein altes Konto in Azure Active Directory und optional in Microsoft 365 und in [anderen, von Azure AD unterstützten SaaS-Anwendungen](../app-provisioning/user-provisioning.md) je nach Präferenz automatisch reaktiviert oder erneut bereitgestellt werden.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Für wen ist diese Benutzerbereitstellungslösung am besten geeignet?

Diese Lösung zur Benutzerbereitstellung von SuccessFactors in Azure Active Directory eignet sich ideal für:

* Organisationen, die eine vordefinierte, cloudbasierte Lösung für die SuccessFactors-Benutzerbereitstellung verwenden möchten

* Organisationen, bei denen Benutzer direkt aus SuccessFactors in Azure Active Directory bereitgestellt werden müssen

* Organisationen, bei denen Benutzer mithilfe von Daten bereitgestellt werden müssen, die aus [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) abgerufen werden

* Organisationen, die Microsoft 365 für E-Mail verwenden

## <a name="solution-architecture"></a>Lösungsarchitektur

In diesem Abschnitt wird die Lösungsarchitektur der End-to-End-Benutzerbereitstellung für ausschließliche Cloudbenutzer beschrieben. Es gibt zwei zugehörige Flows:

* **Autoritativer Personaldatenfluss – von SuccessFactors in ein Azure Active Directory-Verzeichnis:** In diesem Flow treten mitarbeiterbezogene Ereignisse (z. B. Neueinstellungen, Wechsel, Kündigungen) zuerst in der Cloud in SuccessFactors Employee Central auf und werden dann in ein Azure Active Directory-Verzeichnis übertragen. Abhängig vom Ereignis kann dies dann in Azure AD zu Erstellungs-, Aktualisierungs-, Aktivierungs- oder Deaktivierungsvorgängen führen.
* **E-Mail-Rückschreibeflow – vom lokalen Active Directory-Verzeichnis zu SuccessFactors:** Nach Abschluss der Kontoerstellung in Azure Active Directory kann der Wert des E-Mail-Attributs oder der in Azure AD erstellte UPN nach SuccessFactors zurückgeschrieben werden.

  ![Übersicht](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>End-to-End-Benutzerdatenfluss

1. Das Team der Personalabteilung führt Mitarbeitertransaktionen (Einstellungen/Wechsel/Kündigungen) in SuccessFactors Employee Central aus.
2. Der Azure AD-Bereitstellungsdienst führt geplante Synchronisierungen von Identitäten aus SuccessFactors EC aus und ermittelt Änderungen, die für eine Synchronisierung mit dem lokalen Active Directory verarbeitet werden müssen.
3. Der Azure AD-Bereitstellungsdienst ermittelt die Änderung und ruft den entsprechenden Vorgang zum Erstellen, Aktualisieren, Aktivieren oder Deaktivieren für den Benutzer in Azure AD auf.
4. Wenn die [App für das Rückschreiben nach SuccessFactors](sap-successfactors-writeback-tutorial.md) konfiguriert ist, wird die E-Mail-Adresse des Benutzers aus Azure AD abgerufen. 
5. Der Azure AD-Bereitstellungsdienst schreibt das E-Mail-Attribut basierend auf dem verwendeten Übereinstimmungsattribut nach SuccessFactors zurück.

## <a name="planning-your-deployment"></a>Planen der Bereitstellung

Das Konfigurieren einer cloudbasierten Benutzerbereitstellung für die Personalabteilung aus SuccessFactors in Azure AD erfordert eine ausführliche Planung, die unter anderem die folgenden Aspekte abdecken sollte:

* Ermittlung der Übereinstimmungs-ID 
* Attributzuordnung
* Attributtransformation 
* Bereichsfilter

Ausführliche Anleitungen zu diesen Themen finden Sie unter [Planen der HR-Cloudbereitstellung](../app-provisioning/plan-cloud-hr-provision.md). Weitere Informationen zu den unterstützten Entitäten, Verarbeitungsdetails und zum Anpassen der Integration für verschiedene HR-Szenarien finden Sie im [Referenzhandbuch zur Integration von SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md). 

## <a name="configuring-successfactors-for-the-integration"></a>Konfigurieren von SuccessFactors für die Integration

Eine allgemeine Anforderung für alle SuccessFactors-Bereitstellungsconnectors besteht darin, dass sie Anmeldeinformationen eines SuccessFactors-Kontos mit den erforderlichen Berechtigungen zum Aufrufen der OData-APIs von SuccessFactors benötigen. In diesem Abschnitt werden die Schritte zum Erstellen des Dienstkontos in SuccessFactors und zum Erteilen der benötigten Berechtigungen beschrieben. 

* [Erstellen/Ermitteln des API-Benutzerkontos in SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Erstellen einer API-Berechtigungsrolle](#create-an-api-permissions-role)
* [Erstellen einer Berechtigungsgruppe für den API-Benutzer](#create-a-permission-group-for-the-api-user)
* [Erteilen der Berechtigungsrolle für die Berechtigungsgruppe](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Erstellen/Ermitteln des API-Benutzerkontos in SuccessFactors
Wenden Sie sich an das Administratorteam von SuccessFactors oder Ihren Implementierungspartner, um ein Benutzerkonto in SuccessFactors zu erstellen oder zu bestimmen, das zum Aufrufen der OData-APIs verwendet wird. Die Anmeldeinformationen (Benutzername und Kennwort) dieses Kontos sind für das Konfigurieren der Bereitstellungs-Apps in Azure AD erforderlich. 

### <a name="create-an-api-permissions-role"></a>Erstellen einer API-Berechtigungsrolle

* Melden Sie sich bei SAP SuccessFactors mit einem Benutzerkonto an, das Zugriff auf das Admin Center hat.
* Suchen Sie nach *Manage Permission Roles* (Berechtigungsrollen verwalten), und wählen Sie dann in den Suchergebnissen **Manage Permission Roles** (Berechtigungsrollen verwalten) aus.
  ![Verwalten von Berechtigungsrollen](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Klicken Sie in der Liste „Permission Role“ (Berechtigungsrolle) auf **Create New** (Neue erstellen).
  > [!div class="mx-imgBorder"]
  > ![Erstellen einer neuen Berechtigungsrolle](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Fügen Sie einen **Rollennamen** und eine **Beschreibung** für die neue Berechtigungsrolle hinzu. Der Name und die Beschreibung sollten darauf hindeuten, dass die Rolle für Berechtigungen zur API-Verwendung vorgesehen ist.
  > [!div class="mx-imgBorder"]
  > ![Details zur Berechtigungsrolle](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Klicken Sie unter den Berechtigungseinstellungen auf **Permission...** (Berechtigung), scrollen Sie in der Berechtigungsliste nach unten, und klicken Sie auf **Manage Integration Tools** (Integrationstools verwalten). Aktivieren Sie das Kontrollkästchen für **Allow Admin to Access to OData API through Basic Authentication** (Administratoren den Zugriff auf OData-API über die Standardauthentifizierung erlauben).
  > [!div class="mx-imgBorder"]
  > ![Verwalten von Integrationstools](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Scrollen Sie in demselben Feld nach unten, und wählen Sie **Employee Central API** aus. Fügen Sie wie unten gezeigt Berechtigungen für das Lesen und Bearbeiten über die OData-API hinzu. Wählen Sie die Bearbeitungsoption aus, wenn Sie dasselbe Konto für das Rückschreiben in ein SuccessFactors-Szenario verwenden möchten. 
  > [!div class="mx-imgBorder"]
  > ![Lese-/Schreibberechtigungen](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Klicken Sie auf **Done** (Fertig). Klicken Sie auf **Änderungen speichern**.

### <a name="create-a-permission-group-for-the-api-user"></a>Erstellen einer Berechtigungsgruppe für den API-Benutzer

* Suchen Sie im SuccessFactors Admin Center nach *Manage Permission Groups* (Berechtigungsgruppen verwalten), und wählen Sie in den Suchergebnissen dann **Manage Permission Groups** (Berechtigungsgruppen verwalten) aus.
  > [!div class="mx-imgBorder"]
  > ![Verwalten von Berechtigungsgruppen](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* Klicken Sie im Fenster „Manage Permission Groups“ (Berechtigungsgruppen verwalten) auf **Create New** (Neue erstellen).
  > [!div class="mx-imgBorder"]
  > ![Hinzufügen einer neuen Gruppe](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Fügen Sie einen Gruppennamen für die neue Gruppe hinzu. Im Gruppennamen sollte angegeben werden, dass die Gruppe für API-Benutzer vorgesehen ist.
  > [!div class="mx-imgBorder"]
  > ![Name der Berechtigungsgruppe](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Fügen Sie der Gruppe Mitglieder hinzu. Beispielsweise können Sie im Dropdownmenü „People Pool“ (Personenpool) die Option **Username** (Benutzername) auswählen und dann den Benutzernamen des API-Kontos eingeben, das für die Integration verwendet werden soll. 
  > [!div class="mx-imgBorder"]
  > ![Add group members](./media/sap-successfactors-inbound-provisioning/add-group-members.png) (Hinzufügen von Gruppenmitgliedern)
* Klicken Sie auf **Done** (Fertig), um die Erstellung der Berechtigungsgruppe abzuschließen.

### <a name="grant-permission-role-to-the-permission-group"></a>Erteilen der Berechtigungsrolle für die Berechtigungsgruppe

* Suchen Sie im SuccessFactors Admin Center nach *Manage Permission Roles* (Berechtigungsrollen verwalten), und wählen Sie in den Suchergebnissen dann **Manage Permission Roles** (Berechtigungsrollen verwalten) aus.
* Wählen Sie in der Liste **Permission Role** (Berechtigungsrolle) die Rolle aus, die Sie für die Berechtigungen zur API-Verwendung erstellt haben.
* Klicken Sie unter **Grant this role to...** (Rolle erteilen für) auf die Schaltfläche **Add...** (Hinzufügen).
* Wählen Sie im Dropdownmenü die Option **Permission Group...** (Berechtigungsgruppe) aus, und klicken Sie dann auf **Select...** (Auswählen), um das Fenster „Groups“ (Gruppen) zu öffnen. Darin können Sie die oben erstellte Gruppe suchen und auswählen. 
  > [!div class="mx-imgBorder"]
  > ![Hinzufügen der Berechtigungsgruppe](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Überprüfen Sie die erteilte Berechtigungsrolle für die Berechtigungsgruppe. 
  > [!div class="mx-imgBorder"]
  > ![Details zu Berechtigungsrolle und -gruppe](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Klicken Sie auf **Änderungen speichern**.

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Konfigurieren der Benutzerbereitstellung aus SuccessFactors in Azure AD

In diesem Abschnitt werden die Schritte für die Bereitstellung von Benutzerkonten aus SuccessFactors in Azure AD erläutert.

* [Hinzufügen der Bereitstellungsconnector-App und Konfigurieren der Konnektivität mit SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Konfigurieren von Attributzuordnungen](#part-2-configure-attribute-mappings)
* [Aktivieren und Starten der Benutzerbereitstellung](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Teil 1: Hinzufügen der Bereitstellungsconnector-App und Konfigurieren der Konnektivität mit SuccessFactors

**So konfigurieren Sie die Bereitstellung aus SuccessFactors in Azure AD**

1. Besuchen Sie <https://portal.azure.com>.

2. Wählen Sie auf der linken Navigationsleiste **Azure Active Directory** aus.

3. Klicken Sie auf **Unternehmensanwendungen** und dann auf **Alle Anwendungen**.

4. Klicken Sie auf **Anwendung hinzufügen**, und wählen Sie die Kategorie **Alle** aus.

5. Suchen Sie nach **SuccessFactors to Azure Active Directory User Provisioning**, und fügen Sie die App aus dem Katalog hinzu.

6. Sobald die App hinzugefügt wurde und der Bildschirm mit den App-Details angezeigt wird, wählen Sie **Bereitstellung** aus.

7. Ändern Sie den **Modus** **Bereitstellung** in **Automatisch**.

8. Vervollständigen Sie den Abschnitt **Administratoranmeldeinformationen** wie folgt:

   * **Administratorbenutzername**: Geben Sie den Benutzernamen des Benutzerkontos für die SuccessFactors-API ein, und fügen Sie die Unternehmens-ID an. Verwenden Sie das Format **Benutzername\@Unternehmens-ID**.

   * **Administratorkennwort**: Geben Sie das Kennwort des Benutzerkontos für die SuccessFactors-API ein. 

   * **Mandanten-URL**: Geben Sie den Namen des Dienstendpunkts für die SuccessFactors-OData-API ein. Geben Sie nur den Hostnamen des Servers ohne „http“ oder „https“ ein. Dieser Wert sollte das folgende Format aufweisen: **API-Servername.successfactors.com**.

   * **Benachrichtigungs-E-Mail**: Geben Sie Ihre E-Mail-Adresse ein, und aktivieren Sie das Kontrollkästchen „E-Mail senden, wenn Fehler auftritt“.
    > [!NOTE]
    > Der Azure AD-Bereitstellungsdienst sendet eine E-Mail-Benachrichtigung, wenn der Bereitstellungsauftrag in den Zustand [Quarantäne](../app-provisioning/application-provisioning-quarantine-status.md) wechselt.

   * Klicken Sie auf die Schaltfläche **Verbindung testen**. Wenn der Verbindungstest erfolgreich ist, klicken Sie oben auf die Schaltfläche **Speichern**. Falls nicht, sollten Sie überprüfen, ob die SuccessFactors-Anmeldeinformationen und die URL gültig sind.
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * Nachdem die Anmeldeinformationen erfolgreich gespeichert wurden, wird im Abschnitt **Zuordnungen** die Standardzuordnung **SuccessFactors-Benutzer mit Azure Active Directory synchronisieren** angezeigt.

### <a name="part-2-configure-attribute-mappings"></a>Teil 2: Konfigurieren von Attributzuordnungen

In diesem Abschnitt konfigurieren Sie den Fluss von Benutzerdaten aus SuccessFactors in Active Directory.

1. Klicken Sie auf der Registerkarte „Bereitstellung“ unter **Zuordnungen** auf **SuccessFactors-Benutzer in Azure Active Directory synchronisieren**.

1. Im Feld **Quellobjektbereich** können Sie die Benutzergruppen in SuccessFactors für die Bereitstellung in Azure AD auswählen, indem Sie verschiedene attributbasierte Filter definieren. Der Standardbereich ist „Alle Benutzer in SuccessFactors“. Beispielfilter:

   * Beispiel: Auswählen der Benutzer mit personIdExternal von 1000000 bis 1999999

      * Attribut: personIdExternal

      * Operator: REGEX Match

      * Wert: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Beispiel: Nur Mitarbeiter und keine vorübergehend Beschäftigten

      * Attribut: EmployeeID

      * Operator: IS NOT NULL

   > [!TIP]
   > Wenn Sie die Bereitstellungs-App zum ersten Mal konfigurieren, müssen Sie Ihre Attributzuordnungen und Ausdrücke testen und überprüfen, um sicherzustellen, dass sie damit das gewünschte Ergebnis erzielen. Microsoft empfiehlt, die Bereichsfilter unter **Quellobjektbereich** zu verwenden, um Ihre Zuordnungen mit einigen Testbenutzern von SuccessFactors zu testen. Sobald Sie sich vergewissert haben, dass die Zuordnungen funktionieren, können Sie den Filter entweder entfernen oder schrittweise erweitern, um mehr Benutzer einzubinden.

   > [!CAUTION] 
   > Beim Standardverhalten des Bereitstellungsmoduls werden Benutzer deaktiviert/gelöscht, die sich außerhalb des gültigen Bereichs befinden. Dies ist bei Ihrer Integration von SuccessFactors mit Azure AD möglicherweise nicht wünschenswert. Informationen zum Außerkraftsetzen dieses Standardverhaltens finden Sie im Artikel [Überspringen des Löschens von Benutzerkonten außerhalb des gültigen Bereichs](../app-provisioning/skip-out-of-scope-deletions.md).
  
1. Im Feld **Zielobjektaktionen** können Sie global filtern, welche Aktionen auf Active Directory angewendet werden. **Erstellen** und **Aktualisieren** erfolgen am häufigsten.

1. Im Abschnitt **Attributzuordnungen** können Sie definieren, wie einzelne SuccessFactors-Attribute Active Directory-Attributen zugeordnet werden.

  >[!NOTE]
  >Eine vollständige Liste der SuccessFactors-Attribute, die von der Anwendung unterstützt werden, finden Sie in der [SuccessFactors-Attributreferenz](../app-provisioning/sap-successfactors-attribute-reference.md).


1. Klicken Sie auf eine vorhandene Attributzuordnung, um sie zu aktualisieren. Oder klicken Sie am unteren Bildschirmrand auf **Neue Zuordnung hinzufügen**, um neue Zuordnungen hinzuzufügen. Eine einzelne Attributzuordnung unterstützt die folgenden Eigenschaften:

      * **Zuordnungstyp**

         * **Direkt:** schreibt den Wert des SuccessFactors-Attributs unverändert in das AD-Attribut.

         * **Konstant**: Schreibt einen statischen, konstanten Zeichenfolgenwert in das AD-Attribut.

         * **Ausdruck:** ermöglicht das Schreiben eines benutzerdefinierten Werts basierend auf einem oder mehreren SuccessFactors-Attributen in das AD-Attribut. [Weitere Informationen finden Sie im Artikel zu Ausdrücken](../app-provisioning/functions-for-customizing-application-data.md).

      * **Quellattribut:** das Benutzerattribut aus SuccessFactors

      * **Standardwert**: Optional. Wenn das Quellattribut einen leeren Wert aufweist, wird von der Zuordnung stattdessen dieser Wert geschrieben.
            Die meisten Konfigurationen sehen vor, dieses Feld leer zu lassen.

      * **Zielattribut**: Das Benutzerattribut in Active Directory.

      * **Objekte mit diesem Attribut abgleichen:** gibt an, ob diese Zuordnung zum eindeutigen Bestimmen von Benutzern zwischen SuccessFactors und Active Directory verwendet werden soll. Dieser Wert wird meist im Feld „Worker-ID“ für SuccessFactors festgelegt, das in der Regel einem der Mitarbeiter-ID-Attribute in Active Directory zugeordnet ist.

      * **Rangfolge für Abgleich**: Es können mehrere Attribute für den Abgleich festgelegt werden. Falls mehrere vorhanden sind, werden sie entsprechend der in diesem Feld festgelegten Reihenfolge ausgewertet. Sobald eine Übereinstimmung gefunden wird, werden keine weiteren Attribute für den Abgleich mehr ausgewertet.

      * **Diese Zuordnung anwenden**

         * **Immer**: Wenden Sie diese Zuordnung sowohl bei der Aktion zum Erstellen eines Benutzers als auch bei der zum Aktualisieren eines Benutzers an.

         * **Nur während der Erstellung**: Wenden Sie diese Zuordnung nur bei der Aktion zum Erstellen eines Benutzers an.

1. Klicken Sie oben im Abschnitt „Attributzuordnung“ auf **Speichern**, um Ihre Zuordnungen zu speichern.

Sobald Ihre Attributzuordnungskonfiguration abgeschlossen ist, können Sie jetzt [den Benutzerbereitstellungsdienst aktivieren und starten](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Aktivieren und Starten der Benutzerbereitstellung

Nachdem die Konfiguration der SuccessFactors-Bereitstellungs-App abgeschlossen ist, können Sie den Bereitstellungsdienst im Azure-Portal aktivieren.

> [!TIP]
> Beim Aktivieren des Bereitstellungsdiensts werden Bereitstellungsvorgänge für alle Benutzer im Bereich initiiert. Wenn Fehler in der Zuordnung oder Workday-Datenprobleme auftreten, kann der Bereitstellungsauftrag fehlschlagen und in den Quarantänezustand wechseln. Um dies zu vermeiden, empfehlen wir, den Filter **Quellobjektbereich** zu konfigurieren und Ihre Attributzuordnungen mit einigen Testbenutzern zu testen, bevor Sie die vollständige Synchronisierung für alle Benutzer starten. Sobald Sie sich vergewissert haben, dass die Zuordnungen funktionieren und Sie die gewünschten Ergebnisse erhalten, können Sie den Filter entweder entfernen oder schrittweise erweitern, um mehr Benutzer einzubinden.

1. Legen Sie auf der Registerkarte **Bereitstellung** die Einstellung **Bereitstellungsstatus** auf **Ein** fest.

2. Klicken Sie auf **Speichern**.

3. Dieser Vorgang startet die erste Synchronisierung, die abhängig von der Anzahl von Benutzern im SuccessFactors-Mandanten mehrere Stunden dauern kann. Sie können die Statusanzeige überprüfen, um den Fortschritt des Synchronisierungszyklus zu verfolgen. 

4. Im Azure-Portal können Sie sich auf der Registerkarte **Überwachungsprotokolle** jederzeit ansehen, welche Aktionen der Bereitstellungsdienst ausgeführt hat. Die Überwachungsprotokolle enthalten alle einzelnen Synchronisierungsereignisse des Bereitstellungsdiensts – beispielsweise, welche Benutzer in Workday gelesen und anschließend Active Directory hinzugefügt oder dort aktualisiert wurden. 

5. Nach Abschluss der ersten Synchronisierung wird auf der Registerkarte **Bereitstellung** ein Überwachungszusammenfassungsbericht ausgegeben:

   > [!div class="mx-imgBorder"]
   > ![Statusanzeige für die Bereitstellung](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu unterstützten SuccessFactors-Attributen für die eingehende Bereitstellung](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Weitere Informationen zum Konfigurieren des E-Mail-Rückschreibens an SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
* [Weitere Informationen zum Konfigurieren des einmaligen Anmeldens zwischen SuccessFactors und Azure Active Directory](successfactors-tutorial.md)
* [Erfahren Sie, wie Sie andere SaaS-Anwendungen in Azure Active Directory integrieren.](tutorial-list.md)
* [Weitere Informationen zum Exportieren und Importieren von Bereitstellungskonfigurationen](../app-provisioning/export-import-provisioning-configuration.md)
---
title: 'Tutorial: Konfigurieren von Workday für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Workday konfigurieren.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: a34881901fd8642fff9ac37512cd2ef260ad9d1c
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954214"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Workday für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte veranschaulicht, die Sie ausführen müssen, um Workerprofile aus Workday in einer lokalen Active Directory-Instanz (AD) bereitzustellen.

>[!NOTE]
>Verwenden Sie dieses Tutorial, wenn die Benutzer, die Sie aus Workday bereitstellen möchten, ein lokales AD-Konto und ein Azure AD-Konto benötigen. 
>* Falls die Benutzer aus Workday nur Azure AD-Konten benötigen (reine Cloudbenutzer), helfen Ihnen die Informationen im Tutorial zur [Konfiguration von Workday für Azure AD](workday-inbound-cloud-only-tutorial.md) weiter. 
>* Informationen zum Konfigurieren des Rückschreibens von Attributen, z. B. E-Mail-Adresse, Benutzername und Telefonnummer, für Workday finden Sie im Tutorial zur [Konfiguration des Rückschreibens für Workday](workday-writeback-tutorial.md).

## <a name="overview"></a>Übersicht

Der [Azure Active Directory-Benutzerbereitstellungsdienst](../app-provisioning/user-provisioning.md) ist zum Bereitstellen von Benutzerkonten mit der [Workday Human Resources-API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) integriert. Die vom Azure AD-Benutzerbereitstellungsdienst unterstützten Workday-Workflows zur Benutzerbereitstellung ermöglichen die Automatisierung der folgenden Szenarien im Personalwesen und bei der Verwaltung des Lebenszyklus von Identitäten:

* **Einstellung neuer Mitarbeiter:** Wenn Workday ein neuer Mitarbeiter hinzugefügt wird, wird in Active Directory, in Azure Active Directory und optional in Microsoft 365 sowie in [anderen von Azure AD unterstützten SaaS-Anwendungen](../app-provisioning/user-provisioning.md) automatisch ein Benutzerkonto erstellt. Die von der IT-Abteilung verwalteten Kontaktinformationen werden dabei in Workday zurückgeschrieben.

* **Aktualisierungen von Mitarbeiterattributen und -profil:** Wenn ein Mitarbeiterdatensatz in Workday aktualisiert wird (etwa der Name, Titel oder Vorgesetzte), wird das entsprechende Benutzerkonto in Active Directory, in Azure Active Directory und optional in Microsoft 365 und in [anderen von Azure AD unterstützten SaaS-Anwendungen](../app-provisioning/user-provisioning.md) automatisch aktualisiert.

* **Kündigungen von Mitarbeitern:** Wenn einem Mitarbeiter in Workday gekündigt wird, wird das entsprechende Benutzerkonto in Active Directory, in Azure Active Directory und optional in Microsoft 365 und in [anderen von Azure AD unterstützten SaaS-Anwendungen](../app-provisioning/user-provisioning.md) automatisch deaktiviert.

* **Erneute Einstellung von Mitarbeitern:** Wenn ein Mitarbeiter in Workday erneut eingestellt wird, kann sein altes Konto in Active Directory, in Azure Active Directory und optional in Microsoft 365 und in [anderen von Azure AD unterstützten SaaS-Anwendungen](../app-provisioning/user-provisioning.md) automatisch reaktiviert oder erneut bereitgestellt werden (je nachdem, was Sie bevorzugen).

### <a name="whats-new"></a>Neues
In diesem Abschnitt werden die neuesten Verbesserungen in Bezug auf die Workday-Integration beschrieben. Eine vollständige Liste mit den Updates, geplanten Änderungen und Archiven finden Sie auf der Seite [Neuerungen in Azure Active Directory](../fundamentals/whats-new.md). 

* **Okt. 2020: Bedarfsgesteuerte Bereitstellung für Workday aktiviert:** Per [bedarfsgesteuerter Bereitstellung](../app-provisioning/provision-on-demand.md) können Sie jetzt die End-to-End-Bereitstellung für ein spezifisches Benutzerprofil in Workday testen, um Ihre Attributzuordnung und Ausdruckslogik zu überprüfen.   

* **Mai 2020: Rückschreiben von Telefonnummern in Workday:** Zusätzlich zu E-Mail-Adresse und Benutzername können Sie das Rückschreiben nun für geschäftliche und Mobiltelefonnummern aus Azure AD in Workday durchführen. Weitere Informationen finden Sie im [Tutorial zur App für das Rückschreiben](workday-writeback-tutorial.md).

* **April 2020: Unterstützung der aktuellen Version der Workday Web Services-API (WWS):** Zweimal pro Jahr (im März und September) werden von Workday Updates mit vielen Features bereitgestellt, die Ihnen dabei helfen, Ihre Geschäftsziele zu erreichen und die sich ändernden Anforderungen in Bezug auf die Mitarbeiterzahl zu erfüllen. Damit Sie bei den neuen Features von Workday auf dem Laufenden bleiben, können Sie jetzt direkt die WWS-API-Version angeben, die Sie in der Verbindungs-URL nutzen möchten. Ausführliche Informationen zum Angeben der Workday-API-Version finden Sie im Abschnitt zur [Konfiguration der Workday-Konnektivität](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory). 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Für wen ist diese Benutzerbereitstellungslösung am besten geeignet?

Diese Workday-Benutzerbereitstellungslösung eignet sich ideal für:

* Organisationen, die eine vorgefertigte, cloudbasierte Lösung für die Workday-Benutzerbereitstellung verwenden möchten

* Organisationen, bei denen Benutzer direkt aus Workday in Active Directory oder Azure Active Directory bereitgestellt werden müssen

* Organisationen, bei denen Benutzer mithilfe von Daten bereitgestellt werden müssen, die aus dem HCM-Modul von Workday abgerufen werden (siehe [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organisationen, bei denen Benutzer beim Beitreten, Verschieben und Verlassen nur auf Grundlage von Änderungsinformationen, die im HCM-Modul von Workday erkannt werden, mit einer oder mehreren Active Directory-Gesamtstrukturen, -Domänen und -Organisationseinheiten synchronisiert werden müssen (siehe [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organisationen, die Microsoft 365 für E-Mails verwenden

## <a name="solution-architecture"></a>Lösungsarchitektur

In diesem Abschnitt wird die Lösungsarchitektur der End-to-End-Benutzerbereitstellung für häufige Hybridumgebungen beschrieben. Es gibt zwei zugehörige Flows:

* **Autoritativer Personaldatenfluss – von Workday in ein lokales Active Directory-Verzeichnis**: In diesem Flow treten mitarbeiterbezogene Ereignisse (z.B. Neueinstellungen, Wechsel, Kündigungen) zuerst im Cloud-HR-Mandanten von Workday auf und werden dann über Azure AD und den Bereitstellungs-Agent in ein lokales Active Directory-Verzeichnis übertragen. Abhängig vom Ereignis kann dies dann in Active Directory zu Erstellungs-, Aktualisierungs-, Aktivierungs- oder Deaktivierungsvorgängen führen.
* **Rückschreibedatenfluss – vom lokalen Active Directory-Verzeichnis zu Workday**: Nach Abschluss der Kontoerstellung in Active Directory wird das Konto über Azure AD Connect mit Azure AD synchronisiert. Anschließend kann für Informationen wie E-Mail-Adresse, Benutzername und Telefonnummer das Rückschreiben in Workday durchgeführt werden.

![Übersicht](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>End-to-End-Benutzerdatenfluss

1. Das Team der Personalabteilung führt Mitarbeitertransaktionen (Einstellungen/Wechsel/Kündigungen) in Workday HCM aus.
2. Der Azure AD-Bereitstellungsdienst führt geplante Synchronisierungen von Identitäten aus Workday HR aus und ermittelt Änderungen, die für eine Synchronisierung mit dem lokalen Active Directory verarbeitet werden müssen.
3. Der Azure AD-Bereitstellungsdienst ruft den lokalen Azure AD Connect-Bereitstellungs-Agent mit einer Anforderungsnutzlast auf, die die Erstellungs-, Aktualisierungs-, Aktivierungs- oder Deaktivierungsvorgänge für das AD-Konto enthält.
4. Der Azure AD Connect-Bereitstellungs-Agent verwendet ein Dienstkonto zum Hinzufügen/Aktualisieren von AD-Kontodaten.
5. Die Azure AD Connect-/AAD Sync-Engine führt eine Deltasynchronisierung aus, um Updates in Active Directory zu pullen.
6. Die Active Directory-Updates werden mit Azure Active Directory synchronisiert.
7. Wenn die App für das [Rückschreiben in Workday](workday-writeback-tutorial.md) konfiguriert wurde, werden damit Attribute wie E-Mail-Adresse, Benutzername und Telefonnummer in Workday zurückgeschrieben.

## <a name="planning-your-deployment"></a>Planen der Bereitstellung

Für die Konfiguration der Benutzerbereitstellung von Workday zu Active Directory ist eine umfassende Planung erforderlich, bei der die folgenden unterschiedlichen Aspekte berücksichtigt werden:
* Einrichtung des Azure AD Connect-Bereitstellungs-Agents 
* Anzahl von Apps, die für die Benutzerbereitstellung von Workday zu AD bereitgestellt werden müssen
* Richtige Auswahl von passendem Bezeichner, Attributzuordnung, Transformation und Bereichsfiltern

Ausführliche Anleitungen und Informationen zu den empfohlenen bewährten Methoden finden Sie unter [Planen der HR-Cloudbereitstellung](../app-provisioning/plan-cloud-hr-provision.md). 

## <a name="configure-integration-system-user-in-workday"></a>Konfigurieren eines Integrationssystembenutzers in Workday

Eine häufige Anforderung aller Workday-Bereitstellungsconnectors ist, dass sie die Anmeldeinformationen eines Workday-Integrationssystembenutzers benötigen, um sich mit der Workday Human Resources-API zu verbinden. In diesem Abschnitt wird das Erstellen eines Integrationssystembenutzers in Workday beschrieben. Hier finden Sie folgende Themen:

* [Erstellen eines Integrationssystembenutzers](#creating-an-integration-system-user)
* [Erstellen einer Integrationssicherheitsgruppe](#creating-an-integration-security-group)
* [Konfigurieren der Berechtigungen der Domänensicherheitsrichtlinie](#configuring-domain-security-policy-permissions)
* [Konfigurieren von Sicherheitsrichtlinienberechtigungen für Geschäftsprozesse](#configuring-business-process-security-policy-permissions)
* [Aktivieren von Sicherheitsrichtlinienänderungen](#activating-security-policy-changes)

> [!NOTE]
> Sie können diesen Schritt auslassen und stattdessen ein globales Workday-Administratorkonto als Systemintegrationskonto nutzen. Diese Vorgehensweise ist für Demos einwandfrei, wird jedoch für Produktionsbereitstellungen nicht empfohlen.

### <a name="creating-an-integration-system-user"></a>Erstellen eines Integrationssystembenutzers

**So erstellen Sie einen Integrationssystembenutzer**

1. Melden Sie sich mithilfe eines Administratorkontos bei Ihrem Workday-Mandanten an. Geben Sie in der **Workday-Anwendung** die Suchzeichenfolge „Benutzer erstellen“ in das Suchfeld ein, und klicken Sie dann auf den Link **Create Integration System User** (Integrationssystembenutzer erstellen).

   >[!div class="mx-imgBorder"] 
   >![Benutzer erstellen](./media/workday-inbound-tutorial/wd_isu_01.png "Benutzer erstellen")
2. Führen Sie die Aufgabe **Integrationssystembenutzer erstellen** aus, indem Sie einen Benutzernamen und ein Kennwort für einen neuen Integrationssystembenutzer angeben.  

   * Lassen Sie das Kontrollkästchen **Bei der nächsten Anmeldung neues Kennwort anfordern** deaktiviert. Dieser Benutzer meldet sich programmgesteuert an.
   * Übernehmen Sie für **Sitzungstimeout in Minuten** den Standardwert 0. Diese Einstellung verhindert, dass Sitzungen des Benutzers vorzeitig beendet werden.
   * Wählen Sie die Option **Do Not Allow UI Sessions** (Keine Sitzungen mit Benutzeroberfläche zulassen) aus. Sie bietet zusätzliche Sicherheit, da sie verhindert, dass sich ein Benutzer mit dem Kennwort für das Integrationssystem bei Workday anmeldet.

   > [!div class="mx-imgBorder"]
   > ![Integrationssystembenutzer erstellen](./media/workday-inbound-tutorial/wd_isu_02.png "Integrationssystembenutzer erstellen")

### <a name="creating-an-integration-security-group"></a>Erstellen einer Integrationssicherheitsgruppe

In diesem Schritt erstellen Sie eine uneingeschränkte oder eingeschränkte Sicherheitsgruppe für das Integrationssystem in Workday und ordnen den Integrationssystembenutzer, den Sie im vorherigen Schritt erstellt haben, dieser Gruppe zu.

**So erstellen Sie eine Sicherheitsgruppe**

1. Geben Sie „Sicherheitsgruppe erstellen“ in das Suchfeld ein, und klicken Sie dann auf **Sicherheitsgruppe erstellen**.

   > [!div class="mx-imgBorder"]
   > ![Der Screenshot zeigt „Sicherheitsgruppe erstellen“, eingegeben im Suchfeld, und die in den Suchergebnissen angezeigte Aufgabe „Sicherheitsgruppe erstellen“.](./media/workday-inbound-tutorial/wd_isu_03.png)
2. Führen Sie die Aufgabe **Sicherheitsgruppe erstellen** aus. 

   * In Workday gibt es zwei Typen von Sicherheitsgruppen:
     * **Uneingeschränkt:** Alle Mitglieder der Sicherheitsgruppe können auf alle Dateninstanzen zugreifen, die durch die Sicherheitsgruppe gesichert sind.
     * **Eingeschränkt:** Alle Mitglieder der Sicherheitsgruppe haben kontextbezogene Zugriff auf eine Teilmenge der Dateninstanzen (Zeilen), auf die die Sicherheitsgruppe zugreifen kann.
   * Bitte wenden Sie sich an Ihren Workday-Integrationspartner, um den geeigneten Sicherheitsgruppentyp für die Integration auszuwählen.
   * Sobald Sie den Gruppentyp kennen, wählen Sie **Integrationssystem-Sicherheitsgruppe (Uneingeschränkt)** oder **Integrationssystem-Sicherheitsgruppe (Eingeschränkt)** aus der Dropdownliste **Typ der Mandantensicherheitsgruppe**.

     > [!div class="mx-imgBorder"]
     >![Sicherheitsgruppe erstellen](./media/workday-inbound-tutorial/wd_isu_04.png "Sicherheitsgruppe erstellen")

3. Nach dem Erstellen der Sicherheitsgruppe wird eine Seite angezeigt, auf der Sie der Sicherheitsgruppe Mitglieder zuweisen können. Fügen Sie den im vorherigen Schritt erstellten neuen Benutzer des Integrationssystems zu dieser Sicherheitsgruppe hinzu. Wenn Sie die Sicherheitsgruppe *eingeschränkt* verwenden, müssen Sie auch den entsprechenden Organisationsbereich auswählen.

   >[!div class="mx-imgBorder"]
   >![Sicherheitsgruppe bearbeiten](./media/workday-inbound-tutorial/wd_isu_05.png "Sicherheitsgruppe bearbeiten")

### <a name="configuring-domain-security-policy-permissions"></a>Konfigurieren der Berechtigungen der Domänensicherheitsrichtlinie

In diesem Schritt gewähren Sie der Sicherheitsgruppe die Berechtigungen der Domänensicherheitsrichtlinie für die Mitarbeiterdaten.

**So konfigurieren Sie die Berechtigungen der Domänensicherheitsrichtlinie**

1. Geben Sie im Suchfeld **Security Group Membership and Access** (Mitgliedschaft in Sicherheitsgruppe und Zugriff) ein, und klicken Sie auf den Link für den Bericht.
   >[!div class="mx-imgBorder"]
   >![Suchen nach Mitgliedschaft in Sicherheitsgruppe](./media/workday-inbound-tutorial/security-group-membership-access.png)

1. Suchen Sie nach der Sicherheitsgruppe, die Sie im vorherigen Schritt erstellt haben, und wählen Sie sie aus. 
   >[!div class="mx-imgBorder"]
   >![Sicherheitsgruppe auswählen](./media/workday-inbound-tutorial/select-security-group-workday.png)

1. Klicken Sie neben dem Gruppennamen auf die Auslassungszeichen (...), und wählen Sie im Menü die Option **Security Group > Maintain Domain Permissions for Security Group** (Sicherheitsgruppe > Domänenberechtigungen für Sicherheitsgruppe verwalten) aus.
   >[!div class="mx-imgBorder"]
   >![Auswählen der Option zum Verwalten von Domänenberechtigungen](./media/workday-inbound-tutorial/select-maintain-domain-permissions.png)

1. Fügen Sie unter **Integrationsberechtigungen** die folgenden Domänen der Liste **Domain Security Policies permitting Put access** (PUT-Zugriff für Domänensicherheitsrichtlinien zulassen) hinzu.
   * *External Account Provisioning* (Externe Kontobereitstellung)
   * *Worker Data: Public Worker Reports* (Mitarbeiterdaten: öffentliche Mitarbeiterberichte) 
   * *Person Data: Work Contact Information* (Personen: Kontaktinformationen von Mitarbeitern) (erforderlich für das Rückschreiben von Kontaktdaten aus Azure AD nach Workday)
   * *Workday-Konten* (erforderlich für das Rückschreiben von Benutzername/UPN aus Azure AD nach Workday)

1. Fügen Sie unter **Integrationsberechtigungen** die folgenden Domänen der Liste **Domain Security Policies permitting Get access** (GET-Zugriff für Domänensicherheitsrichtlinien zulassen) hinzu.
   * *Worker Data: Worker*
   * *Worker Data: All Positions* (Mitarbeiterdaten: alle Positionen)
   * *Worker Data: Current Staffing Information* (Mitarbeiterdaten: aktuelle Personalinformationen)
   * *Worker Data: Business Title on Worker Profile* (Mitarbeiterdaten: Berufsbezeichnung in Mitarbeiterprofil)
   * *Worker Data: Qualified Workers* (Mitarbeiterdaten: Qualifizierte Mitarbeiter) (Optional: Fügen Sie diese Option hinzu, um Daten zur Mitarbeiterqualifikation für die Bereitstellung abzurufen.)
   * *Worker Data: Skills and Experience* (Mitarbeiterdaten: Kompetenz und Erfahrung) (Optional: Fügen Sie diese Option hinzu, um Daten zu den Kenntnissen von Mitarbeitern für die Bereitstellung abzurufen.)

1. Nachdem Sie die obigen Schritte ausgeführt haben, wird der Bildschirm mit den Berechtigungen wie folgt angezeigt:
   >[!div class="mx-imgBorder"]
   >![Sicherheitsberechtigungen für alle Domänen](./media/workday-inbound-tutorial/all-domain-security-permissions.png)

1. Klicken Sie auf dem nächsten Bildschirm auf **OK** und **Fertig**, um die Konfiguration abzuschließen. 

### <a name="configuring-business-process-security-policy-permissions"></a>Konfigurieren von Sicherheitsrichtlinienberechtigungen für Geschäftsprozesse

In diesem Schritt gewähren Sie der Sicherheitsgruppe Berechtigungen der Sicherheitsrichtlinien für Geschäftsprozesse für die Mitarbeiterdaten. 

> [!NOTE]
> Dieser Schritt ist nur für die Einrichtung des Workday Writeback-App-Connectors erforderlich.

**So konfigurieren Sie Sicherheitsrichtlinienberechtigungen für Geschäftsprozesse**

1. Geben Sie **Business Process Policy** (Geschäftsprozessrichtlinie) in das Suchfeld ein, und klicken Sie dann auf den Link für die Aufgabe **Edit Business Process Security Policy** (Sicherheitsrichtlinie für Geschäftsprozesse bearbeiten).  

   >[!div class="mx-imgBorder"]
   >![Der Screenshot zeigt „Business Process Policy“ im Suchfeld und die ausgewählte Aufgabe „Edit Business Process Security Policy“.](./media/workday-inbound-tutorial/wd_isu_12.png "Sicherheitsrichtlinien für Geschäftsprozesse")  

2. Suchen Sie im Textfeld **Geschäftsprozesstyp** nach *Kontaktinformationen*, wählen Sie den Geschäftsprozess **Mitarbeiterkontaktinformationen ändern** aus, und klicken Sie auf **OK**.

   >[!div class="mx-imgBorder"]
   >![Der Screenshot zeigt die Seite „Edit Business Process Security Policy“ und „Mitarbeiterkontaktinformationen ändern“, ausgewählt im Menü „Geschäftsprozesstyp“.](./media/workday-inbound-tutorial/wd_isu_13.png "Sicherheitsrichtlinien für Geschäftsprozesse")  

3. Scrollen Sie auf der Seite **Sicherheitsrichtlinien für Geschäftsprozesse bearbeiten** zum Abschnitt **Kontaktinformationen von Mitarbeitern ändern (Webdienst)** .


4. Wählen Sie die neue Sicherheitsgruppe des Integrationssystems aus, und fügen Sie sie der Liste der Sicherheitsgruppen hinzu, die Anforderungen an Webdienste initiieren können. 

   >[!div class="mx-imgBorder"]
   >![Sicherheitsrichtlinien für Geschäftsprozesse](./media/workday-inbound-tutorial/wd_isu_15.png "Sicherheitsrichtlinien für Geschäftsprozesse")  

5. Klicken Sie auf **Done** (Fertig). 

### <a name="activating-security-policy-changes"></a>Aktivieren von Sicherheitsrichtlinienänderungen

**So aktivieren Sie Sicherheitsrichtlinienänderungen**

1. Geben Sie „aktivieren“ in das Suchfeld ein, und klicken Sie dann auf den Link **Ausstehende Sicherheitsrichtlinienänderungen aktivieren**.
   >[!div class="mx-imgBorder"]
   >![Aktivieren](./media/workday-inbound-tutorial/wd_isu_16.png "Aktivieren")

1. Geben Sie zum Ausführen der Aufgabe „Ausstehende Sicherheitsrichtlinienänderungen aktivieren“ zunächst einen Kommentar für Überwachungszwecke ein, und klicken Sie dann auf die Schaltfläche **OK**.
1. Führen Sie die Aufgabe auf dem nächsten Bildschirm aus, indem Sie das Kontrollkästchen **Bestätigen** aktivieren und auf **OK** klicken.

   >[!div class="mx-imgBorder"]
   >![Ausstehende Sicherheitsrichtlinienänderungen aktivieren](./media/workday-inbound-tutorial/wd_isu_18.png "Ausstehende Sicherheitsrichtlinienänderungen aktivieren")  

## <a name="provisioning-agent-installation-prerequisites"></a>Voraussetzungen für die Installation des Bereitstellungs-Agents

Lesen Sie sich den Artikel zu den [Voraussetzungen für die Installation des Bereitstellungs-Agents](../cloud-sync/how-to-prerequisites.md) durch, bevor Sie mit dem nächsten Abschnitt fortfahren. 

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfiguration der Benutzerbereitstellung aus Workday in Active Directory

Dieser Abschnitt enthält die Schritte zum Konfigurieren der Bereitstellung von Benutzerkonten aus Workday in jeder Active Directory-Domäne im Geltungsbereich Ihrer Integration.

* [Hinzufügen der Bereitstellungsconnector-App und Herunterladen des Bereitstellungs-Agents](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Installieren und Konfigurieren der lokalen Bereitstellungs-Agents](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Konfigurieren der Konnektivität zwischen Workday und Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [Konfigurieren von Attributzuordnungen](#part-4-configure-attribute-mappings)
* [Aktivieren und Starten der Benutzerbereitstellung](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Teil 1: Hinzufügen der Bereitstellungsconnector-App und Herunterladen des Bereitstellungs-Agents

**So konfigurieren Sie die Bereitstellung aus Workday in Active Directory**

1. Gehe zu <https://portal.azure.com>.

2. Suchen Sie im Azure-Portal nach **Azure Active Directory**, und wählen Sie es aus.

3. Klicken Sie auf **Unternehmensanwendungen** und dann auf **Alle Anwendungen**.

4. Klicken Sie auf **Anwendung hinzufügen**, und wählen Sie die Kategorie **Alle** aus.

5. Suchen Sie nach **Benutzerbereitstellung von Workday in Active Directory**, und fügen Sie die App aus dem Katalog hinzu.

6. Sobald die App hinzugefügt wurde und der Bildschirm mit den App-Details angezeigt wird, wählen Sie **Bereitstellung** aus.

7. Ändern Sie den **Bereitstellungsmodus** in **Automatisch**.

8. Klicken Sie auf das angezeigte Informationsbanner, um den Bereitstellungs-Agent herunterzuladen. 

   >[!div class="mx-imgBorder"]
   >![Herunterladen des Agents](./media/workday-inbound-tutorial/pa-download-agent.png "Bildschirm für das Herunterladen des Agents")

### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Teil 2: Installieren und Konfigurieren der lokalen Bereitstellungs-Agents

Um Active Directory lokal bereitzustellen, muss der Bereitstellungs-Agent auf einem in die Domäne eingebundenen Server installiert werden, der über Netzwerkzugriff auf die gewünschten Active Directory-Domänen verfügt.

Übertragen Sie das heruntergeladene Installationsprogramm für den Agent auf den Serverhost, und führen Sie die Schritte im [Abschnitt zur **Installation des Agents**](../cloud-sync/how-to-install.md) aus, um die Konfiguration des Agents durchzuführen.

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>Teil 3: Konfigurieren der Konnektivität zwischen Workday und Active Directory in der Bereitstellungs-App
In diesem Schritt stellen Sie im Azure-Portal Konnektivität zwischen Workday und Active Directory her. 

1. Wechseln Sie im Azure-Portal zurück zu der in [Teil 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent) erstellten App zur Benutzerbereitstellung von Workday zu Active Directory.

1. Vervollständigen Sie den Abschnitt **Administratoranmeldeinformationen** wie folgt:

   * **Workday-Benutzername:** Geben Sie den Benutzernamen des Workday-Systemintegrationskontos mit angefügtem Mandantendomänennamen ein. Es sollte etwa so aussehen: **benutzername\@mandantenname**

   * **Workday-Kennwort:** Geben Sie das Kennwort des Workday-Systemintegrationskontos ein.

   * **URL der Workday Web Services-API:** Geben Sie die URL des Workday-Webdienstendpunkts für Ihren Mandanten ein. Die URL bestimmt die Version der Workday Web Services-API, die vom Connector verwendet wird. 

     | URL-Format | Verwendete WWS-API-Version | Erforderliche XPath-Änderungen |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v21.1 | Nein |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v21.1 | Nein |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v##.# | Ja |

      > [!NOTE]
     > Wenn in der URL keine Versionsinformationen angegeben sind, wird Workday Web Services (WWS) v21.1 von der App genutzt, und es müssen keine Änderungen an den XPath-API-Standardausdrücken der App vorgenommen werden. Geben Sie die Versionsnummer in der URL an, wenn Sie eine bestimmte WWS-API-Version verwenden möchten. <br>
     > Beispiel: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> Wenn Sie eine WWS-API der Version 30.0 oder höher verwenden, müssen Sie vor der Aktivierung des Bereitstellungsauftrags unter **Attributzuordnung > Erweiterte Optionen > Attributliste für Workday bearbeiten** die **XPath-API-Ausdrücke** aktualisieren (entsprechende Verweise finden Sie im Abschnitt [Verwalten Ihrer Konfiguration](#managing-your-configuration) und in der [Workday-Attributreferenz](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)).  

   * **Active Directory-Gesamtstruktur:** Der „Name“ Ihrer Active Directory-Domäne, mit dem diese beim Agent registriert wurde. Wählen Sie über die Dropdownliste die Zieldomäne für die Bereitstellung aus. Dieser Wert ist meist eine Zeichenfolge wie: *contoso.com*

   * **Active Directory-Container:** Geben Sie den DN des Containers an, in dem der Agent Benutzerkonten standardmäßig erstellen soll.
        Beispiel: *OU=Standard Users,OU=Users,DC=contoso,DC=test*

     > [!NOTE]
     > Diese Einstellung wird nur für die Benutzerkontoerstellung verwendet, wenn das Attribut *parentDistinguishedName* nicht in den Attributzuordnungen konfiguriert ist. Diese Einstellung wird nicht zum Suchen von Benutzern oder für Updatevorgänge verwendet. Der Suchvorgang schließt die gesamte Domänenteilstruktur ein.

   * **Benachrichtigungs-E-Mail**: Geben Sie Ihre E-Mail-Adresse ein, und aktivieren Sie das Kontrollkästchen „E-Mail senden, wenn Fehler auftritt“.

     > [!NOTE]
     > Der Azure AD-Bereitstellungsdienst sendet eine E-Mail-Benachrichtigung, wenn der Bereitstellungsauftrag in den Zustand [Quarantäne](../app-provisioning/application-provisioning-quarantine-status.md) wechselt.

   * Klicken Sie auf die Schaltfläche **Verbindung testen**. Wenn der Verbindungstest erfolgreich ist, klicken Sie oben auf die Schaltfläche **Speichern**. Überprüfen Sie bei einem Fehler, ob die Workday-Anmeldeinformationen und die AD-Anmeldeinformationen, die beim Einrichten des Agents angegeben wurden, gültig sind.

     >[!div class="mx-imgBorder"]
     >![Der Screenshot zeigt die Seite „Bereitstellung“ mit eingegebenen Anmeldeinformationen.](./media/workday-inbound-tutorial/wd_1.png)

   * Sobald die Anmeldeinformationen erfolgreich gespeichert wurden, wird im Abschnitt **Zuordnungen** die Standardzuordnung **Workday-Worker mit lokalem Active Directory synchronisieren** angezeigt.

### <a name="part-4-configure-attribute-mappings"></a>Teil 4: Konfigurieren von Attributzuordnungen

In diesem Abschnitt konfigurieren Sie den Fluss von Benutzerdaten aus Workday in Active Directory.

1. Klicken Sie auf der Registerkarte „Bereitstellung“ unter **Zuordnungen** auf **Workday-Worker in lokalem Active Directory synchronisieren**.

1. Im Feld **Quellobjektbereich** können Sie die Benutzergruppen in Workday für die Bereitstellung in Active Directory auswählen, indem Sie verschiedene attributbasierte Filter definieren. Der Standardbereich ist „Alle Benutzer in Workday“. Beispielfilter:

   * Beispiel: Auswählen der Benutzer mit Worker-IDs von 1000000 bis 2000000 (außer 2000000)

      * Attribut: WorkerID

      * Operator: REGEX Match

      * Wert: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Beispiel: Nur Mitarbeiter und keine vorübergehend Beschäftigten

      * Attribut: EmployeeID

      * Operator: IS NOT NULL

   > [!TIP]
   > Wenn Sie die Bereitstellungs-App zum ersten Mal konfigurieren, müssen Sie Ihre Attributzuordnungen und Ausdrücke testen und überprüfen, um sicherzustellen, dass sie damit das gewünschte Ergebnis erzielen. Microsoft empfiehlt, die [Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) unter **Quellobjektbereich** und die [bedarfsorientierte Bereitstellung](../app-provisioning/provision-on-demand.md) zu verwenden, um Ihre Zuordnungen mit einigen Testbenutzern von Workday zu testen. Sobald Sie sich vergewissert haben, dass die Zuordnungen funktionieren, können Sie den Filter entweder entfernen oder schrittweise erweitern, um mehr Benutzer einzubinden.

   > [!CAUTION] 
   > Beim Standardverhalten des Bereitstellungsmoduls werden Benutzer deaktiviert/gelöscht, die sich außerhalb des gültigen Bereichs befinden. Dies ist bei Ihrer Integration von Workday in AD möglicherweise nicht wünschenswert. Informationen zum Außerkraftsetzen dieses Standardverhaltens finden Sie im Artikel [Überspringen des Löschens von Benutzerkonten außerhalb des gültigen Bereichs](../app-provisioning/skip-out-of-scope-deletions.md).

1. Im Feld **Zielobjektaktionen** können Sie global filtern, welche Aktionen auf Active Directory angewendet werden. **Erstellen** und **Aktualisieren** erfolgen am häufigsten.

1. Im Abschnitt **Attributzuordnungen** können Sie definieren, wie einzelne Workday-Attribute Active Directory-Attributen zugeordnet werden.

1. Klicken Sie auf eine vorhandene Attributzuordnung, um sie zu aktualisieren. Oder klicken Sie am unteren Bildschirmrand auf **Neue Zuordnung hinzufügen**, um neue Zuordnungen hinzuzufügen. Eine einzelne Attributzuordnung unterstützt die folgenden Eigenschaften:

      * **Zuordnungstyp**

         * **Direkt**: Schreibt den Wert des Workday-Attributs unverändert in das AD-Attribut.

         * **Konstant**: Schreibt einen statischen, konstanten Zeichenfolgenwert in das AD-Attribut.

         * **Ausdruck**: Ermöglicht das Schreiben eines benutzerdefinierten Werts basierend auf einem oder mehreren Workday-Attributen in das AD-Attribut. [Weitere Informationen finden Sie im Artikel zu Ausdrücken](../app-provisioning/functions-for-customizing-application-data.md).

      * **Quellattribut**: Das Benutzerattribut aus Workday. Wenn das gesuchte Attribut nicht vorhanden ist, finden Sie weitere Informationen unter [Anpassen der Liste der Workday-Benutzerattribute](#customizing-the-list-of-workday-user-attributes).

      * **Standardwert**: Optional. Wenn das Quellattribut einen leeren Wert aufweist, wird von der Zuordnung stattdessen dieser Wert geschrieben.
            Die meisten Konfigurationen sehen vor, dieses Feld leer zu lassen.

      * **Zielattribut**: Das Benutzerattribut in Active Directory.

      * **Objekte mit diesem Attribut abgleichen**: Gibt an, ob diese Zuordnung zum eindeutigen Bestimmen von Benutzern zwischen Workday und Active Directory verwendet werden soll. Dieser Wert wird meist auf das Feld „Worker-ID“ für Workday festgelegt, das in der Regel den „Mitarbeiter-ID“-Attributen in Active Directory zugeordnet wird.

      * **Rangfolge für Abgleich**: Es können mehrere Attribute für den Abgleich festgelegt werden. Falls mehrere vorhanden sind, werden sie entsprechend der in diesem Feld festgelegten Reihenfolge ausgewertet. Sobald eine Übereinstimmung gefunden wird, werden keine weiteren Attribute für den Abgleich mehr ausgewertet.

      * **Diese Zuordnung anwenden**

         * **Immer**: Wenden Sie diese Zuordnung sowohl bei der Aktion zum Erstellen eines Benutzers als auch bei der zum Aktualisieren eines Benutzers an.

         * **Nur während der Erstellung**: Wenden Sie diese Zuordnung nur bei der Aktion zum Erstellen eines Benutzers an.

1. Klicken Sie oben im Abschnitt „Attributzuordnung“ auf **Speichern**, um Ihre Zuordnungen zu speichern.
   >[!div class="mx-imgBorder"]
   >![Der Screenshot zeigt die Seite „Attributzuordnung“ mit ausgewählter Aktion „Speichern“.](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Nachstehend finden Sie einige Beispiele für Attributzuordnungen zwischen Workday und Active Directory sowie einige häufig verwendete Ausdrücke.

* Der Ausdruck für die Zuordnung zum Attribut *parentDistinguishedName* wird verwendet, um Benutzer basierend auf mindestens einem Workday-Quellattribut in anderen Organisationseinheiten bereitzustellen. In diesem Beispiel werden Benutzer basierend auf dem Ort in verschiedenen Organisationseinheiten platziert.

* Das Attribut *userPrincipalName* im Active Directory wird mit der Deduplizierungsfunktion [SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) erzeugt, die das Vorhandensein eines generierten Wertes in der Ziel-AD-Domäne überprüft und ihn nur dann festlegt, wenn er eindeutig ist.  

* [Hier finden Sie Dokumentation zum Schreiben von Ausdrücken](../app-provisioning/functions-for-customizing-application-data.md). Dieser Abschnitt enthält auch Beispiele zum Entfernen von Sonderzeichen.

| WORKDAY-ATTRIBUT | ACTIVE DIRECTORY-ATTRIBUT |  ÜBEREINSTIMMENDE ID? | ERSTELLEN/AKTUALISIEREN |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Ja** | Wird nur bei der Erstellung geschrieben |
| **PreferredNameData**    |  cn    |   |   Wird nur bei der Erstellung geschrieben |
| **SelectUniqueValue( Join("\@", Join(".", \[FirstName\], \[LastName\]), "contoso.com"), Join("\@", Join(".", Mid(\[FirstName\], 1, 1), \[LastName\]), "contoso.com"), Join("\@", Join(".", Mid(\[FirstName\], 1, 2), \[LastName\]), "contoso.com"))**   | userPrincipalName     |     | Wird nur bei der Erstellung geschrieben 
| `Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )`      |    sAMAccountName            |     |         Wird nur bei der Erstellung geschrieben |
| **Switch(\[Active\], , "0", "True", "1", "False")** |  accountDisabled      |     | Erstellen und aktualisieren |
| **Vorname**   | givenName       |     |    Erstellen und aktualisieren |
| **Nachname**   |   sn   |     |  Erstellen und aktualisieren |
| **PreferredNameData**  |  displayName |     |   Erstellen und aktualisieren |
| **Company**         | company   |     |  Erstellen und aktualisieren |
| **SupervisoryOrganization**  | department  |     |  Erstellen und aktualisieren |
| **ManagerReference**   | manager  |     |  Erstellen und aktualisieren |
| **BusinessTitle**   |  title     |     |  Erstellen und aktualisieren | 
| **AddressLineData**    |  streetAddress  |     |   Erstellen und aktualisieren |
| **Municipality**   |   l   |     | Erstellen und aktualisieren |
| **CountryReferenceTwoLetter**      |   co |     |   Erstellen und aktualisieren |
| **CountryReferenceTwoLetter**    |  c  |     |         Erstellen und aktualisieren |
| **CountryRegionReference** |  st     |     | Erstellen und aktualisieren |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Erstellen und aktualisieren |
| **PostalCode**  |   postalCode  |     | Erstellen und aktualisieren |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Erstellen und aktualisieren |
| **Fax**      | facsimileTelephoneNumber     |     |    Erstellen und aktualisieren |
| **Mobile**  |    mobile       |     |       Erstellen und aktualisieren |
| **LocalReference** |  preferredLanguage  |     |  Erstellen und aktualisieren |                                               
| **Switch(\[Municipality\], "OU=Default Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  Erstellen und aktualisieren |

Nachdem die Konfiguration Ihrer Attributzuordnung abgeschlossen ist, können Sie die Bereitstellung für einen einzelnen Benutzer testen, indem Sie die [bedarfsgesteuerte Bereitstellung](../app-provisioning/provision-on-demand.md) verwenden und dann den [Dienst für die Benutzerbereitstellung aktivieren und starten](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Aktivieren und Starten der Benutzerbereitstellung

Nachdem Sie die Konfiguration der Workday-Bereitstellungs-App abgeschlossen haben und die Bereitstellung für einen einzelnen Benutzer per [bedarfsgesteuerter Bereitstellung](../app-provisioning/provision-on-demand.md) überprüft haben, können Sie den Bereitstellungsdienst im Azure-Portal aktivieren.

> [!TIP]
> Beim Aktivieren des Bereitstellungsdiensts werden Bereitstellungsvorgänge für alle Benutzer im Bereich initiiert. Wenn Fehler in der Zuordnung oder Workday-Datenprobleme auftreten, kann der Bereitstellungsauftrag fehlschlagen und in den Quarantänezustand wechseln. Um dies zu vermeiden, empfehlen wir Ihnen, den Filter **Quellobjektbereich** zu konfigurieren und Ihre Attributzuordnungen mit einigen Testbenutzern per [bedarfsgesteuerter Bereitstellung](../app-provisioning/provision-on-demand.md) zu testen, bevor Sie die vollständige Synchronisierung für alle Benutzer starten. Sobald Sie sich vergewissert haben, dass die Zuordnungen funktionieren und Sie die gewünschten Ergebnisse erhalten, können Sie den Filter entweder entfernen oder schrittweise erweitern, um mehr Benutzer einzubinden.

1. Wechseln Sie zum Blatt **Bereitstellung**, und klicken Sie auf **Bereitstellung beginnen**.

1. Dieser Vorgang startet die erste Synchronisierung, die abhängig von der Anzahl der Benutzer im Workday-Mandanten eine variable Anzahl von Stunden dauern kann. Sie können die Statusanzeige überprüfen, um den Fortschritt des Synchronisierungszyklus zu verfolgen. 

1. Im Azure-Portal können Sie sich auf der Registerkarte **Überwachungsprotokolle** jederzeit ansehen, welche Aktionen der Bereitstellungsdienst ausgeführt hat. Die Überwachungsprotokolle enthalten alle einzelnen Synchronisierungsereignisse des Bereitstellungsdiensts – beispielsweise, welche Benutzer in Workday gelesen und anschließend Active Directory hinzugefügt oder dort aktualisiert wurden. Im Abschnitt „Problembehandlung“ finden Sie Anweisungen, wie Sie die Überwachungsprotokolle überprüfen und Bereitstellungsfehler beheben können.

1. Nach Abschluss der ersten Synchronisierung wird auf der Registerkarte **Bereitstellung** ein Überwachungszusammenfassungsbericht ausgegeben:
   > [!div class="mx-imgBorder"]
   > ![Statusanzeige für die Bereitstellung](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="frequently-asked-questions-faq"></a>Häufig gestellte Fragen (FAQ)

* **Fragen zur Lösungsfunktion**
  * [Wie legt die Lösung bei der Bearbeitung einer Neueinstellung aus Workday das Kennwort für das neue Benutzerkonto in Active Directory fest?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [Unterstützt die Lösung das Senden von E-Mail-Benachrichtigungen nach Abschluss des Bereitstellungsvorgangs?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Speichert die Lösung Workday-Benutzerprofile in der Azure AD Cloud oder auf der Ebene des Bereitstellungs-Agents zwischen?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [Unterstützt die Lösung die Zuweisung von lokalen AD-Gruppen an den Benutzer?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Welche Workday-APIs verwendet die Lösung, um Workday-Workerprofile abzufragen und zu aktualisieren?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Kann ich meinen Workday-HCM-Mandanten mit zwei Azure AD-Mandanten konfigurieren?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Wie kann ich Verbesserungen vorschlagen oder neue Funktionen im Zusammenhang mit der Workday- und Azure AD-Integration anfordern?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Fragen zum Bereitstellungs-Agent**
  * [Wie lautet die allgemein verfügbare Version des Bereitstellungs-Agents?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Wie kann ich die Version meines Bereitstellungs-Agents ermitteln?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Überträgt Microsoft automatisch Updates des Bereitstellungs-Agents per Push?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Kann ich den Bereitstellungs-Agent auf demselben Server installieren, auf dem Azure AD Connect ausgeführt wird?](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [Wie konfiguriere ich den Bereitstellungs-Agent, damit dieser einen Proxyserver für die ausgehende HTTP-Kommunikation verwendet?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Wie stelle ich sicher, dass der Bereitstellungs-Agent mit dem Azure AD-Mandanten kommunizieren kann, und dass keine Firewalls die vom Agenten benötigten Ports blockieren?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Wie hebe ich die Registrierung der meinem Bereitstellungs-Agent zugeordneten Domäne wieder auf?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Wie deinstalliere ich den Bereitstellungs-Agent?](#how-do-i-uninstall-the-provisioning-agent)

* **Fragen zur Zuordnung und Konfiguration des „Workday to AD“-Attributs**
  * [Wie kann ich eine Arbeitskopie der Zuordnung und des Schemas meines Workday-Bereitstellungsattributs sichern und exportieren?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Ich habe benutzerdefinierte Attribute in Workday und Active Directory. Wie konfiguriere ich die Lösung, damit diese mit meinen benutzerdefinierten Attributen arbeitet?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Kann ich Benutzerfotos aus Workday für Active Directory bereitstellen?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Wie synchronisieren ich Mobilfunknummern aus Workday basierend auf der Benutzereinwilligung für die öffentliche Verwendung?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Wie formatiere ich Anzeigenamen in AD basierend auf den Attributen für Abteilung/Land/Stadt des Benutzers und wie gehe mit regionalen Abweichungen um?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Wie kann ich mit „SelectUniqueValue eindeutige Werte für das Attribut „samAccountName“ generieren?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Wie entferne ich Zeichen mit diakritischen Zeichen und konvertiere sie in normale englische Schriftzeichen?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Fragen zur Lösungsfunktion

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Wie legt die Lösung bei der Bearbeitung einer Neueinstellung aus Workday das Kennwort für das neue Benutzerkonto in Active Directory fest?

Wenn der lokale Bereitstellungs-Agent eine Anforderung zur Erstellung eines neuen AD-Kontos erhält, generiert er automatisch ein komplexes Zufallskennwort, das den vom AD-Server definierten Anforderungen an die Komplexität des Kennworts entspricht, und legt dieses auf das Benutzerobjekt fest. Dieses Kennwort wird nirgendwo protokolliert.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>Unterstützt die Lösung das Senden von E-Mail-Benachrichtigungen nach Abschluss des Bereitstellungsvorgangs?

Nein, das Senden von E-Mail-Benachrichtigungen nach Abschluss der Bereitstellungsvorgänge wird in der aktuellen Version nicht unterstützt.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Speichert die Lösung Workday-Benutzerprofile in der Azure AD Cloud oder auf der Ebene des Bereitstellungs-Agents zwischen?

Nein, in der Lösung werden keine Benutzerprofile zwischengespeichert. Der Azure AD-Bereitstellungsdienst fungiert einfach als Datenprozessor, liest Daten von Workday und schreibt in das Active Directory oder Azure AD. Details zum Datenschutz und der Datenaufbewahrung finden Sie unter [Verwalten personenbezogener Daten](#managing-personal-data).

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Unterstützt die Lösung die Zuweisung von lokalen AD-Gruppen an den Benutzer?

Diese Funktion wird derzeit nicht unterstützt. Eine empfohlene Problemumgehung besteht darin, ein PowerShell-Skript bereitzustellen, das den Microsoft Graph-API-Endpunkt auf [Überwachungsprotokolldaten](/graph/api/resources/azure-ad-auditlog-overview) abfragt und diese verwendet, um Szenarien wie eine Gruppenzuweisung auszulösen. Dieses PowerShell-Skript kann an einen Taskplaner angefügt und auf demselben Computer mit dem Bereitstellungs-Agent bereitgestellt werden.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Welche Workday-APIs verwendet die Lösung, um Workday-Workerprofile abzufragen und zu aktualisieren?

Die Lösung verwendet derzeit die folgenden Workday-APIs:

* Das Format der **Workday Web Services-API-URL**, das im Abschnitt **Administratoranmeldeinformationen** verwendet wird, bestimmt die für Get_Workers verwendete API-Version.
  * Wenn das URL-Format „https://\#\#\#\#\.workday\.com/ccx/service/Mandantenname“ lautet, wird API v21.1 verwendet. 
  * Wenn das URL-Format „https://\#\#\#\#\.workday\.com/ccx/service/tenantName/Human\_Resources“ lautet, wird API v21.1 verwendet. 
  * Wenn das URL-Format „https://\#\#\#\#\.workday\.com/ccx/service/tenantName/Human\_Resources/v\#\#\.\#“ lautet, wird die angegebene API-Version verwendet. (Beispiel: Wenn v34.0 angegeben ist, wird diese Version verwendet.)  

* Change_Work_Contact_Information (v30.0) für das Feature zum Rückschreiben von Workday-E-Mails 
* Update_Workday_Account (v31.2) für das Feature zum Rückschreiben von Workday-Benutzernamen 

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Kann ich meinen Workday-HCM-Mandanten mit zwei Azure AD-Mandanten konfigurieren?

Ja, diese Konfiguration wird unterstützt. Hier sind die allgemeinen Schritte zum Konfigurieren dieses Szenarios:

* Bereitstellen des Bereitstellungs-Agent 1, und registrieren in Azure AD-Mandanten 1.
* Bereitstellen des Bereitstellungs-Agent 2, und registrieren in Azure AD-Mandanten 2.
* Konfigurieren Sie die einzelnen Agents mit der/den Domäne(n) basierend auf den „untergeordneten Domänen“, die jeder Bereitstellungs-Agent verwalten wird. Ein Agent kann mehrere Domänen verarbeiten.
* Richten Sie im Azure-Portal für jeden Mandanten die „Workday to AD“-Benutzerbereitstellungs-App ein und konfigurieren Sie sie mit den entsprechenden Domänen.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Wie kann ich Verbesserungen vorschlagen oder neue Funktionen im Zusammenhang mit der Workday- und Azure AD-Integration anfordern?

Ihr Feedback ist uns sehr wichtig, da es uns hilft, die Weichen für die zukünftigen Releases und Verbesserungen zu stellen. Wir freuen uns über jedes Feedback und ermutigen Sie, Ihre Ideen oder Verbesserungsvorschläge im [Feedbackforum von Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory) einzureichen. Für spezifisches Feedback im Zusammenhang mit der Workday-Integration wählen Sie die Kategorie *SaaS-Anwendungen* und suchen Sie anhand des Stichworts *Workday*, um vorhandenes Feedback zum Workday zu finden.

> [!div class="mx-imgBorder"]
> ![UserVoice-SaaS-Apps](media/workday-inbound-tutorial/uservoice_saas_apps.png)

> [!div class="mx-imgBorder"]
> ![UserVoice-Workday](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Wenn Sie eine neue Idee vorschlagen, überprüfen Sie bitte, ob eine ähnliche Funktion vorgeschlagen wurde. In diesem Fall können Sie die Funktion oder die Ergänzungsanforderung bewerten. Sie können auch einen Kommentar zu Ihrem spezifischen Anwendungsfall hinterlassen, um Ihre Unterstützung für die Idee zu zeigen und zu veranschaulichen, wie die Funktion auch für Sie wertvoll ist.

### <a name="provisioning-agent-questions"></a>Fragen zum Bereitstellungs-Agent

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Wie lautet die allgemein verfügbare Version des Bereitstellungs-Agents?

Unter [Azure AD Connect-Bereitstellungs-Agent: Verlauf der Versionsveröffentlichungen](../app-provisioning/provisioning-agent-release-version-history.md) finden Sie die aktuelle GA-Version des Bereitstellungs-Agents.  

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Wie kann ich die Version meines Bereitstellungs-Agents ermitteln?

* Melden Sie sich bei dem Windows Server an, auf dem der Bereitstellungs-Agent installiert ist.
* Wechseln Sie zu **Systemsteuerung** -> **Deinstallieren oder Ändern eines Programms**.
* Suchen Sie nach der Version für den Eintrag **Microsoft Azure AD Connect-Bereitstellungs-Agent**.

  >[!div class="mx-imgBorder"]
  >![Azure portal](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Überträgt Microsoft automatisch Updates des Bereitstellungs-Agents per Push?

Ja. Der Bereitstellungs-Agent wird von Microsoft automatisch aktualisiert, wenn der Windows-Dienst **Microsoft Azure AD Connect Agent Updater** aktiv ist und ausgeführt wird.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Kann ich den Bereitstellungs-Agent auf demselben Server installieren, auf dem Azure AD Connect ausgeführt wird?

Ja, Sie können den Bereitstellungs-Agent auf demselben Server installieren, auf dem Azure AD Connect ausgeführt wird.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>Bei der Konfiguration fordert der Bereitstellungs-Agent die Azure AD-Administratoranmeldeinformationen an. Speichert der Agent die Anmeldeinformationen lokal auf dem Server?

Bei der Konfiguration fordert der Bereitstellungs-Agent die Azure AD-Administratoranmeldeinformationen nur an, um eine Verbindung zum Azure AD-Mandanten herzustellen. Die Anmeldeinformationen werden nicht lokal auf dem Server gespeichert. Die Anmeldeinformationen für die Verbindung mit der lokalen *Active Directory-Domäne* werden jedoch in einem lokalen Windows-Kennworttresor aufbewahrt.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Wie konfiguriere ich den Bereitstellungs-Agent, damit dieser einen Proxyserver für die ausgehende HTTP-Kommunikation verwendet?

Der Agent-Bereitstellung unterstützt die Verwendung von Proxys für ausgehenden Datenverkehr. Dies können Sie durch das Konfigurieren der Konfigurationsdatei des Agent **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config** konfigurieren. Fügen Sie die folgenden Zeilen zum Ende der Datei unmittelbar vor dem `</configuration>`-Tag ein.
Ersetzen Sie der Variablen [[proxy-server] ] und [proxy-port] durch den Namen Ihres Proxyservers und die Portwerte.

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Wie stelle ich sicher, dass der Bereitstellungs-Agent mit dem Azure AD-Mandanten kommunizieren kann, und dass keine Firewalls die vom Agenten benötigten Ports blockieren?

Sie können auch überprüfen, ob alle [erforderlichen Ports](../manage-apps/application-proxy-add-on-premises-application.md#open-ports) geöffnet sind.

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Kann ein Bereitstellungs-Agent für die Bereitstellung mehrerer AD-Domänen konfiguriert werden?

Ja, ein Bereitstellungs-Agent kann konfiguriert werden, um mehrere AD-Domänen zu verwalten, solange der Agent uneingeschränkten Zugriff auf den jeweiligen Domänencontrollern hat. Microsoft empfiehlt die Einrichtung einer Gruppe von 3 Bereitstellungs-Agents, die denselben Satz von AD-Domänen bedienen, um Hochverfügbarkeit und Failover-Support zu gewährleisten.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Wie hebe ich die Registrierung der meinem Bereitstellungs-Agent zugeordneten Domäne wieder auf?

* Rufen Sie über das Azure-Portal die *Mandanten-ID* Ihres Azure AD-Mandanten ab.
* Melden Sie sich bei dem Windows Server an, auf dem der Bereitstellungs-Agent ausgeführt wird.
* Öffnen Sie PowerShell als Windows-Administrator.
* Wechseln Sie in das Verzeichnis mit den Registrierungsskripten und führen Sie die folgenden Befehle aus, um den Parameter \[tenant ID\] durch den Wert Ihrer Mandanten-ID zu ersetzen.

  ```powershell
  cd "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder"
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Kopieren Sie den Wert des Feldes `id` aus der Liste der angezeigten Agents aus der Ressource, deren Wert für *resourceName* Ihrem AD-Domänennamen entspricht.
* Fügen Sie den ID-Wert in den folgenden Befehl ein, und führen sie ihn in PowerShell aus.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Führen Sie den Assistenten für die Agent-Konfiguration erneut aus.
* Alle anderen Agents, die zuvor dieser Domäne zugeordnet waren, müssen neu konfiguriert werden.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Wie deinstalliere ich den Bereitstellungs-Agent?

* Melden Sie sich bei dem Windows Server an, auf dem der Bereitstellungs-Agent installiert ist.
* Wechseln Sie zu **Systemsteuerung** -> **Deinstallieren oder Ändern eines Programms**.
* Deinstallieren Sie die folgenden Programme:
  * Microsoft Azure AD Connect-Bereitstellungs-Agent
  * Microsoft Azure AD Connect Agent Updater
  * Microsoft Azure AD Connect-Bereitstellungs-Agent-Paket

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Fragen zur Zuordnung und Konfiguration des „Workday to AD“-Attributs

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Wie kann ich eine Arbeitskopie der Zuordnung und des Schemas meines Workday-Bereitstellungsattributs sichern und exportieren?

Sie können die Microsoft Graph-API verwenden, um Ihre Workday-Benutzerbereitstellungs-Konfiguration zu exportieren. Weitere Informationen finden Sie in den Schritten im Abschnitt [Exportieren und Importieren Ihrer Konfiguration für die Workday-Benutzerbereitstellungs-Attributzuordnung](#exporting-and-importing-your-configuration).

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Ich habe benutzerdefinierte Attribute in Workday und Active Directory. Wie konfiguriere ich die Lösung, damit diese mit meinen benutzerdefinierten Attributen arbeitet?

Die Lösung unterstützt benutzerdefinierte Workday- und Active Directory-Attribute. Um Ihre benutzerdefinierten Attribute dem Zuordnungsschema hinzuzufügen, öffnen Sie das Blatt **Attributzuordnung** und scrollen Sie nach unten, um den Abschnitt **Erweiterte Optionen anzeigen** zu erweitern. 

![Attributliste bearbeiten](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Um Ihre benutzerdefinierten Workday-Attribute hinzuzufügen, wählen Sie die Option *Attributliste für Workday bearbeiten*, und um Ihre benutzerdefinierten AD-Attribute hinzuzufügen, wählen Sie die Option *Attributliste für lokales Active Directory bearbeiten*.

Weitere Informationen:

* [Anpassen der Liste der Workday-Benutzerattribute](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Wie konfiguriere ich die Lösung so, dass sie nur Attribute im AD aktualisiert, die auf Änderungen an Workday basieren, und keine neuen AD-Konten erstellt?

Diese Konfiguration kann vorgenommen werden, indem die **Zielobjektaktionen** im Blatt **Attributzuordnung** wie unten gezeigt eingestellt werden:

![Updateaktion](./media/workday-inbound-tutorial/wd_target_update_only.png)

Aktivieren Sie das Kontrollkästchen „Update“, damit nur Aktualisierungsvorgänge von Workday zu AD durchgeführt werden. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Kann ich Benutzerfotos aus Workday für Active Directory bereitstellen?

Die Lösung unterstützt derzeit keine Einstellung von binären Attributen wie *thumbnailPhoto* und *jpegPhoto* in Active Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Wie synchronisieren ich Mobilfunknummern aus Workday basierend auf der Benutzereinwilligung für die öffentliche Verwendung?

* Wechseln Sie zum Blatt „Bereitstellung“ Ihrer Workday-Bereitstellungs-App.
* Klicken Sie auf die Attributzuordnungen. 
* Wählen Sie unter **Zuordnungen** die Option **Workday-Worker mit lokalem Active Directory synchronisieren** (oder **Workday-Worker mit Azure AD synchronisieren**).
* Scrollen Sie auf der Seite „Attributzuordnungen“ nach unten und aktivieren Sie das Kontrollkästchen „Erweiterte Optionen anzeigen“.  Klicken Sie auf **Attributliste für Workday bearbeiten**.
* Suchen Sie auf dem Blatt, das geöffnet wird, das Attribut „Mobile“, und klicken Sie auf die Zeile, sodass Sie den **API-Ausdruck** ![Mobile GDPR](./media/workday-inbound-tutorial/mobile_gdpr.png) bearbeiten können.

* Ersetzen Sie den **API-Ausdruck** durch den folgenden neuen Ausdruck. Damit wird die Arbeitsmobilfunknummer nur dann abgerufen, wenn „Public Usage Flag“ auf „True“ festgelegt ist.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Speichern Sie die Attributliste.
* Speichern Sie die Attributzuordnung.
* Löschen Sie den aktuellen Status, und starten Sie die vollständige Synchronisierung neu.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Wie formatiere ich Anzeigenamen in AD basierend auf den Attributen für Abteilung/Land/Stadt des Benutzers und wie gehe mit regionalen Abweichungen um?

Es ist eine häufige Anforderung, das Attribut *displayName* in AD so zu konfigurieren, dass es auch Informationen über die Abteilung und das Land / die Region des Benutzers liefert. Wenn John Smith z.B. in der Marketingabteilung in den USA arbeitet, kann sein *displayName* als *Smith, John (Marketing-US)* angezeigt werden.

So können Sie solche Anforderungen für die Konstruktion von *CN* oder *displayName* verarbeiten, um Attribute wie Unternehmen, Geschäftseinheit, Stadt oder Land/Region aufzunehmen.

* Jedes Workday-Attribut wird über einen zugrundeliegenden XPATH-API-Ausdruck abgerufen, der in **Attributzuordnung -> Abschnitt „Erweitert“ -> Attributliste für Workday bearbeiten** konfigurierbar ist. Hier ist der XPATH-API-Standardausdruck für die Workday-Attribute *PreferredFirstName*, *PreferredLastName*, *Company* und *SupervisoryOrganization*.

     | Workday-Attribut | API-XPATH-Ausdruck |
     | ----------------- | -------------------- |
     | PreferredFirstName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | PreferredLastName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Company | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Company']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Supervisory']/wd:Organization_Name/text() |

   Vergewissern Sie sich bei Ihrem Workday-Team, dass der obige API-Ausdruck für Ihre Workday-Mandantenkonfiguration gültig ist. Bei Bedarf können Sie sie gemäß den Schritten in [Anpassen der Liste der Workday-Benutzerattribute](#customizing-the-list-of-workday-user-attributes) bearbeiten.

* Auf ähnliche Weise werden die in Workday vorhandenen Länder-/Regionsinformationen mit dem folgenden XPATH abgerufen: *wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference*

     Es gibt 5 länder-/regionsbezogene Attribute, die im Abschnitt „Workday-Attributliste“ verfügbar sind.

     | Workday-Attribut | API-XPATH-Ausdruck |
     | ----------------- | -------------------- |
     | CountryReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Vergewissern Sie sich bei Ihrem Workday-Team, dass die obigen API-Ausdrücke für Ihre Workday-Mandantenkonfiguration gültig ist. Bei Bedarf können Sie sie gemäß den Schritten in [Anpassen der Liste der Workday-Benutzerattribute](#customizing-the-list-of-workday-user-attributes) bearbeiten.

* Um den richtigen Attributzuordnungsausdruck zu erstellen, identifizieren Sie, welches Workday-Attribut „autoritativ“ den Vornamen, den Nachnamen, das Land bzw. die Region und die Abteilung des Benutzers darstellt. Nehmen wir an, die Attribute sind *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* und *SupervisoryOrganization*. Sie können dies verwenden, um einen Ausdruck für das AD-Attribut *displayName* wie folgt aufzubauen, um einen Anzeigenamen wie *Smith, John (Marketing-US)* zu erhalten.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Sobald Sie den richtigen Ausdruck gefunden haben, bearbeiten Sie die Attributzuordnungstabelle und ändern Sie die Attributzuordnung *displayName* wie unten gezeigt:   ![DisplayName-Zuordnung](./media/workday-inbound-tutorial/wd_displayname_map.png)

* In Erweiterung des obigen Beispiels möchten Sie Städtenamen, die von Workday kommen, in Kurzwerte umwandeln und damit Anzeigenamen wie *Smith, John (CHI)* oder *Doe, Jane (NYC)* erstellen, dann kann dieses Ergebnis mit einem Switch-Ausdruck mit dem Workday-Attribut *Municipality* als determinante Variable erreicht werden.

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    Weitere Informationen:
  * [Wechseln der Funktionssyntax](../app-provisioning/functions-for-customizing-application-data.md#switch)
  * [Verknüpfen der Funktionssyntax](../app-provisioning/functions-for-customizing-application-data.md#join)
  * [Anfügen der Funktionssyntax](../app-provisioning/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Wie kann ich mit „SelectUniqueValue eindeutige Werte für das Attribut „samAccountName“ generieren?

Nehmen wir an, Sie möchten eindeutige Werte für das Attribut *samAccountName* unter Verwendung einer Kombination der Attribute *FirstName* und *LastName* aus Workday generieren. Nachfolgend finden Sie einen Ausdruck, mit dem Sie starten können:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

So funktioniert der obige Ausdruck: Wenn der Benutzer John Smith ist, wird zuerst versucht „JSmith“ zu erstellen. Ist dieser Ausdruck bereits vorhanden, wird „JoSmith“ generiert. Ist dieser schon vorhanden, wird „JohSmith“ generiert. Der Ausdruck stellt auch sicher, dass der generierte Wert den Längen- und Sonderzeichenbeschränkungen für *samAccountName* entspricht.

Weitere Informationen:

* [Mid-Funktionssyntax](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Ersetzen der Funktionssyntax](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [SelectUniqueValue-Funktionssyntax](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Wie entferne ich Zeichen mit diakritischen Zeichen und konvertiere sie in normale englische Schriftzeichen?

Verwenden Sie die Funktion [NormalizeDiacritics](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics), um Sonderzeichen im Vor- und Nachnamen des Benutzers zu entfernen, wenn Sie die E-Mail-Adresse oder den CN-Wert für den Benutzer erstellen.

## <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung

Dieser Abschnitt enthält spezielle Hinweise zur Behebung von Bereitstellungsproblemen mit Ihrer Workday-Integration mithilfe der Protokolle „Azure AD-Überwachungsprotokolle“ und „Protokolle für die Windows Server-Ereignisanzeige“. Sie bauen auf allgemeinen Schritten zur Problembehandlung und den Konzepten in [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md) auf.

Dieser Abschnitt enthält die folgenden Aspekte bezüglich der Problembehandlung:

* [Konfigurieren des Bereitstellungs-Agents zum Ausgeben von Protokollen der Ereignisanzeige](#configure-provisioning-agent-to-emit-event-viewer-logs)
* [Einrichten der Windows-Ereignisanzeige für die Problembehandlung bei Agents](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Einrichten von Azure-Portal-Überwachungsprotokollen für die Problembehandlung bei einem Dienst](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Grundlegendes zu Protokollen für Erstellungsvorgänge für ein AD-Benutzerkonto](#understanding-logs-for-ad-user-account-create-operations)
* [Grundlegendes zu Protokollen für Aktualisierungsvorgänge für Manager](#understanding-logs-for-manager-update-operations)
* [Beheben von häufig auftretenden Fehlern](#resolving-commonly-encountered-errors)

### <a name="configure-provisioning-agent-to-emit-event-viewer-logs"></a>Konfigurieren des Bereitstellungs-Agents zum Ausgeben von Protokollen der Ereignisanzeige
1. Melden Sie sich beim Windows Server-Computer an, auf dem der Bereitstellungs-Agent bereitgestellt wurde.
1. Beenden Sie den Dienst **Microsoft Azure AD Connect Provisioning Agent**.
1. Erstellen Sie eine Kopie der ursprünglichen Konfigurationsdatei *C:\Programme\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*.
1. Ersetzen Sie den vorhandenen Abschnitt `<system.diagnostics>` durch Folgendes. 
   * Die Listenerkonfiguration **etw** gibt Meldungen an die EventViewer-Protokolle aus.
   * Die Listenerkonfiguration **textWriterListener** sendet Ablaufverfolgungsmeldungen an die Datei *ProvAgentTrace.log*. Heben Sie die Auskommentierung für die Zeilen, die sich auf „textWriterListener“ beziehen, nur für die erweiterte Problembehandlung auf. 

   ```xml
     <system.diagnostics>
         <sources>
         <source name="AAD Connect Provisioning Agent">
             <listeners>
             <add name="console"/>
             <add name="etw"/>
             <!-- <add name="textWriterListener"/> -->
             </listeners>
         </source>
         </sources>
         <sharedListeners>
         <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
         <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
             <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
         </add>
         <!-- <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/> -->
         </sharedListeners>
     </system.diagnostics>

   ```
1. Starten Sie den Dienst **Microsoft Azure AD Connect Provisioning Agent**.

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Einrichten der Windows-Ereignisanzeige für die Problembehandlung bei Agents

1. Melden Sie sich beim Windows Server-Computer an, auf dem der Bereitstellungs-Agent bereitgestellt ist.
1. Öffnen Sie die Desktop-App **Windows Server-Ereignisanzeige**.
1. Klicken Sie auf **Windows-Protokolle > Anwendung**.
1. Verwenden Sie die Option **Aktuelles Protokoll filtern...** , um alle Ereignisse anzuzeigen, die unter der Quelle **Azure AD Connect-Bereitstellungs-Agent** protokolliert sind. Schließen Sie Ereignisse mit der Ereignis-ID „5“ aus, indem Sie wie unten dargestellt den Filter „-5“ angeben.
   > [!NOTE]
   > Mit der Ereignis-ID 5 werden Agent-Bootstrapmeldungen erfasst, die an den Azure AD-Clouddienst gesendet werden. Aus diesem Grund nutzen wir beim Analysieren der Protokolldateien die Filterung. 

   ![Windows-Ereignisanzeige](media/workday-inbound-tutorial/wd_event_viewer_01.png)

1. Klicken Sie auf **OK**, um die Ergebnisansicht anhand der Spalte **Datum und Uhrzeit** zu filtern.

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Einrichten von Azure-Portal-Überwachungsprotokollen für die Problembehandlung bei einem Dienst

1. Starten Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zum Abschnitt **Überwachungsprotokolle** Ihrer Workday-Bereitstellungs-App.
1. Verwenden Sie die Schaltfläche **Spalten** auf der Seite „Überwachungsprotokolle“, um nur die folgenden Spalten in der Ansicht anzuzeigen (Datum, Aktivität, Status, Statusgrund). Mit dieser Konfiguration stellen Sie sicher, dass Sie sich nur auf Daten konzentrieren, die für die Problembehandlung relevant sind.

   ![Spalte „Überwachungsprotokoll“](media/workday-inbound-tutorial/wd_audit_logs_00.png)

1. Verwenden Sie die Abfrageparameter **Ziel** und **Datumsbereich** zum Filtern der Ansicht. 
   * Legen Sie den Abfrageparameter **Ziel** auf „Worker-ID“ oder „Mitarbeiter-ID“ des Workday-Workerobjekts fest.
   * Legen Sie den **Datumsbereich** auf einen entsprechenden Zeitraum fest, den Sie auf Fehler oder Probleme bei der Bereitstellung überprüfen möchten.

   ![Überwachungsprotokollfilter](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Grundlegendes zu Protokollen für Erstellungsvorgänge für ein AD-Benutzerkonto

Wenn ein neuer Mitarbeiter in Workday erkannt wird (z.B. mit der Mitarbeiter-ID *21023*), versucht der Azure AD-Bereitstellungsdienst, ein neues AD-Benutzerkonto für den Mitarbeiter zu erstellen und erstellt dabei 4 Datensätze für das Überwachungsprotokoll, wie unten beschrieben:

  [![Erstellungsvorgänge für Überwachungsprotokolle](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Wenn Sie auf einen Datensatz im Überwachungsprotokoll klicken, wird die Seite **Aktivitätsdetails** geöffnet. Das wird auf der Seite **Aktivitätsdetails** für jeden Protokolldatensatztyp angezeigt.

* Datensatz **Workday-Import**: Dieser Protokolldatensatz zeigt die aus Workday abgerufenen Workerinformationen an. Anhand der Informationen im Abschnitt *Zusätzliche Details* des Protokolldatensatzes können Sie Probleme beim Abrufen von Daten aus Workday beheben. Unten finden Sie einen Beispieldatensatz sowie Hinweise für die Interpretation der einzelnen Felder.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* Datensatz **AD-Import**: Dieser Protokolldatensatz zeigt aus AD abgerufene Kontoinformationen an. Da es bei der ersten Benutzererstellung kein AD-Konto gibt, wird unter *Statusgrund Aktivität* angegeben, dass kein Konto mit dem Attributwert mit einer übereinstimmenden ID im Active Directory gefunden wurde. Anhand der Informationen im Abschnitt *Zusätzliche Details* des Protokolldatensatzes können Sie Probleme beim Abrufen von Daten aus Workday beheben. Unten finden Sie einen Beispieldatensatz sowie Hinweise für die Interpretation der einzelnen Felder.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Um die Protokolldatensätze des Bereitstellungs-Agent zu finden, die diesem AD-Importvorgang entsprechen, öffnen Sie die Protokolle der Windows-Ereignisanzeige, und verwenden Sie die Option **Suche....** , um Protokolleinträge mit dem Attributwert für die übereinstimmende ID/Verknüpfungseigenschaft zu suchen (in diesem Fall *21023*).

  ![Suchen](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Suchen Sie nach dem Eintrag mit *Event ID = 9*, der Ihnen den LDAP-Suchfilter zur Verfügung stellt, mit dem der Agent das AD-Konto abruft. Sie können überprüfen, ob dies der richtige Suchfilter ist, um eindeutige Benutzereingaben zu erhalten.

  ![LDAP-Suche](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  Der Datensatz, der unmittelbar darauf mit *Event ID = 2* folgt, erfasst das Ergebnis des Suchvorgangs und stellt fest, inwieweit Ergebnisse geliefert wurden.

  ![LDAP-Ergebnisse](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* Datensatz **Synchronisierungsregelaktion**: Dieser Protokolldatensatz zeigt die Ergebnisse der Attributzuordnungsregeln und konfigurierten Bereichsfilter sowie die Bereitstellungsaktion an, die zur Verarbeitung des eingehenden Workday-Ereignisses durchgeführt wird. Anhand der Informationen im Abschnitt *Zusätzliche Details* des Protokolldatensatzes können Sie Probleme mit der Synchronisierungsaktion beheben. Unten finden Sie einen Beispieldatensatz sowie Hinweise für die Interpretation der einzelnen Felder.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Wenn es Probleme mit Ihren Attributzuordnungsausdrücken gibt oder die eingehenden Workday-Daten Fehler enthalten (z.B. Leer- oder Nullwert für erforderliche Attribute), dann wird an dieser Stelle ein Fehler ausgegeben, wobei der ErrorCode Details über den Fehler liefert.

* Datensatz **AD-Export**: Dieser Protokolldatensatz zeigt das Ergebnis der AD-Kontoerstellung zusammen mit den Attributwerten, die im Prozess festgelegt wurden. Anhand der Informationen im Abschnitt *Zusätzliche Details* des Protokolldatensatzes können Sie Probleme mit der Kontoerstellung beheben. Unten finden Sie einen Beispieldatensatz sowie Hinweise für die Interpretation der einzelnen Felder. Im Abschnitt „Zusätzliche Details“ wird der „EventName“ auf „EntryExportAdd“, die „JoiningProperty“ auf den Wert des übereinstimmenden ID-Attributs, der „SourceAnchor“ auf die dem Datensatz zugeordnete WorkdayID (WID) und der „TargetAnchor“ auf den Wert des AD-Attributs „ObjectGuid“ des neu erstellten Benutzers festgelegt. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Um die Protokolldatensätze des Bereitstellungs-Agent zu finden, die diesem AD-Exportvorgang entsprechen, öffnen Sie die Protokolle der Windows-Ereignisanzeige, und verwenden Sie die Option **Suche....** , um Protokolleinträge mit dem Attributwert für die übereinstimmende ID/Verknüpfungseigenschaft zu suchen (in diesem Fall *21023*).  

  Suchen Sie nach einem HTTP POST-Datensatz, der dem Zeitstempel des Exportvorgangs mit *Event ID = 2* entspricht. Dieser Datensatz enthält die Attributwerte, die der Bereitstellungsdienst an den Bereitstellungs-Agent gesendet hat.

  :::image type="content" source="media/workday-inbound-tutorial/wd_event_viewer_05.png" alt-text="Der Screenshot zeigt den Datensatz „HTTP POST“ im Protokoll „Bereitstellungs-Agent“." lightbox="media/workday-inbound-tutorial/wd_event_viewer_05.png":::

  Unmittelbar nach dem obigen Ereignis sollte es ein weiteres Ereignis geben, das die Antwort des AD-Kontoerstellungsvorgangs erfasst. Dieses Ereignis gibt die neue „objectGuid“ zurück, die in AD erstellt wurde, und es wird als TargetAnchor-Attribut im Bereitstellungsdienst festgelegt.

  :::image type="content" source="media/workday-inbound-tutorial/wd_event_viewer_06.png" alt-text="Der Screenshot zeigt das Protokoll „Bereitstellungs-Agent“, in dem die in Azure AD erstellte „objectGuid“ hervorgehoben ist." lightbox="media/workday-inbound-tutorial/wd_event_viewer_06.png":::

### <a name="understanding-logs-for-manager-update-operations"></a>Grundlegendes zu Protokollen für Aktualisierungsvorgänge für Manager

Das Manager-Attribut ist ein Verweisattribut in Active Directory. Der Bereitstellungsdienst legt das Manager-Attribut nicht als Teil des Erstellungsvorgangs des Benutzers fest. Das Manager-Attribut wird stattdessen als Teil des *Aktualisierungsvorgangs* nach der AD-Kontoerstellung für den Benutzer festgelegt. Um das obige Beispiel zu erweitern, nehmen wir an, dass ein neuer Mitarbeiter mit der Mitarbeiter-ID „21451“ in Workday aktiviert ist und der Manager des neuen Mitarbeiters (*21023*) bereits ein AD-Konto hat. In diesem Szenario werden bei der Suche im Überwachungsprotokolle für Benutzer 21451 5 Einträge ausgegeben.

  [![Manager-Update](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

Die ersten 4 Datensätze sind wie diejenigen, die wir im Rahmen des Benutzererstellungsvorgangs untersucht haben. Der 5. Datensatz ist der Export, der dem Update des Manager-Attributs zugeordnet ist. Der Protokolldatensatz zeigt das Ergebnis der Aktualisierung des AD-Kontenmanagers an, die mit dem Attribut *objectGuid* des Managers durchgeführt wird.

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>Beheben von häufig auftretenden Fehlern

Dieser Abschnitt behandelt häufig auftretende Fehler bei der Bereitstellung von Workday-Benutzern und erläutert, wie Sie diese beheben können. Die Fehler werden wie folgt gruppiert:

* [Fehler beim Bereitstellungs-Agent](#provisioning-agent-errors)
* [Verbindungsfehler](#connectivity-errors)
* [Fehler bei der Erstellung des AD-Benutzerkontos](#ad-user-account-creation-errors)
* [Fehler bei der Aktualisierung des AD-Benutzerkontos](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Fehler beim Bereitstellungs-Agent

|#|Fehlerszenario |Mögliche Ursachen|Empfohlene Lösung|
|--|---|---|---|
|1.| Fehler bei der Installation des Bereitstellungs-Agent mit Fehlermeldung:  *Dienst „Microsoft Azure AD Connect Provisioning Agent“ (AADConnectProvisioningAgent) konnte nicht gestartet werden. Überprüfen Sie, ob Sie über ausreichend Rechte verfügen, um das System zu starten.* | Dieser Fehler tritt normalerweise auf, wenn Sie versuchen, den Bereitstellungs-Agent auf einem Domänencontroller zu installieren, und die Gruppenrichtlinie verhindert, dass der Dienst gestartet wird.  Es wird auch angezeigt, ob Sie eine frühere Version des Agent ausführen und ihn nicht deinstalliert haben, bevor Sie eine neue Installation starten.| Installieren Sie den Bereitstellungs-Agent auf einem Nicht-DC-Server. Stellen Sie sicher, dass frühere Versionen des Agents deinstalliert werden, bevor Sie den neuen Agent installieren.|
|2.| Der Windows-Dienst „Microsoft Azure AD Connect Provisioning Agent“ befindet sich im Zustand *Wird gestartet* und wechselt nicht in den Zustand *Wird ausgeführt*. | Im Rahmen der Installation erstellt der Agent-Assistent ein lokales Konto (**NT Service\\AADConnectProvisioningAgent**) auf dem Server. Dies ist das Anmeldekonto, das zum Starten des Diensts verwendet wird. Wenn eine Sicherheitsrichtlinie auf Ihrem Windows Server verhindert, dass lokale Konten die Dienste ausgeführt werden, wird dieser Fehler angezeigt. | Öffnen Sie die *Dienstkonsole*. Klicken Sie mit der rechten Maustaste auf den Windows-Dienst „Microsoft Azure AD Connect-Bereitstellungs-Agent“, und geben Sie auf der Registerkarte „Anmelden“ das Konto eines Domänenadministrators an, um den Dienst auszuführen. Starten Sie den Dienst neu. |
|3.| Wenn Sie den Bereitstellungs-Agent mit Ihrer AD-Domäne im Schritt *Active Directory verbinden* konfigurieren, dauert es lange, bis der Assistent versucht, das AD-Schema zu laden und schließlich abbricht. | Dieser Fehler wird in der Regel angezeigt, wenn der Assistent aufgrund von Firewallproblemen den Server des AD-Domänencontrollers nicht kontaktieren kann. | Auf dem Assistentenbildschirm *Active Directory verbinden* gibt es beim Bereitstellen der Anmeldeinformationen für Ihre AD-Domäne eine Option namens *Domänencontrollerpriorität auswählen*. Verwenden Sie diese Option, um einen Domänencontroller auszuwählen, der sich auf der gleichen Site wie der Agent-Server befindet, und stellen Sie sicher, dass es keine Firewallregeln gibt, die die Kommunikation blockieren. |

#### <a name="connectivity-errors"></a>Verbindungsfehler

Wenn der Bereitstellungsdienst keine Verbindung zu Workday oder Active Directory herstellen kann, kann dies dazu führen, dass die Bereitstellung in einen Quarantänezustand versetzt wird. Verwenden Sie die Tabelle unten zum Behandeln von Konnektivitätsproblemen.

|#|Fehlerszenario |Mögliche Ursachen|Empfohlene Lösung|
|--|---|---|---|
|1.| Beim Klicken auf **Verbindung testen** wird die folgende Fehlermeldung angezeigt: *Fehler beim Herstellen einer Verbindung mit Active Directory. Stellen Sie sicher, dass der lokale Bereitstellung-Agent ausgeführt wird und mit der richtigen Active Directory-Domäne konfiguriert ist.* | Dieser Fehler tritt in der Regel auf, wenn der Bereitstellungs-Agent nicht ausgeführt wird oder eine Firewall die Kommunikation zwischen Azure AD und dem Bereitstellungs-Agent blockiert. Dieser Fehler wird auch angezeigt, wenn die Domäne nicht im Agent-Assistenten konfiguriert ist. | Öffnen Sie die *Dienstkonsole* von Windows Server, um zu überprüfen, ob der Agent ausgeführt wird. Öffnen Sie den Assistenten des Bereitstellungs-Agent und überprüfen Sie, ob die richtige Domäne für den Agent registriert ist.  |
|2.| Der Bereitstellungsauftrag geht an den Wochenenden (Fr-Sa) in den Quarantänezustand und wir erhalten eine E-Mail-Benachrichtigung, dass ein Fehler bei der Synchronisation vorliegt. | Eine der häufigsten Ursachen für diesen Fehler ist die geplante Workday-Ausfallzeit. Wenn Sie einen Workday-Implementierungsmandanten verwenden, beachten Sie bitte, dass Workday für seine Implementierungsmandanten an Wochenenden Ausfallzeiten eingeplant hat (in der Regel von Freitagabend bis Samstagmorgen), und während dieses Zeitraums können die Workday-Bereitstellungs-Apps in den Quarantänezustand übergehen, da sie keine Verbindung zum Workday herstellen können. Sie kehrt in den Normalzustand zurück, sobald der Mandant der Workday-Implementierung wieder online ist. In seltenen Fällen kann dieser Fehler auch auftreten, wenn sich das Kennwort des Integrationssystembenutzers durch Mandantenaktualisierung geändert hat oder wenn sich das Konto im gesperrten oder abgelaufenen Zustand befindet. | Erkundigen Sie sich bei Ihrem Workday-Administrator oder Integrationspartner, wann Workday die Ausfallzeit plant, um Warnmeldungen während der Ausfallzeit zu ignorieren und die Verfügbarkeit zu bestätigen, sobald die Workday-Instanz wieder online ist.  |

#### <a name="ad-user-account-creation-errors"></a>Fehler bei der Erstellung des AD-Benutzerkontos

|#|Fehlerszenario |Mögliche Ursachen|Empfohlene Lösung|
|--|---|---|---|
|1.| Fehler beim Exportvorgang im Überwachungsprotokoll mit der Meldung *Fehler: OperationsError-SvcErr: Es ist ein Vorgangsfehler aufgetreten. Im Verzeichnisdienst wurde kein übergeordneter Verweis konfiguriert. Der Verzeichnisdienst kann daher keine Verweise auf Objekte außerhalb dieser Gesamtstruktur ausgeben.* | Dieser Fehler tritt in der Regel auf, wenn die Organisationseinheit *Active Directory-Container* nicht korrekt eingestellt ist oder wenn es Probleme mit der für *parentDistinguishedName* verwendeten Ausdruckszuordnung gibt. | Überprüfen Sie den Parameter der Organisationseinheit *Active Directory-Container* auf Tippfehler. Wenn Sie *parentDistinguishedName* in der Attributzuordnung verwenden, müssen Sie sicherstellen, das die Auswertung immer hinsichtlich eines bekannten Containers innerhalb der AD-Domäne ausgeführt wird. Überprüfen Sie im Ereignis *Export* des Überwachungsprotokolls den generierten Wert. |
|2.| Fehler beim Exportvorgang im Überwachungsprotokoll mit dem Fehlercode: *SystemForCrossDomainIdentityManagementBadResponse* und der Meldung *Fehler: ConstraintViolation-AtrErr: Ein Wert in der Anforderung ist ungültig. Ein Wert für das Attribut befindet sich nicht im zulässigen Wertebereich. \nFehlerdetails: CONSTRAINT_ATT_TYPE - company*. | Dieser Fehler wird zwar meist spezifisch für das Attribut *company* angezeigt, kann aber auch bei Attributen wie *CN* auftreten. Dieser Fehler tritt aufgrund von AD erzwungenen Schemaeinschränkungen auf. Standardmäßig gibt es für die Attribute wie *company* und *CN* in AD eine Obergrenze von 64 Zeichen. Wenn der aus Workday stammende Wert mehr als 64 Zeichen enthält, wird diese Fehlermeldung angezeigt. | Überprüfen Sie das Ereignis *Export* in den Überwachungsprotokollen, um den Wert für das in der Fehlermeldung gemeldete Attribut anzuzeigen. Sie sollten in Betracht ziehen, den aus Workday kommenden Wert mit der Funktion [Mid](../app-provisioning/functions-for-customizing-application-data.md#mid) zu kürzen oder die Zuordnungen in ein AD-Attribut zu ändern, das keine ähnlichen Längenbeschränkungen hat.  |

#### <a name="ad-user-account-update-errors"></a>Fehler bei der Aktualisierung des AD-Benutzerkontos

Während des Aktualisierungsprozesses des AD-Benutzerkontos liest der Bereitstellungsdienst Informationen sowohl vom Workday als auch vom AD, führt die Attributzuordnungsregeln aus und bestimmt, ob eine Änderung wirksam werden muss. Entsprechend wird ein Aktualisierungsereignis ausgelöst. Wenn bei einem dieser Schritte ein Fehler auftritt, wird dieser in den Überwachungsprotokollen protokolliert. Verwenden Sie die Tabelle unten zum Behandeln von häufigen Aktualisierungsfehlern.

|#|Fehlerszenario |Mögliche Ursachen|Empfohlene Lösung|
|--|---|---|---|
|1.| Fehler bei der Synchronisationsregelaktion im Überwachungsprotokoll mit der Meldung *EventName = EntrySynchronizationError and ErrorCode = EndpointUnavailable*. | Dieser Fehler wird angezeigt, wenn der Bereitstellungsdienst aufgrund eines Verarbeitungsfehlers beim lokalen Bereitstellungs-Agent nicht in der Lage ist, Benutzerprofildaten aus Active Directory abzurufen. | Überprüfen Sie die Protokolle der Ereignisanzeige des Bereitstellungs-Agent auf Fehlerereignisse, die auf Probleme mit dem Lesevorgang hinweisen (Filter nach Ereignis-ID #2). |
|2.| Das Manager-Attribut in AD wird für bestimmte Benutzer in AD nicht aktualisiert. | Dieser Fehler tritt am häufigsten auf, wenn Sie Bereichsregeln verwenden und der Manager des Benutzers nicht Teil des Bereichs ist. Dieses Problem kann auch dann auftreten, wenn das übereinstimmende ID-Attribut des Managers (z.B. EmployeeID) nicht in der AD-Zieldomäne gefunden oder nicht auf den richtigen Wert gesetzt wurde. | Überprüfen Sie den Bereichsfilter, und fügen Sie im Bereich den Managerbenutzer hinzu. Überprüfen Sie das Profil des Managers in AD, um sicherzustellen, dass es einen Wert für das übereinstimmende ID-Attribut gibt. |

## <a name="managing-your-configuration"></a>Verwalten Ihrer Konfiguration

Dieser Abschnitt beschreibt, wie Sie Ihre Workday-gesteuerte Konfiguration für die Benutzerbereitstellung weiter erweitern, anpassen und verwalten können. Es werden die folgenden Themen behandelt:

* [Anpassen der Liste der Workday-Benutzerattribute](#customizing-the-list-of-workday-user-attributes)  
* [Exportieren und Importieren Ihrer Konfiguration](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Anpassen der Liste der Workday-Benutzerattribute

Die Workday-Bereitstellungs-Apps für Active Directory und Azure AD umfassen jeweils eine Liste mit Workday-Benutzerattributen, aus denen Sie auswählen können. Diese Listen sind jedoch nicht sehr umfangreich. Workday unterstützt mehrere Hundert möglicher Benutzerattribute, die entweder als Standardattribute oder als eindeutige Attribute für Ihren Workday-Mandanten eingerichtet werden können.

Der Azure AD-Bereitstellungsdienst unterstützt die Möglichkeit, Ihre Liste der Workday-Attribute so anzupassen, dass alle im [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)-Vorgang der Human Resources-API verfügbar gemachten Attribute enthalten sind.

Um diese Änderung vorzunehmen, müssen Sie [Workday Studio](https://community.workday.com/studio-download) verwenden, um die XPath-Ausdrücke zu extrahieren, die die gewünschten Attribute repräsentieren. Anschließend verwenden Sie den erweiterten Attribut-Editor im Azure-Portal, um sie zu Ihrer Bereitstellungskonfiguration hinzuzufügen.

**So rufen Sie einen XPath-Ausdruck für ein Workday-Benutzerattribut ab:**

1. Laden Sie [Workday Studio](https://community.workday.com/studio-download), herunter, und installieren Sie es. Sie benötigen ein Workday-Communitykonto, um auf das Installationsprogramm zuzugreifen.

2. Laden Sie entsprechend der WWS-API-Version, die Sie aus dem [Workday Web Services-Verzeichnis](https://community.workday.com/sites/default/files/file-hosting/productionapi/index.html) verwenden möchten, die Workday-WSDL-Datei **Human_Resources** herunter.

3. Starten Sie Workday Studio.

4. Wählen Sie in der Befehlsleiste die Option **Workday > Test Web Service in Tester** (Workday > Webdienst im Tester testen) aus.

5. Wählen Sie **External** und dann die WSDL-Datei „Human_Resources“ aus, die Sie in Schritt 2 heruntergeladen haben.

    ![Der Screenshot zeigt die in Workday Studio geöffnete Datei „Human_Resources“.](./media/workday-inbound-tutorial/wdstudio1.png)

6. Legen Sie das Feld **Location** auf `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources` fest, ersetzen Sie jedoch „IMPL-CC“ durch den tatsächlichen Typ Ihrer Instanz und „TENANT“ durch den echten Namen Ihres Mandanten.

7. Legen Sie **Operation** auf **Get_Workers** fest.

8.    Klicken Sie auf den **configure**-Link unterhalb der Bereiche „Request“ (Anforderung) und „Response“ (Antwort), um Ihre Workday-Anmeldeinformationen festzulegen. Aktivieren Sie das Kontrollkästchen **Authentifizierung**, und geben Sie den Benutzernamen und das Kennwort für Ihr Systemkonto für die Workday-Integration ein. Stellen Sie sicher, dass der Benutzername das Format „name\@mandant“ aufweist, und behalten Sie die Auswahl der Option **WS-Security UsernameToken** bei.
   ![Der Screenshot zeigt die Registerkarte „Sicherheit“ mit Eingaben in „Benutzername“ und „Kennwort“ sowie ausgewählter Option „WS-Security Username Token“.](./media/workday-inbound-tutorial/wdstudio2.png)

9. Klicken Sie auf **OK**.

10. Fügen Sie im Bereich **Anforderung** den folgenden XML-Code ein. Legen Sie **Employee_ID** auf die Mitarbeiter-ID eines realen Benutzers in Ihrem Workday-Mandanten fest. Legen Sie **wd:version** auf die WWS-Version fest, die Sie verwenden möchten. Wählen Sie einen Benutzer aus, für den das Attribut aufgefüllt ist, das Sie extrahieren möchten.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
          <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```

11. Klicken Sie auf **Send Request** (Anforderung senden, grüner Pfeil), um den Befehl auszuführen. Bei erfolgreicher Ausführung wird die Antwort im Bereich **Response** angezeigt. Überprüfen Sie die Antwort, um sicherzustellen, dass sie die Daten des eingegebenen Benutzers enthält und fehlerfrei ist.

12. Wenn alles richtig ist, kopieren Sie den XML-Code aus dem Bereich **Response**, und speichern Sie ihn als XML-Datei.

13. Wählen Sie in der Befehlsleiste von Workday Studio **File > Open File...** (Datei > Datei öffnen...) aus, und öffnen Sie die gespeicherte XML-Datei. Durch diese Aktion wird die Datei im XML-Editor von Workday Studio geöffnet.

    ![Screenshot einer im XML-Editor von Workday Studio geöffneten XML-Datei.](./media/workday-inbound-tutorial/wdstudio3.png)

14. Navigieren Sie in der Dateistruktur durch **/env: Envelope > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Worker**, um die Daten zu Ihrem Benutzer zu finden.

15. Suchen Sie unter **wd: Worker** das Attribut, das Sie hinzufügen möchten, und wählen Sie es aus.

16. Kopieren Sie den XPath-Ausdruck für das ausgewählte Attribut aus dem Feld **Document Path** (Dokumentpfad).

17. Entfernen Sie das Präfix **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** aus dem kopierten Ausdruck.

18. Wenn es sich beim letzten Element im kopierten Ausdruck um einen Knoten handelt (z.B. „/wd: Birth_Date“), dann fügen Sie **/text()** an das Ende des Ausdrucks an. Dies ist nicht erforderlich, wenn das letzte Element ein Attribut ist (Beispiel: „/@wd: type“).

19. Das Ergebnis sollte in etwa wie folgt aussehen: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Diesen Wert kopieren Sie in das Azure-Portal.

**So fügen Sie Ihr benutzerdefiniertes Workday-Benutzerattribut zu Ihrer Bereitstellungskonfiguration hinzu:**

1. Starten Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zum Bereitstellungsbereich Ihrer Workday-Bereitstellungsanwendung, wie oben in diesem Tutorial beschrieben.

2. Legen Sie den **Bereitstellungsstatus** auf **Aus** fest, und klicken Sie dann auf **Speichern**. Mit diesem Schritt können Sie sicherstellen, dass Ihre Änderungen erst dann wirksam werden, wenn Sie bereit sind.

3. Wählen Sie unter **Zuordnungen** die Option **Workday-Worker mit lokalem Active Directory synchronisieren** (oder **Workday-Worker mit Azure AD synchronisieren**).

4. Scrollen Sie im nächsten Bildschirm nach unten, und wählen Sie **Erweiterte Optionen anzeigen** aus.

5. Wählen Sie **Attributliste für Workday bearbeiten** aus.

    ![Der Screenshot zeigt die Seite „Workday to Azure AD-Benutzerbereitstellung – Bereitstellung“ mit hervorgehobener Aktion „Attributliste für Workday bearbeiten“.](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Scrollen Sie zu den Eingabefeldern am Ende der Attributliste.

7. Geben Sie unter **Name** einen Anzeigenamen für Ihr Attribut ein.

8. Wählen Sie einen **Typ** aus, der zu Ihrem Attribut passt (**Zeichenfolge** ist der am häufigsten verwendete Typ).

9. Geben Sie unter **API-Ausdruck** den XPath-Ausdruck ein, den Sie aus Workday Studio kopiert haben. Beispiel: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Wählen Sie **Attribut hinzufügen** aus.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Wählen Sie oben **Speichern** aus, und klicken Sie im angezeigten Dialogfeld auf **Ja**. Schließen Sie den Bildschirm „Attributzuordnung“, falls dieser noch geöffnet ist.

12. Wählen Sie auf der Registerkarte **Bereitstellung** erneut die Option **Workday-Worker mit lokalem Active Directory synchronisieren** (oder **Worker mit Azure AD synchronisieren**).

13. Wählen Sie **Neue Zuordnung hinzufügen** aus.

14. Das neue Attribut sollte jetzt in der Liste **Quellattribut** angezeigt werden.

15. Fügen Sie wie gewünscht eine Zuordnung für Ihr neues Attribut hinzu.

16. Denken Sie nach Abschluss der Konfiguration daran, den **Bereitstellungsstatus** wieder auf **Ein** festzulegen und zu speichern.

### <a name="exporting-and-importing-your-configuration"></a>Exportieren und Importieren Ihrer Konfiguration

Entsprechende Informationen finden Sie im Artikel [Exportieren und Importieren der Bereitstellungskonfiguration](../app-provisioning/export-import-provisioning-configuration.md).

## <a name="managing-personal-data"></a>Verwalten von personenbezogenen Daten

Die Workday-Bereitstellungslösung für Active Directory erfordert die Installation eines Bereitstellungs-Agent auf einem lokalen Windows Server. Dieser Agent erstellt Protokolle im Windows-Ereignisprotokoll, die abhängig von den Attributzuordnungen von Workday zu AD personenbezogene Daten enthalten können. Um den Datenschutzverpflichtungen des Benutzers nachzukommen, können Sie sicherstellen, dass über 48 Stunden hinaus keine Daten in den Ereignisprotokollen gespeichert werden, indem Sie eine von Windows geplante Aufgabe zum Löschen des Ereignisprotokolls einrichten.

Der Azure AD-Bereitstellungsdienst fällt in die Kategorie **Datenverarbeitung** der DSGVO-Klassifizierung. Als eine Datenverarbeitungs-Pipeline stellt der Dienst Datenverarbeitungsdienste für wichtige Partner und Endbenutzer bereit. Der Azure AD-Bereitstellungsdienst generiert keine Benutzerdaten und kann nicht unabhängig steuern, welche personenbezogenen Daten erfasst und wie diese verwendet werden. Datenabruf, Aggregation, Analyse und Berichterstellung im Azure AD-Bereitstellungsdienst basieren auf vorhandenen Unternehmensdaten.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

In Bezug auf die Datenaufbewahrung erstellt der Azure AD-Bereitstellungsdienst keine Berichte, führt keine Analysen durch und liefert keine Einblicke über 30 Tage hinaus. Aus diesem Grund erfolgt im Azure AD-Bereitstellungsdienst keine Speicherung, Verarbeitung oder Beibehaltung von Daten über 30 Tage hinaus. Dieser Entwurf ist mit den DSGVO-Vorschriften, den Microsoft-Richtlinien für Datenschutz und Compliance und den Azure AD-Richtlinien zur Datenaufbewahrung konform.

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr zu Integrationsszenarien und Webdienstaufrufen für Azure AD und Workday.](../app-provisioning/workday-integration-reference.md)
* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
* [Lesen Sie, wie Sie das einmalige Anmelden zwischen Workday und Azure Active Directory konfigurieren.](workday-tutorial.md)
* [Konfigurieren von Workday Writeback](workday-writeback-tutorial.md)
* [Erfahren Sie, wie Sie Microsoft Graph-APIs verwenden, um die Bereitstellungskonfigurationen zu verwalten](/graph/api/resources/synchronization-overview)
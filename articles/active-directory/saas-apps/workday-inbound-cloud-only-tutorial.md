---
title: 'Tutorial: Konfigurieren der eingehenden Bereitstellung von Workday in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie die eingehende Bereitstellung von Workday in Azure AD konfigurieren.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: ef4381f305292b366348aa3729209dc3f5e8c87b
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954088"
---
# <a name="tutorial-configure-workday-to-azure-ad-user-provisioning"></a>Tutorial: Konfigurieren der Benutzerbereitstellung aus Workday in Azure AD
In diesem Tutorial werden die Schritte veranschaulicht, die Sie ausführen müssen, um Mitarbeiterdaten aus Workday in Azure Active Directory bereitzustellen. 

>[!NOTE]
>Verwenden Sie dieses Tutorial, wenn die Benutzer, die Sie aus Workday bereitstellen möchten, ausschließlich Cloudbenutzer sind und kein lokales Active Directory-Konto benötigen. Wenn die Benutzer nur ein lokales AD-Konto oder ein AD- und ein Azure AD-Konto benötigen, lesen Sie das Tutorial zum [Konfigurieren der Benutzerbereitstellung aus Workday in Active Directory](workday-inbound-tutorial.md). 

## <a name="overview"></a>Übersicht

Der [Azure Active Directory-Benutzerbereitstellungsdienst](../app-provisioning/user-provisioning.md) ist zum Bereitstellen von Benutzerkonten mit der [Workday Human Resources-API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) integriert. Die vom Azure AD-Benutzerbereitstellungsdienst unterstützten Workday-Workflows zur Benutzerbereitstellung ermöglichen die Automatisierung der folgenden Szenarien im Personalwesen und bei der Verwaltung des Lebenszyklus von Identitäten:

* **Einstellung neuer Mitarbeiter:** Wenn Workday ein neuer Mitarbeiter hinzugefügt wird, wird in Azure Active Directory und optional Microsoft 365 sowie in [anderen von Azure AD unterstützten SaaS-Anwendungen](../app-provisioning/user-provisioning.md) automatisch ein Benutzerkonto erstellt, und die E-Mail-Adresse wird in Workday zurückgeschrieben.

* **Aktualisierung von Mitarbeiterattributen und -profilen:** Wenn in Workday ein Mitarbeiterdatensatz aktualisiert wird (beispielsweise der Name, Titel oder Vorgesetzte), wird das entsprechende Benutzerkonto in Azure Active Directory und optional in Microsoft 365 und in [anderen, von Azure AD unterstützten SaaS-Anwendungen](../app-provisioning/user-provisioning.md) automatisch aktualisiert.

* **Kündigung von Mitarbeitern:** Wenn einem Mitarbeiter in Workday gekündigt wird, wird das entsprechende Benutzerkonto in Azure Active Directory und optional in Microsoft 365 und in [anderen, von Azure AD unterstützten SaaS-Anwendungen](../app-provisioning/user-provisioning.md) automatisch deaktiviert.

* **Wiedereinstellung von Mitarbeitern:** Wenn ein Mitarbeiter in Workday erneut eingestellt wird, kann sein altes Konto in Azure Active Directory und optional in Microsoft 365 und in [anderen, von Azure AD unterstützten SaaS-Anwendungen](../app-provisioning/user-provisioning.md) je nach Präferenz automatisch reaktiviert oder erneut bereitgestellt werden.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Für wen ist diese Benutzerbereitstellungslösung am besten geeignet?

Diese Lösung zur Benutzerbereitstellung von Workday in Azure Active Directory eignet sich ideal für:

* Organisationen, die eine vorgefertigte, cloudbasierte Lösung für die Workday-Benutzerbereitstellung verwenden möchten

* Organisationen, bei denen Benutzer direkt aus Workday in Azure Active Directory bereitgestellt werden müssen

* Organisationen, bei denen Benutzer mithilfe von Daten bereitgestellt werden müssen, die aus Workday abgerufen werden

* Organisationen, die Microsoft 365 für E-Mails verwenden

## <a name="solution-architecture"></a>Lösungsarchitektur

In diesem Abschnitt wird die Lösungsarchitektur der End-to-End-Benutzerbereitstellung für ausschließliche Cloudbenutzer beschrieben. Es gibt zwei zugehörige Flows:

* **Autoritativer Personaldatenfluss – von Workday zu Azure Active Directory**: In diesem Flow treten mitarbeiterbezogene Ereignisse (z. B. Neueinstellungen, Wechsel, Kündigungen) zuerst in Workday auf und werden dann in Azure Active Directory übertragen. Abhängig vom Ereignis kann dies dann in Azure AD zu Erstellungs-, Aktualisierungs-, Aktivierungs- oder Deaktivierungsvorgängen führen.
* **Rückschreibedatenfluss – aus lokalem Active Directory zu Workday**: Nach Abschluss der Kontoerstellung in Active Directory wird das Konto über Azure AD Connect mit Azure AD synchronisiert. Anschließend kann für Informationen wie E-Mail-Adresse, Benutzername und Telefonnummer das Rückschreiben in Workday durchgeführt werden.

  ![Übersicht](./media/workday-inbound-tutorial/workday-cloud-only-provisioning.png)

### <a name="end-to-end-user-data-flow"></a>End-to-End-Benutzerdatenfluss

1. Das Team der Personalabteilung führt Mitarbeitertransaktionen (Einstellungen/Wechsel/Kündigungen) in Workday Employee Central aus.
2. Der Azure AD-Bereitstellungsdienst führt geplante Synchronisierungen von Identitäten aus Workday EC aus und ermittelt Änderungen, die für eine Synchronisierung mit dem lokalen Active Directory verarbeitet werden müssen.
3. Der Azure AD-Bereitstellungsdienst ermittelt die Änderung und ruft den entsprechenden Vorgang zum Erstellen, Aktualisieren, Aktivieren oder Deaktivieren für den Benutzer in Azure AD auf.
4. Wenn die App für das [Rückschreiben in Workday](workday-writeback-tutorial.md) konfiguriert wurde, werden damit Attribute wie E-Mail-Adresse, Benutzername und Telefonnummer aus Azure AD abgerufen. 
5. Der Azure AD-Bereitstellungsdienst legt E-Mail-Adresse, Benutzername und Telefonnummer in Workday fest.

## <a name="planning-your-deployment"></a>Planen der Bereitstellung

Das Konfigurieren einer cloudbasierten Benutzerbereitstellung für die Personalabteilung aus Workday in Azure AD erfordert eine ausführliche Planung, die unter anderem die folgenden Aspekte abdecken sollte:

* Ermittlung der Übereinstimmungs-ID 
* Attributzuordnung
* Attributtransformation 
* Bereichsfilter

Ausführliche Anleitungen zu diesen Themen finden Sie unter [Planen der HR-Cloudbereitstellung](../app-provisioning/plan-cloud-hr-provision.md). 

## <a name="configure-integration-system-user-in-workday"></a>Konfigurieren eines Integrationssystembenutzers in Workday

Im Abschnitt [Konfigurieren des Integrationssystembenutzers](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) finden Sie Informationen zum Erstellen eines Benutzerkontos für das Workday-Integrationssystem mit Berechtigungen zum Abrufen von Beschäftigtendaten. 

## <a name="configure-user-provisioning-from-workday-to-azure-ad"></a>Konfigurieren der Benutzerbereitstellung aus Workday in Azure AD

Die folgenden Abschnitte beschreiben Schritte zur Konfiguration der Benutzerbereitstellung von Workday zu Azure AD für reine Cloudbereitstellungen.

* [Hinzufügen der Bereitstellungsconnector-App für Azure AD und Herstellen der Verbindung mit Workday](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Konfigurieren von Workday und Azure AD-Attributzuordnungen](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Aktivieren und Starten der Benutzerbereitstellung](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Teil 1: Hinzufügen der Bereitstellungsconnector-App für Azure AD und Herstellen der Verbindung mit Workday

**So konfigurieren Sie die Workday-zu-Azure Active Directory-Bereitstellung für reine Cloudbenutzer**

1. Gehe zu <https://portal.azure.com>.

2. Suchen Sie im Azure-Portal nach **Azure Active Directory**, und wählen Sie es aus.

3. Klicken Sie auf **Unternehmensanwendungen** und dann auf **Alle Anwendungen**.

4. Klicken Sie auf **Anwendung hinzufügen**, und wählen Sie dann die Kategorie **Alle** aus.

5. Suchen Sie nach **Workday to Azure AD user provisioning**, und fügen Sie die App aus dem Katalog hinzu.

6. Sobald die App hinzugefügt wurde und der Bildschirm mit den App-Details angezeigt wird, wählen Sie **Bereitstellung** aus.

7. Ändern Sie den **Bereitstellungsmodus** in **Automatisch**.

8. Vervollständigen Sie den Abschnitt **Administratoranmeldeinformationen** wie folgt:

   * **Workday-Benutzername:** Geben Sie den Benutzernamen des Workday-Systemintegrationskontos mit angefügtem Mandantendomänennamen ein. Dies sollte ungefähr wie folgt aussehen: username@contoso4

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
     > <br> Wenn Sie eine WWS-API der Version 30.0 oder höher verwenden, müssen Sie vor der Aktivierung des Bereitstellungsauftrags unter **Attributzuordnung > Erweiterte Optionen > Attributliste für Workday bearbeiten** die **XPath-API-Ausdrücke** aktualisieren (entsprechende Verweise finden Sie im Abschnitt [Verwalten Ihrer Konfiguration](workday-inbound-tutorial.md#managing-your-configuration) und in der [Workday-Attributreferenz](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)).  

   * **Benachrichtigungs-E-Mail**: Geben Sie Ihre E-Mail-Adresse ein, und aktivieren Sie das Kontrollkästchen „E-Mail senden, wenn Fehler auftritt“.

   * Klicken Sie auf die Schaltfläche **Verbindung testen**.

   * Wenn der Verbindungstest erfolgreich ist, klicken Sie oben auf die Schaltfläche **Speichern**. Falls nicht, überprüfen Sie, ob die Workday-URL und -Anmeldeinformationen in Workday gültig sind.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Teil 2: Konfigurieren von Workday und Azure AD-Attributzuordnungen

In diesem Abschnitt konfigurieren Sie den Fluss von Benutzerdaten aus Workday in Azure Active Directory für reine Cloudbenutzer.

1. Klicken Sie auf der Registerkarte „Bereitstellung“ unter **Zuordnungen** auf **Mitarbeiter in Azure AD synchronisieren**.

2. Im Feld **Quellobjektbereich** können Sie die Benutzergruppen in Workday für die Bereitstellung in Azure Active Directory auswählen, indem Sie verschiedene attributbasierte Filter definieren. Der Standardbereich ist „Alle Benutzer in Workday“. Beispielfilter:

   * Beispiel: Auswählen der Benutzer mit Mitarbeiter-IDs von 1000000 bis 2000000

      * Attribut: WorkerID

      * Operator: REGEX Match

      * Wert: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Beispiel: Nur vorübergehend Beschäftigte und keine regulären Mitarbeiter

      * Attribut: ContingentID

      * Operator: IS NOT NULL

3. Im Feld **Zielobjektaktionen** können Sie global filtern, welche Aktionen auf Azure AD angewendet werden. **Erstellen** und **Aktualisieren** erfolgen am häufigsten.

4. Im Abschnitt **Attributzuordnungen** können Sie definieren, wie einzelne Workday-Attribute Active Directory-Attributen zugeordnet werden.

5. Klicken Sie auf eine vorhandene Attributzuordnung, um sie zu aktualisieren. Oder klicken Sie am unteren Bildschirmrand auf **Neue Zuordnung hinzufügen**, um neue Zuordnungen hinzuzufügen. Eine einzelne Attributzuordnung unterstützt die folgenden Eigenschaften:

   * **Zuordnungstyp**

      * **Direkt**: Schreibt den Wert des Workday-Attributs unverändert in das AD-Attribut.

      * **Konstant**: Schreibt einen statischen, konstanten Zeichenfolgenwert in das AD-Attribut.

      * **Ausdruck**: Ermöglicht das Schreiben eines benutzerdefinierten Werts basierend auf einem oder mehreren Workday-Attributen in das AD-Attribut. [Weitere Informationen finden Sie im Artikel zu Ausdrücken](../app-provisioning/functions-for-customizing-application-data.md).

   * **Quellattribut**: Das Benutzerattribut aus Workday. Wenn das gesuchte Attribut nicht vorhanden ist, finden Sie weitere Informationen unter [Anpassen der Liste der Workday-Benutzerattribute](workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes).

   * **Standardwert**: Optional. Wenn das Quellattribut einen leeren Wert aufweist, wird von der Zuordnung stattdessen dieser Wert geschrieben.
            Die meisten Konfigurationen sehen vor, dieses Feld leer zu lassen.

   * **Zielattribut**: Das Benutzerattribut in Azure AD.

   * **Objekte mit diesem Attribut abgleichen**: Gibt an, ob dieses Attribut zum eindeutigen Bestimmen von Benutzern zwischen Workday und Azure AD verwendet werden soll. Diese Wert wird meist auf das Feld „Worker-ID“ für Workday festgelegt, das in der Regel dem „Worker-ID“-Attribut (neu) oder einem Erweiterungsattribut in Azure AD zugeordnet wird.

   * **Rangfolge für Abgleich**: Es können mehrere Attribute für den Abgleich festgelegt werden. Falls mehrere vorhanden sind, werden sie entsprechend der in diesem Feld festgelegten Reihenfolge ausgewertet. Sobald eine Übereinstimmung gefunden wird, werden keine weiteren Attribute für den Abgleich mehr ausgewertet.

   * **Diese Zuordnung anwenden**

     * **Immer**: Wenden Sie diese Zuordnung sowohl bei der Aktion zum Erstellen eines Benutzers als auch bei der zum Aktualisieren eines Benutzers an.

     * **Nur während der Erstellung**: Wenden Sie diese Zuordnung nur bei der Aktion zum Erstellen eines Benutzers an.

6. Klicken Sie oben im Abschnitt „Attributzuordnung“ auf **Speichern**, um Ihre Zuordnungen zu speichern.


## <a name="enable-and-launch-user-provisioning"></a>Aktivieren und Starten der Benutzerbereitstellung

Nachdem die Konfiguration der Workday-Bereitstellungs-App abgeschlossen ist, können Sie den Bereitstellungsdienst im Azure-Portal aktivieren.

> [!TIP]
> Beim Aktivieren des Bereitstellungsdiensts werden Bereitstellungsvorgänge für alle Benutzer im Bereich initiiert. Wenn Fehler in der Zuordnung oder Workday-Datenprobleme auftreten, kann der Bereitstellungsauftrag fehlschlagen und in den Quarantänezustand wechseln. Um dies zu vermeiden, empfehlen wir, den Filter **Quellobjektbereich** zu konfigurieren und Ihre Attributzuordnungen mit einigen Testbenutzern zu testen, bevor Sie die vollständige Synchronisierung für alle Benutzer starten. Sobald Sie sich vergewissert haben, dass die Zuordnungen funktionieren und Sie die gewünschten Ergebnisse erhalten, können Sie den Filter entweder entfernen oder schrittweise erweitern, um mehr Benutzer einzubinden.

1. Legen Sie auf der Registerkarte **Bereitstellung** die Einstellung **Bereitstellungsstatus** auf **Ein** fest.

2. Klicken Sie auf **Speichern**.

3. Dieser Vorgang startet die erste Synchronisierung, die abhängig von der Anzahl der Benutzer im Workday-Mandanten eine variable Anzahl von Stunden dauern kann. Sie können die Statusanzeige überprüfen, um den Fortschritt des Synchronisierungszyklus zu verfolgen. 

4. Im Azure-Portal können Sie sich auf der Registerkarte **Überwachungsprotokolle** jederzeit ansehen, welche Aktionen der Bereitstellungsdienst ausgeführt hat. Die Überwachungsprotokolle enthalten alle einzelnen Synchronisierungsereignisse des Bereitstellungsdiensts – beispielsweise, welche Benutzer in Workday gelesen und anschließend Azure Active Directory hinzugefügt oder dort aktualisiert wurden. 

5. Nach Abschluss der ersten Synchronisierung wird auf der Registerkarte **Bereitstellung** ein Überwachungszusammenfassungsbericht ausgegeben:

   > [!div class="mx-imgBorder"]
   > ![Statusanzeige für die Bereitstellung](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr zu Integrationsszenarien und Webdienstaufrufen für Azure AD und Workday.](../app-provisioning/workday-integration-reference.md)
* [Weitere Informationen zu unterstützten Workday-Attributen für die eingehende Bereitstellung](../app-provisioning/workday-attribute-reference.md)
* [Konfigurieren von Workday Writeback](workday-writeback-tutorial.md)
* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
* [Lesen Sie, wie Sie das einmalige Anmelden zwischen Workday und Azure Active Directory konfigurieren.](workday-tutorial.md)
* [Weitere Informationen zum Exportieren und Importieren von Bereitstellungskonfigurationen](../app-provisioning/export-import-provisioning-configuration.md)



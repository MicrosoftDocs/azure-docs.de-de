---
title: Übersicht über Bereitstellungsprotokolle im Azure-Portal (Vorschau) | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Einführung in Bereitstellungsprotokollberichte in Azure Active Directory im Azure-Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 1/29/2021
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad69df37d2635156873dc59d6fbf700a67ade548
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091931"
---
# <a name="overview-of-provisioning-logs-in-the-azure-portal-preview"></a>Übersicht über Bereitstellungsprotokolle im Azure-Portal (Vorschau)

Die Architektur für die Berichterstellung in Azure Active Directory (Azure AD) umfasst die folgenden Komponenten:

- Aktivität: 
    - **Anmeldungen:** Informationen zur Nutzung von verwalteten Anwendungen und Benutzeranmeldungsaktivitäten.
    - [Überwachungsprotokolle](concept-audit-logs.md): Systemaktivitätsinformationen zu Benutzern und zur Gruppenverwaltung, zu verwalteten Anwendungen und Verzeichnisaktivitäten.
    - **Bereitstellungsprotokolle:** Systemaktivitäten im Zusammenhang mit Benutzern, Gruppen und Rollen, die mit dem Azure AD-Bereitstellungsdienst bereitgestellt werden. 

- Sicherheit: 
    - **Risikoanmeldungen:** Eine [Risikoanmeldung](../identity-protection/overview-identity-protection.md) ist ein Hinweis auf einen Anmeldeversuch einer Person, die nicht der rechtmäßige Besitzer eines Benutzerkontos ist.
    - **Benutzer mit Risikokennzeichnung:** Ein [Risikobenutzer](../identity-protection/overview-identity-protection.md) ist ein Hinweis auf ein möglicherweise gefährdetes Benutzerkonto.

In diesem Thema finden Sie eine Übersicht über Bereitstellungsprotokolle. Die Protokolle enthalten Antworten auf Fragen wie: 

* Welche Gruppen wurden erfolgreich in ServiceNow erstellt?
* Welche Benutzer wurden erfolgreich aus Adobe entfernt?
* Welche Benutzer aus Workday wurden in Active Directory erfolgreich erstellt? 

## <a name="prerequisites"></a>Voraussetzungen

Folgende Benutzer können auf die Daten in Bereitstellungsprotokollen zugreifen:

* Anwendungsbesitzer (Protokolle für ihre eigenen Anwendungen)
* Benutzer mit den Rollen „Sicherheitsadministrator“, „Sicherheitsleseberechtigter“, „Berichtsleser“, „Sicherheitsoperator“, „Anwendungsadministrator“ und „Cloudanwendungsadministrator“
* Benutzer in einer benutzerdefinierten Rolle mit der Berechtigung [provisioningLogs](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)
* Globale Administratoren


Ihrem Mandanten muss eine Azure AD Premium-Lizenz zugeordnet sein, damit Sie den Bereitstellungsaktivitätsbericht anzeigen können. Informationen zum Upgraden Ihrer Azure AD-Edition finden Sie unter [Registrieren für Azure Active Directory Premium-Editionen](../fundamentals/active-directory-get-started-premium.md). 


## <a name="ways-of-interacting-with-the-provisioning-logs"></a>Möglichkeiten der Interaktion mit den Bereitstellungsprotokollen 
Kunden können auf vier Arten mit Bereitstellungsprotokollen interagieren:

- Zugreifen auf die Protokolle über das Azure-Portal (wie im nächsten Abschnitt beschrieben)
- Streamen der Bereitstellungsprotokolle an [Azure Monitor](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-log-analytics). Diese Methode ermöglicht eine längere Datenaufbewahrung sowie die Erstellung benutzerdefinierter Dashboards, Warnungen und Abfragen.
- Abfragen der [Microsoft Graph-API](https://docs.microsoft.com/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta) für die Bereitstellungsprotokolle
- Herunterladen der Bereitstellungsprotokolle als CSV- oder JSON-Datei

## <a name="access-the-logs-from-the-azure-portal"></a>Zugreifen auf die Protokolle über das Azure-Portal
Sie können auf die Bereitstellungsprotokolle zugreifen, indem Sie im [Azure-Portal](https://portal.azure.com) im Bereich **Azure Active Directory** im Abschnitt **Überwachung** die Option **Bereitstellungsprotokolle** auswählen. Bei einigen Bereitstellungsdatensätzen kann es bis zu zwei Stunden dauern, bis sie im Portal angezeigt werden.

![Screenshot: Auswahloptionen für den Zugriff auf Bereitstellungsrichtlinien](./media/concept-provisioning-logs/access-provisioning-logs.png "Bereitstellungsprotokolle")


Ein Bereitstellungsprotokoll enthält eine Standardlistenansicht mit folgenden Informationen:

- Identität
- Aktion
- Quellsystem
- Zielsystem
- Status
- Datum


![Screenshot: Standardspalten in einem Bereitstellungsprotokoll](./media/concept-provisioning-logs/default-columns.png "Standardspalten")

Die Listenansicht kann durch Auswählen der Symbolleistenoption **Spalten** angepasst werden.

![Screenshot: Schaltfläche zum Anpassen von Spalten](./media/concept-provisioning-logs/column-chooser.png "Spaltenauswahl")

In diesem Bereich können Sie weitere Felder anzeigen oder bereits angezeigte Felder entfernen.

![Screenshot: Verfügbare Spalten mit einigen ausgewählten Elementen](./media/concept-provisioning-logs/available-columns.png "Verfügbare Spalten")

Wählen Sie in der Listenansicht ein Element aus, um ausführlichere Informationen zu erhalten.

![Screenshot: Ausführliche Informationen](./media/concept-provisioning-logs/steps.png "Filtern")


## <a name="filter-provisioning-activities"></a>Filtern von Bereitstellungsaktivitäten

Sie können Ihre Bereitstellungsdaten filtern. Einige Filterwerte werden basierend auf Ihrem Mandanten dynamisch aufgefüllt. Sind im Mandanten beispielsweise keine Erstellungsereignisse vorhanden, steht keine Filteroptionen vom Typ **Erstellen** zur Verfügung.

In der Standardansicht können Sie die folgenden Filter auswählen:

- **Identität**
- **Datum**
- **Status**
- **Aktion**


![Screenshot: Filterwerte](./media/concept-provisioning-logs/default-filter.png "Filtern")

Mit dem Filter **Identität** können Sie den Namen oder die Identität angeben, der bzw. die für Sie relevant ist. Diese Identität kann ein Benutzer, eine Gruppe, eine Rolle oder ein anderes Objekt sein. 

Sie können nach dem Namen oder der ID des Objekts suchen. Die ID variiert je nach Szenario. Wenn Sie beispielsweise ein Objekt aus Azure AD in Salesforce bereitstellen, ist die Quell-ID die Objekt-ID des Benutzers in Azure AD. Die Ziel-ID ist die ID des Benutzers in Salesforce. Wenn Sie ein Objekt aus Workday in Active Directory bereitstellen, ist die Quell-ID die Mitarbeiter-ID des Workday-Mitarbeiters. 

> [!NOTE]
> Der Name des Benutzers ist möglicherweise nicht immer in der Spalte **Identität** enthalten. Es gibt jedoch immer eine ID. 


Mit dem Filter **Datum** können Sie einen Zeitrahmen für die zurückgegebenen Daten festlegen. Mögliche Werte:

- 1 Monat
- 7 Tage
- 30 Tage
- 24 Stunden
- Benutzerdefiniertes Zeitintervall

Beim Auswählen eines benutzerdefinierten Zeitraums können Sie ein Startdatum und ein Enddatum konfigurieren.

Für den Filter **Zustand** können Sie eine der folgenden Optionen auswählen:

- **Alle**
- **Erfolgreich**
- **Fehler**
- **Übersprungen**

Mit dem Filter **Aktion** können folgende Aktionen gefiltert werden:

- **Erstellen** 
- **Aktualisieren**
- **Löschen**
- **Deaktivieren**
- **Andere**

Neben den Filtern in der Standardansicht können auch folgende Filter festgelegt werden:

![Screenshot: Felder, die als Filter hinzugefügt werden können](./media/concept-provisioning-logs/add-filter.png "Feld auswählen")

- **Auftrags-ID**: Jeder Anwendung, für die Sie die Bereitstellung aktiviert haben, ist eine eindeutige Auftrags-ID zugeordnet.   

- **Zyklus-ID**: Durch die Zyklus-ID wird der Bereitstellungszyklus eindeutig identifiziert. Anhand dieser ID kann der Produktsupport bei Bedarf nach dem Zyklus suchen, in dem das Ereignis aufgetreten ist.

- **Änderungs-ID**: Die Änderungs-ID ist ein eindeutiger Bezeichner für das Bereitstellungsereignis. Anhand dieser ID kann der Produktsupport bei Bedarf nach dem Bereitstellungsereignis suchen.   

- **Quellsystem**: Sie können angeben, von wo aus die Identität bereitgestellt wird. Wenn Sie also beispielsweise ein Objekt aus Azure AD in ServiceNow bereitstellen, ist Azure AD das Quellsystem. 

- **Zielsystem**: Sie können angeben, wo die Identität bereitgestellt wird. Wenn Sie also beispielsweise ein Objekt aus Azure AD in ServiceNow bereitstellen, ist ServiceNow das Zielsystem. 

- **Anwendung**: Sie können die Anzeige auf Datensätze von Anwendungen mit einem Anzeigenamen beschränken, der eine bestimmte Zeichenfolge enthält.

## <a name="provisioning-details"></a>Bereitstellungsdetails 

Wenn Sie ein Element in der Bereitstellungslistenansicht auswählen, erhalten Sie weitere Informationen zu diesem Element. Die Details werden zu folgenden Registerkarten gruppiert:

![Screenshot: Vier Registerkarten mit Bereitstellungsdetails](./media/concept-provisioning-logs/provisioning-tabs.png "Registerkarten")

- **Schritte**: Hier sind die ausgeführten Schritte für die Objektbereitstellung aufgeführt. Die Bereitstellung eines Objekts kann aus vier Schritten bestehen:
  
  1. Importieren des Objekts
  1. Ermitteln, ob sich das Objekt innerhalb des Gültigkeitsbereichs befindet
  1. Abgleichen des Objekts zwischen Quelle und Ziel
  1. Bereitstellen des Objekts (Erstellen, Aktualisieren, Löschen oder Deaktivieren)

  ![Screenshot: Registerkarte „Schritte“ mit den Bereitstellungsschritten](./media/concept-provisioning-logs/steps.png "Filtern")

- **Problembehandlung und Empfehlungen**: Enthält den Fehlercode und den Grund für den Fehler. Die Fehlerinformationen sind nur verfügbar, wenn ein Fehler auftritt.

- **Geänderte Eigenschaften**: Enthält den alten und den neuen Wert. Sollte kein alter Wert vorhanden sein, ist diese Spalte leer.

- **Zusammenfassung**: Enthält eine Übersicht über die Vorgänge und die Bezeichner für das Objekt im Quell- und Zielsystem.

## <a name="download-logs-as-csv-or-json"></a>Herunterladen von Protokollen als CSV- oder JSON-Datei

Sie können die Bereitstellungsprotokolle zur späteren Verwendung herunterladen. Navigieren Sie hierzu im Azure-Portal zu den Protokollen, und wählen Sie **Herunterladen** aus. Die Datei wird basierend auf den ausgewählten Filterkriterien gefiltert. Verwenden Sie möglichst spezifische Filter, um Größe und Dauer des Downloads zu verringern. 

Der CSV-Download umfasst drei Dateien:

* **ProvisioningLogs**: Lädt alle Protokolle mit Ausnahme der Bereitstellungsschritte und der geänderten Eigenschaften herunter
* **ProvisioningLogs_ProvisioningSteps**: Enthält die Bereitstellungsschritte und die Änderungs-ID. Über die Änderungs-ID kann das Ereignis mit den anderen beiden Dateien verknüpft werden.
* **ProvisioningLogs_ModifiedProperties**: Enthält die geänderten Attribute und die Änderungs-ID. Über die Änderungs-ID kann das Ereignis mit den anderen beiden Dateien verknüpft werden.

#### <a name="open-the-json-file"></a>Öffnen der JSON-Datei
Verwenden Sie zum Öffnen der JSON-Datei einen Text-Editor wie [Microsoft Visual Studio Code](https://aka.ms/vscode). Visual Studio Code vereinfacht das Lesen der Datei mittels Syntaxhervorhebung. Alternativ kann die JSON-Datei auch in einem Browser wie [Microsoft Edge](https://aka.ms/msedge) in einem nicht bearbeitbaren Format geöffnet werden. 

#### <a name="prettify-the-json-file"></a>Verbessern der Lesbarkeit der JSON-Datei
Die JSON-Datei wird in einem sehr minimalistischen Format heruntergeladen, um die Größe des Downloads zu verringern. In diesem Format sind die Nutzdaten unter Umständen nur schwer lesbar. Es gibt zwei Optionen zum Formatieren der Datei:

- [Formatieren des JSON-Codes mit Visual Studio Code](https://code.visualstudio.com/docs/languages/json#_formatting)

- Formatieren des JSON-Codes mit PowerShell. Das folgende Skript gibt den JSON-Code in einem Format mit Tabulatoren und Leerzeichen aus: 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>Analysieren der JSON-Datei

Im Anschluss finden Sie einige Beispielbefehle für die Verwendung der JSON-Datei mit PowerShell. Sie können Ihre bevorzugte Programmiersprache verwenden.  

Führen Sie zum [Lesen der JSON-Datei](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json?view=powershell-7.1) zunächst den folgenden Befehl aus:

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

Nun können Sie die Daten gemäß Ihrem Szenario analysieren. Hier sind einige Beispiele angegeben: 

- Ausgeben aller Auftrags-IDs in der JSON-Datei:

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- Ausgeben aller Änderungs-IDs für Ereignisse mit der Aktion „Create“ (Erstellen):

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>Wichtige Informationen

Hier finden Sie einige Tipps und Überlegungen zu Bereitstellungsberichten:

- Gemeldete Bereitstellungsdaten werden bei Verwendung einer Premium-Edition 30 Tage und bei Verwendung einer kostenlosen Edition sieben Tage im Azure-Portal gespeichert. Wenn Sie die Bereitstellungsprotokolle länger als 30 Tage aufbewahren möchten, können Sie sie in [Log Analytics](../app-provisioning/application-provisioning-log-analytics.md) veröffentlichen. 

- Das Änderungs-ID-Attribut kann als eindeutiger Bezeichner verwendet werden. Das ist beispielsweise bei der Kommunikation mit dem Produktsupport hilfreich.

- Unter Umständen werden übersprungene Ereignisse für Benutzer aufgeführt, die nicht dem Gültigkeitsbereich angehören. Dies entspricht dem erwarteten Verhalten, insbesondere dann, wenn der Synchronisierungsbereich auf alle Benutzer und Gruppen festgelegt ist. Der Dienst wertet alle Objekte im Mandanten aus – auch solche, die außerhalb des Gültigkeitsbereichs liegen. 

- Die Bereitstellungsprotokolle sind zurzeit in der Government-Cloud nicht verfügbar. Sollten Sie nicht auf die Bereitstellungsprotokolle zugreifen können, verwenden Sie als vorübergehende Problemumgehung die Überwachungsprotokolle. 

- Die Bereitstellungsprotokolle enthalten keine Rollenimporte (gilt für AWS, Salesforce und Zendesk). Die Protokolle für Rollenimporte finden Sie in den Überwachungsprotokollen. 

## <a name="error-codes"></a>Fehlercodes

Die folgende Tabelle ist bei der Behebung von Fehlern hilfreich, die in den Bereitstellungsprotokollen enthalten sein können. Verwenden Sie die Links am Ende der Seite, um Feedback für fehlende Fehlercodes bereitzustellen. 

|Fehlercode|BESCHREIBUNG|
|---|---|
|Conflict, EntryConflict|Korrigieren Sie die in Konflikt stehenden Attributwerte entweder in Azure AD oder in der Anwendung. Oder: Überprüfen Sie die Konfiguration der übereinstimmenden Attribute, wenn das in Konflikt stehende Benutzerkonto hätte abgeglichen und übernommen werden sollen. Weitere Informationen zum Konfigurieren von übereinstimmenden Attributen finden Sie in der [Dokumentation](../app-provisioning/customize-application-attributes.md).|
|TooManyRequests|Die Ziel-App hat diesen Versuch zum Aktualisieren des Benutzers abgelehnt, da sie überlastet ist und zu viele Anforderungen empfängt. In diesem Fall ist keine Aktion erforderlich. Dieser Versuch wird automatisch wiederholt. Außerdem wurde Microsoft über dieses Problem informiert.|
|InternalServerError |Die Ziel-App hat einen unerwarteten Fehler zurückgegeben. Möglicherweise liegt ein Dienstproblem mit der Zielanwendung vor. Dieser Versuch wird automatisch in 40 Minuten wiederholt.|
|InsufficientRights, MethodNotAllowed, NotPermitted, Unauthorized| Azure AD wurde zwar bei der Zielanwendung authentifiziert, war aber nicht zum Ausführen der Aktualisierung autorisiert. Lesen Sie alle ggf. von der Zielanwendung bereitgestellten Anweisungen sowie das entsprechende [Tutorial](../saas-apps/tutorial-list.md) für die Anwendung.|
|UnprocessableEntity|Die Zielanwendung hat eine unerwartete Antwort zurückgegeben. Die Konfiguration der Zielanwendung ist möglicherweise nicht korrekt, oder es liegt ein Dienstproblem mit der Zielanwendung vor.|
|WebExceptionProtocolError |Bei der Verbindungsherstellung mit der Zielanwendung ist ein HTTP-Protokollfehler aufgetreten. Sie brauchen nichts zu tun. Dieser Versuch wird automatisch in 40 Minuten wiederholt.|
|InvalidAnchor|Ein Benutzer, der zuvor vom Bereitstellungsdienst erstellt oder abgeglichen wurde, ist nicht mehr vorhanden. Stellen Sie sicher, dass der Benutzer vorhanden ist. Um einen erneuten Abgleich aller Benutzer zu erzwingen, können Sie mithilfe der Microsoft Graph-API den [Auftrag neu starten](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). <br><br>Durch den Neustart der Bereitstellung wird ein Startzyklus auslöst, der einige Zeit dauern kann. Außerdem wird der vom Bereitstellungsdienst verwendete Cache gelöscht. Das bedeutet, dass alle Benutzer und Gruppen im Mandanten erneut ausgewertet werden müssen, und bestimmte Bereitstellungsereignisse werden möglicherweise verworfen.|
|NotImplemented | Die Ziel-App hat eine unerwartete Antwort zurückgegeben. Die Konfiguration der App ist möglicherweise nicht korrekt, oder es liegt ein Dienstproblem mit der Ziel-App vor. Lesen Sie alle ggf. von der Zielanwendung bereitgestellten Anweisungen sowie das entsprechende [Tutorial](../saas-apps/tutorial-list.md) für die Anwendung. |
|MandatoryFieldsMissing, MissingValues |Der Benutzer konnte nicht erstellt werden, da erforderliche Werte fehlten. Korrigieren Sie die fehlenden Attributwerte im Quelldatensatz, oder überprüfen Sie die Konfiguration der übereinstimmenden Attribute, um sicherzustellen, dass die erforderlichen Felder nicht ausgelassen wurden. [Erfahren Sie mehr](../app-provisioning/customize-application-attributes.md) über das Konfigurieren von übereinstimmenden Attributen.|
|SchemaAttributeNotFound |Der Vorgang konnte nicht ausgeführt werden, da ein Attribut angegeben wurde, das in der Zielanwendung nicht vorhanden ist. Informationen zum Anpassen von Attributen finden Sie in der [Dokumentation](../app-provisioning/customize-application-attributes.md). Stellen Sie außerdem sicher, dass Ihre Konfiguration korrekt ist.|
|InternalError |Im Azure AD-Bereitstellungsdienst ist ein interner Dienstfehler aufgetreten. Sie brauchen nichts zu tun. Dieser Versuch wird automatisch in 40 Minuten wiederholt.|
|InvalidDomain |Der Vorgang konnte aufgrund eines Attributwerts mit einem ungültigen Domänennamen nicht ausgeführt werden. Aktualisieren Sie den Domänennamen für den Benutzer, oder fügen Sie ihn der Liste zulässiger Domänen in der Zielanwendung hinzu. |
|Timeout |Der Vorgang konnte nicht abgeschlossen werden, da die Antwort der Zielanwendung zu lange gedauert hat. Sie brauchen nichts zu tun. Dieser Versuch wird automatisch in 40 Minuten wiederholt.|
|LicenseLimitExceeded|Der Benutzer konnte in der Zielanwendung nicht erstellt werden, da für diesen Benutzer keine Lizenzen verfügbar sind. Erwerben Sie weitere Lizenzen für die Zielanwendung. Oder: Überprüfen Sie die Konfiguration der Benutzerzuweisungen und Attributzuordnungen, um sicherzustellen, dass die richtigen Attribute den richtigen Benutzern zugewiesen sind.|
|DuplicateTargetEntries  |Der Vorgang konnte nicht abgeschlossen werden, da in der Zielanwendung mehrere Benutzer mit den konfigurierten übereinstimmenden Attributen gefunden wurden. Entfernen Sie den doppelten Benutzer aus der Zielanwendung, oder [konfigurieren Sie die Attributzuordnungen neu](../app-provisioning/customize-application-attributes.md).|
|DuplicateSourceEntries | Der Vorgang konnte nicht abgeschlossen werden, da mehrere Benutzer mit den konfigurierten übereinstimmenden Attributen gefunden wurden. Entfernen Sie den doppelten Benutzer, oder [konfigurieren Sie die Attributzuordnungen neu](../app-provisioning/customize-application-attributes.md).|
|ImportSkipped | Bei der Auswertung von Benutzern wird jeweils versucht, den Benutzer aus dem Quellsystem zu importieren. Dieser Fehler tritt in der Regel auf, wenn für den zu importierenden Benutzer die in den Attributzuordnungen definierte Übereinstimmungseigenschaft fehlt. Wenn im Benutzerobjekt kein Wert für das entsprechende Attribut vorhanden ist, können keine Bereichs-, Vergleichs- oder Exportänderungen ausgewertet werden. Dieser Fehler bedeutet nicht, dass sich der Benutzer innerhalb des Gültigkeitsbereichs befindet, da der Gültigkeitsbereich für den Benutzer noch nicht ausgewertet wurde.|
|EntrySynchronizationSkipped | Der Bereitstellungsdienst hat das Quellsystem erfolgreich abgefragt und den Benutzer identifiziert. Es wurden keine weiteren Aktionen für den Benutzer durchgeführt, und er wurde übersprungen. Der Benutzer befand sich möglicherweise außerhalb des Gültigkeitsbereichs, oder er war im Zielsystem bereits vorhanden, und es waren keine weiteren Änderungen erforderlich.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Bei einer GET-Anforderung zum Abrufen eines Benutzers oder einer Gruppe wurden in der Antwort mehrere Benutzer oder Gruppen empfangen. In der Antwort wird vom System nur ein einzelner Benutzer bzw. eine einzelne Gruppe erwartet. [Bespiel:](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group) Dieser Fehler wird ausgelöst, wenn Sie eine GET-Anforderung zum Abrufen einer Gruppe mit einem Filter zum Ausschließen von Mitgliedern ausführen und Ihr SCIM-Endpunkt (System for Cross-Domain Identity Management) die Mitglieder zurückgibt.|

## <a name="next-steps"></a>Nächste Schritte

* [Überprüfen des Status der Benutzerbereitstellung](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Problem beim Konfigurieren der Benutzerbereitstellung für eine Azure AD-Kataloganwendung](../app-provisioning/application-provisioning-config-problem.md)
* [Ressourcentyp „provisioningObjectSummary“](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)

---
title: Berichte zu Anmeldeaktivitäten im Azure Active Directory-Portal | Microsoft-Dokumentation
description: Enthält eine Einführung in die Berichte zu Anmeldeaktivitäten im Azure Active Directory-Portal.
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
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d61962667953b20f4b542874e902411bb579b9c3
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93122842"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Berichte zu Anmeldeaktivitäten im Azure Active Directory-Portal

Die Architektur für die Berichterstellung in Azure Active Directory (Azure AD) umfasst die folgenden Komponenten:

- **Aktivität** 
    - **Anmeldungen** : Informationen zur Nutzung von verwalteten Anwendungen und Aktivitäten der Benutzeranmeldung.
    - **Überwachungsprotokolle** - [Überwachungsprotokolle](concept-audit-logs.md) stellen Systemaktivitätsinformationen zu Benutzern und zur Gruppenverwaltung, zu verwalteten Anwendungen und zu Verzeichnisaktivitäten bereit.
    - **Bereitstellungsprotokolle** - [Bereitstellungsprotokolle](./concept-provisioning-logs.md) ermöglichen Kunden die Überwachung von Aktivitäten durch den Bereitstellungsdienst, z. B. die Erstellung einer Gruppe in ServiceNow oder eines aus Workday importierten Benutzers. 
- **Security** 
    - **Riskante Anmeldungen** : Eine [riskante Anmeldung](../identity-protection/overview-identity-protection.md) ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist.
    - **Benutzer mit Risikomarkierung** : Ein [Benutzer mit Risikomarkierung](../identity-protection/overview-identity-protection.md) ist ein Indikator für ein ggf. kompromittiertes Benutzerkonto.

In diesem Artikel erhalten Sie einen Überblick über den Bericht zu Anmeldeaktivitäten.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="who-can-access-the-data"></a>Wer kann auf die Daten zugreifen?

* Benutzer mit den Rollen „Sicherheitsadministrator“, „Globaler Leser“, „Sicherheitsleseberechtigter“ und „Berichtsleser“
* Globale Administratoren
* Jeder Benutzer (Nicht-Administratoren) kann auf seine eigenen Anmeldungen zugreifen. 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Welche Azure AD-Lizenz benötigen Sie für den Zugriff auf die Anmeldeaktivität?

Der Bericht zu Anmeldeaktivitäten ist in [allen Editionen von Azure AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data) verfügbar, und Sie können auch über die Microsoft Graph-API darauf zugreifen.

## <a name="sign-ins-report"></a>Bericht zu Anmeldeaktivitäten

Der Bericht zu Anmeldeaktivitäten enthält Antworten auf die folgenden Fragen:

* Wie sieht das Anmeldemuster eines Benutzers aus?
* Wie viele Benutzer haben sich im Laufe einer Woche angemeldet?
* Wie lautet der Status dieser Anmeldungen?

Wählen Sie im Menü im [Azure-Portal](https://portal.azure.com) die Option **Azure Active Directory** aus. Sie können auch auf einer beliebigen Seite **Azure Active Directory** suchen und auswählen.

![Auswählen von Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

Wählen Sie unter **Überwachung** die Option **Anmeldungen** aus, um den [Bericht zu Anmeldeaktivitäten](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns) zu öffnen.

![Screenshot: Menü „Überwachung“ mit ausgewählten Anmeldungen](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Anmeldeaktivität")

Es kann bei einigen Anmeldedatensätzen bis zu zwei Stunden dauern, bis sie im Portal angezeigt werden.

> [!IMPORTANT]
> Im Bericht zu Anmeldeaktivitäten werden nur die **interaktiven** Anmeldungen angezeigt, also Anmeldungen, bei denen sich ein Benutzer manuell mit seinem Benutzernamen und Kennwort anmeldet. Nicht interaktive Anmeldungen wie Dienst-zu-Dienst-Authentifizierungen werden im Bericht zu Anmeldeaktivitäten nicht angezeigt. 

Ein Anmeldungsprotokoll enthält eine Standardlistenansicht mit folgenden Informationen:

- Anmeldedatum
- Zugehöriger Benutzer
- Die Anwendung, bei der sich der Benutzer angemeldet hat
- Anmeldestatus
- Status der Risikoerkennung
- Status der MFA-Anforderung (Multi-Factor Authentication)

![Screenshot: Anmeldungen bei Office 365 SharePoint Online](./media/concept-sign-ins/sign-in-activity.png "Anmeldeaktivität")

Sie können die Listenansicht anpassen, indem Sie in der Symbolleiste auf **Spalten** klicken.

![Screenshot: Seite „Anmeldungen“ mit der Option „Spalten“](./media/concept-sign-ins/19.png "Anmeldeaktivität")

Im Dialogfeld **Spalten** erhalten Sie Zugriff auf die auswählbaren Attribute. In einem Bericht zu Anmeldeaktivitäten dürfen keine Felder enthalten sein, die mehr als einen Wert für eine beliebige Anmeldeanforderung als Spalte aufweisen. Dies gilt beispielsweise für Authentifizierungsdetails, Daten zum bedingten Zugriff und den Netzwerkspeicherort.   

![Screenshot: Dialogfeld „Spalten“ mit auszuwählenden Attributen](./media/concept-sign-ins/columns.png "Anmeldeaktivität")

Wählen Sie in der Listenansicht ein Element aus, um ausführlichere Informationen zu erhalten.

![Screenshot: Ansicht mit ausführlichen Informationen](./media/concept-sign-ins/basic-sign-in.png "Anmeldeaktivität")

> [!NOTE]
> Benutzer können nun in allen Anmeldeberichten Probleme mit Richtlinien für bedingten Zugriff beheben. Durch Klicken auf die Registerkarte **Bedingter Zugriff** eines Anmeldedatensatzes können Benutzer den Status des bedingten Zugriffs überprüfen und die Richtliniendetails für die Anmeldung sowie die jeweiligen Richtlinienergebnisse ansehen.
> Weitere Informationen finden Sie in den [häufig gestellten Fragen zum bedingten Zugriff in allen Anmeldungen](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtern von Anmeldeaktivitäten

Zuerst schränken Sie die gemeldeten Daten auf einen Umfang ein, der für Sie geeignet ist. Filtern Sie die Anmeldedaten anschließend mit dem Datumsfeld als Standardfilter. Azure AD bietet eine breite Palette zusätzlicher Filter, die Sie festlegen können:

![Screenshot: Option „Filter hinzufügen“](./media/concept-sign-ins/04.png "Anmeldeaktivität")

**Anforderungs-ID-** : ID der Anforderung, die Sie interessiert.

**Benutzer** : Name oder Benutzerprinzipalname des Benutzers, der Sie interessiert.

**Anwendung** : Name der Zielanwendung.
 
**Status** : der Anmeldestatus, der Sie interessiert:

- Erfolg

- Fehler

- Unterbrochen


**IP-Adresse** : IP-Adresse des Geräts, mit dem die Verbindung mit Ihrem Mandanten hergestellt wird.

**Standort** : Standort, von dem aus die Verbindung eingeleitet wurde:

- City

- Bundesland/Kanton

- Land/Region


**Ressource** : Name des Diensts, der für die Anmeldung verwendet wird.


**Ressourcen-ID** : ID des Diensts, der für die Anmeldung verwendet wird.


**Client-App** : Typ der Client-App, die zum Herstellen einer Verbindung mit Ihrem Mandanten verwendet wird:

![Client-App-Filter](./media/concept-sign-ins/client-app-filter.png)


|Name|Moderne Authentifizierung|BESCHREIBUNG|
|---|:-:|---|
|Authentifiziertes SMTP| |Wird von POP- und IMAP-Clients zum Senden von E-Mails verwendet.|
|AutoErmittlung| |Wird von Outlook- und EAS-Clients verwendet, um Postfächer in Exchange Online zu suchen und eine Verbindung damit herzustellen.|
|Exchange ActiveSync| |Dieser Filter zeigt alle Anmeldeversuche, bei denen das EAS-Protokoll versucht wurde.|
|Browser|![Blaues Häkchen](./media/concept-sign-ins/check.png)|Zeigt alle Anmeldeversuche von Benutzern über Webbrowser|
|Exchange ActiveSync| | Zeigt alle Anmeldeversuche von Benutzern mit Client-Apps, die Exchange Active Sync zur Herstellung einer Verbindung mit Exchange Online verwenden|
|Exchange Online PowerShell| |Wird zum Herstellen einer Verbindung mit Exchange Online über Remote-PowerShell verwendet. Wenn Sie die Standardauthentifizierung für Exchange Online PowerShell blockieren, müssen Sie das Exchange Online PowerShell-Modul verwenden, um eine Verbindung herzustellen. Anweisungen finden Sie unter [Herstellen einer Verbindung mit Exchange Online PowerShell mithilfe der mehrstufigen Authentifizierung](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).|
|Exchange-Webdienste| |Eine Programmierschnittstelle, die von Outlook, Outlook für Mac und Drittanbieter-Apps verwendet wird.|
|IMAP4| |Älterer E-Mail-Client, der IMAP zum Abrufen von E-Mails verwendet.|
|MAPI über HTTP| |Wird von Outlook 2010 und höher verwendet.|
|Mobile Apps und Desktop-Apps|![Blaues Häkchen](./media/concept-sign-ins/check.png)|Zeigt alle Anmeldeversuche von Benutzern mithilfe von mobilen Anwendungen und Desktopclients.|
|Offlineadressbuch| |Eine Kopie der Adressenlistensammlungen, die von Outlook heruntergeladen und verwendet werden.|
|Outlook Anywhere (RPC über HTTP)| |Wird bis Outlook 2016 verwendet.|
|Outlook-Dienst| |Wird von der Mail- und Kalender-App für Windows 10 verwendet.|
|POP3| |Älterer E-Mail-Client, der POP3 zum Abrufen von E-Mails verwendet.|
|Reporting Web Services| |Wird zum Abrufen von Berichtsdaten in Exchange Online verwendet.|
|Andere Clients| |Zeigt alle Anmeldeversuche von Benutzern, bei denen die Client-App nicht inbegriffen oder unbekannt ist.|



**Betriebssystem** : das Betriebssystem, das auf dem Gerät ausgeführt wird, mit dem die Anmeldung bei Ihrem Mandanten erfolgt. 


**Gerätebrowser** : Wenn die Verbindung in einem Browser eingeleitet wurde, können Sie in diesem Feld nach Browsername filtern.


**Korrelations-ID** : Korrelations-ID der Aktivität.




**Bedingter Zugriff** : Status der angewendeten Regeln für bedingten Zugriff

- **Nicht angewendet** : Keine Richtlinie betraf den Benutzer und die Anwendung bei der Anmeldung.

- **Erfolg** : Eine oder mehrere Richtlinien für bedingten Zugriff betrafen den Benutzer und die Anwendung (aber nicht notwendigerweise die anderen Bedingungen) bei der Anmeldung. 

- **Fehler** : Die Anmeldung hat zwar die Benutzer- und Anwendungsbedingung mindestens einer Richtlinie für bedingten Zugriff erfüllt, die Gewährungssteuerelemente wurden jedoch nicht erfüllt oder sind auf Blockieren des Zugriffs eingestellt.









## <a name="download-sign-in-activities"></a>Herunterladen von Anmeldeaktivitäten

Durch Klicken auf die Option **Herunterladen** können Sie eine CSV- oder JSON-Datei der letzten 250.000 Datensätze erstellen. Beginnen Sie damit, [Daten zu Anmeldungen herunterzuladen](quickstart-download-sign-in-report.md), wenn Sie sie außerhalb des Azure-Portals verwenden möchten.  

![Download](./media/concept-sign-ins/71.png "Download")

> [!IMPORTANT]
> Die Anzahl von Datensätzen, die Sie herunterladen können, ist durch die [Aufbewahrungsrichtlinien für Azure Active Directory-Berichte](reference-reports-data-retention.md) eingeschränkt.  


## <a name="sign-ins-data-shortcuts"></a>Tastenkombinationen für Anmeldedaten

Azure AD und das Azure-Portal enthalten weitere Einstiegspunkte für Anmeldedaten:

- Übersicht über Identitätssicherheit und Schutz
- Benutzer
- Gruppen
- Unternehmensanwendungen

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Benutzeranmeldedaten für Identitätssicherheit und Schutz

Mit dem Graphen für Benutzeranmeldungen auf der Übersichtsseite **Identitätssicherheit und Schutz** werden wöchentliche Aggregationen der Anmeldungen angezeigt. Die Standardeinstellung für den Zeitraum beträgt 30 Tage.

![Screenshot: Diagramm der Anmeldungen in einem Monat](./media/concept-sign-ins/06.png "Anmeldeaktivität")

Wenn Sie im Diagramm der Anmeldungen auf einen Tag klicken, wird eine Übersicht über die Anmeldeaktivitäten für den entsprechenden Tag angezeigt.

Jede Zeile in der Liste mit den Anmeldeaktivitäten zeigt Folgendes:

* Wer hat sich angemeldet?
* Welche Anwendung war das Ziel der Anmeldung?
* Welchen Status hat die Anmeldung?
* Welchen MFA-Status hat die Anmeldung?

Durch Klicken auf ein Element können Sie ausführlichere Informationen zum entsprechenden Anmeldevorgang anzeigen:

- Benutzer-ID
- Benutzer
- Username
- Anwendungs-ID
- Application
- Client
- Position
- IP-Adresse
- Date
- MFA erforderlich
- Anmeldestatus

> [!NOTE]
> IP-Adressen werden so ausgestellt, dass es keine definitive Verbindung zwischen einer IP-Adresse und dem physischen Standort des Computers mit dieser Adresse gibt. Das Zuordnen von IP-Adressen wird außerdem durch Faktoren wie Mobilfunkanbieter und VPNs verkompliziert, die IP-Adressen aus zentralen Pools zuweisen, die oft sehr weit von den Orten entfernt sind, an denen das Clientgerät tatsächlich verwendet wird. Derzeit erfolgt das Konvertieren einer IP-Adresse in einen physischen Standort basierend auf Ablaufverfolgungen, Registrierungsdaten, umgekehrten Suchvorgängen und anderen Informationen.

Wenn Sie auf der Seite **Benutzer** im Abschnitt **Aktivität** auf **Anmeldevorgänge** klicken, wird eine umfassenden Übersicht über alle Benutzeranmeldungen angezeigt.

![Screenshot: Abschnitt „Aktivität“ mit auszuwählenden Anmeldungen](./media/concept-sign-ins/08.png "Anmeldeaktivität")

## <a name="usage-of-managed-applications"></a>Nutzung von verwalteten Anwendungen

Mit einer anwendungsorientierten Ansicht Ihrer Anmeldedaten können Sie beispielsweise folgende Fragen beantworten:

* Wer verwendet meine Anwendungen?
* Welche drei Anwendungen sind in Ihrer Organisation am beliebtesten?
* Wie arbeitet meine neueste Anwendung?

Der Einstiegspunkt für diese Daten sind die drei wichtigsten Anwendungen in Ihrer Organisation. Die Daten sind im Bericht über die letzten 30 Tage im Abschnitt **Übersicht** unter **Unternehmensanwendungen** enthalten.

![Der Screenshot zeigt, wo Sie die Übersicht auswählen können.](./media/concept-sign-ins/10.png "Anmeldeaktivität")

Der Graph zur App-Nutzung gibt die wöchentlichen Aggregationen von Anmeldungen für Ihre beliebtesten drei Anwendungen in einem bestimmten Zeitraum an. Die Standardeinstellung für den Zeitraum beträgt 30 Tage.

![Screenshot: App-Nutzung für den Zeitraum von einem Monat](./media/concept-sign-ins/graph-chart.png "Anmeldeaktivität")

Wenn Sie möchten, können Sie den Fokus auf eine bestimmte Anwendung festlegen.

![Reporting](./media/concept-sign-ins/single-app-usage-graph.png "Berichterstellung")

Wenn Sie im Graph für die App-Nutzung auf einen Tag klicken, wird eine ausführliche Liste mit den Anmeldeaktivitäten angezeigt.

Mit der Option **Anmeldungen** können Sie eine vollständige Übersicht über alle Anmeldeereignisse für Ihre Anwendungen anzeigen.

## <a name="microsoft-365-activity-logs"></a>Microsoft 365-Aktivitätsprotokolle

Sie können Microsoft 365-Aktivitätsprotokolle im [Microsoft 365 Admin Center](/office365/admin/admin-overview/about-the-admin-center) anzeigen. Beachten Sie, dass Microsoft 365- und Azure AD-Aktivitätsprotokolle eine beträchtliche Anzahl von Verzeichnisressourcen gemeinsam nutzen. Nur das Microsoft 365 Admin Center bietet eine vollständige Übersicht über die Microsoft 365-Aktivitätsprotokolle. 

Mithilfe der [Office 365-Verwaltungs-APIs](/office/office-365-management-api/office-365-management-apis-overview) können Sie auch programmgesteuert auf die Microsoft 365-Aktivitätsprotokolle zugreifen.

## <a name="next-steps"></a>Nächste Schritte

* [Fehlercodes des Berichts mit den Anmeldeaktivitäten](reference-sign-ins-error-codes.md)
* [Aufbewahrungsrichtlinien für Azure Active Directory-Berichte](reference-reports-data-retention.md)
* [Latenzen bei Azure Active Directory-Berichten](reference-reports-latencies.md)
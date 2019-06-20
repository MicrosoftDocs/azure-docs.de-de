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
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b69dca5abddd56b29abf3e482e51b3d2a41612e7
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864467"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Berichte zu Anmeldeaktivitäten im Azure Active Directory-Portal

Die Architektur für die Berichterstellung in Azure Active Directory (Azure AD) umfasst die folgenden Komponenten:

- **Aktivität** 
    - **Anmeldungen**: Informationen zur Nutzung von verwalteten Anwendungen und Aktivitäten der Benutzeranmeldung.
    - **Überwachungsprotokolle** - [Überwachungsprotokolle](concept-audit-logs.md) stellen Systemaktivitätsinformationen zu Benutzern und zur Gruppenverwaltung, zu verwalteten Anwendungen und zu Verzeichnisaktivitäten bereit.
- **Sicherheit** 
    - **Riskante Anmeldungen**: Eine [riskante Anmeldung](concept-risky-sign-ins.md) ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist.
    - **Benutzer mit Risikomarkierung**: Ein [Benutzer mit Risikomarkierung](concept-user-at-risk.md) ist ein Indikator für ein ggf. kompromittiertes Benutzerkonto.

In diesem Thema erhalten Sie einen Überblick über den Bericht zu Anmeldeaktivitäten.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="who-can-access-the-data"></a>Wer kann auf die Daten zugreifen?
* Benutzer mit den Rollen „Sicherheitsadministrator“, „Sicherheitsleseberechtigter“ und „Berichtsleser“
* Globale Administratoren
* Darüber hinaus kann jeder Benutzer (Nicht-Administratoren) auf seine eigenen Anmeldungen zugreifen. 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Welche Azure AD-Lizenz benötigen Sie für den Zugriff auf die Anmeldeaktivität?
* Ihrem Mandanten muss eine Azure AD Premium-Lizenz zugewiesen sein, damit der Gesamtbericht für Anmeldeaktivitäten angezeigt werden kann. Unter [Erste Schritte mit Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) erfahren Sie, wie Sie ein Upgrade für Ihre Azure Active Directory-Edition durchführen. Beachten Sie Folgendes: Wenn Sie vor dem Upgrade über keine Aktivitätsdaten verfügten, dauert es ein paar Tage, bis die Daten in den Berichten angezeigt werden, nachdem Sie ein Upgrade auf eine Premium-Lizenz durchgeführt haben.

## <a name="sign-ins-report"></a>Bericht zu Anmeldeaktivitäten

Der Bericht zu Anmeldeaktivitäten enthält Antworten auf die folgenden Fragen:

* Wie sieht das Anmeldemuster eines Benutzers aus?
* Wie viele Benutzer haben sich im Laufe einer Woche angemeldet?
* Wie lautet der Status dieser Anmeldungen?

Sie können auf den Bericht zu Anmeldeaktivitäten zugreifen, indem Sie im [Azure-Portal](https://portal.azure.com) auf dem Blatt **Azure Active Directory** im Abschnitt **Aktivität** die Option **Anmeldungen** wählen. Beachten Sie, dass es bei einigen Anmeldedatensätzen bis zu zwei Stunden dauern kann, bis sie im Portal angezeigt werden.

![Anmeldeaktivität](./media/concept-sign-ins/61.png "Anmeldeaktivität")

> [!IMPORTANT]
> Im Bericht zu Anmeldeaktivitäten werden nur die **interaktiven** Anmeldungen angezeigt, also Anmeldungen, bei denen sich ein Benutzer manuell mit seinem Benutzernamen und Kennwort anmeldet. Nicht interaktive Anmeldungen wie Dienst-zu-Dienst-Authentifizierungen werden im Bericht zu Anmeldeaktivitäten nicht angezeigt. 

Ein Anmeldungsprotokoll enthält eine Standardlistenansicht mit folgenden Informationen:

- Anmeldedatum
- Zugehöriger Benutzer
- Anwendung, bei der sich der Benutzer angemeldet hat
- Anmeldestatus
- Status der Risikoerkennung
- Status der MFA-Anforderung (Multi-Factor Authentication)

![Anmeldeaktivität](./media/concept-sign-ins/01.png "Anmeldeaktivität")

Sie können die Listenansicht anpassen, indem Sie in der Symbolleiste auf **Spalten** klicken.

![Anmeldeaktivität](./media/concept-sign-ins/19.png "Anmeldeaktivität")

Sie können dann weitere Felder anzeigen oder Felder entfernen, die bereits angezeigt werden.

![Anmeldeaktivität](./media/concept-sign-ins/02.png "Anmeldeaktivität")

Wählen Sie in der Listenansicht ein Element aus, um ausführlichere Informationen zu erhalten.

![Anmeldeaktivität](./media/concept-sign-ins/03.png "Anmeldeaktivität")

> [!NOTE]
> Benutzer können nun in allen Anmeldeberichten Probleme mit Richtlinien für bedingten Zugriff beheben. Durch Klicken auf die Registerkarte **Bedingter Zugriff** eines Anmeldedatensatzes können Benutzer den Status des bedingten Zugriffs überprüfen und die Richtliniendetails für die Anmeldung sowie die jeweiligen Richtlinienergebnisse ansehen.
> Weitere Informationen finden Sie in den [häufig gestellten Fragen zum bedingten Zugriff in allen Anmeldungen](reports-faq.md#conditional-access).

![Anmeldeaktivität](./media/concept-sign-ins/ConditionalAccess.png "Anmeldeaktivität")


## <a name="filter-sign-in-activities"></a>Filtern von Anmeldeaktivitäten

Sie können die Anmeldungsdaten mit den folgenden Standardfeldern filtern, um die gemeldeten Daten einzugrenzen:

- Benutzer
- Anwendung
- Anmeldestatus
- Bedingter Zugriff
- Date

![Anmeldeaktivität](./media/concept-sign-ins/04.png "Anmeldeaktivität")

Mit dem Filter **Benutzer** können Sie den Namen oder den Dienstprinzipalnamen (UPN) des gewünschten Benutzers angeben.

Mit dem Filter **Anwendung** können Sie den Namen der gewünschten Anwendung angeben.

Für den Filter **Anmeldestatus** können Sie eine der folgenden Optionen auswählen:

- Alle
- Erfolgreich
- Fehler

Mit dem Filter **Bedingter Zugriff** können Sie den Richtlinienstatus des bedingten Zugriffs für die Anmeldung auswählen:

- Alle
- Nicht angewendet
- Erfolgreich
- Fehler

Mit dem Filter **Datum** können Sie einen Zeitrahmen für die zurückgegebenen Daten festlegen.  
Mögliche Werte:

- 1 Monat
- 7 Tage
- 24 Stunden
- Benutzerdefiniertes Zeitintervall

Beim Auswählen eines benutzerdefinierten Zeitraums können Sie eine Startzeit und eine Endzeit konfigurieren.

Wenn Sie der Anmeldungsansicht zusätzliche Felder hinzufügen, werden diese automatisch in die Filterliste aufgenommen. Wenn Sie Ihrer Liste also beispielsweise das Feld **Client-App** hinzufügen, erhalten Sie auch eine weitere Filteroption, mit der Sie folgende Filter festlegen können:  
![Anmeldeaktivität](./media/concept-sign-ins/12.png "Anmeldeaktivität")

- **Browser**  
    Dieser Filter zeigt alle Ereignisse, bei denen Anmeldeversuche mithilfe von Browserflows ausgeführt wurden.
- **Exchange ActiveSync (unterstützt)**  
    Dieser Filter zeigt alle Anmeldeversuche mit dem Exchange ActiveSync (EAS)-Protokoll von unterstützten Plattformen wie iOS, Android und Windows Phone.
- **Exchange ActiveSync (nicht unterstützt)**  
    Dieser Filter zeigt alle Anmeldeversuche mit dem EAS-Protokoll von nicht unterstützten Plattformen wie Linux-Distributionen.
- **Mobile Apps und Desktopclients** Dieser Filter zeigt alle Anmeldeversuche, die keine Browserflows verwendet haben. Dabei kann es sich um mobile Apps von einer beliebigen Plattform (mithilfe eines beliebigen Protokolls) oder um Apps auf Desktopclients wie Office unter Windows oder MacOS handeln.
  
- **Andere Clients**
    - **IMAP**  
        Älterer E-Mail-Client, der IMAP zum Abrufen von E-Mails verwendet.
    - **MAPI**  
        Office 2013 mit aktivierter ADAL und Verwendung von MAPI.
    - **Ältere Office-Clients**  
        Office 2013 in der Standardkonfiguration, bei der MAPI verwendet wird und ADAL nicht aktiviert ist, oder Office 2016 mit deaktivierter ADAL.
    - **POP**  
        Älterer E-Mail-Client, der POP3 zum Abrufen von E-Mails verwendet.
    - **SMTP**  
        Älterer E-Mail-Client, der SMTP zum Senden von E-Mails verwendet.

## <a name="download-sign-in-activities"></a>Herunterladen von Anmeldeaktivitäten

Sie können die [Daten zu Anmeldungen herunterladen](quickstart-download-sign-in-report.md), wenn Sie sie außerhalb des Azure-Portals verwenden möchten. Durch Klicken auf **Herunterladen** können Sie eine CSV- oder JSON-Datei der letzten 250.000 Datensätze erstellen.  

![Herunterladen](./media/concept-sign-ins/71.png "Herunterladen")

> [!IMPORTANT]
> Die Anzahl von Datensätzen, die Sie herunterladen können, ist durch die [Aufbewahrungsrichtlinien für Azure Active Directory-Berichte](reference-reports-data-retention.md) eingeschränkt.  


## <a name="sign-ins-data-shortcuts"></a>Tastenkombinationen für Anmeldedaten

Zusätzlich zu Azure AD enthält das Azure-Portal weitere Einstiegspunkte für Anmeldedaten:

- Übersicht über Identitätssicherheit und Schutz
- Benutzer
- Gruppen
- Unternehmensanwendungen

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Benutzeranmeldedaten für Identitätssicherheit und Schutz

Mit dem Graphen für Benutzeranmeldungen auf der Übersichtsseite **Identitätssicherheit und Schutz** werden wöchentliche Aggregationen der Anmeldungen aller Benutzer in einem bestimmten Zeitraum angezeigt. Die Standardeinstellung für den Zeitraum beträgt 30 Tage.

![Anmeldeaktivität](./media/concept-sign-ins/06.png "Anmeldeaktivität")

Wenn Sie im Anmeldungsgraph auf einen Tag klicken, wird eine Übersicht über die Anmeldeaktivitäten für den entsprechenden Tag angezeigt.

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
- Anwendung
- Client
- Location
- IP-Adresse
- Date
- MFA erforderlich
- Anmeldestatus

> [!NOTE]
> IP-Adressen werden so ausgestellt, dass es keine definitive Verbindung zwischen einer IP-Adresse und dem physischen Standort des Computers mit dieser Adresse gibt. Das Zuordnen von IP-Adressen wird außerdem durch Faktoren wie Mobilfunkanbieter und VPNs verkompliziert, die IP-Adressen aus zentralen Pools zuweisen, die oft sehr weit von den Orten entfernt sind, an denen das Clientgerät tatsächlich verwendet wird. Derzeit erfolgt das Konvertieren einer IP-Adresse in einen physischen Standort basierend auf Ablaufverfolgungen, Registrierungsdaten, umgekehrten Suchvorgängen und anderen Informationen.

Wenn Sie auf der Seite **Benutzer** im Abschnitt **Aktivität** auf **Anmeldevorgänge** klicken, wird eine umfassenden Übersicht über alle Benutzeranmeldungen angezeigt.

![Anmeldeaktivität](./media/concept-sign-ins/08.png "Anmeldeaktivität")

## <a name="usage-of-managed-applications"></a>Nutzung von verwalteten Anwendungen

Mit einer anwendungsorientierten Ansicht Ihrer Anmeldedaten können Sie beispielsweise folgende Fragen beantworten:

* Wer verwendet meine Anwendungen?
* Welche drei Anwendungen sind im Unternehmen am beliebtesten?
* Ich habe vor Kurzem eine Anwendung eingeführt. Wie gut funktioniert sie?

Ihr Einstiegspunkt für diese Daten ist die Liste mit den beliebtesten drei Anwendungen in Ihrem Unternehmen im Bericht zu den letzten 30 Tagen (Abschnitt **Übersicht** unter **Unternehmensanwendungen**).

![Anmeldeaktivität](./media/concept-sign-ins/10.png "Anmeldeaktivität")

Der Graph zur App-Nutzung gibt die wöchentlichen Aggregationen von Anmeldungen für Ihre beliebtesten drei Anwendungen in einem bestimmten Zeitraum an. Die Standardeinstellung für den Zeitraum beträgt 30 Tage.

![Anmeldeaktivität](./media/concept-sign-ins/47.png "Anmeldeaktivität")

Wenn Sie möchten, können Sie den Fokus auf eine bestimmte Anwendung festlegen.

![Berichterstellung](./media/concept-sign-ins/single_spp_usage_graph.png "Reporting")

Wenn Sie im Graph für die App-Nutzung auf einen Tag klicken, wird eine ausführliche Liste mit den Anmeldeaktivitäten angezeigt.

Mit der Option **Anmeldungen** können Sie eine vollständige Übersicht über alle Anmeldeereignisse für Ihre Anwendungen anzeigen.

![Anmeldeaktivität](./media/concept-sign-ins/11.png "Anmeldeaktivität")

## <a name="office-365-activity-logs"></a>Office 365-Aktivitätsprotokolle

Sie können Office 365-Aktivitätsprotokolle im [Microsoft 365 Admin Center](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center) anzeigen. Obwohl Office 365- und Azure AD-Aktivitätsprotokolle einen Großteil der Verzeichnisressourcen gemeinsam nutzen, bietet nur das Microsoft 365 Admin Center eine vollständige Ansicht der Office 365-Aktivitätsprotokolle. 

Mithilfe der [Office 365-Verwaltungs-APIs](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) können Sie auch programmgesteuert auf die Office 365-Aktivitätsprotokolle zugreifen.

## <a name="next-steps"></a>Nächste Schritte

* [Fehlercodes des Berichts mit den Anmeldeaktivitäten](reference-sign-ins-error-codes.md)
* [Aufbewahrungsrichtlinien für Azure Active Directory-Berichte](reference-reports-data-retention.md)
* [Latenzen bei Azure Active Directory-Berichten](reference-reports-latencies.md)


---
title: Was ist Azure Active Directory Identity Protection (aktualisiert)? | Microsoft-Dokumentation
description: Was ist Azure Active Directory Identity Protection (aktualisiert)?
services: active-directory
keywords: Azure Active Directory Identity Protection, Cloud App Discovery, Verwalten von Anwendungen, Sicherheit, Risiko, Risikostufe, Sicherheitsrisiko, Sicherheitsrichtlinie
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37c63e32f1ee9c404e8b84a6eb17bc6eec30a761
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956937"
---
# <a name="what-is-azure-active-directory-identity-protection-refreshed"></a>Was ist Azure Active Directory Identity Protection (aktualisiert)?

Die Funktionen von Identity Protection wurden aktualisiert, um die Identitäten Ihrer Organisation besser zu schützen. Die aktualisierten Funktionen ermöglichen Folgendes:

- Neu gestaltete Administratoroberfläche im Hinblick auf die für Sie wichtigsten Risiken: Benutzerrisiko und Anmelderisiko

- Leistungsstarke Untersuchungen mit Unterstützung für Filterung, Sortierung und intelligente Downloads

- Verbesserte Berechnung des Benutzerrisikos, sodass Sie Ihre Anstrengungen in Bezug auf die Benutzer priorisieren können, die am ehesten kompromittiert werden

- Unterstützung neuer APIs für den programmgesteuerten Zugriff auf Risikodaten

- Vereinfachter Feedbackvorgang für Administratoren, damit Sie Ihre Benutzer sofort schützen können

- Neues überwachtes maschinelles Lernen zur Verbesserung der Genauigkeit von Risikobewertungen



Sicherheit ist heute eines der Hauptanliegen für Organisationen. Die meisten Sicherheitsverletzungen kommen vor, wenn Angreifer Zugriff auf eine Umgebung erhalten, indem sie die Identität eines Benutzers stehlen. Angreifer sind im Laufe der Zeit immer besser darin geworden, Drittanbieter-Sicherheitsverletzungen auszunutzen und ausgefeilte Phishingangriffe zu starten. Sobald sich Angreifer Zugriff auf ein mit geringen Rechten ausgestattetes Benutzerkonto verschafft haben, ist es für sie relativ einfach, per Seitwärtsbewegung Zugriff auf wichtige Unternehmensressourcen zu erlangen. 

Um auf diese Bedrohungen reagieren zu können, haben Sie mit Azure AD Identity Protection folgende Möglichkeiten: 

- Proaktiv verhindern, dass gefährdete Identitäten missbraucht werden 

- Automatisch Risiken verringern, wenn verdächtige Aktivitäten erkannt werden 

- Riskante Benutzer und Anmeldungen überprüfen, um potenzielle Sicherheitslücken zu beheben  

- Warnungen erhalten, wenn das Risiko eines Benutzers einen bestimmten Schwellenwert erreicht 

 

Azure AD Identity Protection ist eine Funktion von Azure Active Directory Premium P2, mit der Sie Richtlinien für die automatische Reaktion für den Fall konfigurieren können, dass die Identität eines Benutzers kompromittiert wurde oder eine andere Person als der Kontobesitzer versucht, sich mit dessen Identität anzumelden. Mit diesen Richtlinien in Verbindung mit anderen in Azure AD bereitgestellten Steuerungen für den bedingten Zugriff können automatisch entweder der Zugriff blockiert oder Entschärfungsaktionen initiiert werden. Hierzu gehören die Kennwortzurücksetzung und die Durchsetzung der mehrstufigen Authentifizierung. Darüber hinaus umfasst Identity Protection Überwachungs- und Berichtsfunktionen, die tiefere Einblicke in Risiken und potenzielle Gefährdungen in Ihrer Organisation gewähren. 

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWsS6Q]


## <a name="risk-events"></a>Risikoereignisse

Azure AD Identity Protection erkennt die folgenden Risikoereignisse: 

 

| Risikoereignistyp | BESCHREIBUNG | Erkennungstyp |
| ---             | ---         | ---            |
| Ungewöhnlicher Ortswechsel | Anmeldung von einem ungewöhnlichen Standort, basierend auf den letzten Anmeldevorgängen des Benutzers | Offline |
| Anonyme IP-Adresse | Anmeldung von einer anonymen IP-Adresse (z.B. Tor-Browser, anonymisierte VPNs) | Echtzeit |
| Ungewöhnliche Anmeldeeigenschaften | Anmeldung mit Eigenschaften, die für den angegebenen Benutzer in letzter Zeit nicht verwendet wurden | Echtzeit |
| Mit Schadsoftware verknüpfte IP-Adresse | Anmeldung von einer mit Schadsoftware verknüpften IP-Adresse | Offline |
| Kompromittierte Anmeldeinformationen | Dieses Risikoereignis gibt an, dass die gültigen Anmeldeinformationen des Benutzers kompromittiert wurden. | Offline |





## <a name="types-of-risk"></a>Risikotypen 

Identity Protection basiert auf zwei Risikotypen:

- Anmelderisiko

- Benutzerrisiko

### <a name="sign-in-risk"></a>Anmelderisiko

Ein Anmelderisiko stellt die Wahrscheinlichkeit dar, dass eine bestimmte Authentifizierungsanforderung vom Identitätsbesitzer nicht autorisiert wurde.

Es gibt zwei Auswertungen des Anmelderisikos: 

- **Anmelderisiko (Echtzeit):** Das Anmelderisiko (Echtzeit) basiert auf allen Echtzeiterkennungen, die während der Verarbeitung der Anmeldung ausgelöst werden.  

- **Anmelderisiko (aggregiert):** Das Anmelderisiko (aggregiert) stellt das gesamte Risiko einer Anmeldung dar. Es wird durch ein Machine Learning-Modell berechnet, das Folgendes berücksichtigt:

    - Echtzeiterkennungen (siehe oben)
    
    - Offlineerkennungen (die ausgelöst werden, nachdem die Anmeldung erfolgt ist) 
    
    - Alle anderen Funktionen der Anmeldung


### <a name="user-risk"></a>Benutzerrisiko

Ein Benutzerrisiko stellt die Wahrscheinlichkeit dar, dass eine bestimmte Identität kompromittiert wurde. 

Das Benutzerrisiko wird durch Berücksichtigung aller dem Benutzer zugeordneten Risiken berechnet:

- Alle riskanten Anmeldevorgänge
- Alle Risikoereignisse ohne Bezug zu einer Anmeldung
- Aktuelles Benutzerrisiko
- Alle für den Benutzer bis dato ausgeführten Aktionen zum Beheben oder Ignorieren von Risiken



## <a name="how-identity-protection-detects-risk"></a>Wie werden Risiken mit Identity Protection erkannt?  

In Azure AD werden Anomalien und verdächtige Aktivitäten mithilfe von maschinellem Lernen ermittelt. Dabei werden die in Echtzeit erkannten Signale sowie die nicht in Echtzeit erfolgten Signale in Bezug auf Benutzer und ihre Anmeldeaktivitäten herangezogen. Anhand dieser Daten berechnet Identity Protection bei jeder Authentifizierung durch einen Benutzer ein Anmelderisiko in Echtzeit und bestimmt zudem eine allgemeine Benutzerrisikostufe für die einzelnen Benutzer. Mit Identity Protection können Sie automatisch auf diese Risikoerkennungen reagieren, indem Sie Identity Protection-Richtlinien für Benutzer- und Anmelderisiken konfigurieren.  

 

Zum besseren Verständnis, wie Identity Protection Risiken erkennt, gibt es zwei wichtige Konzepte: das Benutzerrisiko und das Anmelderisiko. Das Anmelderisiko stellt die Wahrscheinlichkeit dar, dass eine bestimmte Authentifizierungsanforderung vom Identitätsbesitzer nicht autorisiert wurde. Es gibt zwei Typen von Anmelderisiken: Echtzeit und gesamt. Ein Anmelderisiko in Echtzeit wird zum Zeitpunkt des jeweiligen Anmeldeversuchs (z.B. Anmeldungen von anonymen IP-Adressen) erkannt. Beim gesamten Anmelderisiko handelt es sich um das Aggregat der erkannten Anmelderisiken in Echtzeit sowie aller nachfolgend nicht in Echtzeit erfolgten Risikoereignisse, die den Anmeldungen des Benutzers zugeordnet werden (z.B. unmöglicher Ortswechsel). Das Benutzerrisiko stellt die Gesamtwahrscheinlichkeit dar, dass ein böswilliger Benutzer eine bestimmte Identität kompromittiert hat. Das Benutzerrisiko umfasst alle Risikoaktivitäten für einen bestimmten Benutzer, z.B.

- Anmelderisiko in Echtzeit
- Nachfolgendes Anmelderisiko
- Erkennungen riskanter Benutzer   

 

 
 ![Flow](./media/overview-v2/01.png)
 

 

Der grundlegende Ablauf der Identity Protection-Risikoerkennung und der entsprechenden Reaktion für jede angegebene Anmeldung ist in der Abbildung oben zusammenfassend dargestellt.  

 

 

 

## <a name="common-scenarios"></a>Häufige Szenarios 

Sehen Sie sich das Beispiel zu einem Mitarbeiter von Contoso an. 

1. Ein Mitarbeiter versucht, sich über den Tor-Browser bei Exchange Online anzumelden. Zum Zeitpunkt der Anmeldung erkennt Azure AD Echtzeitrisikoereignisse. 

2. Azure AD erkennt, dass sich der Mitarbeiter über eine anonyme IP-Adresse anmeldet, und löst die Risikostufe „Mittel“ für die Anmeldung aus. 

3. Der Mitarbeiter erhält eine MFA-Aufforderung, da der IT-Administrator von Contoso die Identity Protection-Richtlinie für bedingten Zugriff bei einem Anmelderisiko konfiguriert hat. Die Richtlinie legt fest, dass für das Anmelderisiko „Mittel“ oder höher eine mehrstufige Authentifizierung erfolgen muss. 

4. Der Mitarbeiter führt die mehrstufige Authentifizierung durch und greift auf Exchange Online zu. Seine Benutzerrisikostufe wird nicht geändert. 

Was ist hinter den Kulissen passiert? Der Anmeldeversuch über den Tor-Browser hat in Azure AD ein Anmelderisiko in Echtzeit für eine anonyme IP-Adresse ausgelöst. Bei der Verarbeitung der Anforderung in Azure AD wurde die in Identity Protection konfigurierte Richtlinie zum Anmelderisiko angewandt, da die Risikostufe der Anmeldung des Mitarbeiters den Schwellenwert (Mittel) erreicht hat. Da sich der Mitarbeiter bereits zuvor für die mehrstufige Authentifizierung registriert hat, konnte er die mehrstufige Authentifizierung durchführen. Dadurch dass er die mehrstufige Authentifizierung erfolgreich durchführen konnte, wurde in Azure AD signalisiert, dass er wahrscheinlich der rechtmäßige Identitätsbesitzer ist, und seine Benutzerrisikostufe wurde nicht erhöht. 


Was passiert aber, wenn nicht der Mitarbeiter versucht hat, sich anzumelden? 

1. Ein böswilliger Akteur mit den Anmeldeinformationen des Mitarbeiters versucht, sich über den Tor-Browser beim Exchange Online-Konto des Mitarbeiters anzumelden, da er versucht, seine IP-Adresse zu verbergen. 

2. Azure AD erkennt, dass der Anmeldeversuch von einer anonymen IP-Adresse stammt, und löst ein Anmelderisiko in Echtzeit aus. 

3. Der böswillige Akteur erhält eine MFA-Aufforderung, da der IT-Administrator von Contoso die Identity Protection-Richtlinie für bedingten Zugriff bei einem Anmelderisiko so konfiguriert hat, dass bei der Risikostufe „Mittel“ oder höher für die Anmeldung eine mehrstufige Authentifizierung erforderlich ist. 

4. Der böswillige Akteur kann die mehrstufige Authentifizierung nicht durchführen und nicht auf das Exchange Online-Konto des Mitarbeiters zugreifen. 

5. Durch die fehlerhafte mehrstufige Authentifizierung wurde ein aufzuzeichnendes Risikoereignis ausgelöst, durch das sich das Benutzerrisiko des Mitarbeiters für zukünftige Anmeldungen erhöht. 

Nachdem ein böswilliger Akteur versucht hat, auf das Konto des Mitarbeiters zuzugreifen, sehen Sie sich nun an, was passiert, wenn sich der Mitarbeiter das nächste Mal anmelden möchte. 

1. Der Mitarbeiter versucht, sich über Outlook bei Exchange Online anzumelden. Zum Zeitpunkt der Anmeldung erkennt Azure AD Echtzeitrisikoereignisse sowie alle vorherigen Benutzerrisiken. 

2. Azure AD erkennt kein Anmelderisiko in Echtzeit, jedoch aufgrund der früheren riskanten Aktivitäten in den vorherigen Szenarien ein hohes Benutzerrisiko.  

3. Der Mitarbeiter wird aufgefordert, das Kennwort zurückzusetzen, da der IT-Administrator von Contoso die Identity Protection-Richtlinie zum Benutzerrisiko so konfiguriert hat, dass eine Kennwortänderung erforderlich ist, wenn sich ein Benutzer mit hohem Risiko anmeldet. 

4. Der Mitarbeiter setzt sein Kennwort zurück, da er für Self-Service-Kennwortzurücksetzung und mehrstufige Authentifizierung registriert ist. 

5. Durch die Kennwortzurücksetzung sind die Anmeldeinformationen des Mitarbeiters nicht mehr kompromittiert, und seine Identität weist wieder einen sicheren Status auf. 

6. Die vorherigen Risikoereignisse des Mitarbeiters sind behoben, und seine Benutzerrisikostufe wird als Reaktion auf die Behebung der kompromittierten Anmeldeinformationen automatisch zurückgesetzt. 

## <a name="how-do-i-configure-identity-protection"></a>Wie konfiguriere ich Identity Protection? 

Für die ersten Schritte mit Identity Protection konfigurieren Sie zunächst eine Richtlinie zum Benutzerrisiko und eine Richtlinie zum Anmelderisiko. Nachdem diese Richtlinien konfiguriert und auf eine Testgruppe angewandt wurden, können Sie Risikoereignisse simulieren, um zu überprüfen wie Identity Protection in Ihrer Umgebung reagiert. Die nachfolgend aufgeführten Schnellstarts enthalten jeweils eine exemplarische Vorgehensweise zum Einrichten der oben genannten Richtlinien und Testen Ihrer Umgebung. 

 

Identity Protection unterstützt 3 Rollen in Azure AD für die Verwaltungsaktivitäten bei Ihrer Bereitstellung: 

| Rolle | Möglich | Nicht möglich |
| --- | --- | --- |
| Globaler Administrator | Vollzugriff auf Identity Protection, Integrieren von Identity Protection | |
| Sicherheitsadministrator | Vollzugriff auf Identity Protection | Integrieren von Identity Protection, Zurücksetzen von Kennwörtern für einen Benutzer |
| Sicherheitsleseberechtigter | Schreibgeschützter Zugriff auf Identity Protection | Integrieren von Identity Protection, Bereinigen von Benutzern, Konfigurieren von Richtlinien, Zurücksetzen von Kennwörtern| 

Weitere Informationen finden Sie unter [Zuweisen von Administratorrollen in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

 
## <a name="licensing"></a>Lizenzierung

>[!NOTE]
> In der öffentlichen Vorschauversion von Identity Protection (aktualisiert) haben nur Azure AD Premium P2-Kunden Zugriff auf die Berichte „Riskante Benutzer“ und „Riskante Anmeldungen“.



| Funktion | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/Free |
| --- | --- | --- | --- |
| Richtlinie zum Benutzerrisiko | Ja | Nein | Nein |
| Richtlinie zum Anmelderisiko | Ja | Nein | Nein |
| Bericht „Riskante Benutzer“ | Vollzugriff | Eingeschränkte Informationen | Eingeschränkte Informationen |
| Bericht über riskante Anmeldungen | Vollzugriff | Eingeschränkte Informationen | Eingeschränkte Informationen |
| Richtlinie für MFA-Registrierung | Ja | Nein | Nein |







## <a name="next-steps"></a>Nächste Schritte 

Informationen zu den ersten Schritten mit Identity Protection finden Sie unter [Konfigurieren der Richtlinie zum Anmelderisiko](quickstart-sign-in-risk-policy.md). 







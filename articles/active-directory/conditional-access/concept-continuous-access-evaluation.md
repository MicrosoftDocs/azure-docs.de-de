---
title: Fortlaufende Zugriffsevaluierung in Azure AD
description: Schnellere Reaktion auf Benutzerstatusänderungen mit der fortlaufenden Zugriffsevaluierung in Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562c90dcc4f802290b0ed8b4d544fce9d526fa10
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524667"
---
# <a name="continuous-access-evaluation"></a>Fortlaufende Zugriffsevaluierung

Tokenablauf und -aktualisierung sind Standardmechanismen der Branche. Wenn eine Clientanwendung wie Outlook eine Verbindung mit einem Dienst wie Exchange Online herstellt, werden die API-Anforderungen mithilfe von OAuth 2.0-Zugriffstoken autorisiert. Standardmäßig sind diese Zugriffstoken eine Stunde lang gültig. Nach Ablauf dieser Zeit wird der Client zurück an Azure AD geleitet, und die Token werden aktualisiert. Die Aktualisierung bietet eine Möglichkeit, Richtlinien für den Benutzerzugriff neu auszuwerten. Möglicherweise soll das Token nicht aktualisiert werden, zum Beispiel aufgrund einer Richtlinie für bedingten Zugriff oder da der Benutzer im Verzeichnis deaktiviert wurde. 

Kunden haben Bedenken wegen der Verzögerung geäußert, die zwischen der Änderung der Bedingungen für Benutzer (z. B. Netzwerkadresse oder Diebstahl von Anmeldeinformationen) und dem Erzwingen von Richtlinien im Zusammenhang mit dieser Änderung auftritt. Wir haben mit dem als „Blunt Object“ (stumpfer Gegenstand) bezeichneten Ansatz mit reduzierten Tokenlebensdauer experimentiert, dabei aber festgestellt, dass die Benutzerfreundlichkeit und Zuverlässigkeit beeinträchtigt wurden, ohne die Risiken auszuschließen.

Die rechtzeitige Reaktion auf Richtlinienverstöße oder Sicherheitsprobleme erfordert einen echten Austausch zwischen dem Tokenaussteller (z. B. Azure AD) und der vertrauenden Seite (z. B. Exchange Online). Dieser bidirektionale Austausch bietet zwei wichtige Funktionen. Die vertrauende Seite kann sehen, wann Änderungen vorgenommen wurden, z. B. wenn sich ein Client an einem neuen Standort befindet, und den Aussteller des Tokens informieren. Außerdem ermöglicht dies dem Tokenaussteller, der vertrauenden Seite mitzuteilen, keine Token für einen bestimmten Benutzer anzunehmen, da dessen Konto kompromittiert oder deaktiviert wurde oder da andere Bedenken bestehen. Der Mechanismus für diesen Austausch ist die fortlaufende Zugriffsevaluierung (Continuous Access Evaluation, CAE). Ziel ist es, dass die Reaktion nahezu in Echtzeit erfolgt, aber in einigen Fällen kann durch die Ereignispropagierungszeit eine Latenzzeit von bis zu 15 Minuten beobachtet werden.

Die anfängliche Implementierung der fortlaufenden Zugriffsevaluierung konzentriert sich auf Exchange, Teams und SharePoint Online.

Informationen zum Vorbereiten Ihrer Anwendungen auf die Verwendung der fortlaufenden Zugriffsevaluierung finden Sie unter [Verwenden von APIs mit aktivierter fortlaufender Zugriffsevaluierung in Ihren Anwendungen](../develop/app-resilience-continuous-access-evaluation.md).

### <a name="key-benefits"></a>Hauptvorteile

- Benutzerkündigung oder Kennwortänderung oder -zurücksetzung: Die Sperrung der Benutzersitzung wird nahezu in Echtzeit erzwungen.
- Änderung der Netzwerkadresse: Standortrichtlinien für den bedingten Zugriff werden nahezu in Echtzeit erzwungen.
- Der Export von Token auf einen Computer außerhalb eines vertrauenswürdigen Netzwerks kann mit Standortrichtlinien für den bedingten Zugriff verhindert werden.

## <a name="scenarios"></a>Szenarien 

Zwei Szenarien ermöglichen die fortlaufende Zugriffsevaluierung: die Auswertung kritischer Ereignisse und die Auswertung von Richtlinien für bedingten Zugriff.

### <a name="critical-event-evaluation"></a>Auswertung kritischer Ereignisse

Die fortlaufende Zugriffsevaluierung wird implementiert, indem für Dienste wie Exchange Online, SharePoint Online und Teams die Möglichkeit besteht, kritische Ereignisse in Azure AD zu abonnieren, sodass diese Ereignisse nahezu in Echtzeit ausgewertet und erzwungen werden können. Die Auswertung kritischer Ereignisse beruht nicht auf Richtlinien für den bedingten Zugriff, sodass sie in jedem Mandanten verfügbar ist. Derzeit werden folgende Ereignisse ausgewertet:

- Benutzerkonto wird gelöscht oder deaktiviert
- Kennwort für einen Benutzer wird geändert oder zurückgesetzt
- Mehrstufige Authentifizierung wird für den Benutzer aktiviert.
- Administrator sperrt explizit alle Aktualisierungstoken für einen Benutzer.
- Azure AD Identity Protection hat ein hohes Benutzerrisiko erkannt.

Dies ermöglicht ein Szenario, bei dem Benutzer innerhalb von wenigen Minuten nach einem dieser kritischen Ereignisse den Zugriff auf SharePoint Online-Dateien, E-Mails, Kalender oder Aufgaben der Organisation und Teams aus Microsoft 365-Client-Apps verlieren. 

### <a name="conditional-access-policy-evaluation-preview"></a>Auswertung von Richtlinien für bedingten Zugriff (Vorschau)

In Exchange und SharePoint können wichtige Richtlinien für bedingten Zugriff synchronisiert werden, sodass sie im Dienst selbst ausgewertet werden können.

Dies ermöglicht ein Szenario, bei dem Benutzer unmittelbar nach Änderungen der Netzwerkadresse den Zugriff auf Dateien, E-Mails, Kalender oder Aufgaben der Organisation aus Microsoft 365-Client-Apps oder SharePoint Online verlieren.

> [!NOTE]
> Dabei werden nicht alle Kombinationen aus App- und Ressourcenanbietern unterstützt. Siehe dazu die folgende Tabelle. Office bezieht sich auf Word, Excel und PowerPoint.

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Unterstützt | Unterstützt | Nicht unterstützt | Nicht unterstützt | Unterstützt |
| **Exchange Online** | Unterstützt | Unterstützt | Unterstützt | Unterstützt | Unterstützt |

| | Office-Web-Apps | Office Win32-Apps | Office für iOS | Office für Android | Office für Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Nicht unterstützt | Unterstützt | Unterstützt | Unterstützt | Unterstützt |
| **Exchange Online** | Nicht unterstützt | Unterstützt | Unterstützt | Unterstützt | Unterstützt |

### <a name="client-side-claim-challenge"></a>Clientseitige Anspruchsaufforderung

Vor der fortlaufenden Zugriffsevaluierung versuchten Clients immer, das Zugriffstoken aus dem Cache wiederzugeben, solange es nicht abgelaufen war. Mit der fortlaufenden Zugriffsevaluierung wird ein neuer Fall eingeführt. Ein Ressourcenanbieter kann ein Token ablehnen, auch wenn es nicht abgelaufen ist. Um Clients zu informieren, den Cache zu umgehen, auch wenn die zwischengespeicherten Token nicht abgelaufen sind, führen wir einen Mechanismus mit dem Namen **Anspruchsaufforderung** ein, um anzugeben, dass das Token abgelehnt wurde und ein neues Zugriffstoken in Azure AD ausgegeben werden muss. Für die fortlaufende Zugriffsevaluierung ist ein Client-Update erforderlich, damit der Client die Anspruchsaufforderung verstehen kann. Die aktuelle Version der folgenden Anwendungen unterstützt die Anspruchsaufforderung:

- Outlook Windows
- Outlook iOS
- Outlook Android
- Outlook Mac
- Outlook Web App
- Teams für Windows (nur für die Teams-Ressource)
- Teams iOS (nur für die Teams-Ressource)
- Teams Android (nur für die Teams-Ressource)
- Teams Mac (nur für die Teams-Ressource)
- Word, Excel, PowerPoint für Windows
- Word, Excel, PowerPoint für iOS
- Word, Excel, PowerPoint für Android
- Word, Excel, PowerPoint für Mac

### <a name="token-lifetime"></a>Lebensdauer von Token

Da Risiken und Richtlinien in Echtzeit ausgewertet werden, verlassen sich Clients, die Sitzungen mit fortlaufender Zugriffsevaluierung (CAE) aushandeln, auf CAE und nicht auf die vorhandenen statischen Richtlinien zur Gültigkeitsdauer von Zugriffstoken. Das bedeutet, dass konfigurierbare Richtlinien zur Tokengültigkeitsdauer von CAE-fähigen Clients, die Sitzungen mit CAE-Unterstützung aushandeln, nicht mehr berücksichtigt werden.

Die Tokengültigkeitsdauer wird erhöht, sodass sie in CAE-Sitzungen bei bis zu 28 Stunden liegt. Sperrungen werden durch kritische Ereignisse und Richtlinienauswertung, nicht einfach durch einen willkürlichen Zeitraum gesteuert. Diese Änderung erhöht die Stabilität von Anwendungen, ohne den Sicherheitsstatus zu beeinträchtigen. 

Wenn Sie keine CAE-fähigen Clients verwenden, beträgt die Standardgültigkeitsdauer für das Zugriffstoken 1 Stunde, es sei denn, Sie haben die Gültigkeitsdauer des Zugriffstokens mit der Previewfunktion [Konfigurierbare Tokengültigkeitsdauer (Configurable Token Lifetime, CTL)](../develop/active-directory-configurable-token-lifetimes.md) konfiguriert.

## <a name="example-flows"></a>Beispielabläufe

### <a name="user-revocation-event-flow"></a>Ablauf bei Benutzersperrereignis:

![Ablauf bei Benutzersperrereignis](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. Ein CAE-fähiger Client stellt Anmeldeinformationen oder ein Aktualisierungstoken für Azure AD bereit und fordert ein Zugriffstoken für eine Ressource an.
1. Ein Zugriffstoken wird zusammen mit anderen Artefakten an den Client zurückgegeben.
1. Ein Administrator [sperrt explizit alle Aktualisierungstoken für den Benutzer](/powershell/module/azuread/revoke-azureaduserallrefreshtoken). Von Azure AD wird ein Sperrereignis an den Ressourcenanbieter gesendet.
1. Dem Ressourcenanbieter wird ein Zugriffstoken präsentiert. Der Ressourcenanbieter wertet die Gültigkeit des Tokens aus und überprüft, ob für den Benutzer ein Sperrereignis vorliegt. Der Ressourcenanbieter verwendet diese Informationen, um den Zugriff auf die Ressource zu gewähren oder zu untersagen.
1. In diesem Fall verweigert der Ressourcenanbieter den Zugriff und sendet die Anspruchsaufforderung „401+“ an den Client zurück.
1. Der CAE-fähige Client versteht die Anspruchsaufforderung „401+“. Er umgeht die Caches, geht zurück zu Schritt 1 und sendet das Aktualisierungstoken zusammen mit der Anspruchsaufforderung zurück an Azure AD. Azure AD wertet dann alle Bedingungen erneut aus und fordert in diesem Fall den Benutzer auf, sich erneut zu authentifizieren.

### <a name="user-condition-change-flow-preview"></a>Ablauf bei Änderungen von Benutzerbedingungen (Vorschau):

Im folgenden Beispiel hat ein Administrator für den bedingten Zugriff eine standortbasierte Richtlinie für bedingten Zugriff so konfiguriert, dass der Zugriff nur über bestimmte IP-Adressbereiche zulässig ist:

![Ablauf der Ereignisse für Benutzerbedingungen](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. Ein CAE-fähiger Client stellt Anmeldeinformationen oder ein Aktualisierungstoken für Azure AD bereit und fordert ein Zugriffstoken für eine Ressource an.
1. Azure AD wertet alle Richtlinien für bedingten Zugriff aus, um zu überprüfen, ob der Benutzer und der Client die Bedingungen erfüllen.
1. Ein Zugriffstoken wird zusammen mit anderen Artefakten an den Client zurückgegeben.
1. Der Benutzer wechselt zu einer IP-Adresse außerhalb eines zulässigen IP-Adressbereichs.
1. Der Client stellt ein Zugriffstoken für den Ressourcenanbieter außerhalb eines zulässigen IP-Adressbereichs bereit.
1. Der Ressourcenanbieter wertet die Gültigkeit des Tokens aus und überprüft die über Azure AD synchronisierte Standortrichtlinie.
1. In diesem Fall verweigert der Ressourcenanbieter den Zugriff und sendet eine Anspruchsaufforderung ab 401 an den Client zurück, da das Token nicht aus einem zulässigen IP-Adressbereich stammt.
1. Der CAE-fähige Client versteht die Anspruchsaufforderung „401+“. Er umgeht die Caches, geht zurück zu Schritt 1 und sendet das Aktualisierungstoken zusammen mit der Anspruchsaufforderung zurück an Azure AD. Azure AD wertet alle Bedingungen erneut aus, und der Zugriff wird in diesem Fall verweigert.

## <a name="enable-or-disable-cae-preview"></a>Aktivieren oder Deaktivieren der fortlaufenden Zugriffsevaluierung (Vorschau)

1. Melden Sie sich als Administrator für bedingten Zugriff, Sicherheitsadministrator oder globaler Administrator beim **Azure-Portal** an.
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Fortlaufende Zugriffsevaluierung**.
1. Wählen Sie **Vorschau aktivieren** aus.

Auf dieser Seite können Sie optional die Benutzer und Gruppen für die Vorschau einschränken.

![Aktivieren der Vorschau der fortlaufenden Zugriffsevaluierung im Azure-Portal](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

## <a name="troubleshooting"></a>Problembehandlung

### <a name="supported-location-policies"></a>Unterstützte Standortrichtlinien

Für die fortlaufende Zugriffsevaluierung sind nur Erkenntnisse zu benannten Standorten auf der Basis der IP-Adresse möglich. Erkenntnisse zu anderen Standorteinstellungen, z. B. [durch MFA bestätigte IP-Adressen](../authentication/howto-mfa-mfasettings.md#trusted-ips), oder in länderbasierte Standorte sind dagegen nicht möglich. Wenn der Benutzer von einer durch MFA bestätigten IP-Adresse, von vertrauenswürdigen Standorten, die durch MFA bestätigte IP-Adressen enthalten, oder von einem länderbasierten Standort stammt, wird die fortlaufende Zugriffsevaluierung erst nach dem Wechsel des Benutzers zu einem anderen Standort erzwungen. In diesen Fällen wird ein 1-stündiges CAE-Token ohne sofortige erzwungene Überprüfung der IP-Adresse ausgegeben.

> [!IMPORTANT]
> Verwenden Sie bei der Konfiguration von Standorten für die fortlaufende Zugriffsevaluierung nur die [Standortbedingung für den auf IP-Adressen basierenden bedingten Zugriff](../conditional-access/location-condition.md#preview-features), und konfigurieren Sie alle IP-Adressen, **einschließlich IPv4- und IPv6-Adressbereichen**, die vom Identitätsanbieter und Ressourcenanbieter angezeigt werden können. Verwenden Sie keine länderspezifischen Standortbedingungen und auch nicht die Funktion für vertrauenswürdige IP-Adressen, die auf der Seite der Diensteinstellungen für Azure AD Multi-Factor Authentication verfügbar ist.

### <a name="ip-address-configuration"></a>IP-Adresskonfiguration

Im Identitäts- und Ressourcenanbieter werden möglicherweise andere IP-Adressen angezeigt. Diese Diskrepanz kann aufgrund von Implementierungen des Netzwerkproxys in Ihrer Organisation oder von falschen IPv4/IPv6-Konfigurationen zwischen dem Identitäts- und Ressourcenanbieter auftreten. Beispiel:

- Im Identitätsanbieter wird eine IP-Adresse vom Client angezeigt.
- Im Ressourcenanbieter wird nach der Weiterleitung über einen Proxy eine andere IP-Adresse vom Client angezeigt.
- Die IP-Adresse für den Identitätsanbieter ist Teil eines in der Richtlinie zulässigen IP-Adressbereichs, die IP-Adresse des Ressourcenanbieters dagegen nicht.

Wenn dieses Szenario in Ihrer Umgebung besteht, um Endlosschleifen zu vermeiden, wird in Azure AD ein einstündiges CAE-Token ausgegeben und keine Änderung des Clientstandorts erzwungen. Selbst in diesem Fall ist die Sicherheit im Vergleich zu herkömmlichen einstündigen Token verbessert, da neben den Ereignissen der Änderung des Clientstandorts die [anderen Ereignisse](#critical-event-evaluation) weiterhin ausgewertet werden.

### <a name="office-and-web-account-manager-settings"></a>Einstellungen für Office und Web Account Manager

| Office-Updatekanal | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| Halbjährlicher Enterprise-Kanal | Wenn diese Einstellung auf „aktiviert“ oder „1“ festgelegt ist, wird die fortlaufende Zugriffsevaluierung nicht unterstützt. | Wenn diese Einstellung auf „aktiviert“ oder „1“ festgelegt ist, wird die fortlaufende Zugriffsevaluierung nicht unterstützt. |
| Aktueller Kanal <br> oder <br> Monatlicher Enterprise-Kanal | Die fortlaufende Zugriffsevaluierung wird unabhängig von der Einstellung unterstützt. | Die fortlaufende Zugriffsevaluierung wird unabhängig von der Einstellung unterstützt. |

Eine Erläuterung der Office-Updatekanäle finden Sie unter [Übersicht über die Updatekanäle von Microsoft 365 Apps](/deployoffice/overview-update-channels). Es wird Organisationen empfohlen, Web Account Manager (WAM) nicht zu deaktivieren.

### <a name="policy-change-timing"></a>Zeitliche Steuerung von Richtlinienänderungen

Aufgrund der Möglichkeit einer Replikationsverzögerung zwischen Azure AD und Ressourcenanbietern kann es bis zu 2 Stunden dauern, bis Richtlinienänderungen von Administratoren für Exchange Online wirksam werden.

Beispiel: Der Administrator fügt um 11:00 Uhr eine Richtlinie hinzu, um einen Bereich von IP-Adressen für den Zugriff auf E-Mails zu sperren. Ein Benutzer aus diesem IP-Adressbereich kann möglicherweise bis 13:00 Uhr weiterhin auf E-Mails zugreifen.

### <a name="coauthoring-in-office-apps"></a>Gemeinsame Dokumenterstellung in Office-Apps

Wenn mehrere Benutzer gleichzeitig am selben Dokument arbeiten, wird der Zugriff des Benutzers auf das Dokument basierend auf Benutzersperr- oder Richtlinienänderungsereignissen möglicherweise nicht sofort durch die fortlaufende Zugriffsevaluierung gesperrt. In diesem Fall verliert der Benutzer den Zugriff vollständig nach dem Schließen des Dokuments, dem Schließen von Word, Excel oder PowerPoint oder nach einem Zeitraum von 10 Stunden.

Um diesen Zeitraum zu verkürzen, kann ein SharePoint-Administrator durch [Konfigurieren einer Netzwerkadressenrichtlinie in SharePoint Online](/sharepoint/control-access-based-on-network-location) die maximale Lebensdauer von Sitzungen zur gemeinsamen Dokumenterstellung für in SharePoint Online und OneDrive for Business gespeicherten Dokumenten optional verringern. Nachdem diese Konfiguration geändert wurde, wird die maximale Lebensdauer von Sitzungen zur gemeinsamen Dokumenterstellung auf 15 Minuten verringert und kann über den SharePoint Online-PowerShell-Befehl „Set-SPOTenant –IPAddressWACTokenLifetime“ weiter angepasst werden.

### <a name="enable-after-a-user-is-disabled"></a>Aktivieren eines Benutzers nach seiner Deaktivierung

Wenn Sie einen Benutzer direkt nach seiner Deaktivierung aktivieren, kann das Konto erst nach einer gewissen Latenzzeit aktiviert werden. SPO und Teams weisen eine Verzögerung von 15 Minuten auf. Bei EXO beträgt die Verzögerung 35–40 Minuten.

## <a name="faqs"></a>Häufig gestellte Fragen

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Wie funktioniert die fortlaufende Zugriffsevaluierung (CAE) mit der Anmeldehäufigkeit?

Die Anmeldehäufigkeit wird mit oder ohne CAE berücksichtigt.

## <a name="next-steps"></a>Nächste Schritte

[Ankündigung der fortlaufenden Zugriffsevaluierung](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)

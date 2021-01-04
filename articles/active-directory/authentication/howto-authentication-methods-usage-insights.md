---
title: 'Azure Active Directory: Authentifizierungsmethoden: Nutzung und Erkenntnisse'
description: Bericht zur Nutzung der Authentifizierungsmethoden für Azure AD-Self-Service-Kennwortzurücksetzung und Multi-Factor Authentication
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8633ae0d23650eb12ee2e1fdcd355864dfd8b6c3
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743631"
---
# <a name="authentication-methods-usage--insights-preview"></a>Authentifizierungsmethoden: Nutzung und Erkenntnisse (Vorschau)

Mithilfe von „Nutzung und Erkenntnisse“ können Sie verstehen, wie Authentifizierungsmethoden für Features wie Azure AD Multi-Factor Authentication und Self-Service-Kennwortzurücksetzung in Ihrem Unternehmen funktionieren. Mit dieser Berichtsfunktion kann Ihr Unternehmen besser verstehen, welche Methoden registriert werden und wie sie verwendet werden.

## <a name="permissions-and-licenses"></a>Berechtigungen und Lizenzen

Die folgenden Rollen können auf „Nutzung und Erkenntnisse“ zugreifen:

- Globaler Administrator
- Sicherheitsleseberechtigter
- Sicherheitsadministrator
- Meldet Reader

Für den Zugriff auf „Nutzung und Erkenntnisse“ ist keine zusätzliche Lizenzierung erforderlich. Informationen zur Lizenzierung für Azure AD Multi-Factor Authentication und Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) finden Sie auf der [Website mit Preisen für Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Funktionsweise

So greifen Sie auf „Authentifizierungsmethoden: Nutzung und Erkenntnisse“ zu:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Navigieren Sie zu **Azure Active Directory** > **Kennwort zurücksetzen** > **Nutzung und Erkenntnisse**.
1. In der Übersicht **Registrierung** oder **Nutzung** können Sie die vorgefilterten Berichte öffnen und nach Bedarf filtern.

![Übersicht „Nutzung und Erkenntnisse“](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Um direkt auf „Nutzung und Erkenntnisse“ zuzugreifen, wechseln Sie zu [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade). Über diesen Link gelangen Sie zur Registrierungsübersicht.

Auf den Kacheln „Registrierte Benutzer“, „Aktivierte Benutzer“ und „Berechtigte Benutzer“ werden die folgenden Registrierungsdaten für Ihre Benutzer angezeigt:

- Registriert: Ein Benutzer gilt als registriert, wenn er (oder ein Admin) genügend Authentifizierungsmethoden registriert hat, um die SSPR- oder Multi-Faktor Authentication-Richtlinie Ihres Unternehmens zu erfüllen.
- Aktiviert: Ein Benutzer gilt als aktiviert, wenn er in den Geltungsbereich der SSPR-Richtlinie fällt. Wenn SSPR für eine Gruppe aktiviert ist, gilt der Benutzer als aktiviert, wenn er sich in dieser Gruppe befindet. Wenn SSPR für alle Benutzer aktiviert ist, gelten alle Benutzer im Mandanten (außer Gäste) als aktiviert.
- Berechtigt: Ein Benutzer gilt als berechtigt, wenn er sowohl registriert als auch aktiviert ist. Dieser Status bedeutet, dass er bei Bedarf jederzeit SSPR durchführen kann.

Wenn Sie auf eine dieser Kacheln oder die darauf angezeigten Erkenntnisse klicken, gelangen Sie zu einer vorgefilterten Liste mit Registrierungsdetails.

Das Diagramm **Registrierungen** auf der Registerkarte **Registrierung** zeigt die Anzahl der erfolgreichen und fehlgeschlagenen Registrierungen von Authentifizierungsmethoden nach Authentifizierungsmethode. Das Diagramm **Zurücksetzungen** auf der Registerkarte **Nutzung** zeigt die Anzahl der erfolgreichen und fehlgeschlagenen Authentifizierungen beim Kennwortzurücksetzungsablauf nach Authentifizierungsmethode.

Wenn Sie auf eines der Diagramme klicken, gelangen Sie zu einer vorgefilterten Liste mit Registrierungs- oder Zurücksetzungsereignissen.

Mit dem Steuerelement in der oberen rechten Ecke können Sie den Datumsbereich für die im Diagramm „Registrierungen“ bzw. „Zurücksetzungen“ angezeigten Überwachungsdaten auf 24 Stunden, 7 Tage oder 30 Tage ändern.

### <a name="registration-details"></a>Registrierungsdetails

Wenn Sie auf die Kacheln **Registrierte Benutzer**, **Aktivierte Benutzer** oder **Berechtigte Benutzer** bzw. auf die Erkenntnisse klicken, gelangen Sie zu den Registrierungsdetails.

Der Bericht mit den Registrierungsdetails enthält für jeden Benutzer die folgenden Informationen:

- Name
- Benutzername
- Registrierungsstatus (Alle, Registriert, Nicht registriert)
- Aktivierungsstatus (Alle, Aktiviert, Nicht aktiviert)
- Berechtigungsstatus (Alle, Berechtigt, Nicht berechtigt)
- Methoden (App-Benachrichtigung, App-Code, Telefonanruf, SMS, E-Mail, Sicherheitsfragen)

Mit den Steuerelementen oben in der Liste können Sie nach einem Benutzer suchen und die Liste der Benutzer nach den angezeigten Spalten filtern.

### <a name="reset-details"></a>Zurücksetzungsdetails

Wenn Sie auf das Diagramm „Registrierungen“ oder „Zurücksetzungen“ klicken, gelangen Sie zu den Zurücksetzungsdetails.

Der Bericht mit den Zurücksetzungsdetails enthält Registrierungs- und Zurücksetzungsereignisse der letzten 30 Tage sowie folgende Informationen:

- Name
- Benutzername
- Funktion (Alle, Registrierung, Zurücksetzung)
- Authentifizierungsmethode (App-Benachrichtigung, App-Code, Telefonanruf, geschäftlicher Telefonanruf, SMS, E-Mail, Sicherheitsfragen)
- Status (Alle, Erfolgreich, Fehler)

Mit den Steuerelementen oben in der Liste können Sie nach einem Benutzer suchen und die Liste der Benutzer nach den angezeigten Spalten filtern.

## <a name="limitations"></a>Einschränkungen

Die in diesen Berichten angezeigten Daten sind um bis zu 60 Minuten zeitverzögert. Im Azure-Portal können Sie mithilfe des Felds „Zuletzt aktualisiert“ feststellen, wie aktuell Ihre Daten sind.

Die Daten im Bericht „Nutzung und Erkenntnisse“ sind kein Ersatz für die Aktivitätsberichte von Azure AD Multi-Factor Authentication oder für die Informationen, die im Azure AD-Anmeldebericht enthalten sind.

Der Bericht kann derzeit nicht gefiltert werden, um externe Benutzer auszuschließen.

## <a name="next-steps"></a>Nächste Schritte

- [Arbeiten mit der Nutzungsbericht-API für Authentifizierungsmethoden](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Auswählen von Authentifizierungsmethoden für Ihr Unternehmen](concept-authentication-methods.md)
- [Kombinierte Registrierung](concept-registration-mfa-sspr-combined.md)
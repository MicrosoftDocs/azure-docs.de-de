---
title: Aktivieren der Altersbeschränkung in Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Minderjährige identifizieren, die Ihre Anwendung verwenden.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 33b379a03c92b81885f7adfc70f7025a85ce9057
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511663"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Aktivieren der Altersbeschränkung in Azure Active Directory B2C

>[!IMPORTANT]
>Dieses Feature befindet sich in der Phase der öffentlichen Vorschau. Verwenden Sie dieses Feature nicht für Produktionsanwendungen. 
>

Mit der Altersbeschränkung in Azure Active Directory (Azure AD) B2C können Sie Minderjährige identifizieren, die Ihre Anwendung nutzen möchten. Sie können Minderjährige bei Bedarf daran hindern, sich an der Anwendung anzumelden. Benutzer können auch zur Anwendung zurückkehren und ihre Altersgruppe und den Status der Zustimmung durch die Erziehungsberechtigten angeben. Azure AD B2C kann Minderjährige ohne elterliche Zustimmung sperren. Außerdem kann Azure AD B2C so eingerichtet werden, dass von der Anwendung entschieden werden kann, wie mit Minderjährigen verfahren wird.

Nachdem Sie die Altersbeschränkung in Ihrem [Benutzerflow](active-directory-b2c-reference-policies.md) aktiviert haben, werden die Benutzer gefragt, wann sie geboren wurden und in welchem Land / welcher Region sie leben. Wenn sich ein Benutzer anmeldet, der diese Informationen noch nicht eingegeben hat, muss dies bei der nächsten Anmeldung erfolgen. Die Regeln werden jedes Mal angewendet, wenn sich ein Benutzer anmeldet.

Azure AD B2C nutzt die vom Benutzer eingegebenen Informationen, um zu ermitteln, ob es sich um eine minderjährige Person handelt. Anschließend wird das Feld **ageGroup** im Konto aktualisiert. Der Wert kann `null`, `Undefined`, `Minor`, `Adult` oder `NotAdult` lauten.  Die Felder **ageGroup** und **consentProvidedForMinor** werden dann verwendet, um den Wert von **legalAgeGroupClassification** zu berechnen.

Die Altersbeschränkung umfasst zwei Alterswerte: das Alter, in dem eine Person nicht mehr als minderjährig angesehen wird, und das Alter, in dem eine minderjährige Person die Zustimmung durch die Erziehungsberechtigten benötigt. In der folgenden Tabelle sind die Altersregeln aufgeführt, die zum Definieren einer minderjährigen Person und des Alters für die Zustimmung durch die Erziehungsberechtigten verwendet werden.

| Land/Region | Name des Landes / der Region | Alter für Zustimmung für Minderjährige | Alter für Einstufung als minderjährig |
| -------------- | ------------------- | ----------------- | --------- |
| Standard | Keine | Keine | 18 |
| AE | Vereinigte Arabische Emirate | Keine | 21 |
| AT | Österreich | 14 | 18 |
| BE | Belgien | 14 | 18 |
| BG | Bulgarien | 16 | 18 |
| BH | Bahrain | Keine | 21 |
| CM | Kamerun | Keine | 21 |
| CY | Zypern | 16 | 18 |
| CZ | Tschechische Republik | 16 | 18 |
| DE | Deutschland | 16 | 18 |
| DK | Dänemark | 16 | 18 |
| EE | Estland | 16 | 18 |
| EG | Ägypten | Keine | 21 |
| ES | Spanien | 13 | 18 |
| BV | Frankreich | 16 | 18 |
| GB | Vereinigtes Königreich | 13 | 18 |
| GR | Griechenland | 16 | 18 |
| HR | Kroatien | 16 | 18 |
| HU | Ungarn | 16 | 18 |
| IE | Irland | 13 | 18 |
| IT | Italien | 16 | 18 |
| KR | Republik Korea | 14 | 18 |
| LT | Litauen | 16 | 18 |
| LU | Luxemburg | 16 | 18 |
| LV | Lettland | 16 | 18 |
| MT | Malta | 16 | 18 |
| Nicht verfügbar | Namibia | Keine | 21 |
| NL | Niederlande | 16 | 18 |
| PL | Polen | 13 | 18 |
| PT | Portugal | 16 | 18 |
| RO | Rumänien | 16 | 18 |
| SE | Schweden | 13 | 18 |
| SG | Singapur | Keine | 21 |
| SI | Slowenien | 16 | 18 |
| SK | Slowakei | 16 | 18 |
| TD | Tschad | Keine | 21 |
| TH | Thailand | Keine | 20 |
| TW | Taiwan | Keine | 20 | 
| US | USA | 13 | 18 |

## <a name="age-gating-options"></a>Optionen für die Altersbeschränkung
 
### <a name="allowing-minors-without-parental-consent"></a>Zulassen von Minderjährigen ohne Zustimmung eines Erziehungsberechtigten

Bei Benutzerflows, die entweder eine Registrierung, eine Anmeldung oder beides zulassen, können Sie wählen, ob Minderjährige ohne Zustimmung Ihre Anwendung verwenden dürfen. Minderjährige ohne Zustimmung eines Erziehungsberechtigten können sich normal anmelden oder registrieren. Dann stellt Azure AD B2C ein ID-Token mit dem Anspruch **legalAgeGroupClassification** aus. Mit diesem Anspruch wird die Benutzeroberfläche definiert, die Benutzern angezeigt wird, z.B. für das Einholen der Zustimmung eines Erziehungsberechtigten und das Aktualisieren des Felds **consentProvidedForMinor**.

### <a name="blocking-minors-without-parental-consent"></a>Sperren von Minderjährigen ohne Zustimmung eines Erziehungsberechtigten

Bei Benutzerflows, die entweder eine Registrierung, eine Anmeldung oder beides zulassen, können Sie festlegen, ob Minderjährige ohne Zustimmung für die Anwendung gesperrt werden. Die folgenden Optionen sind für den Umgang mit gesperrten Benutzern in Azure AD B2C verfügbar:

- JSON-Funktion zurück an die Anwendung senden: Diese Option sendet eine Antwort mit dem Hinweis zurück an die Anwendung, dass ein Minderjähriger gesperrt wurde.
- Fehlerseite anzeigen: Dem Benutzer wird eine Seite angezeigt, die ihn darüber informiert, dass er nicht auf die Anwendung zugreifen kann.

## <a name="set-up-your-tenant-for-age-gating"></a>Einrichten Ihres Mandanten für die Altersbeschränkung

Um die Altersbeschränkung in einem Benutzerflow zu verwenden, müssen Sie für Ihren Mandanten zusätzliche Eigenschaften konfigurieren.

1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken. Wählen Sie das Verzeichnis aus, das den Mandanten enthält. 
2. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und wählen Sie diesen Eintrag aus.
3. Wählen Sie für Ihren Mandanten im Menü auf der linken Seite die Option **Eigenschaften**.
2. Klicken Sie unter dem Abschnitt **Altersbeschränkung** auf **Konfigurieren**.
3. Warten Sie, bis der Vorgang abgeschlossen ist und Ihr Mandant für die Altersbeschränkung eingerichtet wird.

## <a name="enable-age-gating-in-your-user-flow"></a>Aktivieren der Altersbeschränkung im Benutzerflow

Nachdem Ihr Mandant für die Nutzung der Altersbeschränkung eingerichtet wurde, können Sie dieses Feature dann in den [Benutzerflows](user-flow-versions.md) verwenden, für die es aktiviert ist. Zum Aktivieren der Altersbeschränkung führen Sie die folgenden Schritte aus:

1. Erstellen Sie einen Benutzerflow, für den die Altersbeschränkung aktiviert ist.
2. Wählen Sie nach dem Erstellen des Benutzerflows im Menü die Option **Eigenschaften**.
3. Wählen Sie im Abschnitt **Altersbeschränkung** die Option **Aktiviert**.
4. Sie können dann wählen, wie Sie Benutzer verwalten möchten, die sich als Minderjährige identifizieren. Wählen Sie unter **Registrieren oder anmelden** die Option `Allow minors to access your application` oder `Block minors from accessing your application`. Wenn Sie die Option zum Ausschließen von Minderjährigen gewählt haben, wählen Sie anschließend `Send a JSON back to the application` oder `Show an error message`. 





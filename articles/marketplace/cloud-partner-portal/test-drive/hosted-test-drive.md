---
title: Gehostete Testversion | Azure Marketplace
description: Einrichten einer Verwalten einer im Marketplace gehosteten Testversion
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b8f9ca96ac9386037460ad5c1c9f56fe7b9c2e18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939998"
---
# <a name="hosted-test-drive"></a>Gehostete Testversion

Eine gehostete Testversion vereinfacht die Einrichtung dadurch, dass Microsoft das Hosting und die Wartung des Diensts übernimmt, der die Bereitstellung und Aufhebung der Bereitstellung der Testversion durchführt. Dieser Artikel richtet sich an Herausgeber, die ihr Angebot in AppSource veröffentlichen oder ein neues Angebot erstellen und eine gehostete Testversion anbieten möchten, die sich mit einer Instanz von Dynamics 365 for Customer Engagement, Dynamics 365 for Finance and Operations oder Dynamics 365 Business Central verbindet.

## <a name="how-to-publish-a-test-drive"></a>Veröffentlichen einer Testversion

Navigieren Sie zu einem bestehenden Angebot oder erstellen Sie ein neues Angebot.

Wählen Sie die Option „Testversion“ aus dem seitlichen Menü aus.

Wählen Sie \'Ja\' für die Option \'Testversion aktivieren\' aus.

Geben Sie die folgenden Felder im Abschnitt \'Details\' an.

- **Beschreibung:** Stellen Sie eine Übersicht über die Testversion bereit. Dieser Text wird dem Benutzer während der Bereitstellung der Testversion angezeigt. Dieses Feld unterstützt HTML, wenn Sie formatierte Inhalte bereitstellen möchten.
- **Benutzerhandbuch**: Laden Sie ein detailliertes Benutzerhandbuch (PDF-Datei) hoch, das den Benutzern der Testversion Informationen zur Verwendung Ihrer App bereitstellt.
- **Demovideo für Testversion**: Laden Sie optional ein Video hoch, in dem Ihre App vorgestellt wird.

Erteilen Sie der AppSource die Berechtigung, Benutzer der Testversion in Ihrem Mandanten mithilfe der [hier](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md) angezeigten Anweisungen bereitzustellen und diese Bereitstellung wieder aufzuheben.

In diesem Schritt generieren Sie die unten erwähnten Werte \'Azure AD-App-ID\' und \'Azure AD-App-Schlüssel\'.

Geben Sie die folgenden Felder im Abschnitt \'Technische Konfiguration\' an:

- **Typ der Testversion**: Wählen Sie die Option \'Von Microsoft gehostet (Beispiel: Dynamics 365 for Customer Engagement) aus. Dies weist darauf hin, dass Microsoft den Dienst hosten und warten wird, der die Bereitstellung und das Aufheben der Bereitstellung von Benutzern der Testversion durchführt.
- **Maximale Anzahl gleichzeitiger Testversionen**: Legen Sie in diesem Feld die Anzahl der Benutzer fest, die gleichzeitig zu einem bestimmten Zeitpunkt eine aktive Testversion verwenden können. Jeder Benutzer verwendet eine Dynamics-Lizenz, während seine Testversion aktiv ist, sodass Sie sicherstellen müssen, dass Sie mindestens so viele Dynamics-Lizenzen für die Benutzer der Testversionen zur Verfügung stellen. Empfohlener Wert liegt zwischen 3 und 5.
- **Dauer der Testversion (Stunden)** : Legen Sie in diesem Feld die Anzahl der Stunden fest, für die die Testversion des Benutzers aktiv sein soll. Nach dieser Anzahl von Stunden wird die Bereitstellung des Benutzers für Ihren Mandanten aufgehoben. Empfohlener Wert von 2 bis 24 Stunden, abhängig von der Komplexität Ihrer App. Der Benutzer kann jederzeit eine weitere Testversion anfordern, wenn seine Zeit abgelaufen ist und er erneut auf die Testversion zugreifen möchte.
- **Instanz-URL**: Geben Sie eine URL an, zu der der Benutzer der Testversion beim Start der Testversion zunächst navigiert wird. Dies ist in der Regel die URL Ihrer Dynamics 365-Instanz, auf der Ihre App und Beispieldaten installiert sind. Beispielwert: https:\//testdrive.crm.dynamics.com
- **Azure AD-Mandanten-ID**: Geben Sie die ID des Azure-Mandanten für Ihre Dynamics 365-Instanz an. Um diesen Wert abzurufen, melden Sie sich am Azure-Portal an und navigieren Sie zu \'Azure Active Directory\' -\> Wählen Sie „Eigenschaften“ auf dem Menüblatt -\> aus. Kopieren Sie die Verzeichnis-ID. Beispielwert: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **Azure AD-App-ID**: ID der Azure AD-App, die Sie in Schritt 7 erstellt haben. \ Beispielwert: 53852862-a2ae-4e43-9461-faa49650a096
- **Azure AD-App-Schlüssel**: Geheimer Schlüssel für die Azure AD-App, die Sie in Schritt 7 erstellt haben.\ Beispielwert: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Name des Azure AD-Mandanten**: Geben Sie den Namen des Azure-Mandanten für Ihre Dynamics 365-Instanz an. Verwenden Sie das Format der \<Mandantenname.\>onmicrosoft.com. Beispielwert: testdrive.onmicrosoft.com
- **Web-API-URL der Instanz**: Geben Sie die Web-API-URL für Ihre Dynamics 365-Instanz an. Sie können diesen Wert abrufen, indem Sie sich bei Ihrer Microsoft Dynamics 365-Instanz anmelden und zu „Einstellung -\> Anpassung -\> Entwicklerressourcen -\> Web-API der Instanz“ navigieren (diese URL kopieren). Beispielwert: https:\//testdrive.crm.dynamics.com/api/data/v9.0 
- **Rollenname**: Geben Sie den Namen der benutzerdefinierten Dynamics 365-Sicherheitsrolle an, die Sie für die Testversion erstellt haben. Dies ist die Rolle, die den Benutzern während der Testversion zugewiesen wird. Beispielwert: testdriverole

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihre App erfolgreich die Zertifizierung durchlaufen hat und Sie bereit sind, Ihr Angebot zu  **veröffentlichen** , wird Ihnen eine  **Vorschauversion**  Ihres Angebots angezeigt. Starten Sie eine Testversion in der Benutzeroberfläche, und überprüfen Sie, ob Ihre Testversionen ordnungsgemäß ausgeführt werden. Wenn Sie mit der Vorschauversion Ihres Angebots zufrieden sind, können Sie es  **veröffentlichen**.

---
title: Dynamics 365 for Customer Engagement-Anwendungsangebot – Registerkarte „Testversion“ | Azure Marketplace
description: Beschreibung des Konfigurierens einer Testversion für ein Dynamics 365 for Customer Engagement-Anwendungsangebot auf dem AppSource Marketplace.
services: Azure, Marketplace, AppSource, Cloud Partner Portal, Dynamics 365 for Customer Engagement
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 5bb5f39ef5f5bce09a8639ba9eedc6d042e60c1d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942362"
---
# <a name="dynamics-365-for-customer-engagement-application-test-drive-tab"></a>Dynamics 365 for Customer Engagement-Angebot – Registerkarte „Testversion“

Über die Registerkarte **Testversion** können Sie eine Testversion für Ihre Kunden erstellen.  Diese Option bietet Kunden einen praktischen, selbstgesteuerten Test der wichtigsten Funktionen und Vorteile Ihres Angebots, die in einem realitätsnahen Implementierungsszenario veranschaulicht werden.  Unter den verfügbaren Testoptionen ist „Testversion“ die wirksamste Option, um aussichtsreiche Leads zu gewinnen und eine vermehrte Umwandlung dieser Leads zu erreichen.  Weitere Informationen finden Sie unter [Was ist die Testversion?](../test-drive/what-is-test-drive.md)

Die Testversion von Dynamics 365-Anwendungen wird automatisch als von Microsoft gehostete Lösung ausgeführt.  Weitere Informationen finden Sie unter [Gehostete Testversion](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive).

Die Registerkarte „Testversion“ verfügt über drei mögliche Abschnitte: **Testversion**, **Details** und **Technische Konfiguration**.  Die letzten beiden Abschnitten werden nur angezeigt, nachdem Sie die Testversion-Funktionalität aktiviert haben.  Mit einem Sternchen (*) gekennzeichnete Felder sind Pflichtfelder. 


## <a name="test-drive-section"></a>Abschnitt „Testversion“

Wählen Sie zum Aktivieren dieser Funktionalität **Ja** für **Testversion aktivieren** aus.


## <a name="details-section"></a>Abschnitt „Details“

Grundlegende Informationen zur Testversion stellen Sie im Abschnitt **Details** zur Verfügung.   

![Abschnitt „Details“ der Testversion](./media/test-drive-tab-details.png)

In der folgenden Tabelle sind die Felder beschrieben, die zum Einrichten der Testversion für Ihre Dynamics 365-Anwendung erforderlich sind. Erforderliche Felder sind durch ein Sternchen (*) gekennzeichnet.

|      Feld                    |    BESCHREIBUNG                  |
|    ---------                  |  ---------------                |
|      Beschreibung\*            |   Beschreiben Sie, wozu Ihre Testversion verwendet werden kann. Sie können einfache HTML-Tags verwenden, um diese Beschreibung zu formatieren. Zum Beispiel &lt;p&gt;, &lt;Em&gt;, &lt;Ul&gt;, &lt;li&gt;, &lt;Ol&gt; und Überschriften.  |
|  Benutzerhandbuch\*                |   Laden Sie ein Benutzerhandbuch hoch, über das Ihre Kunden die Benutzeroberfläche der Testversion durchlaufen können. Dieses Dokument muss eine PDF-Datei sein. |
|  Demovideo für Testversion (optional) |  Sie können eine Videoanleitung für Ihre Testversion bereitstellen. Ein Kunde kann sich dieses Video vor dem Nutzen der Testversion ansehen. Geben Sie die URL zu dem auf YouTube oder Vimeo befindlichen Video an. Wenn Sie **+ Video hinzufügen** auswählen, werden Sie aufgefordert, die folgenden Informationen bereitzustellen:<ul><li>NAME</li><li>URL</li><li>Vorschauminiatur (im PNG-Format, 533 x 324 Pixel)</li></ul>  |
|   |   |


## <a name="technical-configuration-section"></a>Abschnitt „Technische Konfiguration“

In diesem Abschnitt stellen Sie technische Details über Ihre Testversion bereit.

![Abschnitt „Details“ der Testversion](./media/test-drive-tab-tech-config.png)

Die Felder haben folgende Zwecke.  Erforderliche Felder sind durch ein Sternchen (*) gekennzeichnet.

|      Feld                    |    BESCHREIBUNG                  |
|    ---------                  |  ---------------                |
| Typ der Testversion\*            | Wählen Sie **Von Microsoft gehostet (Dynamics 365 for Customer Engagement)** aus.  |
| Maximale Anzahl gleichzeitiger Testversionen\*    | Die Anzahl gleichzeitiger Instanzen einer aktiven Testversion zu einem bestimmten Zeitpunkt. Jeder Benutzer verwendet eine Dynamics-Lizenz, während seine Testversion aktiv ist, sodass Sie sicherstellen müssen, dass Sie mindestens so viele Dynamics-Lizenzen für die Benutzer der Testversionen zur Verfügung stellen. Empfohlener Wert liegt zwischen 3 und 5.  |
| Dauer der Testversion (Stunden)\*   | Maximale Anzahl von Stunden, für die die Testversioninstanz des Benutzers aktiv ist. Nachdem dieser Zeitraum überschritten ist, wird die Bereitstellung der Instanz für Ihren Mandanten aufgehoben. Empfohlener Wert von 2 bis 24 Stunden, abhängig von der Komplexität Ihrer App. Der Benutzer kann jederzeit eine weitere Testversion anfordern, wenn seine Zeit abgelaufen ist und er die Testversion erneut bewerten möchte.  |
| Instanz-URL\*                  | URL, zu der die Testversion zu Anfang wechselt. Dies ist in der Regel die URL Ihrer Dynamics 365-Instanz, auf der Ihre App und Beispieldaten installiert sind.  |
| Azure AD-Mandanten-ID\*            | GUID des Azure-Mandanten für Ihre Dynamics 365-Instanz. Um diesen Wert abzurufen, melden Sie sich beim Azure-Portal an und navigieren zu **Azure Active Directory** > **wählen Sie „Eigenschaften“ aus** > **kopieren Sie die Verzeichnis-ID**.  |
| Azure AD-App-ID\*               | GUID Ihrer Azure AD-Anwendung  |
| Azure AD-App-Schlüssel\*              | Geheimnis Ihrer Azure AD-Anwendung, z.B.: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` |
| Name des Azure AD-Mandanten\*          | Name des Azure-Mandanten für Ihre Dynamics 365-Instanz. Verwenden Sie das Format <Mandantenname>.onmicrosoft.com, z.B.: `testdrive.onmicrosoft.com`  |
| Web-API-URL der Instanz\*          | Web-API-URL für Ihre Dynamics 365-Instanz. Sie können diesen Wert abrufen, indem Sie sich bei Ihrer Microsoft Dynamics 365-Instanz anmelden und zu **Einstellungen** > **Anpassung** > **Entwicklerressourcen** > **Web-API der Instanz (diese URL kopieren)** navigieren. Beispielwert: `https://testdrive.crm.dynamics.com/api/data/v9.0`  |
| Rollenname\*                     | Name der benutzerdefinierten Dynamics 365-Sicherheitsrolle, die Sie für Ihre Testversion erstellt haben, und die den Benutzern zugewiesen wird, wenn sie sie ausführen, z.B. `testdriveuser`. |
|  |  |

Nachdem Sie alle erforderlichen Informationen angegeben haben, wählen Sie **Speichern** aus.


## <a name="next-steps"></a>Nächste Schritte

Als Nächstes stellen Sie Marketing- und Vertriebsinformationen auf der [Registerkarte „Details der digitalen Ladenzeile“](./cpp-storefront-details-tab.md) bereit.


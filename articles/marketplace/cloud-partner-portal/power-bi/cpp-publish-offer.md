---
title: Veröffentlichen eines Power BI-App-Angebots | Azure Marketplace
description: Veröffentlichen eines Power BI-App-Angebots im Microsoft AppSource-Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pabutler
ms.openlocfilehash: aae23feaf1cc5887de061414af985ef16070546b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943197"
---
# <a name="publish-a-power-bi-app-offer"></a>Veröffentlichen eines Power BI-App-Angebots

Nachdem Sie das Angebot im Cloud-Partnerportal definiert und die zugehörigen technischen Ressourcen erstellt haben, wird das Angebot im letzten Schritt zur Veröffentlichung übermittelt. Wählen Sie im linken Bereich des Fensters **Neues Angebot** die Option **Veröffentlichen** aus, um diesen Vorgang zu starten. Weitere Informationen finden Sie unter [Veröffentlichen von Azure Marketplace- und AppSource-Angeboten](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Veröffentlichungsschritte

Im Veröffentlichungsprozess gibt es folgende Hauptschritte:

![Schritte des Veröffentlichungsprozesses für ein Power BI-App-Angebot](./media/publishing-process-steps.png)

In dieser Tabelle sind die einzelnen Schritte und die geschätzte Zeit bis zum Abschluss beschrieben:

|   Veröffentlichungsschritt            |   Time     |   BESCHREIBUNG                                                                  |
| --------------------         |------------| ----------------                                                               |
| Überprüfen der Voraussetzungen       | 15 Minuten     | Die Angebotsinformationen und -einstellungen werden überprüft.                            |
| Zertifizierung                | 1–7 Tage   | Das Power BI-Zertifizierungsteam analysiert Ihr Angebot. Das Team unterzieht Ihre Power BI-App einem manuellen Prüfungstest. Dazu wird die App über die angegebene Installations-URL installiert. Die primären Prüfungen erfolgen als Teil des App-Zertifizierungsprozesses (weiter unten beschrieben).         |
| Verpackung                    | \< 1 Stunde  | Die technischen Ressourcen des Angebots werden für die Verwendung durch den Kunden verpackt.                        |
| Registrierung der Leadgenerierung | \< 1 Stunde  | Die Leadsysteme werden konfiguriert und bereitgestellt.                                      |
| Einholen der Genehmigung des Herausgebers            | \-         | Sie müssen eine letzte Überprüfung und Bestätigung abschließen, bevor das Angebot live geschaltet wird. Nun wird auch ein Link zu einer Vorschau Ihres Angebots angezeigt. Wenn Sie mit der Darstellung in der Vorschau zufrieden sind, wählen Sie **Live schalten** auf der Registerkarte **Status** aus. Damit wird eine Anforderung an das Onboardingteam gesendet, Ihre App in AppSource aufzulisten.    |
| Live                         | \< 3 Stunden | Ihr Angebot wird jetzt in AppSource öffentlich aufgelistet (live geschaltet), und Kunden können Ihre App anzeigen und in ihren Power BI-Abonnements bereitstellen. Außerdem erhalten Sie eine Bestätigungs-E-Mail. In der rechten Spalte auf der Registerkarte **Alle Angebote** wird der Status für all Ihre Angebote angezeigt. Auf der Registerkarte **Status** können Sie den Status des Veröffentlichungsvorgangs für Ihr Angebot im Detail anzeigen. |
|   |   |

Es kann bis zu acht Tage dauern, bis dieser Prozess abgeschlossen ist. Nach dem Ausführen dieser Veröffentlichungsschritte wird Ihr Power BI-App-Angebot in [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) unter den Power BI-Apps aufgeführt.


### <a name="app-certification-process"></a>App-Zertifizierungsprozess

Das Microsoft-Onboardingteam überprüft die Übermittlung Ihres Power BI-Angebots mit folgendem Verfahren:

1. Die rechtlich relevanten Dokumente und Hilfelinks werden überprüft.
2. Die Kontaktinformationen des Supports werden überprüft.
3. Die ordnungsgemäße Installation wird über die Installations-URL überprüft.
4. Die App wird auf Schadsoftware und andere schädliche Inhalte überprüft.
5. Es wird überprüft, ob der angezeigte Inhalt mit der Beschreibung der App übereinstimmt.
6. Es wird überprüft, ob die Vorgänge im Zusammenhang mit der App in Power BI erwartungsgemäß funktionieren. Das Team öffnet Berichte und Dashboards mit Beispieldaten, stellt eine Verbindung zu benutzerdefinierten Datenquellen her, aktualisiert Daten usw.

Das Zertifizierungsteam gibt Feedback, wenn Probleme gefunden werden.  Weitere Informationen zu den Anforderungen für Power BI-Apps finden Sie in der [Dokumentation zu Power BI-Apps](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Nächste Schritte

Es wird empfohlen, Ihre App im [AppSource-Marketplace](https://appsource.microsoft.com) regelmäßig zu überwachen.  Sie sollten auch die Funktion [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) im [Cloud-Partnerportal](https://cloudpartner.azure.com/#insights) nutzen, um Erkenntnisse über Ihre Marketplace-Kunden und die Verwendung der App zu gewinnen. Schließlich können Sie [Ihr Angebot aktualisieren](./cpp-update-existing-offer.md).

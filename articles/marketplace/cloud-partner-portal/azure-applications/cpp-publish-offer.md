---
title: Veröffentlichen eines Azure-Anwendungsangebots | Azure Marketplace
description: Beschreibt den Prozess und die Schritte zum Veröffentlichen eines Azure-Anwendungsangebots im Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: pabutler
ms.openlocfilehash: 2326ce1a591d1276dbaf9c7f3238f7214e5134ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942913"
---
# <a name="publish-azure-application-offer"></a>Veröffentlichen eines Azure-Anwendungsangebots

Nachdem Sie die Informationen auf der Seite **Neues Angebot** angegeben haben, um ein Angebot zu erstellen, können Sie das Angebot veröffentlichen. Klicken Sie auf **Veröffentlichen**, um den Veröffentlichungsprozess zu starten.

Das folgende Diagramm zeigt die wichtigsten Schritte des Veröffentlichungsprozesses, die ausgeführt werden müssen, um ein Angebot live zu schalten.

![Angebotsveröffentlichungsschritte](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Ausführliche Beschreibung der Veröffentlichungsschritte

In der folgenden Tabelle sind die einzelnen Veröffentlichungsschritte sowie deren voraussichtliche Dauer aufgeführt und beschrieben.  Zeitschätzungen in „Tagen“ beziehen sich auf Werktage, ohne Wochenenden und Feiertage.

|  **Veröffentlichungsschritt**           | **Time**    | **Beschreibung**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Überprüfen der Voraussetzungen         | < 15 Minuten    | Die Angebotsinformationen und -einstellungen werden überprüft.                        |
| Überprüfen der beeinflussten Umsatzeinstellungen | < 15 Minuten  | Die Zuordnung der Azure-Ressourcennutzung wird für das Angebot überprüft.             |
| Zertifizierung                  | < 1 Tag     | Das Angebot wird vom Azure-Zertifizierungsteam analysiert. Das Angebot wird auf Viren, Schadsoftware, Sicherheitskonformität und Sicherheitsprobleme untersucht. Das Angebot wird überprüft, um festzustellen, ob es die Eignungskriterien erfüllt. Weitere Informationen finden Sie unter [Voraussetzungen](./cpp-prerequisites.md). Sollte ein Problem gefunden werden, erhalten Sie entsprechendes Feedback. |
| Überprüfung der Testversion          | < 2 Stunden   | (Optional) Wenn ein Testlaufwerk vorhanden ist, überprüft Microsoft, ob dieses bereitgestellt und repliziert werden kann.  |
| Paketerstellung und Registrierung von generierten Leads | < 1 Stunde  | Die technischen Ressourcen des Angebots werden für die Verwendung durch die Kunden gepackt, und die Leadsysteme werden konfiguriert und bereitgestellt. |
|  Abzeichnung des Verlegers             |  manual    | Letzte Überprüfung und Bestätigung durch den Herausgeber, bevor das Angebot live geschaltet wird. Das Angebot ist jetzt als Vorschau verfügbar.  Sie können Ihr Angebot in den ausgewählten Abonnements (in den Schritten für die Angebotsinformationen) bereitstellen, um sicherzugehen, dass es alle Ihre Anforderungen erfüllt.  Nachdem Sie Ihr Angebot überprüft haben, wählen Sie **Freischalten** aus, damit der nächste Schritt für Ihr Angebot ausgeführt werden kann. |
| Prüfung durch Microsoft                | 7-14 Tage | Microsoft überprüft Ihre Azure-Anwendung ganzheitlich und sendet Ihnen E-Mails, wenn Probleme entdeckt werden.  Die Dauer dieses Schrittes hängt von der Komplexität der Anwendung, den aufgedeckten Problemen ab und davon, wie schnell Sie darauf reagieren.  |
| Live                           | < 1 Tag | Das Angebot wird veröffentlicht, in den angegebenen Regionen repliziert und für die Öffentlichkeit verfügbar gemacht. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

Sie können den Veröffentlichungsvorgang auf der Registerkarte **Status** für Ihr Angebot im Cloud-Partnerportal überwachen.

![Registerkarte „Status“ für ein Azure-App-Angebot](./media/offer-status-tab.png)

Nach Abschluss des Veröffentlichungsvorgangs wird Ihr Angebot in der [Microsoft Azure Marketplace-Anwendungskategorie](https://azuremarketplace.microsoft.com/marketplace/apps/) aufgelistet.

>[!Note]
>Die Nutzung des Cloud Solution Provider (CSP)-Partnerkanals ist jetzt verfügbar.  Unter [Cloud Solution Providers](../../cloud-solution-providers.md) finden Sie weitere Informationen zum Vermarkten Ihres Angebots über die Microsoft CSP-Partnerkanäle.

## <a name="errors-and-review-feedback"></a>Fehler und Prüfungsfeedback

Zusätzlich zum Veröffentlichungsstatus Ihres Angebots werden auf der Registerkarte **Status** auch Fehlermeldungen und Feedback zu Veröffentlichungsschritten angezeigt, bei denen ein Fehler aufgetreten ist.  Wenn das Problem kritisch ist, wird die Veröffentlichung abgebrochen.  Sie müssen die gemeldeten Probleme beheben und das Angebot erneut veröffentlichen.  Da der Schritt **Prüfung durch Microsoft** eine umfassende Überprüfung Ihres Angebots und der damit verbundenen technischen Ressourcen (insbesondere der Azure Resource Manager-Vorlage) darstellt, werden Probleme in der Regel als Pull Request-Links dargestellt.  Eine Erklärung, wie Sie diese Pull Requests anzeigen und auf sie reagieren, finden Sie unter [Verarbeiten von Prüfungsfeedback](./cpp-handling-review-feedback.md).


## <a name="next-steps"></a>Nächste Schritte

Wenn Sie in einem oder mehreren der Veröffentlichungsschritte Fehler festgestellt haben, müssen Sie diese korrigieren und Ihr Angebot erneut veröffentlichen.  Wenn im Schritt **Prüfung durch Microsoft** kritische Probleme festgestellt werden, müssen Sie [das Prüfungsfeedback verarbeiten](./cpp-handling-review-feedback.md), indem Sie auf das Azure DevOps-Repository des Microsoft-Prüfungsteams zugreifen.

Sobald eine Azure-App erfolgreich veröffentlicht wurde, können Sie [das bestehende Angebot aktualisieren](./cpp-update-existing-offer.md), damit es sich ändernden geschäftlichen oder technischen Anforderungen gerecht wird. 

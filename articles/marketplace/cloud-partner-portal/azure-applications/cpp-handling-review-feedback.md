---
title: Verarbeiten von Prüfungsfeedback zu Azure-Anwendungen – Azure Marketplace
description: In diesem Artikel wird erklärt, wie Sie Azure DevOps verwenden können, um Prüfungsfeedback zu Azure-Anwendungsangeboten für Azure Marketplace zu verarbeiten.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: pabutler
ms.openlocfilehash: 57efbfe12df8072d619cf71f4b08da2e2e5be3ce
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942959"
---
# <a name="handling-review-feedback"></a>Verarbeiten von Prüfungsfeedback

In diesem Artikel wird erläutert, wie Sie auf die Azure DevOps-Umgebung zugreifen können, die vom Microsoft Azure Marketplace-Prüfungsteam verwendet wird.  Wenn während des Schritts **Prüfung durch Microsoft** kritische Probleme in Ihrem Azure-Anwendungsangebot gefunden werden, können Sie sich bei diesem System anmelden, um detaillierte Informationen zu diesen Problemen einzusehen (Prüfungsfeedback).  Nachdem Sie alle diese Probleme behoben haben, müssen Sie Ihr Angebot erneut übermitteln, damit es in Azure Marketplace weiter veröffentlicht wird.  Das folgende Diagramm veranschaulicht, wie sich dieser Feedbackvorgang auf den Veröffentlichungsvorgang auswirkt.

![Veröffentlichungsschritte mit VSTS-Feedback](./media/pub-flow-vsts-access.png)

In der Regel werden bei der Prüfung festgestellte Probleme als Pull-Requests (PR) bezeichnet.  Jeder Pull Request ist mit einem Onlineelement von [Azure DevOps](https://azure.microsoft.com/services/devops/) (zuvor Visual Studio Team Services [VSTS] genannt) verknüpft, das Details zu dem Problem enthält.  Die folgende Abbildung zeigt ein Beispiel für einen PR-Verweis einer Prüfung.  Für komplexe Szenarien erhalten Sie E-Mails vom Prüfungs- und Supportteam. 

![Registerkarte „Status“ mit Prüfungsfeedback](./media/status-tab-ms-review.png)


## <a name="vsts-access"></a>VSTS-Zugriff

Zum Anzeigen der Pull Request-Elemente, auf die im Prüfungsfeedback verwiesen wird, müssen Herausgeber über eine entsprechende Autorisierung verfügen.  Andernfalls wird neuen Herausgebern eine Antwortseite des Typs `401 - Not Authorized` angezeigt, wenn versucht wird, Pull Requests einzusehen.  Um den Zugriff auf dieses Azure DevOps-Repository anzufordern, führen Sie die folgenden Schritte aus:

1. Stellen Sie die folgenden Informationen zusammen:
    - Name und ID des Herausgebers
    - Angebotstyp (Azure-App), Angebotsnamen und SKU-ID
    - Pull-Request-Link, z. B.: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` Diese URL kann aus der Benachrichtigungsnachricht oder der Adresse der Antwortseite 401 abgerufen werden.
    - Die E-Mail-Adresse(n) der Personen aus Ihrer Veröffentlichungsorganisation, für die Sie Zugriff erhalten möchten.  Diese Liste muss die Besitzeradressen enthalten, die Sie bei der Registrierung im Cloud-Partnerportal als Herausgeber angegeben haben.
2. Erstellen Sie einen Supportvorfall.  Wählen Sie in der Titelleiste des Cloud-Partnerportals die Schaltfläche **Hilfe** und dann **Support** aus dem Menü aus.  Ihr Standardwebbrowser sollte gestartet werden und zur Microsoft-Seite für den neuen Supportvorfall navigieren.  (Möglicherweise müssen Sie sich zunächst anmelden.)
3. Geben Sie **Marketplace-Onboarding** als **Problemtyp** und **Zugriffsproblem** als **Kategorie** an, und wählen Sie dann **Anforderung starten** aus.

    ![Supportticketkategorie](./media/support-incident1.png)

4. Geben Sie auf der Seite **Schritt 1 von 2** Ihre Kontaktinformationen ein, und wählen Sie **Weiter** aus.
5. Geben Sie auf der Seite **Schritt 2 von 2** einen Vorfalltitel (z. B. `Request VSTS access`) sowie die Informationen an, die Sie im ersten Schritt (oben) gesammelt haben.  Lesen und akzeptieren Sie die Vereinbarung, und wählen Sie dann **Senden** aus.

Wenn die Erstellung des Vorfalls erfolgreich war, wird eine Bestätigungsseite angezeigt.  Speichern Sie die Bestätigungsinformationen auf dieser Seite zur künftigen Bezugnahme.  Das Microsoft-Supportteam sollte innerhalb weniger Werktage auf Ihre Zugriffsanforderung antworten.


## <a name="reviewing-the-pull-request"></a>Prüfen des Pull Requests 

Gehen Sie wie folgt vor, um die im Pull Request dokumentierten Probleme zu prüfen.

1. Klicken Sie im Abschnitt **Prüfung durch Microsoft** des Formulars **Veröffentlichungsschritte** auf einen Pull Request-Link, um Ihren Browser zu öffnen, und wechseln Sie dann zur Seite **Übersicht** (Startseite) dieses Pull Requests.  Die folgende Abbildung zeigt eine exemplarische Startseite mit kritischem Problem für das Beispielangebot einer App von Contoso.  Diese Seite enthält nützliche zusammenfassende Informationen zu den bei der Prüfung der Azure-App gefundenen Problemen.  

    [![Pull Request-Startseite](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Klicken Sie auf die Abbildung, um sie zu vergrößern.*
    
2. (Optional) Klicken Sie rechts im Fenster im Abschnitt **Richtlinien** auf die Problemmeldung (in diesem Beispiel: **Fehler bei Validierung der Richtlinie**), um die untergeordneten Details des Problems einschließlich der zugehörigen Protokolldateien zu untersuchen.  Fehler werden in der Regel am unteren Rand der Protokolldateien angezeigt.

3. Wählen Sie im Menü auf der linken Seite der Startseite **Dateien**, um die Listendateien anzuzeigen, die die technischen Ressourcen für dieses Angebot darstellen.  Die Microsoft-Prüfer sollten Kommentare hinzugefügt haben, die die ermittelten kritischen Probleme beschreiben.  Im folgenden Beispiel wurden zwei Probleme ermittelt. 

    [![Pull Request-Startseite](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Klicken Sie auf die Abbildung, um sie zu vergrößern.*

4. Klicken Sie in der linken Struktur auf die einzelnen Kommentarknoten, um im Kontext des umgebenden Codes zum Kommentar zu navigieren.  Korrigieren Sie Ihren Quellcode im Projekt Ihres Teams, um das im Kommentar beschriebene Problem zu beheben.

> [!Note]
> Sie können die technischen Ressourcen Ihres Angebots nicht in der Azure DevOps-Umgebung des Prüfungsteams bearbeiten.  Für Herausgeber ist dies eine schreibgeschützte Umgebung für den enthaltenen Quellcode.  Sie können jedoch Antworten auf die Kommentare hinterlassen, die dem Microsoft-Prüfungsteam zugutekommen.

   Im folgenden Beispiel hat der Herausgeber das erste Problem geprüft, korrigiert und mit einer Antwort versehen.

   ![Erste Korrektur und Antwort auf Kommentar](./media/first-comment-reply.png)


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die kritischen Probleme korrigiert haben, die in den Pull Requests der Prüfung dokumentiert sind, müssen Sie [Ihr Azure-App-Angebot erneut veröffentlichen](./cpp-publish-offer.md).

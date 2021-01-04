---
title: 'Prüfen des Feedbacks zu Azure-App-Angeboten: Kommerzieller Microsoft-Marketplace'
description: Hier erfahren Sie, wie Sie mit Feedback zu Ihrem Azure-Anwendungsangebot vom Microsoft Azure Marketplace-Prüfungsteam umgehen. Auf das Feedback können Sie in Azure DevOps mit Ihren Partner Center-Anmeldeinformationen zugreifen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: ede4e5dd781851c781407a8acea640e3e501f259
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436384"
---
# <a name="handling-review-feedback-for-azure-application-offers"></a>Behandeln von Überprüfungsfeedback zu Azure-Anwendungsangeboten

In diesem Artikel erfahren Sie, wie Sie in [Azure DevOps](https://azure.microsoft.com/services/devops/) auf Feedback vom Microsoft Azure Marketplace-Prüfungsteam zugreifen können. Wenn während des Schritts **Prüfung durch Microsoft** kritische Probleme in Ihrem Azure-Anwendungsangebot gefunden werden, können Sie sich bei diesem System anmelden, um detaillierte Informationen zu diesen Problemen einzusehen (Prüfungsfeedback). Nachdem Sie alle Probleme behoben haben, müssen Sie Ihr Angebot erneut übermitteln, damit es weiterhin im Azure Marketplace veröffentlicht wird. Das folgende Diagramm veranschaulicht, wie sich dieser Feedbackvorgang auf den Veröffentlichungsvorgang auswirkt.

![Der Prozess des Prüfungsfeedbacks](./media/review-feedback-process.png)

In der Regel werden bei der Prüfung festgestellte Probleme als Pull Request (PR) angegeben. Jeder PR ist mit einem Azure DevOps-Onlineelement verknüpft, das Details zu dem Problem enthält. Die folgende Abbildung zeigt ein Beispiel der Partner Center-Oberfläche, wenn während der Prüfung Fehler gefunden werden. 

![Veröffentlichungsstatus](./media/publishing-status.png)

Der Pull Request, der bestimmte Informationen über die Übermittlung enthält, wird im Link „Zertifizierungsbericht anzeigen“ erwähnt. Für komplexe Szenarien erhalten Sie E-Mails vom Prüfungs- und Supportteam.

## <a name="azure-devops-access"></a>Azure DevOps-Zugriff

Alle Benutzer mit Zugriff auf die Rolle „Entwickler“ im Partner Center können die Pull Request-Elemente im Prüfungsfeedback sehen.

## <a name="reviewing-the-pull-request"></a>Prüfen des Pull Requests

Gehen Sie wie folgt vor, um die im Pull Request dokumentierten Probleme zu prüfen.

1. Wählen Sie in den Abschnitten vom Typ **Prüfung durch Microsoft** des Formulars mit den Veröffentlichungsschritten einen PR-Link aus, um Ihren Browser zu öffnen, und navigieren Sie dann zur Seite **Übersicht** (Startseite) dieses PR. Die folgende Abbildung zeigt eine exemplarische Startseite mit kritischem Problem für das Beispielangebot einer App von Contoso. Diese Seite enthält nützliche zusammenfassende Informationen zu den bei der Prüfung der Azure-App gefundenen Problemen.

    [![Pull Request-Startseite](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Klicken Sie auf diese Abbildung, um sie zu vergrößern.*

1. (Optional) Wählen Sie rechts im Fenster im Abschnitt **Richtlinien** die Problemmeldung (in diesem Beispiel: **Fehler bei Validierung der Richtlinie**) aus, um die untergeordneten Details des Problems einschließlich der zugehörigen Protokolldateien zu untersuchen. Fehler werden in der Regel am unteren Rand der Protokolldateien angezeigt.

1. Wählen Sie im Menü auf der linken Seite der Startseite **Dateien**, um die Listendateien anzuzeigen, die die technischen Ressourcen für dieses Angebot darstellen. Die Microsoft-Prüfer sollten Kommentare hinzugefügt haben, die die ermittelten kritischen Probleme beschreiben. Im folgenden Beispiel wurden zwei Probleme ermittelt.

    [![Screenshot mit hervorgehobener Option „Dateien“ und den beiden ermittelten Problemen](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Klicken Sie auf diese Abbildung, um sie zu vergrößern.*

1. Wählen Sie in der linken Struktur die einzelnen Kommentarknoten aus, um im Kontext des umgebenden Codes zum Kommentar zu navigieren. Korrigieren Sie Ihren Quellcode im Projekt Ihres Teams, um das im Kommentar beschriebene Problem zu beheben.

>[!Note]
>Sie können die technischen Ressourcen Ihres Angebots nicht in der Azure DevOps-Umgebung des Prüfungsteams bearbeiten. Für Herausgeber ist dies eine schreibgeschützte Umgebung für den enthaltenen Quellcode. Sie können jedoch Antworten auf die Kommentare hinterlassen, die dem Microsoft-Prüfungsteam zugutekommen.

   Im folgenden Beispiel hat der Herausgeber das erste Problem geprüft, korrigiert und mit einer Antwort versehen.

   ![Erste Korrektur und Antwort auf Kommentar](./media/first-comment-reply.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die kritischen Probleme korrigiert haben, die in den Pull Requests der Prüfung dokumentiert sind, müssen Sie [Ihr Azure-App-Angebot erneut veröffentlichen](../create-new-azure-apps-offer.md).

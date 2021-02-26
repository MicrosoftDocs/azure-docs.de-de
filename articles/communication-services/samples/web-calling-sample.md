---
title: 'Azure Communication Services: Webanrufbeispiel'
titleSuffix: An Azure Communication Services sample
description: Informationen zum Beispiel für Webanrufe in Communication Services
author: chriswhilar
manager: mariusu-msft
services: azure-communication-services
ms.author: mariusu
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: fbd5d3c4489d124014e0d956359f64a8ec9c568a
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592788"
---
# <a name="get-started-with-the-web-calling-sample"></a>Erste Schritte mit dem Webanrufbeispiel

Das Webanrufbeispiel ist eine Webanwendung, die als schrittweise exemplarische Vorgehensweise für die verschiedenen Funktionen der Webanruf-Clientclibliothek von Communication Services fungiert. 

Dieses Beispiel wurde für Entwickler erstellt und erleichtert Ihnen den Einstieg in Communication Services. Seine Benutzeroberfläche ist in mehrere Abschnitte unterteilt, die jeweils eine Schaltfläche „Show Code“ (Code anzeigen) aufweisen, mit der Sie Code direkt aus Ihrem Browser in Ihre eigene Communication Services-Anwendung kopieren können.

## <a name="get-started-with-the-web-calling-sample"></a>Erste Schritte mit dem Webanrufbeispiel

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

> [!IMPORTANT]
> [Dieses Beispiel ist auf GitHub ](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/) verfügbar.

Halten Sie sich an „/Project/readme.md“, um das Projekt einzurichten und lokal auf Ihrem Computer auszuführen.
Wenn das [Webanrufbeispiel](https://github.com/Azure-Samples/communication-services-web-calling-tutorial) auf dem Computer ausgeführt wird, wird die folgende Landing Page angezeigt:

:::image type="content" source="./media/web-calling-tutorial-page-1.png" alt-text="Web calling tutorial 1 (Webanrufe: Tutorial 1)" lightbox="./media/web-calling-tutorial-page-1.png":::

:::image type="content" source="./media/web-calling-tutorial-page-2.png" alt-text="Web calling tutorial 2 (Webanrufe: Tutorial 2)" lightbox="./media/web-calling-tutorial-page-2.png":::

## <a name="user-provisioning-and-sdk-initialization"></a>Benutzerbereitstellung und SDK-Initialisierung 

Klicken Sie auf „Provisioning user and initialize SDK“ (Benutzer bereitstellen und SDK initialisieren), um das SDK mit einem Token zu initialisieren, das vom Back-End-Tokenbereitstellungsdienst bereitgestellt wird. Dieser Back-End-Dienst befindet sich in `/project/webpack.config.js`.

Klicken Sie auf die Schaltfläche „Show Code“ (Code anzeigen), um den Beispielcode anzuzeigen, den Sie in Ihrer eigenen Lösung verwenden können.

Nachdem das SDK initialisiert wurde, sollte Folgendes angezeigt werden:

:::image type="content" source="./media/user-provisioning.png" alt-text="Benutzerbereitstellung" lightbox="./media/user-provisioning.png":::

Sie sind jetzt bereit, mit der Communication Services-Ressource Anrufe zu tätigen.

## <a name="placing-and-receiving-calls"></a>Tätigen und Annehmen von Anrufen

Das Webanruf-SDK für Communication Services ermöglicht **1:1**-, **1:N**- und **Gruppenanrufe**.

Bei ausgehenden 1:1- oder 1:N-Anrufen können Sie mehrere anzurufende Communication Services-Benutzeridentitäten angeben, indem Sie durch Trennzeichen getrennte Werte verwenden. Sie können auch anzurufende herkömmliche Telefonnummern (PSTN) angeben, indem Sie durch Trennzeichen getrennte Werte verwenden. 

Wenn Sie PSTN-Telefonnummern anrufen, geben Sie Ihre alternative Anrufer-ID an. Klicken Sie auf die Schaltfläche „Place call“ (Anrufen), um einen ausgehenden Anruf zu tätigen:

:::image type="content" source="./media/place-a-call.png" alt-text="Tätigen eines Anrufs" lightbox="./media/place-a-call.png":::

Geben Sie zum Beitreten zu einem Gruppenanruf die GUID ein, die den Anruf identifiziert, und klicken Sie auf die Schaltfläche „Join group“ (Gruppe beitreten):

:::image type="content" source="./media/join-a-group-call.png" alt-text="Teilnehmen an einem Gruppenanruf" lightbox="./media/join-a-group-call.png":::

Klicken Sie auf die Schaltfläche „Show code“ (Code anzeigen), um den Beispielcode für das Tätigen von Anrufen, das Empfangen von Anrufen und das Beitreten zu Gruppenanrufen anzuzeigen.

Ein aktiver Anruf sieht wie folgt aus:

:::image type="content" source="./media/group-call.png" alt-text="Gruppenanruf" lightbox="./media/group-call.png":::

Dieses Beispiel enthält auch Codeausschnitte für die folgenden Funktionen:

  - Klicken auf das Videosymbol zum Ein-/Ausschalten Ihrer Videokamera
  - Klicken auf das Mikrofonsymbol zum Ein-/Ausschalten des Mikrofons
  - Klicken auf das Wiedergabesymbol, um den Anruf zu halten bzw. das Halten aufzuheben
  - Klicken auf das Bildschirmsymbol zum Starten/Abbrechen der Bildschirmfreigabe
  - Klicken auf das Personensymbol, um dem Anruf einen Teilnehmer hinzuzufügen
  - Klicken auf „Remove participant“ (Teilnehmer entfernen) in der Teilnehmerliste, um einen bestimmten Teilnehmer aus dem Anruf zu entfernen


## <a name="next-steps"></a>Nächste Schritte

>[!div class="nextstepaction"] 
>[Laden Sie das Beispiel von GitHub herunter](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/).

Weitere Informationen finden Sie in den folgenden Artikeln:

- Machen Sie sich mit der [Verwendung der Clientbibliothek für Anrufe](../quickstarts/voice-video-calling/calling-client-samples.md) vertraut.
- Informieren Sie sich über die [Funktionsweise von Anrufen](../concepts/voice-video-calling/about-call-types.md).
- Lesen Sie die [API-Referenzdokumentation](/javascript/api/azure-communication-services/@azure/communication-calling/?view=azure-communication-services-js).
- Sehen Sie sich das Beispiel für die [Contoso Med-App](https://github.com/Azure-Samples/communication-services-contoso-med-app) an.

## <a name="additional-reading"></a>Zusätzliche Lektüre

- [Azure Communication GitHub](https://github.com/Azure/communication): Weitere Beispiele und Informationen finden Sie auf der offiziellen GitHub-Seite
- [Redux](https://redux.js.org/): Clientseitige Zustandsverwaltung
- [Fluent-UI](https://aka.ms/fluent-ui): UI-Bibliothek von Microsoft
- [React](https://reactjs.org/): Bibliothek zum Erstellen von Benutzeroberflächen
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1): Framework für die Erstellung von Webanwendungen

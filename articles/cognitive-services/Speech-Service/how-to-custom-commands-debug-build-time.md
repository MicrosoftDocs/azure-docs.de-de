---
title: Debuggen von Fehlern beim Erstellen einer Anwendung für benutzerdefinierte Befehle (Vorschau)
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie Fehler beim Erstellen einer Anwendung für benutzerdefinierte Befehle debuggen.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: aeb90e8e064c44f4d17f920261ed58310f0e55f0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025700"
---
# <a name="debug-errors-when-authoring-a-custom-commands-application"></a>Debuggen von Fehlern beim Erstellen einer Anwendung für benutzerdefinierte Befehle

Dieser Artikel beschreibt, wie Sie debuggen können, wenn beim Erstellen der Anwendung für benutzerdefinierte Befehle entsprechende Fehler auftreten. 

## <a name="errors-when-creating-an-application"></a>Fehler beim Erstellen einer Anwendung
Mit benutzerdefinierten Befehlen wird beim Erstellen einer Anwendung für benutzerdefinierte Befehle auch eine Anwendung in [LUIS](https://www.luis.ai/) erstellt. 

[In LUIS gilt die Beschränkung von 500 Anwendungen pro Erstellungsressource](../luis/luis-limits.md). Bei der Erstellung einer LUIS-Anwendung könnte ein Fehler auftreten, wenn Sie eine Erstellungsressource verwenden, die bereits 500 Anwendungen enthält. 

Stellen Sie sicher, dass die ausgewählte LUIS-Erstellungsressource weniger als 500 Anwendungen enthält. Andernfalls können Sie eine neue LUIS-Erstellungsressource erstellen, zu einer anderen wechseln oder versuchen, Ihre LUIS-Anwendungen zu bereinigen.  

## <a name="errors-when-deleting-an-application"></a>Fehler beim Löschen einer Anwendung
### <a name="cant-delete-luis-application"></a>LUIS-Anwendung kann nicht gelöscht werden
Wenn eine Anwendung für benutzerdefinierte Befehle gelöscht wird, können benutzerdefinierte Befehle auch versuchen, die mit der Anwendung für benutzerdefinierte Befehle verbundene LUIS-Anwendung zu löschen.

Wenn beim Löschvorgang der LUIS-Anwendung ein Fehler aufgetreten ist, wechseln Sie zu Ihrem [LUIS](https://www.luis.ai/)-Konto, um sie manuell zu löschen.

### <a name="toomanyrequests"></a>TooManyRequests
Wenn Sie versuchen, eine große Anzahl von Anwendungen auf einmal zu löschen, werden Ihnen wahrscheinlich Fehler vom Typ „TooManyRequests“ angezeigt. Diese Fehler bedeuten, dass Ihre Anforderungen von Löschvorgängen durch Azure gedrosselt werden. 

Aktualisieren Sie Ihre Seite, und versuchen Sie, weniger Anwendungen zu löschen.

## <a name="errors-when-modifying-an-application"></a>Fehler beim Ändern einer Anwendung

### <a name="cant-delete-a-parameter-or-a-web-endpoint"></a>Ein Parameter oder Webendpunkt kann nicht gelöscht werden.
Es ist Ihnen nicht gestattet, einen Parameter zu löschen, wenn er gerade verwendet wird. Entfernen Sie jeglichen Verweis auf den Parameter in allen Sprachantworten, Beispielsätzen, Bedingungen, Aktionen, und versuchen Sie es erneut.

### <a name="cant-delete-a-web-endpoint"></a>Ein Webendpunkt kann nicht gelöscht werden.
Es ist Ihnen nicht gestattet, einen Webendpunkt zu löschen, wenn er gerade verwendet wird. Entfernen Sie alle Aktionen vom Typ **Webendpunkt aufrufen**, die diesen Webendpunkt verwenden, bevor Sie einen Webendpunkt entfernen.

## <a name="errors-when-training-an-application"></a>Fehler beim Trainieren einer Anwendung
### <a name="built-in-intents"></a>Vordefinierte Absichten
LUIS verfügt über integrierte „Ja/Nein“-Absichten. Mit Beispielsätzen, die nur „Ja“, „Nein“ enthalten, würde das Training scheitern. 

| Schlüsselwort | Abweichungen | 
| ------- | --------- | 
| Ja | Sicher, OK |
| Nein | Falsch, Nicht | 

### <a name="common-sample-sentences"></a>Allgemeine Beispielsätze
Benutzerdefinierte Befehle gestatten es nicht, allgemeine Beispielsätze zwischen verschiedenen Befehlen zu teilen. Beim Training einer Anwendung könnte ein Fehler auftreten, wenn einige Beispielsätze in einem Befehl bereits in einem anderen Befehl definiert sind. 

Achten Sie darauf, keine allgemeinen Beispielsätze zu verwenden, die von verschiedenen Befehlen gemeinsam genutzt werden. 

Die bewährte Methode für den Ausgleich Ihrer Beispielsätze über verschiedene Befehle hinweg finden Sie unter [LUIS: Bewährte Methode](../luis/luis-concept-best-practices.md).

### <a name="empty-sample-sentences"></a>Leere Beispielsätze
Sie müssen über mindestens einen Beispielsatz für jeden Befehl verfügen.

### <a name="undefined-parameter-in-sample-sentences"></a>Nicht definierter Parameter in Beispielsätzen
Mindestens ein Parameter wird in den Beispielsätzen verwendet, aber nicht definiert.

### <a name="training-takes-too-long"></a>Das Training dauert zu lange
Das LUIS-Training ist dafür vorgesehen, mit wenigen Beispielen schnell zu lernen. Fügen Sie nicht zu viele Beispielsätze hinzu. 

Wenn Sie viele ähnliche Beispielsätze verwenden, definieren Sie einen Parameter, abstrahieren Sie sie in ein Muster, und fügen Sie es den Beispielsätzen hinzu.

Sie können z. B. einen Parameter „{Fahrzeug}“ für die folgenden Beispielsätze definieren und nur „Reserviere ein {Fahrzeug}“ zu den Beispielsätzen hinzufügen.

| Beispielsätze | Muster | 
| ------- | ------- | 
| Book a car (Reserviere ein Auto) | Book a {vehicle} (Reserviere ein {Fahrzeug}) | 
| Book a flight | Book a {vehicle} (Reserviere ein {Fahrzeug}) |
| Book a taxi (Reserviere ein Taxi) | Book a {vehicle} (Reserviere ein {Fahrzeug}) |

Bewährte Methoden für LUIS-Trainings sind unter [LUIS: Bewährte Methode](../luis/luis-concept-best-practices.md) beschrieben.

## <a name="cant-update-luis-key"></a>LUIS-Schlüssel kann nicht aktualisiert werden
### <a name="reassign-to-e0-authoring-resource"></a>Neuzuweisung zur Erstellungsressource E0
LUIS unterstützt nicht die Neuzuweisung von LUIS-Anwendungen zur Erstellungsressource E0.

Wenn Sie Ihre Erstellungsressource von F0 in E0 ändern oder zu einer anderen E0-Ressource wechseln müssen, erstellen Sie die Anwendung neu. 

Informationen zum schnellen Exportieren einer vorhandenen Anwendung und zum Importieren der Anwendung in eine neue Anwendung finden Sie unter [Continuous Deployment mit Azure DevOps](./how-to-custom-commands-deploy-cicd.md).

### <a name="save-button-is-disabled"></a>Die Schaltfläche zum Speichern ist deaktiviert.
Wenn Sie Ihrer Anwendung nie eine LUIS-Vorhersageressource zuweisen, wird die Schaltfläche „Speichern“ deaktiviert, wenn Sie versuchen, Ihre Erstellungsressource zu ändern, ohne eine Vorhersageressource hinzuzufügen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen finden Sie in den Beispielen auf GitHub](https://aka.ms/speech/cc-samples)
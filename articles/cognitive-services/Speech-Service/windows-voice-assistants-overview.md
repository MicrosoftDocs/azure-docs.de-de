---
title: 'Sprachassistenten unter Windows: Übersicht'
titleSuffix: Azure Cognitive Services
description: Eine Übersicht der Sprachassistenten unter Windows, einschließlich der verfügbaren Funktionen und Entwicklungsressourcen.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 9e8b009ecc2181edfaad5da3d8d05ad0c1909051
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024754"
---
# <a name="voice-assistants-on-windows"></a>Sprachassistenten unter Windows

Unter Windows 10, Version 2004 und höher, können Sprachassistentenanwendungen die APIs des Windows ConversationalAgent nutzen, um eine vollständige, sprachfähige Benutzerumgebung im Assistenten zu erreichen.

## <a name="voice-assistant-features"></a>Funktionen von Sprachassistenten

Sprach-Agent-Anwendungen können mit einem gesprochenen Schlüsselwort aktiviert werden, um eine sprachgestützte Freisprecherfahrung zu verfügbar zu machen. Die Sprachaktivierung funktioniert bei geschlossener Anwendung und gesperrtem Bildschirm.

Darüber hinaus bietet Windows eine Reihe von Datenschutzeinstellungen zur Sprachaktivierung, mit denen die Benutzer die Kontrolle über die Sprachaktivierung und vor allem die Sperraktivierung pro App erhalten.

Nach der Sprachaktivierung verwaltet Windows mehrere aktive Agents ordnungsgemäß und benachrichtigt jeden der Sprachassistenten, wenn sie unterbrochen oder deaktiviert werden. Dadurch können Anwendungen Unterbrechungen und andere Ereignisse zwischen Agents ordnungsgemäß verwalten.

## <a name="how-does-voice-activation-work"></a>Wie funktioniert die Sprachaktivierung?

Die Agent-Aktivierungs-Runtime (AAR) ist der fortlaufende Prozess in Windows, der die Anwendungsaktivierung auf ein gesprochenes Schlüsselwort oder einen Tastendruck hin steuert. Sie wird zusammen mit Windows gestartet, sofern auf dem System mindestens eine Anwendung vorhanden ist, die bei dem System registriert ist. Anwendungen interagieren mit AAR über die ConversationalAgent-APIs im Windows SDK.

Wenn der Benutzer ein Schlüsselwort spricht, benachrichtigt die in Software oder Hardware realisierte Schlüsselworterkennung des Systems AAR darüber, dass ein Schlüsselwort erkannt wurde und übergibt eine Schlüsselwort-ID. AAR sendet wiederum eine Anforderung zum Starten der Anwendung mit der entsprechenden Anwendungs-ID an BackgroundService.

### <a name="registration"></a>Registrierung

Wenn eine sprachaktivierte Anwendung zum ersten Mal ausgeführt wird, registriert sie ihre App-ID und die Schlüsselwortinformationen mithilfe der ConversationalAgent-APIs. AAR registriert alle Konfigurationen in der globalen Zuordnung bei der Hardware- oder Software-Schlüsselworterkennung des Systems und gibt ihr so die Möglichkeit, das Schlüsselwort der Anwendung zu erkennen. Die Anwendung wird ebenfalls [beim Hintergrunddienst](/windows/uwp/launch-resume/register-a-background-task) registriert.

Beachten Sie, dass dies bedeutet, dass eine Anwendung erst durch Sprache aktiviert werden kann, wenn sie einmal ausgeführt und die Registrierung erfolgreich abgeschlossen wurde.

### <a name="receiving-an-activation"></a>Empfangen einer Aktivierung

Beim Empfangen der Anforderung von AAR startet der Hintergrunddienst die Anwendung. Die Anwendung empfängt ein Signal von der OnBackgroundActivated-Lebenszyklusmethode in `App.xaml.cs` mit einem eindeutigen Ereignisargument. Dieses Argument teilt der Anwendung mit, dass sie durch AAR aktiviert wurde und mit der Schlüsselwortprüfung beginnen soll.

Wenn die Anwendung das Schlüsselwort erfolgreich überprüft, kann sie eine Anforderung ausgeben, im Vordergrund dargestellt zu werden. Wenn diese Anforderung erfolgreich ist, zeigt die Anwendung die Benutzeroberfläche an und setzt ihre Interaktion mit dem Benutzer fort.

AAR signalisiert aktiven Anwendungen weiterhin, wenn ihr Schlüsselwort gesprochen wird. Statt für die Signalisierung die Lebenszyklusmethode in `App.xaml.cs` zu verwenden, wird ein Ereignis in den ConversationalAgent-APIs genutzt.

### <a name="keyword-verification"></a>Schlüsselwortüberprüfung

Die Schlüsselworterkennung, die das Starten der Anwendung auslöst, weist jetzt einen niedrigen Stromverbrauch auf, der durch Vereinfachen des Schlüsselwortmodells erreicht wurde. Dadurch kann die Schlüsselworterkennung ständig aktiv sein, ohne die Leistungsbilanz zu beeinträchtigen. Allerdings bringt das auch mit sich, dass die Schlüsselworterkennung wahrscheinlich eine große Anzahl falsch positiver Ereignisse (falsche Annahmen) sendet, in denen sie ein Schlüsselwort erkannt hat, obwohl kein Schlüsselwort gesprochen wurde. Das ist der Grund, warum das Sprachaktivierungssystem die Anwendung im Hintergrund startet: um der Anwendung die Möglichkeit zu geben, zu überprüfen, dass das Schlüsselwort gesprochen wurde, bevor die aktuelle Sitzung des Benutzers unterbrochen wird. AAR speichert das Audiosignal einige Sekunden vor dem erkannten Schlüsselwort und stellt es der Anwendung zur Verfügung. Die Anwendung kann diese Aufzeichnung dazu verwenden, eine zuverlässigere Schlüsselworterkennung auf den gleichen Audioclip anzuwenden.

## <a name="next-steps"></a>Nächste Schritte

- **Überprüfen der Entwurfsrichtlinien:** In unseren [Entwurfsrichtlinien](windows-voice-assistants-best-practices.md) sind die erforderlichen Kernarbeiten beschrieben, die für optimale Benutzererfahrungen bei der Sprachaktivierung unter Windows 10 ausgeführt werden müssen.
- **Zur Seite „Erste Schritte“ wechseln:** Starten Sie [hier](how-to-windows-voice-assistants-get-started.md) mit den Schritten zum Implementieren von Sprachassistenten unter Windows, vom Einrichten Ihrer Entwicklungsumgebung über eine Einführung bis hin zum Implementierungsleitfaden.
- **Ausprobieren der Beispiel-App**: Um diese Funktionen aus erster Hand zu erleben, suchen Sie die Seite [UWP Voice Assistant Sample](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) auf, und befolgen Sie die Schrittanweisungen, um den Beispielclient auszuführen.
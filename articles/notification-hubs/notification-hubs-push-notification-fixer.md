---
title: Diagnostizieren verworfener Benachrichtigungen in Azure Notification Hubs
description: Erfahren Sie, wie Sie allgemeine Probleme mit verworfenen Benachrichtigungen in Azure Notification Hubs diagnostizieren.
services: notification-hubs
documentationcenter: Mobile
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 04/04/2019
ms.author: jowargo
ms.openlocfilehash: eebf9ef63a8622c4cc431322b786fdf30f6352fe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64925818"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnostizieren verworfener Benachrichtigungen in Azure Notification Hubs

Eine der häufigsten Fragen zu Azure Notification Hubs bezieht sich darauf, dass von einer Anwendung gesendete Benachrichtigungen auf Clientgeräten nicht angezeigt werden. Kunden möchten wissen, wo und warum Benachrichtigungen verworfen werden und wie sich das Problem lösen lässt. Dieser Artikel erläutert, warum Benachrichtigungen möglicherweise verworfen oder von Geräten nicht empfangen werden. Zudem wird erläutert, wie die Ursache ermittelt werden kann.

Zunächst müssen Sie wissen, wie Notification Hubs Benachrichtigungen an ein Gerät übermittelt.

![Notification Hubs-Architektur][0]

In einem typischen Benachrichtigungsflow wird eine Nachricht vom *Anwendungs-Back-End* an Notification Hubs gesendet. Notification Hubs verarbeitet alle Registrierungen. Dabei werden die konfigurierten Tags und Tagausdrücke berücksichtigt, um Ziele zu bestimmen. Bei den Zielen handelt es sich um die Registrierungen, die die Pushbenachrichtigung erhalten sollen. Diese Registrierungen können alle von uns unterstützten Plattformen umfassen: Android, Baidu (Android-Geräte in China), Fire OS (Amazon) iOS, Windows und Windows Phone.

Wenn die Ziele bestimmt sind, übermittelt Notification Hubs Benachrichtigungen per Push an den *Pushbenachrichtigungsdienst* für die Geräteplattform. Apple Push Notification Service (APNs) für Apple und Firebase Cloud Messaging (FCM) für Google sind zwei Beispiele für einen solchen Dienst. Notification Hubs übermittelt die Benachrichtigungen aufgeteilt auf mehrere Registrierungsbatches. Die Authentifizierung erfolgt beim jeweiligen Pushbenachrichtigungsdienst mit den Anmeldeinformationen, die Sie im Azure-Portal unter **Notification Hub konfigurieren** festgelegt haben. Danach leitet der Pushbenachrichtigungsdienst die Benachrichtigungen an die entsprechenden *Clientgeräte* weiter.

Der letzte Abschnitt einer Benachrichtigungsübermittlung liegt zwischen dem Pushbenachrichtigungsdienst der Plattform und dem Gerät. Die Benachrichtigungsübermittlung kann in jeder der vier Phasen im Pushbenachrichtigungsprozess (Client, Anwendungs-Back-End, Notification Hubs und Pushbenachrichtigungsdienst der Plattform) fehlschlagen. Weitere Informationen zur Architektur von Notification Hubs finden Sie in der Übersicht über [Übersicht über Notification Hubs].

Fehler bei der Übermittlung von Benachrichtigungen können während der anfänglichen Test-/Stagingphase auftreten. In dieser Phase verworfenen Benachrichtigungen können auf ein Konfigurationsproblem hindeuten. Wenn Fehler bei der Übermittlung von Benachrichtigungen in der Produktionsphase auftreten, werden einige oder möglicherweise alle Benachrichtigungen verworfen. In einem solchen Fall ist die Ursache in einem tiefer liegenden Anwendungs- oder Nachrichtenmusterfehler zu suchen.

Der nächste Abschnitt beschreibt Szenarien, in denen Benachrichtigungen verworfen werden können. Zunächst werden häufiger anzutreffende Szenarien beschrieben, danach seltenere.

## <a name="notification-hubs-misconfiguration"></a>Fehlkonfiguration von Notification Hubs ##

Notification Hubs muss sich im Kontext Ihrer Anwendung authentifizieren, um Benachrichtigungen an den jeweiligen Pushbenachrichtigungsdienst senden zu können. Sie müssen ein Entwicklerkonto für den Benachrichtigungsdienst der Zielplattform (Microsoft, Apple, Google usw.) erstellen. Anschließend müssen Sie Ihre Anwendung beim Betriebssystem registrieren, von dem Sie ein Token oder einen Schlüssel für die Kommunikation mit dem Ziel-Pushbenachrichtigungsdienst erhalten.

Sie müssen die Anmeldeinformationen der Plattform zum Azure-Portal hinzufügen. Wenn auf einem Gerät keine Benachrichtigungen ankommen, müssen Sie zuerst sicherstellen, dass in Notification Hubs die richtigen Anmeldeinformationen konfiguriert sind. Die Anmeldeinformationen müssen der Anwendung entsprechen, die in einem plattformspezifischen Entwicklerkonto erstellt wurde.

Schrittanleitungen zum Durchführen dieses Prozesses finden Sie unter [Erste Schritte mit Azure Notification Hubs].

Dies sind einige häufige Konfigurationsfehler, nach denen Sie suchen können:

### <a name="notification-hub-name-location"></a>Adresse des Notification Hub-Namens

Stellen Sie sicher, dass der Name des Notification Hubs (ohne Tippfehler) an folgenden Stellen identisch ist:
   * Bei der Registrierung vom Client aus
   * Beim Senden von Benachrichtigungen aus dem Back-End
   * Beim Konfigurieren der Anmeldeinformationen für den Pushbenachrichtigungsdienst

Stellen Sie sicher, dass Sie auf dem Client und im Anwendungs-Back-End die richtigen Konfigurationszeichenfolgen für die Signatur des gemeinsamen Zugriffs verwenden. Allgemein gilt: Sie müssen auf dem Client **DefaultListenSharedAccessSignature** und **DefaultFullSharedAccessSignature** im Anwendungs-Back-End verwenden. Dadurch wird die Berechtigung erteilt, Benachrichtigungen an Notification Hubs zu senden.

### <a name="apn-configuration"></a>APN-Konfiguration ###

Sie müssen zwei unterschiedliche Hubs verwalten – einen für die Produktion und einen anderen für Testzwecke. Sie müssen das Zertifikat, das Sie in einer Sandboxumgebung verwenden, auf einen anderen Hub hochladen als das Zertifikat bzw. den Hub für die Produktionsumgebung. Versuchen Sie nicht, verschiedene Arten von Zertifikaten auf den gleichen Hub hochzuladen. Dies kann Benachrichtigungsfehler bewirken.

Wenn Sie versehentlich unterschiedliche Arten von Zertifikaten auf den gleichen Hub hochgeladen haben, sollten Sie den Hub löschen und mit einem neuen Hub von vorn beginnen. Wenn Sie den Hub aus irgendeinem Grund nicht löschen können, müssen Sie zumindest alle vorhandenen Registrierungen aus dem Hub löschen.

### <a name="fcm-configuration"></a>FCM-Konfiguration ###

1. Stellen Sie sicher, dass der *Serverschlüssel*, den Sie von Firebase erhalten haben, mit dem Serverschlüssel übereinstimmt, den Sie im Azure-Portal registriert haben.

   ![Firebase-Serverschlüssel][3]

2. Stellen Sie sicher, dass Sie die **Projekt-ID** auf dem Client konfiguriert haben. Sie können den Wert der **Projekt-ID** aus dem Firebase-Dashboard abrufen.

   ![Firebase-Projekt-ID][1]

## <a name="application-issues"></a>Anwendungsprobleme ##

### <a name="tags-and-tag-expressions"></a>Tags und Tagausdrücke ###

Wenn Sie Tags oder Tagausdrücke verwenden, um Ihre Zielgruppe zu segmentieren, wird möglicherweise beim Senden einer Benachrichtigung kein Ziel gefunden. Dieser Fehler ist auf die im Sendeaufruf angegebenen Tags oder Tagausdrücke zurückzuführen.

Überprüfen Sie Ihre Registrierungen, um sicherzustellen, dass die Tags beim Senden einer Benachrichtigung übereinstimmen. Überprüfen Sie dann den Benachrichtigungsempfang nur für die Clients, auf denen diese Registrierungen vorhanden sind.

Angenommen, alle Ihre Registrierungen bei Notification Hubs verwenden das Tag „Politik“. Wenn Sie eine Benachrichtigung mit dem Tag „Sport“ senden, wird diese an kein Gerät gesendet. Ein komplexer Fall könnte Tagausdrücke umfassen, die Sie für „Tag A“ *oder* „Tag B“ registriert haben, beim Senden von Benachrichtigungen wird aber „Tag A && Tag B“ verwendet. Im Abschnitt mit Tipps für die Selbstdiagnose (siehe unten) wird erläutert, wie Sie Ihre Registrierungen und die zugehörigen Tags überprüfen.

### <a name="template-issues"></a>Vorlagenprobleme ###

Wenn Sie Vorlagen verwenden, stellen Sie sicher, dass Sie die unter [Vorlagen] beschriebenen Richtlinien befolgen.

### <a name="invalid-registrations"></a>Ungültige Registrierungen ###

Wenn der Notification Hub ordnungsgemäß konfiguriert wurde und Tags bzw. Tagausdrücke richtig verwendet werden, werden gültige Ziele gefunden. Benachrichtigungen sollten an diese Ziele gesendet werden. Notification Hubs sendet mehrere Verarbeitungsbatches parallel. Jeder Batch sendet Nachrichten an eine Reihe von Registrierungen.

> [!NOTE]
> Da Notification Hubs Batches parallel verarbeitet, ist die Reihenfolge, in der die Benachrichtigungen übermittelt werden, nicht garantiert.

Notification Hubs ist für ein Modell optimiert, in dem Nachrichten maximal einmal übermittelt werden. Es wird versucht, eine Deduplizierung durchzuführen, sodass keine Benachrichtigung häufiger als einmal an ein Gerät übermittelt wird. Registrierungen werden überprüft, um sicherzustellen, dass nur eine Nachricht pro Gerätebezeichner gesendet wird, bevor die Nachricht an den Pushbenachrichtigungsdienst gesendet wird.

Jeder Batch wird an den Pushbenachrichtigungsdienst gesendet, der wiederum die Registrierungen akzeptiert und überprüft. Während dieses Vorgangs stellt der Pushbenachrichtigungsdienst möglicherweise einen Fehler mit einer oder mehreren Registrierungen in einem Batch fest. In diesem Fall gibt der Pushbenachrichtigungsdienst einen Fehler an Notification Hubs zurück, und der Prozess wird beendet. Der Pushbenachrichtigungsdienst verwirft diesen Batch vollständig. Dies gilt insbesondere für APNs, in dem ein TCP-Streamprotokoll verwendet wird.

In diesem Fall wird die Registrierung, die den Fehler verursacht, aus der Datenbank entfernt. Dann wird erneut versucht, die Benachrichtigungen für den Rest der Geräte in diesem Batch zu übermitteln.

Um weitere Informationen zum Übermittlungsfehler bei einer Registrierung zu erhalten, können Sie diese Notification Hubs-REST-APIs verwenden: [Per Message Telemetry: Get Notification Message Telemetry](https://msdn.microsoft.com/library/azure/mt608135.aspx) (Nachrichtenbasierte Telemetrie: Abrufen der Telemetrie für Benachrichtigungsmeldungen) und [PNS-Feedback](https://msdn.microsoft.com/library/azure/mt705560.aspx). Beispielcode finden Sie im [Beispiel für „Send REST“](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Probleme mit dem Pushbenachrichtigungsdienst

Nachdem der Pushbenachrichtigungsdienst die Benachrichtigung empfangen hat, übermittelt er die Benachrichtigung an das Gerät. An diesem Punkt hat Notification Hubs keinerlei Kontrolle über die Zustellung der Benachrichtigung an das Gerät.

Da Plattformbenachrichtigungsdienste sehr robust sind, erfolgt die Übertragung von Benachrichtigungen an die Geräte normalerweise innerhalb weniger Sekunden. Wenn der Pushbenachrichtigungsdienst gedrosselt ist, wendet Notification Hubs eine exponentielle Backoffstrategie an. Wenn der Pushbenachrichtigungsdienst 30 Minuten lang nicht erreichbar ist, sorgt eine Richtlinie dafür, dass diese Nachrichten als abgelaufen markiert und dauerhaft verworfen werden.

Wenn ein Pushbenachrichtigungsdienst versucht, eine Benachrichtigung zu übermitteln, das Gerät aber offline ist, wird die Benachrichtigung vom Pushbenachrichtigungsdienst gespeichert. Sie wird lediglich für einen begrenzten Zeitraum gespeichert. Die Benachrichtigung wird an das Gerät gesendet, wenn es wieder verfügbar ist.

Für jede App wird nur eine (die aktuelle) Benachrichtigung gespeichert. Werden mehrere Benachrichtigungen gesendet, während ein Gerät offline ist, bewirkt jede neue Benachrichtigung, dass die vorherige Benachrichtigung verworfen wird. Das Aufbewahren nur der aktuellen Benachrichtigung wird in APNs als *Zusammenfügen* und in FCM als *Reduzieren* bezeichnet. (FCM verwendet einen reduzierenden Schlüssel.) Wenn das Gerät über einen längeren Zeitraum offline bleibt, werden die für das Gerät gespeicherten Benachrichtigungen verworfen. Weitere Informationen finden Sie unter [APNs overview] (Übersicht über APNs) und [About FCM messages] (Informationen zu FCM-Nachrichten).

Bei Notification Hubs können Sie einen zusammenfügenden Schlüssel mit der generischen SendNotification-API in einem HTTP-Header übergeben. Für das .NET SDK verwenden Sie z.B. `SendNotificationAsync`. Die SendNotification-API akzeptiert auch HTTP-Header, die unverändert an den jeweiligen Pushbenachrichtigungsdienst übergeben werden.

## <a name="self-diagnosis-tips"></a>Tipps für die Selbstdiagnose

Hier finden Sie Möglichkeiten, die Ursache von verworfenen Benachrichtigungen in Notification Hubs zu diagnostizieren.

### <a name="verify-credentials"></a>Überprüfen von Anmeldeinformationen ###

#### <a name="push-notification-service-developer-portal"></a>Entwicklerportal für den Pushbenachrichtigungsdienst ####

Überprüfen Sie die Anmeldeinformationen im Entwicklerportal des jeweiligen Pushbenachrichtigungsdiensts (APNs, FCM, Windows-Benachrichtigungsdienst usw.). Weitere Informationen finden Sie unter [Tutorial: Senden von Benachrichtigungen an Apps für die universelle Windows-Plattform mit Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification) erstellt haben.

#### <a name="azure-portal"></a>Azure-Portal ####

Um die Anmeldeinformationen zu überprüfen und mit denjenigen abzugleichen, die Sie aus dem Entwicklerportal für den Pushbenachrichtigungsdienst erhalten haben, wechseln Sie im Azure-Portal zur Registerkarte **Zugriffsrichtlinien**.

![Azure-Portal – Zugriffsrichtlinien][4]

### <a name="verify-registrations"></a>Überprüfen von Registrierungen

#### <a name="visual-studio"></a>Visual Studio ####

In Visual Studio können Sie über den Server-Explorer eine Verbindung mit Azure herstellen, um verschiedene Azure-Dienste anzuzeigen und zu verwalten, einschließlich Notification Hubs. Dies ist in erster Linie für Ihre Test-/Entwicklungsumgebung nützlich.

![Server-Explorer von Visual Studio][9]

Sie können alle Registrierungen im Hub anzeigen und verwalten. Die Registrierungen können nach Plattform, nativer oder Vorlagenregistrierung, Tags, Bezeichner des Pushbenachrichtigungsdiensts, Registrierungs-ID und Ablaufdatum kategorisiert werden. Auf dieser Seite können Sie Registrierungen auch bearbeiten. Dies ist besonders nützlich zur Bearbeitung von Tags.

Klicken Sie im **Server-Explorer** auf Ihren Notification Hub, und wählen Sie **Diagnose** aus. 

![Server-Explorer von Visual Studio: Menü „Diagnose“](./media/notification-hubs-diagnosing/diagnose-menu.png)

Die folgende Seite wird angezeigt:

![Visual Studio: Seite „Diagnose“](./media/notification-hubs-diagnosing/diagnose-page.png)

Wechseln Sie zur Seite **Geräteregistrierungen**:

![Visual Studio: Geräteregistrierungen](./media/notification-hubs-diagnosing/VSRegistrations.png)

Auf der Seite **Testsendevorgang** können Sie eine Testnachricht senden:

![Visual Studio: Testsendung](./media/notification-hubs-diagnosing/test-send-vs.png)

> [!NOTE]
> Verwenden Sie Visual Studio, um während der Test-/Entwicklungsphase einige wenige Registrierungen zu bearbeiten. Wenn Sie Ihre Registrierungen per Massenvorgang bearbeiten müssen, bietet es sich an, die Funktion zum Exportieren/Importieren von Registrierungen zu verwenden, die unter [ Exportieren und Ändern von Registrierungen in einem Massenvorgang](https://msdn.microsoft.com/library/dn790624.aspx) beschrieben wird.

#### <a name="service-bus-explorer"></a>Service Bus-Explorer ####

Viele Kunden verwenden [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer), um ihre Notification Hubs anzuzeigen und zu verwalten. Service Bus-Explorer ist ein Open Source-Projekt. 

### <a name="verify-message-notifications"></a>Überprüfen von Benachrichtigungen

#### <a name="azure-portal"></a>Azure-Portal ####

Um eine Testbenachrichtigung an Ihre Clients zu senden, ohne ein Dienst-Back-End einrichten und ausführen zu müssen, wählen Sie unter **SUPPORT UND PROBLEMBEHANDLUNG** die Option **Testsendevorgang** aus.

![Funktion für Testsendevorgang in Azure][7]

#### <a name="visual-studio"></a>Visual Studio ####

Sie können Testbenachrichtigungen auch aus Visual Studio senden.

![Funktion für Testsendevorgang in Visual Studio][10]

Weitere Informationen zur Verwendung von Notification Hubs mit Visual Studio-Server-Explorer finden Sie in diesen Artikeln:

* [Anzeigen von Geräteregistrierungen für Notification Hubs](https://docs.microsoft.com/previous-versions/windows/apps/dn792122(v=win.10))
* [Ausführliche Betrachtung: Visual Studio 2013 Update 2 RC und Azure SDK 2.3]
* [Announcing release of Visual Studio 2013 Update 3 and Azure SDK 2.4] (Ankündigung der Releases Visual Studio 2013 Update 3 und Azure SDK 2.4)

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Debuggen von Benachrichtigungsfehlern und Überprüfen von Benachrichtigungsergebnissen

#### <a name="enabletestsend-property"></a>EnableTestSend-Eigenschaft ####

Wenn Sie eine Benachrichtigung über Notification Hubs senden, wird die Benachrichtigung zunächst in die Warteschlange eingereiht. Notification Hubs bestimmt die richtigen Ziele und sendet die Benachrichtigung dann an den Pushbenachrichtigungsdienst. Wenn Sie die REST-API oder eines der Client-SDKs verwenden, bedeutet die Rückgabe des Sendeaufrufs nur, dass die Nachricht in die Warteschlange von Notification Hubs eingereiht wurde. Sie erfahren nicht, was geschieht, wenn Notification Hubs die Nachricht schließlich an den Pushbenachrichtigungsdienst sendet.

Wenn Ihre Benachrichtigung nicht auf dem Clientgerät ankommt, ist beim Versuch der Übermittlung der Nachricht an den Pushbenachrichtigungsdienst durch Notification Hubs möglicherweise ein Fehler aufgetreten. Möglicherweise überschreitet die Größe der Nutzlast das im Pushbenachrichtigungsdienst zulässige Maximum, oder die in Notification Hubs konfigurierten Anmeldeinformationen sind ungültig.

Um Einblick in Fehler des Pushbenachrichtigungsdiensts zu erhalten, können Sie die Eigenschaft [EnableTestSend] verwenden. Diese Eigenschaft wird automatisch aktiviert, wenn Sie Testnachrichten aus dem Portal oder aus dem Visual Studio-Client senden. Sie können mit dieser Eigenschaft detaillierte Debuginformationen anzeigen; auch die Verwendung über APIs ist möglich. Zurzeit können Sie sie im .NET SDK verwenden. In Zukunft soll sie allen Client-SDKs hinzugefügt werden.

Um die `EnableTestSend`-Eigenschaft mit dem REST-Aufruf zu verwenden, fügen Sie den Abfragezeichenfolgen-Parameter *test* an das Ende des Sendeaufrufs an. Beispiel:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>Beispiel für .NET SDK ####

Im Folgenden finden Sie ein Beispiel für die Verwendung des .NET SDK zum Senden einer nativen Popupbenachrichtigung:

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

Am Ende der Ausführung hat `result.State` einfach den Wert `Enqueued`. Die Ergebnisse bieten keinerlei Informationen darüber, was mit Ihrer Pushbenachrichtigung geschehen ist.

Anschließend können Sie die boolesche `EnableTestSend`-Eigenschaft verwenden. Verwenden Sie die `EnableTestSend`-Eigenschaft, während Sie `NotificationHubClient` initialisieren, um einen detaillierten Status zu Fehlern im Pushbenachrichtigungsdienst zu erhalten, die beim Senden einer Benachrichtigung auftreten. Der Sendeaufruf benötigt zusätzliche Zeit für die Rückgabe, da zuerst Notification Hubs die Benachrichtigung an den Pushbenachrichtigungsdienst übermitteln muss.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>Beispielausgabe ####

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Diese Nachricht weist darauf hin, dass entweder in Notification Hubs ungültige Anmeldeinformationen konfiguriert wurden oder dass ein Problem mit den Registrierungen im Hub vorliegt. Löschen Sie diese Registrierung, und lassen Sie den Client die Registrierung vor dem Senden der Nachricht erneut erstellen.

> [!NOTE]
> Die Verwendung der `EnableTestSend`-Eigenschaft ist stark eingeschränkt. Verwenden Sie diese Option nur in einer Entwicklungs-/Testumgebung und nur mit einer begrenzten Anzahl von Registrierungen. Debugbenachrichtigungen werden nur an 10 Geräte gesendet. Zudem ist die Anzahl von Sendevorgängen für Debugbenachrichtigungen auf 10 pro Minute begrenzt.

### <a name="review-telemetry"></a>Überprüfen der Telemetrie ###

#### <a name="azure-portal"></a>Azure-Portal ####

Das Portal bietet Ihnen einen schnellen Überblick über alle Aktivitäten in Ihrem Notification Hub.

1. Auf der Registerkarte **Übersicht** finden Sie eine aggregierte Ansicht der Registrierungen, Benachrichtigungen und Fehler für jede Plattform.

   ![Übersicht über Notification Hubs – Dashboard][5]

2. Auf der Registerkarte **Überwachen** können Sie viele weitere plattformspezifische Metriken hinzufügen, um detailliertere Einblicke zu erhalten. Sie können speziell nach Fehlern suchen, die zurückgegeben werden, wenn Notification Hubs versucht, die Benachrichtigung an den Pushbenachrichtigungsdienst zu senden.

   ![Azure-Portal – Aktivitätsprotokoll][6]

3. Beginnen Sie mit der Überprüfung von **Eingehende Nachrichten**, **Registrierungsvorgänge** und **Erfolgreiche Benachrichtigungen**. Wechseln Sie dann zu der Registerkarte für die jeweilige Plattform, um spezifische Fehler beim Pushbenachrichtigungsdienst zu überprüfen.

4. Wenn die Authentifizierungseinstellungen für Ihren Notification Hub falsch sind, wird die Nachricht **PNS-Authentifizierungsfehler** angezeigt. Dies ist ein Hinweis darauf, dass Sie die Anmeldeinformationen für den Pushbenachrichtigungsdienst überprüfen sollten.

#### <a name="programmatic-access"></a>Programmgesteuerter Zugriff ####

Weitere Informationen zum programmgesteuerten Zugriff finden Sie unter [Programmgesteuerter Zugriff](https://docs.microsoft.com/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Verschiedene telemetriebezogene Features wie z.B. der Export und Import von Registrierungen und der Telemetriezugriff über APIs sind nur auf der Dienstebene „Standard“ verfügbar. Wenn Sie versuchen, diese Funktionen auf der Dienstebene „Free“ oder „Basic“ zu nutzen, wird bei Verwenden des SDK eine Ausnahmemeldung ausgegeben. Ein Fehler des Typs „HTTP 403 (Verboten)“ wird ausgegeben, wenn Sie die Funktionen direkt aus den REST-APIs aufrufen.
>
> Um telemetriebezogene Features zu verwenden, stellen Sie zunächst im Azure-Portal sicher, dass Sie die Dienstebene „Standard“ verwenden.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Übersicht über Notification Hubs]: notification-hubs-push-notification-overview.md
[Erste Schritte mit Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Vorlagen]: https://msdn.microsoft.com/library/dn530748.aspx
[APNs overview]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html (Übersicht über APNs)
[About FCM messages]: https://firebase.google.com/docs/cloud-messaging/concept-options (Informationen zu FCM-Nachrichten)
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Ausführliche Betrachtung: Visual Studio 2013 Update 2 RC und Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Announcing release of Visual Studio 2013 Update 3 and Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ (Ankündigung der Releases Visual Studio 2013 Update 3 und Azure SDK 2.4)
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx

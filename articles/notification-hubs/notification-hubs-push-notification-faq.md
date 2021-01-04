---
title: 'Azure Notification Hubs: häufig gestellte Fragen (FAQs) | Microsoft-Dokumentation'
description: Häufig gestellte Fragen zum Entwerfen und Implementieren von Lösungen in Azure Notification Hubs
services: notification-hubs
documentationcenter: mobile
author: sethmanheim
manager: femila
keywords: Pushbenachrichtigung, Pushbenachrichtigungen, iOS-Pushbenachrichtigungen, Android-Pushbenachrichtigungen, iOS-Push, Android-Push
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 11/13/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: 9d476b1db645ed1f91b62fcf11464f7077a8fb3c
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491425"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Pushbenachrichtigungen mit Azure Notification Hubs: Häufig gestellte Fragen

## <a name="general"></a>Allgemein

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Wie ist die Ressourcenstruktur von Notification Hubs aufgebaut?

Azure Notification Hubs umfasst zwei Ressourcenebenen: Hubs und Namespaces. Ein Hub ist eine zentrale Ressource für Pushbenachrichtigungen, die plattformübergreifende Push-Informationen einer App enthalten kann. Ein Namespace ist eine Sammlung von Hubs in einem Bereich. Bei der empfohlenen Zuordnung wird ein einzelner Namespace mit einer einzelnen App verknüpft. Ein Namespace kann einen Produktionshub enthalten, der mit Ihrer Produktions-App zusammenarbeitet, einen Test-Hub, der mit Ihrer Test-App zusammenarbeitet, usw.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Welches Preismodell gilt für Notification Hubs?

Aktuelle Preisinformationen finden Sie auf der Seite [Notification Hubs – Preise]. Die Abrechnung für Notification Hubs erfolgt auf Namespaceebene. (Die Definition des Begriffs „Namespace“ finden Sie unter „Wie ist die Ressourcenstruktur von Notification Hubs aufgebaut?“.) Für Notification Hubs stehen drei Tarife zur Verfügung:

* **Free:** Dieser Einstiegstarif eignet sich sehr gut, um sich mit Pushfunktionen vertraut zu machen. Für Produktions-Apps ist er nicht zu empfehlen. Der Tarif beinhaltet 500 Geräte und eine Million Pushbenachrichtigungen pro Namespace und Monat. Eine SLA-Garantie (Service Level Agreement, Vereinbarung zum Servicelevel) ist nicht enthalten.
* **Basic**: Dieser Tarif (oder der Standard-Tarif) wird für kleinere Produktions-Apps empfohlen. Er beinhaltet 200.000 Geräte und zehn Millionen Pushbenachrichtigungen pro Namespace und Monat als Basis.
* **Standard**: Dieser Tarif wird für mittelgroße bis große Produktions-Apps empfohlen. Er beinhaltet zehn Millionen Geräte und zehn Millionen Pushbenachrichtigungen pro Namespace und Monat als Basis. Enthält umfangreiche Telemetriedaten (zusätzliche Daten zum bereitgestellten Pushstatus).

Features des Standard-Tarifs:

* **Umfangreiche Telemetrie:** Mit der Telemetrie auf Nachrichtenbasis von Notification Hubs können Sie Pushanforderungen und Feedback des Plattformbenachrichtigungssystems zu Debuggingzwecken nachverfolgen.
* **Mehrinstanzenfähigkeit**: Sie können Anmeldeinformationen des Plattformbenachrichtigungssystems auf Namespaceebene verwenden. Dadurch können Sie Mandanten innerhalb eines Namespaces problemlos in Hubs unterteilen.
* **Geplante Pushbenachrichtigungen:** Sie können das Senden von Benachrichtigungen für einen beliebigen Zeitpunkt planen.
* **Massenvorgänge**: Ermöglicht Registrierungen für Export/Import wie im Dokument [Vorgehensweise: Massenhaftes Exportieren und Ändern von Registrierungen] beschrieben.

### <a name="what-is-the-notification-hubs-sla"></a>Was ist die Notification Hubs-SLA?

Bei den Notification Hubs-Tarifen „Basic“ und „Standard“ können ordnungsgemäß konfigurierte Anwendungen in mindestens 99,9 Prozent der Fälle Pushbenachrichtigungen senden oder Registrierungsverwaltungsvorgänge ausführen. Weitere Informationen zur Vereinbarung zum Servicelevel finden Sie auf der Seite [SLA für Notification Hubs](https://azure.microsoft.com/support/legal/sla/notification-hubs/).

> [!NOTE]
> Da Pushbenachrichtigungen auf Plattformbenachrichtigungssysteme von Drittanbietern wie dem Push Notification Service von Apple (APNs) oder Firebase Cloud Messaging von Google (FCM) angewiesen sind, wird die Übermittlung derartiger Nachrichten nicht per SLA garantiert. Nachdem Notification Hubs die Batches an Plattformbenachrichtigungssysteme gesendet hat (was per SLA garantiert wird), sind die Plattformbenachrichtigungssysteme für die Zustellung der Pushbenachrichtigungen zuständig (nicht per SLA garantiert).

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Wie stufe ich meinen Hub oder meinen Namespace auf einen anderen Tarif hoch oder herab?

Navigieren Sie zu **[Azure portal]**  > **Notification Hubs-Namespaces** oder **Notification Hubs**. Wählen Sie die Ressource aus, die Sie aktualisieren möchten, und navigieren Sie zu **Tarif**. Beachten Sie folgende Punkte:

* Der aktualisierte Tarif gilt für *alle* Hubs in dem Namespace, mit dem Sie arbeiten.
* Falls die Anzahl von Geräten den Grenzwert des niedrigeren Tarifs übersteigt, zu dem Sie wechseln möchten, müssen Sie zuerst Geräte löschen.

## <a name="design-and-development"></a>Entwurf und Entwicklung

### <a name="which-server-side-platforms-do-you-support"></a>Welche dienstseitigen Plattformen werden unterstützt?

Server-SDKs sind für .NET, Java, Node.js, PHP und Python verfügbar. Die APIs von Notification Hubs basieren auf REST-Schnittstellen, sodass Sie direkt mit REST-APIs arbeiten können, wenn Sie andere Plattformen verwenden oder weitere Abhängigkeiten vermeiden möchten. Weitere Informationen finden Sie auf der [REST-APIs für Notification Hubs].

### <a name="which-client-platforms-do-you-support"></a>Welche Clientplattformen werden unterstützt?

Pushbenachrichtigungen werden für [iOS](ios-sdk-get-started.md), [Android](notification-hubs-android-push-notification-google-fcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Android China (via Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) und [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)) und [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari) unterstützt. Weitere Informationen finden Sie auf der Seite [Einführungstutorials für Notification Hubs](ios-sdk-get-started.md).

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Werden SMS-, E-Mail- oder Webbenachrichtigungen unterstützt?

Notification Hubs sendet Benachrichtigungen an Geräte, auf denen mobile Apps ausgeführt werden. E-Mail- oder SMS-Funktionen stehen nicht zur Verfügung. Notification Hubs bietet auch keinen vorgefertigten Zustelldienst für Browserpushbenachrichtigungen. Kunden können dieses Feature implementieren, indem sie zusätzlich zu den unterstützten serverseitigen Plattformen SignalR verwenden. 

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Wie viele Geräte kann ich unterstützen, wenn ich Pushbenachrichtigungen über Notification Hubs sende?

Auf der Seite [Notification Hubs – Preise] finden Sie ausführliche Informationen zur Anzahl unterstützter Geräte.

Wenn Sie Unterstützung für mehr als 10 Millionen registrierte Geräte benötigen, müssen Sie Ihre Geräte über mehrere Namespaces partitionieren.

### <a name="how-many-push-notifications-can-i-send-out"></a>Wie viele Pushbenachrichtigungen kann ich senden?

Je nach gewähltem Tarif erfolgt die Skalierung von Azure Notification Hubs automatisch auf der Grundlage der Anzahl von Benachrichtigungen, die das System durchlaufen.

> [!NOTE]
> Die Gesamtkosten für die Nutzung können sich abhängig von der Anzahl gesendeter Pushbenachrichtigungen erhöhen. Machen Sie sich daher auf der Seite [Notification Hubs – Preise] mit den Grenzwerten der einzelnen Tarife vertraut.

Unsere Kunden senden mit Notification Hubs täglich mehrere Millionen von Pushbenachrichtigungen. Solange Sie Azure Notification Hubs verwenden, müssen Sie sich nicht extra um die Skalierung der Reichweite Ihrer Pushbenachrichtigungen kümmern.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Wie lange dauert das Senden der Pushbenachrichtigungen auf mein Gerät?

In einem normalen Nutzungsszenario mit konstanter Eingangslast ohne nennenswerte Spitzen kann Azure Notification Hubs *pro Minute mindestens eine Million Sendevorgänge für Pushbenachrichtigungen* verarbeiten. Diese Rate kann abhängig von der Anzahl von Tags, der Art der eingehenden Sendevorgänge und anderen externen Faktoren variieren.

Während der geschätzten Zustellzeit berechnet der Dienst die Ziele pro Plattform und leitet Nachrichten auf der Grundlage der registrierten Tags oder Tagausdrücke an den entsprechenden Pushbenachrichtigungsdienst (Push Notification Service, PNS) weiter. Der PNS hat die Aufgabe, die Benachrichtigungen an das Gerät zu senden.

Die Zustellung von Benachrichtigungen durch den PNS wird nicht per SLA garantiert. Die meisten Pushbenachrichtigungen werden allerdings nach dem Senden an Notification Hubs innerhalb weniger Minuten (in der Regel innerhalb von zehn Minuten) an die Zielgeräte übermittelt. Bei einigen Benachrichtigungen dauert es unter Umständen etwas länger.

> [!NOTE]
> Azure Notification Hubs verfügt über eine Richtlinie, nach der Pushbenachrichtigungen verworfen werden, wenn sie nicht innerhalb von 30 Minuten an den PNS übermittelt werden können. Diese Verzögerung kann aus verschiedenen Gründen auftreten. Meistens ist sie jedoch darauf zurückzuführen, dass der PNS Ihre Anwendung drosselt.

### <a name="is-there-any-latency-guarantee"></a>Gibt es garantierte Latenzzeiten?

Da Pushbenachrichtigungen von einem externen, plattformspezifischen PNS übermittelt werden, können keine Latenzzeiten garantiert werden. Die Mehrzahl der Pushbenachrichtigungen wird jedoch innerhalb weniger Minuten zugestellt.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Welche Aspekte müssen beim Entwerfen einer Lösung mit Namespaces und Notification Hubs berücksichtigt werden?

#### <a name="mobile-appenvironment"></a>Mobile App/Umgebung

* Verwenden Sie pro mobiler App und Umgebung jeweils einen Notification Hub.
* In einem mehrinstanzenfähigen Szenario sollte jeder Mandant über einen separaten Hub verfügen.
* Verwenden Sie einen Notification Hub niemals gleichzeitig für Produktions- und Testumgebungen. Das kann beim Senden von Benachrichtigungen zu Problemen führen. (Apple bietet Pushendpunkte für Sandbox und Produktion an, die jeweils über separate Anmeldeinformationen verfügen.)
* Standardmäßig können Sie über das Azure-Portal oder über die integrierte Azure-Komponente in Visual Studio Testbenachrichtigungen an registrierte Geräte senden. Der Schwellenwert wird auf 10 Geräte festgelegt, die nach dem Zufallsprinzip aus dem Registrierungspool ausgewählt werden.

> [!NOTE]
> Wenn Ihr Hub ursprünglich mit einem Apple-Sandbox-Zertifikat konfiguriert war und später mit einem Apple-Produktionszertifikat konfiguriert wurde, sind die ursprünglichen Gerätetoken ungültig. Ungültige Token können bei Pushbenachrichtigungen zu Fehlern führen. Trennen Sie Ihre Produktions- und Testumgebungen, und verwenden Sie unterschiedliche Hubs für unterschiedliche Umgebungen.

#### <a name="pns-credentials"></a>PNS-Anmeldeinformationen

Wenn eine mobile App beim Entwicklerportal einer Plattform (etwa Apple oder Google) registriert wird, werden eine App-ID und Sicherheitstoken gesendet. Das App-Back-End stellt diese Token für den PNS der Plattform bereit, damit Pushbenachrichtigungen an Geräte gesendet werden können. Sicherheitstoken können als Zertifikate (beispielsweise für Apple iOS oder Windows Phone) oder als Sicherheitsschlüssel (beispielsweise für Google Android oder Windows) vorliegen. Sie müssen in Notification Hubs konfiguriert werden. Die Konfiguration erfolgt üblicherweise auf Notification Hub-Ebene, kann in einem Szenario mit mehreren Mandanten jedoch auch auf Namespaceebene durchgeführt werden.

#### <a name="namespaces"></a>Namespaces

Namespaces können für eine Bereitstellungsgruppierung eingesetzt werden. In einem Szenario mit mehreren Mandanten können sie auch alle Notification Hubs für alle Mandanten der gleichen App darstellen.

#### <a name="geo-distribution"></a>Geografische Verteilung

Die geografische Verteilung ist bei Szenarios mit Pushbenachrichtigungen nicht immer ein wichtiger Aspekt. Verschiedene Pushbenachrichtigungsdienste (etwa APNs oder FCM), die Pushbenachrichtigungen an Geräte übermitteln, sind nicht gleichmäßig verteilt.

Wenn Sie über eine weltweit verwendete Anwendung verfügen, können Sie den Notification Hubs-Dienst in unterschiedlichen Azure-Regionen auf der ganzen Welt verwenden und so Hubs in unterschiedlichen Namespaces erstellen.

> [!NOTE]
> Dieser Ansatz wird jedoch nicht empfohlen, da er mit höheren Verwaltungskosten (insbesondere für Registrierungen) verbunden ist. Er sollte daher nur in Ausnahmefällen verwendet werden.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Sollten Registrierungen vom App-Back-End oder direkt über Clientgeräte erfolgen?

Registrierungen über das App-Back-End sind hilfreich, wenn Sie Clients vor der Registrierung authentifizieren müssen. Sie sind auch hilfreich, wenn Sie über Tags verfügen, die vom App-Back-End auf der Grundlage der App-Logik erstellt oder geändert werden müssen. Weitere Informationen finden Sie im [Leitfaden zur Back-End-Registrierung] und im [Leitfaden zur Back-End-Registrierung 2].

### <a name="what-is-the-push-notification-delivery-security-model"></a>Welches Sicherheitsmodell gilt für die Übermittlung von Pushbenachrichtigungen?

Azure Notification Hubs verwendet ein auf [Shared Access Signatures (SAS)](../storage/common/storage-sas-overview.md) basierendes Sicherheitsmodell. Sie können die SAS-Token auf der Namespace-Stammebene oder auf der granularen Notification Hub-Ebene verwenden. Für SAS-Token können verschiedene Autorisierungsregeln festgelegt werden, um beispielsweise Nachrichtenberechtigungen zu senden oder auf Benachrichtigungsberechtigungen zu lauschen. Weitere Informationen finden Sie im [Sicherheitsmodell von Notification Hubs].

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Wie sollte ich vertrauliche Nutzlasten in Pushbenachrichtigungen behandeln?

Alle Benachrichtigungen werden über den PNS der Plattform an die Zielgeräte übermittelt. Wenn eine Benachrichtigung an Azure Notification Hubs gesendet wird, wird sie verarbeitet und an den jeweiligen PNS übergeben.

Bei allen Verbindungen zwischen Absender, Azure Notification Hubs und PNS wird HTTPS verwendet.

> [!NOTE]
> Die Nutzlast der Nachrichten wird von Azure Notification Hubs nicht protokolliert.

Für den Versand vertraulicher Nutzlasten empfehlen wir die Verwendung sicherer Pushbenachrichtigungen. Der Absender übermittelt eine Pingbenachrichtigung mit einer Nachrichten-ID und ohne die vertrauliche Nutzlast an das Gerät. Wenn die App auf dem Gerät die Nutzlast empfängt, ruft sie direkt eine sichere API auf, um die Nachrichtendetails abzurufen. Eine Implementierungsanleitung für dieses Muster finden Sie im [Tutorial zu sicheren Pushbenachrichtigungen mit Notification Hubs].

## <a name="operations"></a>Operationen (Operations)

### <a name="what-support-is-provided-for-disaster-recovery"></a>Welche Unterstützung steht für die Notfallwiederherstellung zur Verfügung?

Wir bieten eine Notfallwiederherstellung der Metadaten (Notification Hubs-Name, Verbindungszeichenfolge und andere wichtige Informationen). Wenn eine Notfallwiederherstellung ausgelöst wird, gehen *nur* die Registrierungsdaten der Notification Hubs-Infrastruktur verloren. Sie müssen eine Lösung implementieren, die Ihren neuen Hub nach der Wiederherstellung wieder mit diesen Daten auffüllt:

1. Erstellen Sie einen sekundären Notification Hub in einem anderen Rechenzentrum. Es empfiehlt sich, gleich zu Anfang einen sekundären Notification Hub zu erstellen, um vor Notfallwiederherstellungsereignissen geschützt zu sein, die Ihre Verwaltungsmöglichkeiten beeinträchtigen. Sie können den sekundären Notification Hub aber auch erst zum Zeitpunkt des Notfallwiederherstellungsereignisses erstellen.

2. Sorgen Sie mithilfe einer der folgenden Optionen dafür, dass der sekundäre Notification Hub mit dem primären Notification Hub synchron ist:

   * Verwenden Sie ein App-Back-End, das Installationen gleichzeitig in beiden Notification Hubs erstellt und aktualisiert. Mithilfe der Installationen können Sie Ihren eigenen eindeutigen Gerätebezeichner angeben, der besser für das Replikationsszenario geeignet ist. Weitere Informationen finden Sie in [diesem Beispielcode](https://github.com/Azure/azure-notificationhubs-dotnet/tree/main/Samples/RedundantHubSample).
   * Verwenden Sie ein App-Back-End, das regelmäßig eine Sicherungskopie der Registrierungen aus dem primären Notification Hub abruft. Das ermöglicht die Durchführung einer Masseneinfügung in den sekundären Notification Hub.

Der sekundäre Notification Hub kann abgelaufene Installationen/Registrierungen aufweisen. Wenn die Pushbenachrichtigung an ein abgelaufenes Handle übermittelt wird, bereinigt Notification Hubs den zugehörigen Datensatz der Installation/Registrierung basierend auf der Antwort vom PNS-Server. Zum Bereinigen abgelaufener Datensätze eines sekundären Notification Hubs fügen Sie benutzerdefinierte Logik hinzu, die Feedback von allen Sendevorgängen verarbeitet. Anschließend läuft die Installation/Registrierung im sekundären Notification Hub ab.

Falls Sie über kein Back-End verfügen und die App auf Zielgeräten gestartet wird, führen die Geräte eine neue Registrierung beim sekundären Notification Hub durch. Irgendwann sind dann alle aktiven Geräte beim sekundären Notification Hub registriert.

Innerhalb eines gewissen Zeitraums erhalten Geräte mit nicht geöffneten Apps keine Benachrichtigungen.

### <a name="is-all-of-my-data-stored-in-encrypted-form"></a>Werden alle meine Daten in verschlüsselter Form gespeichert?

Azure Notification Hubs verschlüsselt alle ruhenden Kundendaten mit Ausnahme von Registrierungstags. Aus diesem Grund sollten Sie keine personenbezogenen oder vertrauliche Daten unter Verwendung von Tags speichern.

### <a name="is-there-audit-log-capability"></a>Steht ein Überwachungsprotokoll zur Verfügung?

Ja. Alle Notification Hubs-Verwaltungsvorgänge aktualisieren das Azure-Aktivitätsprotokoll, das im [Azure portal] zur Verfügung steht. Das Azure-Aktivitätsprotokoll bietet Einblicke in Vorgänge, die für Ressourcen in Ihren Abonnements durchgeführt wurden. Mit dem Aktivitätsprotokoll können Sie die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) ermitteln, die für die Ressourcen Ihres Abonnements durchgeführt wurden. Sie können auch den Status der Vorgänge und andere relevante Eigenschaften ermitteln. Das Aktivitätsprotokoll umfasst jedoch keine Lesevorgänge (GET).

## <a name="monitoring-and-troubleshooting"></a>Überwachung und Problembehandlung

### <a name="what-troubleshooting-capabilities-are-available"></a>Welche Funktionen für die Problembehandlung stehen zur Verfügung?

Azure Notification Hubs bietet verschiedene Problembehandlungsfeatures – insbesondere für das häufig auftretende Problem verworfener Benachrichtigungen. Ausführliche Informationen finden Sie im [Problembehandlung für Notification Hubs].

### <a name="what-telemetry-features-are-available"></a>Welche Telemetriefunktionen stehen zur Verfügung?

Azure Notification Hubs ermöglicht die Anzeige von Telemetriedaten im [Azure portal]. Ausführliche Informationen zu den Metriken finden Sie auf der Seite [Notification Hubs-Metriken].

Sie können auch programmgesteuert auf Metriken zugreifen. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Retrieve Azure Monitor metrics with .NET (Abrufen von Azure Monitor-Metriken mit .NET)](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/). In diesem Beispiel werden Benutzername und Kennwort verwendet. Um ein Zertifikat zu verwenden, überladen Sie die FromServicePrincipal-Methode, um ein Zertifikat wie [in diesem Beispiel](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs) gezeigt anzugeben. 
- [Getting metrics and activity logs for a resource](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/) (Abrufen von Metriken und Aktivitätsprotokollen für eine Ressource)
- [Exemplarische Vorgehensweise für die Azure Monitoring-REST-API](../azure-monitor/platform/rest-api-walkthrough.md)

> [!NOTE]
> Wenn Benachrichtigungen erfolgreich waren, bedeutet das einfach, dass Pushbenachrichtigungen an den externen PNS (beispielsweise APNs für iOS-Geräte und macOS oder FCM für Android-Geräte) übermittelt wurden. Der PNS hat die Aufgabe, die Benachrichtigungen an Zielgeräte zu übermitteln. In der Regel macht der PNS Übermittlungsmetriken nicht für Dritte verfügbar.  

[Azure portal]: https://portal.azure.com
[Notification Hubs – Preise]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[REST-APIs für Notification Hubs]: /previous-versions/azure/reference/dn530746(v=azure.100)
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[Leitfaden zur Back-End-Registrierung]: /previous-versions/azure/azure-services/dn743807(v=azure.100)
[Leitfaden zur Back-End-Registrierung 2]: /previous-versions/azure/azure-services/dn530747(v=azure.100)
[Sicherheitsmodell von Notification Hubs]: /previous-versions/azure/azure-services/dn495373(v=azure.100)
[Tutorial zu sicheren Pushbenachrichtigungen mit Notification Hubs]: ./notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md
[Problembehandlung für Notification Hubs]: ./notification-hubs-push-notification-fixer.md
[Notification Hubs-Metriken]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Vorgehensweise: Massenhaftes Exportieren und Ändern von Registrierungen]: ./export-modify-registrations-bulk.md
[Azure portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[App Service Pricing]: https://azure.microsoft.com/pricing/details/app-service/

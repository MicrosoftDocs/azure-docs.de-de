---
title: Häufig gestellte Fragen zum Azure SignalR Service
description: Erhalten Sie Antworten auf häufig gestellte Fragen zu Azure SignalR Service, einschließlich Informationen zur Problembehandlung und typischen Verwendungsszenarien.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: c65bc7e92d925f819a48fd8ab9a8160bc3eb72e3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579304"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR Service FAQ

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Ist Azure SignalR Service bereit für den Produktionseinsatz?

Ja, sowohl die Unterstützung von [ASP.NET Core SignalR](https://dotnet.microsoft.com/apps/aspnet/signalr) als auch die von [ASP.NET SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr) sind allgemein verfügbar.

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Wenn mehrere Anwendungsserver vorhanden sind, werden Clientmeldungen an alle Server oder nur an einen von ihnen gesendet?

Zwischen einem Client und einem Anwendungsserver besteht eine 1:1-Zuordnung. Meldungen von einem Client werden immer an denselben Anwendungsserver gesendet.

Die Zuordnung wird aufrecht erhalten, bis die Verbindung vom Client oder Anwendungsserver getrennt wird.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Wenn einer meiner Anwendungsserver nicht betriebsbereit ist, wie kann ich ihn auffinden und darüber informiert werden?

Azure SignalR Service überwacht den Heartbeat der Anwendungsserver.
Wenn über einen angegebenen Zeitraum hinweg keine Heartbeats empfangen werden, gilt der Anwendungsserver als offline. Alle Clientverbindungen, die diesem Anwendungsserver zugeordnet sind, werden getrennt.

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>Warum löst mein benutzerdefinierter `IUserIdProvider` eine Ausnahme aus, wenn ich vom ASP.NET Core SignalR SDK zum Azure SignalR Service SDK wechsle?

Der Parameter `HubConnectionContext context` unterscheidet sich zwischen dem ASP.NET Core SignalR SDK und dem Azure SignalR Service SDK, wenn `IUserIdProvider` aufgerufen wird.

In ASP.NET Core SignalR ist `HubConnectionContext context` der Kontext aus der physischen Clientverbindung mit gültigen Werten für alle Eigenschaften.

Im Azure SignalR Service SDK ist `HubConnectionContext context` der Kontext aus der logischen Clientverbindung. Die physische Client ist mit der Azure SignalR Service-Instanz verbunden, weshalb also nur eine begrenzte Anzahl von Eigenschaften bereitgestellt wird.

Vorerst sind nur `HubConnectionContext.GetHttpContext()` und `HubConnectionContext.User` für den Zugriff verfügbar.
Sie können den [Quellcode überprüfen](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>Kann ich die in Azure SignalR Service verfügbaren Transporte auf der Serverseite mit ASP.NET Core SignalR konfigurieren? Kann ich beispielsweise den WebSocket-Transport deaktivieren?

Nein.

Azure SignalR Service bietet alle drei Transporte, die von ASP.NET Core SignalR standardmäßig unterstützt werden. Dies ist nicht konfigurierbar. Azure SignalR Service verarbeitet Verbindungen und Transporte für alle Clientverbindungen.

Sie können clientseitige Transporte konfigurieren, wie in [ASP.NET Core SignalR-Konfiguration](/aspnet/core/signalr/configuration#configure-allowed-transports-1) dokumentiert.

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Welche Bedeutung haben im Azure-Portal angezeigte Metriken wie die Nachrichtenanzahl oder die Anzahl der Verbindungen? Welchen Aggregationstyp sollte ich auswählen?

Ausführliche Informationen zur Berechnung dieser Metriken finden Sie unter [Nachrichten und Verbindungen in Azure SignalR Service](signalr-concept-messages-and-connections.md).

Im Bereich „Übersicht“ von Azure SignalR Service-Ressourcen ist bereits der entsprechende Aggregationstyp für Sie ausgewählt. Wenn Sie in den Bereich „Metriken“ wechseln, können Sie den Aggregationstyp [Nachrichten und Verbindungen in Azure SignalR Service](../azure-monitor/essentials/metrics-supported.md#microsoftsignalrservicesignalr) als Referenz verwenden.

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Welche Bedeutung haben die Dienstmodi `Default`, `Serverless` und `Classic`? Wie kann ich eine Auswahl treffen?

Für neue Anwendungen sollten nur der Standard- und der serverlose Modus verwendet werden. Der Hauptunterschied besteht darin, ob Sie über Anwendungsserver verfügen, die Serververbindungen mit dem Dienst herstellen (d. h. `AddAzureSignalR()` verwenden, um Verbindungen mit dem Dienst herzustellen). Wenn dies der Fall ist, verwenden Sie den Standardmodus, sonst den serverlosen Modus.

Der klassische Modus ist für Abwärtskompatibilität mit vorhandenen Anwendungen ausgelegt und sollte für neue Anwendungen nicht verwendet werden.

Weitere Informationen zum Dienstmodus finden Sie in [diesem Dokument](concept-service-mode.md).

## <a name="can-i-send-message-from-client-in-serverless-mode"></a>Kann ich im serverlosen Modus Nachrichten vom Client senden?

Sie können Nachrichten vom Client senden, wenn Sie in Ihrer SignalR-Instanz Upstream konfigurieren. Upstream ist ein Satz von Endpunkten, die Nachrichten und Verbindungsereignisse vom SignalR-Dienst empfangen können. Wenn kein Upstream konfiguriert ist, werden Nachrichten vom Client ignoriert.

Weitere Informationen über Upstream finden Sie in [diesem Dokument](concept-upstream.md).

Upstream befindet sich derzeit in der öffentlichen Vorschauphase.

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>Gibt es bei der Verwendung von Azure SignalR Service für ASP.NET SignalR Funktionsunterschiede?

Bei Verwendung von Azure SignalR Service werden einige APIs und Features von ASP.NET SignalR nicht unterstützt:
- Die Möglichkeit, beliebige Zustände zwischen Clients und dem Hub zu übergeben (häufig als `HubState` bezeichnet), wird nicht unterstützt.
- Die `PersistentConnection`-Klasse wird nicht unterstützt.
- *Forever Frame-Transport* wird nicht unterstützt.
- Azure SignalR Service gibt keine an den Client gesendeten Nachrichten mehr wieder, wenn der Client offline ist.
- Wenn Sie Azure SignalR Service verwenden, wird der Datenverkehr für eine Clientverbindung für die Dauer der Verbindung immer an eine App-Serverinstanz weitergeleitet (auch als *sticky* bezeichnet).

Bei der Unterstützung für ASP.NET SignalR liegt der Fokus auf der Kompatibilität. Daher werden nicht alle neuen Features von ASP.NET Core SignalR unterstützt. Beispielsweise sind *MessagePack* und *Streaming* nur für ASP.NET Core SignalR-Anwendungen verfügbar.

Sie können Azure SignalR Service für verschiedene Dienstmodi konfigurieren: `Classic`, `Default` und `Serverless`. Der `Serverless`-Modus wird für ASP.NET nicht unterstützt. Die REST-API auf Datenebene wird ebenfalls nicht unterstützt.

## <a name="where-does-my-data-reside"></a>Wo befinden sich meine Daten?

Azure SignalR Service ist ein Datenverarbeitungsdienst. Inhalte von Kunden werden nicht gespeichert, und Data Residency wird durch den Entwurf garantiert. Wenn Sie Azure SignalR Service in Verbindung mit anderen Azure-Diensten (z. B. Azure Storage) zu Diagnosezwecken verwenden, finden Sie in [diesem Whitepaper](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) Informationen zum Aufrechterhalten von Data Residency in Azure-Regionen.
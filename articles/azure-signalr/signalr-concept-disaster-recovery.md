---
title: Resilienz und Notfallwiederherstellung in Azure SignalR Service
description: Übersicht über die Einrichtung mehrerer SignalR Service-Instanzen zur Erreichung von Resilienz und Notfallwiederherstellung
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: kenchen
ms.openlocfilehash: eb70e65db4a086afc60e91cadf55a8844b102591
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61402131"
---
# <a name="resiliency-and-disaster-recovery"></a>Resilienz und Notfallwiederherstellung

Resilienz und Notfallwiederherstellung sind eine übliche Anforderung für Onlinesysteme. Azure SignalR Service garantiert schon heute eine Verfügbarkeit von 99,9 %, ist aber noch immer ein regionaler Dienst.
Ihre Dienstinstanz wird immer in einer einzigen Region ausgeführt, und im Fall eines regionsweiten Ausfalls findet kein Failover zu einer anderen Region statt.

Stattdessen bietet unser Dienst-SDK eine Funktion, mit der Sie mehrere SignalR Service-Instanzen unterstützen und automatisch zu anderen Instanzen wechseln können, wenn einige der Instanzen nicht verfügbar sind.
Mit diesem Feature können Sie bei einem Notfall eine Wiederherstellung durchführen, Sie müssen jedoch selbst die richtige Systemtopologie einrichten. In diesem Dokument erfahren Sie, wie Sie dazu vorgehen.

## <a name="high-available-architecture-for-signalr-service"></a>Hochverfügbare Architektur für SignalR Service

Um regionsübergreifende Resilienz für SignalR Service zu erreichen, müssen Sie mehrere Dienstinstanzen in verschiedenen Regionen einrichten. So können beim Ausfall einer Region die anderen Regionen als Sicherung fungieren.
Wenn Sie mehrere Dienstinstanzen mit einem App-Server verbinden, gibt es zwei Rollen: die primäre und die sekundäre Instanz.
Die primäre Instanz ist eine Instanz, die Onlinedatenverkehr akzeptiert. Die sekundäre Instanz ist voll funktionsfähig, dient aber als Sicherungsinstanz für die primäre Instanz.
In unserer SDK-Implementierung werden bei der Aushandlung nur primäre Endpunkte zurückgegeben. Im Normalfall stellen Clients daher nur eine Verbindung mit primären Endpunkten her.
Wenn die primäre Instanz ausfällt, werden bei der Aushandlung jedoch sekundäre Endpunkte zurückgegeben, damit Clients weiterhin eine Verbindung herstellen können.
Die primäre Instanz und der App-Server werden über normale Serververbindungen verbunden. Die sekundäre Instanz und der App-Server aber werden über einen speziellen Verbindungstyp verbunden, der als „unsichere Verbindung“ bezeichnet wird.
Der Hauptunterschied bei einer unsicheren Verbindung besteht darin, dass sie kein Verbindungsrouting von Clients akzeptiert, weil sich die sekundäre Instanz in der Regel in einer anderen Region befindet. Das Routing eines Clients zu einer anderen Region ist keine optimale Lösung (die Wartezeit nimmt zu).

Eine Dienstinstanz kann beim Herstellen einer Verbindung mit mehreren App-Servern unterschiedliche Rollen innehaben.
Eine typische Konfiguration für ein regionsübergreifendes Szenario ist die Verwendung von zwei (oder mehr) Paaren von SignalR Service-Instanzen und App-Servern.
Innerhalb jedes Paars befinden sich der App-Server und die SignalR Service-Instanz in derselben Region, und SignalR Service wird als primäre Rolle mit dem App-Server verbunden.
Zwischen den einzelnen Paaren werden der App-Server und SignalR Service ebenfalls verbunden. SignalR wird beim Herstellen einer Verbindung mit einem Server in einer anderen Region jedoch zu einer sekundären Instanz.

In dieser Topologie können Nachrichten von einem Server weiterhin an alle Clients übermittelt werden, da alle App-Server und SignalR Service-Instanzen miteinander verbunden sind.
Wenn ein Client eine Verbindung herstellt, wird er aber immer zum App-Server in der gleichen Region weitergeleitet, um eine optimale Netzwerkwartezeit zu erzielen.

Das folgende Diagramm veranschaulicht diese Topologie:

![Topologie](media/signalr-concept-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>Konfigurieren von App-Servern mit mehreren SignalR Service-Instanzen

Nachdem Sie SignalR Service und App-Server in jeder Region erstellt haben, können Sie Ihre App-Server zum Herstellen einer Verbindung mit allen SignalR Service-Instanzen konfigurieren.

Dafür stehen Ihnen zwei Möglichkeiten zur Auswahl:

### <a name="through-config"></a>Über die Konfigurationsdatei

Sie sollten bereits wissen, wie Sie die SignalR Service-Verbindungszeichenfolge über „Umgebungsvariablen/App-Einstellungen/web.config“ mit einem Konfigurationseintrag namens `Azure:SignalR:ConnectionString` festlegen.
Wenn Sie mehrere Endpunkte haben, können Sie diese in mehreren Konfigurationseinträgen im folgenden Format festlegen:

```
Azure:SignalR:Connection:<name>:<role>
```

Hier ist `<name>` der Name des Endpunkts, und `<role>` ist die zugehörige Rolle (primär oder sekundär).
Der Name ist optional. Er ist jedoch hilfreich, wenn Sie das Routingverhalten zwischen mehreren Endpunkten zusätzlich anpassen möchten.

### <a name="through-code"></a>Über den Code

Wenn Sie die Verbindungszeichenfolge an anderer Stelle speichern möchten, können Sie sie auch in Ihrem Code einlesen und als Parameter beim Aufrufen von `AddAzureSignalR()` (ASP.NET Core) oder `MapAzureSignalR()` (ASP.NET) verwenden.

Hier sehen Sie den Beispielcode:

ASP.NET Core:

```cs
services.AddSignalR()
        .AddAzureSignalR(options => options.Endpoints = new ServiceEndpoint[]
        {
            new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
            new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
        });
```

ASP.NET:

```cs
app.MapAzureSignalR(GetType().FullName, hub,  options => options.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
        new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
    };
```

## <a name="failover-sequence-and-best-practice"></a>Failoversequenz und bewährte Methode

Sie verfügen jetzt über die richtige Konfiguration der Systemtopologie. Sollte eine SignalR Service-Instanz ausfallen, wird Onlinedatenverkehr zu anderen Instanzen geleitet.
Folgendes geschieht, wenn eine primäre Instanz ausfällt (und später wiederhergestellt wird):

1. Die primäre Dienstinstanz ist ausgefallen, und alle Serververbindungen für diese Instanz werden gelöscht.
2. Alle Server, die mit dieser Instanz verbunden sind, markieren die Instanz als „offline“. Bei der Aushandlung wird nicht mehr dieser Endpunkt zurückgegeben, sondern der sekundäre Endpunkt.
3. Alle Clientverbindungen mit dieser Instanz werden ebenfalls geschlossen, und Clients stellen erneut eine Verbindung her. Da App-Server jetzt den sekundären Endpunkt zurückgeben, stellen Clients eine Verbindung mit der sekundären Instanz her.
4. Die sekundäre Instanz übernimmt nun den gesamten Onlinedatenverkehr. Alle Nachrichten vom Server an Clients können weiterhin übermittelt werden, da die sekundäre Instanz mit allen App-Servern verbunden ist. Nachrichten von Clients an Server werden jedoch nur an den App-Server in der gleichen Region weitergeleitet.
5. Nachdem die primäre Instanz wiederhergestellt wurde und wieder online ist, stellt der App-Server die Verbindungen wieder her und markiert die Instanz als „online“. Bei der Aushandlung wird jetzt wieder der primäre Endpunkt zurückgegeben, sodass neue Clients mit dem primären Endpunkt verbunden werden. Verbindungen vorhandener Clients werden jedoch nicht gelöscht. Die Clients werden weiterhin zur sekundären Instanz geleitet, bis sie die Verbindung selbst trennen.

Die folgenden Diagramme veranschaulichen das Failover in SignalR Service:

Abb. 1: Vor dem Failover ![Vor dem Failover](media/signalr-concept-disaster-recovery/before-failover.png)

Abb. 2: Nach dem Failover ![Nach dem Failover](media/signalr-concept-disaster-recovery/after-failover.png)

Abb. 3: Kurz nach der Wiederherstellung der primären Instanz ![Kurz nach der Wiederherstellung der primären Instanz](media/signalr-concept-disaster-recovery/after-recover.png)

Wie Sie sehen, liegt im Normalfall nur für den primären App-Server und die primäre SignalR Service-Instanz Onlinedatenverkehr vor (blau dargestellt).
Nach dem Failover werden auch der sekundäre App-Server und die sekundäre SignalR Service-Instanz aktiv.
Wenn die primäre SignalR Service-Instanz wieder online ist, stellen neue Clients eine Verbindung mit der primären SignalR-Instanz her. Vorhandene Clients stellen jedoch weiterhin eine Verbindung mit der sekundären Instanz her, sodass beide Instanzen Datenverkehr verarbeiten.
Nachdem alle vorhandenen Clients die Verbindung getrennt haben, kehrt das System zum normalen Betriebszustand zurück (Abb. 1).

Für die Implementierung einer regionsübergreifenden hochverfügbaren Architektur stehen zwei grundlegende Muster zur Verfügung:

1. Verwendung eines Paars von App-Server und SignalR Service-Instanz, das den gesamten Onlinedatenverkehr verarbeitet, und eines weiteren Paars als Sicherung (bezeichnet als Aktiv/Passiv-Konfiguration, siehe Abb. 1). 
2. Verwendung von zwei (oder mehr) Paaren von App-Servern und Azure SignalR Service-Instanzen, die alle Onlinedatenverkehr verarbeiten und als Sicherung für andere Paare fungieren (bezeichnet als Aktiv/Aktiv-Konfiguration, siehe Abb. 3).

SignalR Service kann beide Muster unterstützen. Der wesentliche Unterschied besteht in der Implementierung der App-Server.
Wenn App-Server aktiv/passiv sind, sind auch die SignalR Service-Instanzen aktiv/passiv (weil der primäre App-Server nur seine primäre SignalR Service-Instanz zurückgibt).
Wenn App-Server aktiv/aktiv sind, sind auch die SignalR Service-Instanzen aktiv/aktiv (weil alle App-Server ihre eigenen primären SignalR-Instanzen zurückgeben und daher alle Datenverkehr empfangen können).

Beachten Sie, dass Sie unabhängig vom ausgewählten Muster jede SignalR Service-Instanz als primäre Instanz mit einem App-Server verbinden müssen.

Da die SignalR-Verbindung eine lange Verbindung ist, treten zudem im Fall eines Notfalls und Failovers Verbindungsabbrüche bei Clients auf.
Sie müssen solche Verbindungsabbrüche auf der Clientseite behandeln, damit sie für Ihre Endkunden transparent sind. Stellen Sie beispielsweise nach dem Schließen einer Verbindung erneut eine Verbindung her.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Ihre Anwendung konfigurieren, um Resilienz für SignalR Service zu erreichen. Weitere Details zur Server-/Clientverbindung und zum Verbindungsrouting in SignalR Service finden Sie in [diesem Artikel](signalr-concept-internals.md).

Informationen zu Skalierungsszenarien wie z.B. Sharding, bei denen mehrere Instanzen zusammen verwendet werden, um eine große Anzahl von Verbindungen zu handhaben, finden Sie unter [Skalieren mehrerer Instanzen](signalr-howto-scale-multi-instances.md).
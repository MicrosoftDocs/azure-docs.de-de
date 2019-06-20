---
title: Skalieren von ASP.NET Core SignalR mit Azure SignalR
description: Eine Übersicht zur Verwendung des Azure SignalR-Diensts zum Skalieren von ASP.NET Core SignalR-Anwendungen.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 8a4012d204b6dafa1233e4ce3d878590120be47d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60640224"
---
# <a name="scale-aspnet-core-signalr-applications-with-azure-signalr-service"></a>Skalieren von ASP.NET Core SignalR-Anwendungen mit Azure SignalR

## <a name="developing-signalr-apps"></a>Entwickeln von SignalR-Apps

Derzeit gibt es [zwei Versionen](https://docs.microsoft.com/aspnet/core/signalr/version-differences) von SignalR, die Sie mit Ihren Webanwendungen verwenden können: SignalR für ASP.NET sowie ASP.NET Core SignalR (neueste Version). Azure SignalR Service ist ein von Azure verwalteter Dienst, der auf ASP.NET Core SignalR basiert.

Bei ASP.NET Core SignalR handelt es sich um eine umgeschriebene Version der vorherigen Version. Aus diesem Grund ist ASP.NET Core SignalR nicht abwärtskompatibel mit der früheren SignalR-Version. Die APIs und Verhalten unterscheiden sich. Das ASP.NET Core SignalR SDK ist für .NET-Standard konzipiert, sodass Sie es weiterhin mit .NET Framework nutzen können. Sie müssen anstelle der alten APIs aber die neuen APIs verwenden. Wenn Sie SignalR nutzen und auf ASP.NET Core SignalR oder den Azure SignalR Service umstellen möchten, müssen Sie Ihren Code ändern, um Unterschiede bei den APIs verarbeiten zu können.

Mit dem Azure SignalR Service wird die serverseitige Komponente von ASP.NET Core SignalR in Azure gehostet. Da die Technologie aber auf ASP.NET Core basiert, können Sie Ihre eigentliche Webanwendung auf mehreren Plattformen ausführen (Windows, Linux und macOS), während Sie [Azure App Service](../app-service/overview.md), [IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), [Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache) oder [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index) zum Hosten verwenden. Außerdem können Sie das Self-Hosting in Ihrem eigenen Prozess nutzen.

Falls die Ziele für Ihre Anwendung u.a. die Unterstützung der aktuellen Funktionalität zum Aktualisieren von Webclients mit Inhaltsupdates in Echtzeit, die Ausführung auf mehreren Plattformen (Azure, Windows, Linux und macOS) und das Hosten in unterschiedlichen Umgebungen umfassen, ist der Azure SignalR-Dienst unter Umständen die beste Wahl.

## <a name="why-not-deploy-signalr-myself"></a>Was spricht dagegen, SignalR selbst bereitzustellen?

Es ist weiterhin ein gültiger Ansatz, Ihre eigene Azure-Web-App mit Unterstützung von ASP.NET Core SignalR als Back-End-Komponente für die übergreifende Webanwendung bereitzustellen.

Einer der wichtigsten Gründe für die Nutzung des Azure SignalR Service ist Einfachheit. Beim Azure SignalR Service müssen Sie sich nicht mit Problemen in Bezug auf die Leistung, Skalierbarkeit und Verfügbarkeit befassen. Diese Probleme sind für Sie durch eine Vereinbarung zum Servicelevel mit einem Wert von 99,9% abgedeckt.

Außerdem sind WebSockets normalerweise das bevorzugte Verfahren zur Unterstützung von Echtzeit-Inhaltsupdates. Das Durchführen eines Lastenausgleichs für eine große Zahl von persistenten WebSocket-Verbindungen wird zu einem komplizierten Problem, das Sie beim Skalieren lösen müssen. Häufig wird Folgendes genutzt: DNS-Lastenausgleich, Hardwarelastenausgleich und Softwarelastenausgleich. Der Azure SignalR Service übernimmt die Lösung dieses Problems für Sie.

Ein weiterer Grund kann sein, dass bei Ihnen gar nicht die Anforderung besteht, eine Webanwendung zu hosten. Die Logik Ihrer Webanwendung nutzt ggf. das [serverlose Computing](https://azure.microsoft.com/overview/serverless-computing/). Es kann beispielsweise sein, dass Ihr Code mit [Azure Functions](https://docs.microsoft.com/azure/azure-functions/)-Triggern nur bedarfsgesteuert gehostet und ausgeführt wird. Dieses Szenario kann Schwierigkeiten bereiten, da Ihr Code nur bedarfsgesteuert ausgeführt wird und keine dauerhaften Verbindungen mit Clients aufrechterhält. Diese Situation kann mit dem Azure SignalR Service gelöst werden, da der Dienst bereits Verbindungen für Sie verwaltet. Weitere Details finden Sie in der [Übersicht zur Verwendung des SignalR-Diensts mit Azure Functions](signalr-concept-azure-functions.md).

## <a name="how-does-it-scale"></a>Wie wird die Skalierung durchgeführt?

Es ist üblich, SignalR mit SQL Server, Azure Service Bus oder Azure Cache for Redis zu skalieren. Der Azure SignalR Service übernimmt den Skalierungsansatz für Sie. Leistung und Kosten sind mit diesen Ansätzen vergleichbar, ohne dass der komplexe Umgang mit diesen anderen Diensten anfällt. Sie müssen lediglich die Einheitenanzahl für Ihren Dienst aktualisieren. Jede Einheit unterstützt bis zu 1.000 Clientverbindungen.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen eines Chatraums per Azure SignalR](signalr-quickstart-dotnet-core.md)
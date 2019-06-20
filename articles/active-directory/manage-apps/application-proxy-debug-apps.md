---
title: 'Debuggen von Anwendungsproxyanwendungen: Azure Active Directory | Microsoft-Dokumentation'
description: Informationen zum Debuggen von Problemen mit Azure Active Directory-Anwendungsproxyanwendungen (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: d0a12bde119e9dae3f950603fac4bce060bb5f91
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145955"
---
# <a name="debug-application-proxy-application-issues"></a>Debuggen von Problemen mit Anwendungsproxyanwendungen 

In diesem Artikel wird erläutert, wie Sie Probleme mit Azure Active Directory-Anwendungsproxyanwendungen (Azure AD) beheben. Wenn Sie den Anwendungsproxydienst für den Remotezugriff auf eine lokale Webanwendung verwenden, aber Probleme beim Herstellen einer Verbindung mit der Anwendung haben, können Sie Anwendungsprobleme mithilfe dieses Flussdiagramms debuggen. 

## <a name="before-you-begin"></a>Voraussetzungen

Wir empfehlen, beim Behandeln von Problemen mit dem Anwendungsproxy mit den Connectors zu beginnen. Folgen Sie zunächst der empfohlenen Vorgehensweise bei der Problembehandlung unter [Debug Application Proxy Connector issues](application-proxy-debug-connectors.md) (Debuggen von Problemen mit Anwendungsproxyconnectors) um sicherzustellen, dass die Anwendungsproxyconnectors korrekt konfiguriert sind. Falls weiterhin Probleme auftreten, kehren Sie zu diesem Artikel zurück, um die Problembehandlung für die Anwendung durchzuführen.  

Weitere Informationen zum Anwendungsproxy finden Sie unter:

- [Remote access to on-premises applications through Application Proxy](application-proxy.md) (Remotezugriff auf lokale Anwendungen über den Anwendungsproxy)
- [Anwendungsproxyconnectors](application-proxy-connectors.md)
- [Installieren und Registrieren eines Connectors](application-proxy-add-on-premises-application.md)
- [Beheben von Problemen mit Anwendungsproxys und Fehlermeldungen](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Flussdiagramm für Anwendungsprobleme

Dieses Flussdiagramm führt Sie durch die Schritte zum Debuggen einiger der häufigsten Probleme beim Herstellen einer Verbindung mit der Anwendung. Ausführliche Informationen zu den einzelnen Schritten finden Sie in der Tabelle unter dem Flussdiagramm.

![Flussdiagramm mit den Schritten zum Debuggen einer Anwendung](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Aktion | BESCHREIBUNG | 
|---------|---------|---------|
|1 | Browser öffnen, auf die App zugreifen und Anmeldeinformationen eingeben | Versuchen Sie, sich mit Ihren Anmeldeinformationen bei der App anzumelden, und überprüfen Sie, ob benutzerbezogene Fehler auftreten, z. B. [Zugriff auf diese Unternehmens-App nicht möglich](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | Benutzerzuweisung zur App überprüfen | Stellen Sie sicher, dass Ihr Benutzerkonto über die Berechtigung verfügt, innerhalb des Unternehmensnetzwerks auf die App zuzugreifen, und testen Sie dann die Anmeldung bei der App anhand der Schritte in [Testen der Anwendung](application-proxy-add-on-premises-application.md#test-the-application). Falls weiterhin Probleme bei der Anmeldung auftreten, finden Sie unter [Beheben von Anmeldefehlern](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-troubleshoot-sign-in-errors) weitere Informationen.  |
|3 | Browser öffnen und auf die App zugreifen | Wenn sofort ein Fehler angezeigt wird, überprüfen Sie, ob der Anwendungsproxy richtig konfiguriert ist. Informationen zu bestimmten Fehlermeldungen finden Sie unter [Beheben von Problemen mit Anwendungsproxys und Fehlermeldungen](application-proxy-troubleshoot.md).  |
|4 | Konfiguration Ihrer benutzerdefinierten Domäne überprüfen oder den Fehler beheben | Wenn die Seite gar nicht angezeigt wird, stellen Sie wie unter [Arbeiten mit benutzerdefinierten Domänen](application-proxy-configure-custom-domain.md) beschrieben sicher, dass Ihre benutzerdefinierte Domäne korrekt konfiguriert ist.<br></br>Wenn die Seite nicht geladen wird und eine Fehlermeldung angezeigt wird, beheben Sie den Fehler wie unter [Beheben von Problemen mit Anwendungsproxys und Fehlermeldungen](application-proxy-troubleshoot.md) beschrieben. <br></br>Wenn es länger als 20 Sekunden dauert, bis eine Fehlermeldung angezeigt wird, liegt möglicherweise ein Konnektivitätsproblem vor. Weitere Informationen finden Sie im Artikel zur Problembehandlung [Debug Application Proxy connectors](application-proxy-debug-connectors.md) (Debuggen von Anwendungsproxyconnectors).  |
|5 | Wenn weiterhin Probleme auftreten, Connectors debuggen | Möglicherweise liegt ein Konnektivitätsproblem zwischen dem Proxy und dem Connector oder zwischen dem Connector und dem Back-End vor. Weitere Informationen finden Sie im Artikel zur Problembehandlung [Debug Application Proxy connectors](application-proxy-debug-connectors.md) (Debuggen von Anwendungsproxyconnectors). |
|6 | Alle Ressourcen veröffentlichen, mit Entwicklertools des Browsers überprüfen und Links reparieren | Stellen Sie sicher, dass der Veröffentlichungspfad alle erforderlichen Bilder, Skripts und Stylesheets für Ihre Anwendung enthält. Ausführliche Informationen finden Sie unter [Hinzufügen einer lokalen App zu Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). <br></br>Verwenden Sie die Entwicklungstools des Browsers (F12-Tools in Internet Explorer oder Microsoft Edge), und überprüfen Sie wie unter [Anwendungsseite für eine Anwendungsproxyanwendung wird nicht richtig angezeigt](application-proxy-page-appearance-broken-problem.md) beschrieben, ob Probleme mit der Veröffentlichung vorliegen. <br></br>Lesen Sie unter [Links auf der Seite funktionieren nicht](application-proxy-page-links-broken-problem.md) die Methoden zum Beheben fehlerhafter Links nach. |
|7 | Netzwerklatenz überprüfen | Wenn die Seite langsam geladen wird, lesen Sie unter [Reduzieren der Wartezeit](application-proxy-network-topology.md#considerations-for-reducing-latency) die Möglichkeiten zum Minimieren der Netzwerklatenz nach. | 
|8 | Weitere Artikel zur Problembehandlung zurate ziehen | Wenn die Probleme weiterhin auftreten, finden Sie in der [Dokumentation zur Problembehandlung für Anwendungsproxys](application-proxy-page-appearance-broken-problem.md) weitere Artikel zum Beheben von Problemen. |

## <a name="next-steps"></a>Nächste Schritte


* [Veröffentlichen von Anwendungen in getrennten Netzwerken und an getrennten Standorten mithilfe von Connectorgruppen](application-proxy-connector-groups.md)
* [Verwenden von vorhandenen lokalen Proxyservern](application-proxy-configure-connectors-with-proxy-servers.md)
* [Beheben von Problemen mit Anwendungsproxys und Connectors](application-proxy-troubleshoot.md)
* [Installieren des Azure AD-Anwendungsproxyconnectors im Hintergrund](application-proxy-register-connector-powershell.md)

---
title: 'Debuggen von Anwendungsproxyconnectors: Azure Active Directory | Microsoft-Dokumentation'
description: Enthält Informationen zum Debuggen von Problemen mit Azure AD-Anwendungsproxyconnectors (Azure Active Directory).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: cbbb5328f2d7e814be9b5b94ec522bbb01df39e5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658246"
---
# <a name="debug-application-proxy-connector-issues"></a>Debuggen von Problemen mit Anwendungsproxyconnectors 

In diesem Artikel wird erläutert, wie Sie Probleme mit Azure AD-Anwendungsproxyconnectors (Azure Active Directory) beheben. Wenn Sie den Anwendungsproxydienst für den Remotezugriff auf eine lokale Webanwendung verwenden, aber Probleme beim Herstellen einer Verbindung mit der Anwendung haben, können Sie Connectorprobleme mithilfe dieses Flussdiagramms debuggen. 

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie den Anwendungsproxyconnector installiert haben und ein Problem aufgetreten ist. Für die Behandlung von Problemen mit dem Anwendungsproxy empfehlen wir Ihnen, mit diesen Problembehandlungsschritten zu beginnen, um zu ermitteln, ob die Anwendungsproxyconnectors richtig konfiguriert wurden. Falls Sie immer noch Probleme mit dem Herstellen der Verbindung mit der Anwendung haben, helfen Ihnen die Problembehandlungsschritte unter [Debuggen von Problemen mit Anwendungsproxyanwendungen](application-proxy-debug-apps.md) weiter.  


Weitere Informationen zum Anwendungsproxy und zur Verwendung der zugehörigen Connectors finden Sie unter:

- [Remote access to on-premises applications through Application Proxy](application-proxy.md) (Remotezugriff auf lokale Anwendungen über den Anwendungsproxy)
- [Anwendungsproxyconnectors](application-proxy-connectors.md)
- [Installieren und Registrieren eines Connectors](application-proxy-add-on-premises-application.md)
- [Beheben von Problemen mit Anwendungsproxys und Fehlermeldungen](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Flussdiagramm für Connectorprobleme

In diesem Flussdiagramm werden die Schritte zum Debuggen von einigen häufigeren Connectorproblemen beschrieben. Ausführliche Informationen zu den einzelnen Schritten finden Sie in der Tabelle unter dem Flussdiagramm.

![Flussdiagramm mit den Schritten zum Debuggen eines Connectors](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

| Schritt | Aktion | BESCHREIBUNG |
|---------|---------|---------|
|1 | Ermitteln der Connectorgruppe, die der App zugewiesen ist | Vermutlich haben Sie einen Connector auf mehreren Servern installiert. In diesem Fall sollten die Connectors [Connectorgruppen zugewiesen sein](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Weitere Informationen zu Connectorgruppen finden Sie unter [Veröffentlichen von Anwendungen in getrennten Netzwerken und an getrennten Standorten mithilfe von Connectorgruppen](application-proxy-connector-groups.md). |
|2 | Installieren des Connectors und Zuweisen einer Gruppe | Wenn Sie keinen Connector installiert haben, helfen Ihnen die Informationen unter [Installieren und Registrieren eines Connectors](application-proxy-add-on-premises-application.md#install-and-register-a-connector) weiter.<br></br> Wenn Probleme beim Installieren des Connectors auftreten, lesen Sie [Problem beim Installieren des Anwendungsproxy-Agent-Connectors](application-proxy-connector-installation-problem.md).<br></br> Lesen Sie den Abschnitt [Erstellen von Connectorgruppen](application-proxy-connector-groups.md#create-connector-groups), wenn der Connector keiner Gruppe zugewiesen ist.<br></br>Lesen Sie den Abschnitt [Zuweisen von Anwendungen zu Ihren Connectorgruppen](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups), wenn die Anwendung keiner Connectorgruppe zugewiesen ist.|
|3 | Ausführen eines Porttests auf dem Connectorserver | Führen Sie auf dem Connectorserver einen Porttest aus, indem Sie [telnet](/windows-server/administration/windows-commands/telnet) oder ein anderes Tool zum Testen von Ports verwenden, um zu überprüfen, ob die [Ports 443 und 80 geöffnet](application-proxy-add-on-premises-application.md#open-ports) sind.|
|4 | Konfigurieren der Domänen und Ports | [Stellen Sie sicher, dass Ihre Domänen und Ports richtig konfiguriert sind](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). Damit der Connector richtig funktioniert, müssen bestimmte Ports geöffnet sein, und Ihr Server muss auf einige URLs zugreifen können. |
|5 | Überprüfen, ob ein Back-End-Proxy verwendet wird | Überprüfen Sie, ob für die Connectors Back-End-Proxyserver genutzt oder umgangen werden. Ausführliche Informationen finden Sie unter [Problembehandlung für Proxyprobleme des Connectors und Verbindungsprobleme von Diensten](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6 | Aktualisieren des Connectors und Updaters zur Verwendung des Back-End-Proxys | Wenn ein Back-End-Proxy verwendet wird, sollten Sie sicherstellen, dass für den Connector derselbe Proxy genutzt wird. Ausführliche Informationen zur Problembehandlung und Konfiguration für Connectors in Bezug auf die Zusammenarbeit mit Proxyservern finden Sie unter [Verwenden von vorhandenen lokalen Proxyservern](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Laden der internen URL der App auf dem Connectorserver | Laden Sie auf dem Connectorserver die interne URL der App. |
|8 | Überprüfen der internen Netzwerkkonnektivität | In Ihrem internen Netzwerk besteht ein Konnektivitätsproblem, das von diesem Debugvorgang nicht diagnostiziert werden kann. Für die Anwendung muss der interne Zugriff möglich sein, damit die Connectors funktionieren. Sie können Connector-Ereignisprotokolle wie unter [Anwendungsproxyconnectors](application-proxy-connectors.md#under-the-hood) beschrieben aktivieren und anzeigen. |
|9 | Erhöhen des Timeoutwerts auf dem Back-End | Ändern Sie unter **Zusätzliche Einstellungen** für Ihre Anwendung die Einstellung **Timeout der Back-End-Anwendung** in **Lang**. Weitere Informationen finden Sie unter [Hinzufügen einer lokalen App zu Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Falls die Probleme weiterhin bestehen: Untersuchen bestimmter Ablaufprobleme, Überprüfen von App- und SSO-Debugvorgängen | Verwenden Sie die Vorgehensweise unter [Debuggen von Problemen mit Anwendungsproxyanwendungen](application-proxy-debug-apps.md) für die Problembehandlung. |

## <a name="next-steps"></a>Nächste Schritte


* [Veröffentlichen von Anwendungen in getrennten Netzwerken und an getrennten Standorten mithilfe von Connectorgruppen](application-proxy-connector-groups.md)
* [Verwenden von vorhandenen lokalen Proxyservern](application-proxy-configure-connectors-with-proxy-servers.md)
* [Beheben von Problemen mit Anwendungsproxys und Connectors](application-proxy-troubleshoot.md)
* [Installieren des Azure AD-Anwendungsproxyconnectors im Hintergrund](application-proxy-register-connector-powershell.md)
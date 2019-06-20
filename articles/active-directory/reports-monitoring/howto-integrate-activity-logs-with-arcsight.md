---
title: Integrieren von Azure Active Directory-Protokollen in ArcSight mit Azure Monitor | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Azure Active Directory-Protokolle mit Azure Monitor in ArcSight integrieren.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08a265637274f396497da37706391bf44e0c9107
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60437021"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Integrieren von Azure Active Directory-Protokollen in ArcSight mit Azure Monitor

[Micro Focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) ist eine SIEM-Lösung (Security Information & Event Management), mit der Sie Sicherheitsbedrohungen in Ihrer Plattform erkennen und darauf reagieren können. Jetzt können Sie Azure AD-Protokolle (Azure Active Directory) mithilfe von Azure Monitor und dem ArcSight-Connector für Azure AD an ArcSight weiterleiten. Durch dieses Feature können Sie Ihren Mandanten mithilfe von ArcSight auf Sicherheitsgefährdungen überwachen.  

In diesem Artikel erfahren Sie, wie Azure AD-Protokolle mithilfe von Azure Monitor an ArcSight weitergeleitet werden. 

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um dieses Feature verwenden zu können:
* Ein Azure Event Hub, der Azure AD-Aktivitätsprotokolle enthält. Erfahren Sie, [wie Sie Aktivitätsprotokolle an einen Event Hub streamen](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Eine konfigurierte Instanz von ArcSight Syslog NG Daemon SmartConnector (SmartConnector) oder ArcSight Load Balancer. Wenn die Ereignisse an ArcSight Load Balancer gesendet werden, werden sie anschließend von Load Balancer an den SmartConnector gesendet.

Laden Sie den [Konfigurationsleitfaden für ArcSight SmartConnector für den Azure Monitor-Event Hub](https://community.softwaregrp.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/2/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf) herunter, und öffnen Sie ihn. Dieser Leitfaden enthält die Schritte zum Installieren und Konfigurieren von ArcSight SmartConnector für Azure Monitor. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Integrieren von Azure AD-Protokollen in ArcSight

1. Führen Sie zuerst die Schritte im Abschnitt **Prerequisites** (Voraussetzungen) des Konfigurationsleitfadens aus. Dieser Abschnitt umfasst die folgenden Schritte:
    * Legen Sie Benutzerberechtigungen in Azure fest, um sicherzustellen, dass ein Benutzer mit der Rolle **Besitzer** vorliegt, der den Connector bereitstellen und konfigurieren kann.
    * Öffnen Sie Ports auf dem Server mit Syslog NG Daemon SmartConnector, sodass er von Azure aus zugänglich ist. 
    * Bei der Bereitstellung wird ein Windows PowerShell-Skript ausgeführt. Daher müssen Sie PowerShell die Ausführung von Skripts auf dem Computer ermöglichen, auf dem Sie den Connector bereitstellen möchten.

2. Befolgen Sie die Schritte im Abschnitt **Deploying the Connector** (Bereitstellen des Connectors) im Konfigurationsleitfaden, um den Connector bereitzustellen. Dieser Abschnitt führt Sie durch die Schritte zum Herunterladen und Extrahieren des Connectors, zum Konfigurieren der Anwendungseigenschaften und zum Ausführen des Bereitstellungsskripts aus dem extrahierten Ordner. 

3. Stellen Sie anhand der Schritte im Abschnitt **Verifying the Deployment in Azure** (Überprüfen der Bereitstellung in Azure) sicher, dass der Connector eingerichtet ist und ordnungsgemäß funktioniert. Überprüfen Sie Folgendes:
    * Die erforderlichen Azure-Funktionen wurden in Ihrem Azure-Abonnement erstellt.
    * Die Azure AD-Protokolle werden an das richtige Ziel gestreamt. 
    * Die Anwendungseinstellungen aus Ihrer Bereitstellung werden in den Anwendungseinstellungen in Azure-Funktions-Apps dauerhaft gespeichert. 
    * In Azure wird eine neue Ressourcengruppe für ArcSight erstellt, die eine Azure AD-Anwendung für den ArcSight-Connector und Speicherkonten mit den zugeordneten Dateien im CEF-Format umfasst.

4. Führen Sie abschließend die Schritte nach der Bereitstellung im Abschnitt **Post-Deployment Configurations** (Konfigurationsschritte nach der Bereitstellung) des Konfigurationsleitfadens durch. In diesem Abschnitt werden die zusätzlichen Konfigurationsschritte erläutert, die bei Verwendung eines App Service-Plans verhindern, dass die Funktions-Apps nach einem Zeitlimit in den Leerlauf wechseln. Zudem wird beschrieben, wie Sie das Streamen von Diagnoseprotokollen aus dem Event Hub konfigurieren und das SysLog NG Daemon SmartConnector-Keystorezertifikat aktualisieren, um es einem neu erstellten Speicherkonto zuzuordnen.

5. Ferner wird im Konfigurationsleitfaden die Anpassung von Connectoreigenschaften in Azure sowie das Aktualisieren und Deinstallieren des Connectors erläutert. Es gibt auch einen Abschnitt zu Möglichkeiten der Leistungssteigerung, darunter das Upgrade auf einen [Azure Consumption-Plan](https://azure.microsoft.com/pricing/details/functions) und die Konfiguration von ArcSight Load Balancer, wenn die Ereignislast die Kapazitäten eines einzelnen Syslog NG Daemon SmartConnectors übersteigt.

## <a name="next-steps"></a>Nächste Schritte

[Konfigurationsleitfaden für ArcSight SmartConnector für den Azure Monitor-Event Hub](https://community.softwaregrp.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/2/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf)

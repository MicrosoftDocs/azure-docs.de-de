---
title: Verbinden von Microsoft Web Application Firewall-Daten mit Azure Sentinel Preview | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Microsoft Web Application Firewall-Daten mit Azure Sentinel verknüpfen.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 99c553145f0a600826e48bec437449f278b74fbf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65205642"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Verbinden von Daten von Microsoft Web Application Firewall

> [!IMPORTANT]
> Azure Sentinel ist derzeit als öffentliche Vorschauversion (Public Preview) verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sie können Protokolle von der Microsoft Web Application Firewall (WAF) für Azure Application Gateway in Azure Sentinel streamen. Diese Web Application Firewall schützt Ihre Anwendungen vor gängigen Sicherheitsrisiken im Web wie Einschleusung von SQL-Befehlen und Cross-Site-Scripting und ermöglicht Ihnen, Regeln anzupassen, um falsch positive Ergebnisse zu verringern. Befolgen Sie diese Anweisungen, um Protokolle Ihrer Microsoft Web Application Firewall in Azure Sentinel zu streamen.


## <a name="prerequisites"></a>Voraussetzungen

- Eine vorhandene Anwendungsgatewayressource

## <a name="connect-to-microsoft-web-application-firewall"></a>Eine Verbindung mit der Microsoft Web Application Firewall

Wenn Sie bereits über eine Microsoft Web Application Firewall verfügen, stellen Sie sicher, dass eine Gatewayressource vorhanden ist.
Sobald Ihre Microsoft Web Application Firewall bereitgestellt ist und Daten empfängt, können die Warnungsdaten problemlos in Azure Sentinel gestreamt werden.
    
1. Wählen Sie im Azure Sentinel-Portal **Datenconnectors** aus.
1. Wählen Sie auf der Seite „Datenconnectors“ die Kachel **WAF** aus.
1. Wechseln Sie zur [Application Gateway-Ressource](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) , und wählen Sie Ihre Web Application Firewall aus.
    1. Wählen Sie  **Diagnoseeinstellungen** aus.
    1. Klicken Sie unter der Tabelle auf  **+ Diagnoseeinstellung hinzufügen**.
    1. Geben Sie auf der Seite  **Diagnoseeinstellungen** einen  **Namen** ein, und klicken Sie auf  **An Log Analytics senden**.
    1. Wählen Sie unter **Log Analytics-Arbeitsbereich** den Azure Sentinel-Arbeitsbereich aus.
    1. Wählen Sie die Protokolltypen aus, die Sie analysieren möchten. Folgendes wird empfohlen: ApplicationGatewayAccessLog und ApplicationGatewayFirewallLog.
1. Um in Log Analytics das relevante Schema für die Microsoft Web Application Firewall-Warnungen zu verwenden, suchen Sie nach **AzureDiagnostics**.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument wurde erläutert, wie Sie Microsoft Web Application Firewall mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats.md).

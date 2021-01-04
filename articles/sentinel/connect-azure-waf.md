---
title: Verknüpfen von Azure Web Application Firewall (WAF)-Daten mit Azure Sentinel
description: Erfahren Sie, wie Sie Azure WAF-Daten mit Azure Sentinel verbinden.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 05/07/2020
ms.author: yelevin
ms.openlocfilehash: c554f3582e67622a5a1739c9e410328c902d491b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655900"
---
# <a name="connect-data-from-azure-web-application-firewall-waf"></a>Verbinden von Daten aus Azure Web Application Firewall (WAF)

Webanwendungen sind zunehmend Ziele böswilliger Angriffe, die allgemein bekannte Sicherheitslücken ausnutzen. Azure Web Application Firewall (WAF) schützt Ihre Webanwendungen zentralisiert vor gängigen Exploits und Bedrohungen wie der Einschleusung von Code und Cross-Site Scripting. Azure WAF kann im [Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)-Dienst, dem [Azure Front Door](../web-application-firewall/afds/afds-overview.md)-Dienst und über eine [Azure Content Delivery Network (CDN)-](../web-application-firewall/cdn/cdn-overview.md)-WAF-Richtlinie bereitgestellt werden (letztere ist zurzeit als öffentliche Vorschau verfügbar).
Sie können Azure WAF-Protokolle mit Azure Sentinel verbinden, was es Ihnen ermöglicht, Protokolldaten in Arbeitsmappen anzuzeigen, sie zum Erstellen benutzerdefinierter Warnungen zu verwenden und sie zur Verbesserung Ihrer Untersuchung zu integrieren.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für den Azure Sentinel-Arbeitsbereich.

## <a name="connect-to-azure-waf"></a>Verbinden mit Azure WAF

### <a name="instructions-tab"></a>Registerkarte „Anweisungen“

1. Klicken Sie im Azure Sentinel-Navigationsmenü auf **Data connectors** (Datenconnectors).

1. Wählen Sie **Azure Web Application Firewall (WAF)-** aus dem Datenconnectors-Katalog aus, und wählen Sie dann **Connectorseite öffnen** im Vorschaubereich aus.

1. Wählen Sie den Link für den Typ der WAF-Ressource aus, deren Protokolle Sie verbinden möchten – **Application Gateway-Ressource öffnen >** , **> Front Door-Ressource öffnen** oder **Content Delivery Network (CDN)-WAF-Richtlinie öffnen >** – und wählen Sie im Bildschirm der Ressourcenliste eine WAF-Ressource aus der Liste aus.

    1. Wählen Sie im Navigationsmenü der WAF-Ressource **Diagnoseeinstellungen** aus.

    1. Wählen Sie am Ende der Liste **+ Diagnoseeinstellung hinzufügen** aus.

    1. Geben Sie im Bildschirm **Diagnoseeinstellungen** einen Namen in das Feld **Name der Diagnoseeinstellungen** ein.

    1. Aktivieren Sie das Kontrollkästchen **An Log Analytics senden**. Zwei neue Felder werden darunter angezeigt. Wählen Sie das relevante **Abonnement** und den **Log Analytics-Arbeitsbereich** (in dem sich Azure Sentinel befindet) aus.

    1. Aktivieren Sie die Kontrollkästchen der Regeltypen, deren Protokolle Sie erfassen möchten. Unsere Empfehlungen für den jeweiligen Ressourcentyp:

        | Resource | Für die Erfassung auszuwählende Protokolle |
        |----------|------------------------------|
        | Application Gateway | ApplicationGatewayAccessLog<br>ApplicationGatewayFirewallLog |
        | Front Door          | FrontdoorAccessLog<br>FrontdoorWebApplicationFirewallLog |
        | CDN-WAF-Richtlinie      | WebApplicationFirewallLogs |
        |

    1. Wählen Sie **Speichern** aus.

### <a name="next-steps-tab"></a>Registerkarte „Nächste Schritte“

- Sehen Sie sich die empfohlenen Arbeitsmappen, Abfragebeispiele und Analyseregelvorlagen an, die mit dem Datenconnector der **Azure Web Application Firewall** gebündelt sind, um Erkenntnisse zu Ihren Azure WAF-Protokolldaten zu erhalten.

- Um Azure WAF-Daten in **Protokolle** abzufragen, geben Sie **AzureDiagnostics** in das Abfragefenster ein.

> [!NOTE]
>
> Mit diesem speziellen Datenconnector werden die Konnektivitätsstatusindikatoren (ein Farbstreifen im Datenconnectors-Katalog sowie Verbindungssymbole neben den Datentypnamen) nur dann als *verbunden* (grün) angezeigt, wenn Daten irgendwann innerhalb der letzten zwei Wochen erfasst wurden. Wenn zwei Wochen ohne Datenerfassung vergangen sind, wird der Connector als „getrennt“ angezeigt. In dem Moment, in dem weitere Daten den Connector passieren, wird der Status wieder als *verbunden* angezeigt.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Azure WAF-Protokolle mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
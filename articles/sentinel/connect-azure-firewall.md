---
title: Verknüpfen von Azure Firewall-Daten mit Azure Sentinel
description: Hier erfahren Sie, wie Sie Azure Firewall-Daten mit Azure Sentinel verknüpfen.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 4dffaac329f1581d9082fd8ab2c314f52b1730ab
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656019"
---
# <a name="connect-data-from-azure-firewall"></a>Verknüpfen von Daten aus Azure Firewall

> [!IMPORTANT]
> Der Azure Firewall-Datenconnector in Azure Sentinel ist derzeit als öffentliche Vorschau verfügbar.
> Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Firewall ist ein verwalteter, cloudbasierter Netzwerksicherheitsdienst, der Ihre Azure Virtual Network-Ressourcen schützt. Es ist eine vollständig zustandsbehaftete Firewall als ein Dienst mit integrierter Hochverfügbarkeit und uneingeschränkter Cloudskalierbarkeit. 

Sie können Azure Firewall-Protokolle mit Azure Sentinel verbinden. Das ermöglicht es Ihnen, Protokolldaten in Arbeitsmappen anzuzeigen, sie zum Erstellen benutzerdefinierter Warnungen zu verwenden und sie zur Verbesserung Ihrer Untersuchung zu integrieren.

Informieren Sie sich ausführlicher über das [Überwachen von Azure Firewall-Protokollen und -Metriken](../firewall/firewall-diagnostics.md).

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für den Azure Sentinel-Arbeitsbereich.

## <a name="connect-to-azure-firewall"></a>Herstellen einer Verbindung mit Azure Firewall
    
1. Klicken Sie im Azure Sentinel-Navigationsmenü auf **Data connectors** (Datenconnectors).

1. Wählen Sie im Katalog für Datenconnectors **Azure Firewall** und dann im Vorschaubereich **Connectorseite öffnen** aus.

1. Aktivieren Sie **Diagnoseprotokolle** für alle Firewalls, deren Protokolle Sie verbinden möchten:

    1. Wählen Sie den Link **Open Azure Firewall resource >** (Azure Firewall-Ressource öffnen >) aus.

    1. Wählen Sie im Navigationsmenü für **Firewalls** die Option **Diagnoseeinstellungen** aus.

    1. Wählen Sie am Ende der Liste **+ Diagnoseeinstellung hinzufügen** aus.

    1. Geben Sie auf dem Bildschirm **Diagnoseeinstellungen** einen Namen in das Feld **Name der Diagnoseeinstellungen** ein.
    
    1. Aktivieren Sie das Kontrollkästchen **An Log Analytics senden**. Zwei neue Felder werden darunter angezeigt. Wählen Sie das relevante **Abonnement** und den **Log Analytics-Arbeitsbereich** (in dem sich Azure Sentinel befindet) aus.

    1. Aktivieren Sie die Kontrollkästchen der Regeltypen, deren Protokolle Sie erfassen möchten. Empfohlen werden **AzureFirewallApplicationRule** und **AzureFirewallNetworkRule**.

    1. Wählen Sie im oberen Bereich des Bildschirms **Speichern** aus.

1. Um in Log Analytics das relevante Schema für die Azure Firewall-Warnungen zu verwenden, suchen Sie nach **AzureDiagnostics**.

> [!NOTE]
>
> Mit diesem speziellen Datenconnector werden die Konnektivitätsstatusindikatoren (ein Farbstreifen im Datenconnectors-Katalog sowie Verbindungssymbole neben den Datentypnamen) nur dann als *verbunden* (grün) angezeigt, wenn Daten irgendwann innerhalb der letzten zwei Wochen erfasst wurden. Wenn zwei Wochen ohne Datenerfassung vergangen sind, wird der Connector als „getrennt“ angezeigt. In dem Moment, in dem weitere Daten den Connector passieren, wird der Status wieder als *verbunden* angezeigt.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Azure Firewall-Protokolle mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
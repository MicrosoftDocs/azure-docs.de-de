---
title: Azure DDoS Protection – Simulationstests
description: Erfahren Sie, wie Sie Simulationstests durchführen
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: eff738e24b3abce52e80291c55a3ae64c3c8c853
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92904951"
---
# <a name="test-through-simulations"></a>Durchführen von Simulationstests

Sie sollten Ihre Annahmen über die Reaktionen Ihrer Dienste auf einen Angriff unbedingt mithilfe regelmäßiger Simulationen testen. Überprüfen Sie während der Tests, ob Ihre Dienste oder Anwendungen weiterhin wie erwartet funktionieren und der Benutzer keine Unterbrechung merkt. Identifizieren Sie Lücken sowohl vom technologischen als auch Prozessstandpunkt, und beziehen Sie sie in die DDoS-Reaktionsstrategie ein. Generell sollten Sie derartige Tests in Stagingumgebungen oder außerhalb der Spitzenzeiten ausführen, um Auswirkungen auf die Produktionsumgebung zu minimieren.

Wir haben gemeinsam mit [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud), einem Self-Service-Datenverkehrgenerator, eine Schnittstelle entwickelt, mit der Azure-Kunden zu Simulationszwecken Datenverkehr für öffentliche Endpunkte generieren können, für die DDoS Protection aktiviert ist. Sie können die Simulation für folgende Zwecke nutzen:

- Zur Überprüfung, wie Azure DDoS Protection Ihre Azure-Ressourcen vor DDoS-Angriffen schützt
- Zur Optimierung Ihres Reaktionsprozesses auf Incidents während DDoS-Angriffen
- Zur Dokumentation der DDoS-Konformität
- Zur Schulung Ihrer Netzwerksicherheitsteams

## <a name="prerequisites"></a>Voraussetzungen

- Um die Schritte in diesem Tutorial auszuführen, müssen Sie zunächst einen [Azure DDoS Standard-Schutzplan](manage-ddos-protection.md) mit geschützten öffentlichen IP-Adressen erstellen.
- Erstellen Sie zunächst ein Konto bei [BreakingPoint Cloud](http://breakingpoint.cloud/). 

## <a name="configure-a-ddos-test-attack"></a>Konfigurieren eines DDoS-Testangriffs

1. Geben Sie die folgenden Werte ein bzw. wählen Sie sie aus, und wählen Sie dann **Test starten** aus:

    |Einstellung        |Wert                                              |
    |---------      |---------                                          |
    |Ziel-IP-Adresse           | Geben Sie eine Ihrer öffentlichen IP-Adressen ein, die Sie testen möchten.                     |
    |Portnummer   | Geben Sie _443_ ein.                       |
    |DDoS-Profil | Wählen Sie **TCP-SYN-Flood** aus.|
    |Testgröße       | Wählen Sie **200.000 PPS, 100 MBit/s und 8 Quell-IP-Adressen** aus.                                  |
    |Testdauer | Wählen Sie **10 Minuten** aus.|

Die Konfiguration sollte jetzt wie folgt aussehen:

![Beispielsimulation eines DDoS-Angriffs: BreakingPoint Cloud](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>Überwachen und Überprüfen

1. Melden Sie sich bei https://portal.azure.com an, und wechseln Sie zu Ihrem Abonnement.
1. Wählen Sie die öffentliche IP-Adresse aus, mit der Sie den Angriff getestet haben.
1. Wählen Sie unter **Überwachung** die Option **Metriken** .
1. Wählen Sie für **Metrik** die Option _Unter DDoS-Angriff oder nicht_ aus.

Sobald die Ressource angegriffen wird, sollte der Wert von **0** in **1** geändert werden, wie in der folgenden Abbildung gezeigt:

![Beispielsimulation eines DDoS-Angriffs: Portal](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Telemetriedaten zum DDoS-Schutz anzeigen und konfigurieren](telemetry-monitoring-alerting.md).
- Erfahren Sie, wie Sie [Berichte und Datenflussprotokolle zur Entschärfung von DDoS-Angriffen konfigurieren](reports-and-flow-logs.md).
- Erfahren Sie, wie Sie [DDoS Rapid Response einbinden](ddos-rapid-response.md).
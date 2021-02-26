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
ms.openlocfilehash: e95495e48725a68ab1fe3f37d235e5765b2c8015
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806238"
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
    |DDoS-Profil | Mögliche Werte sind unter anderem `DNS Flood`, `NTPv2 Flood`, `SSDP Flood`, `TCP SYN Flood`, `UDP 64B Flood`, `UDP 128B Flood`, `UDP 256B Flood`, `UDP 512B Flood`, `UDP 1024B Flood`, `UDP 1514B Flood`, `UDP Fragmentation`, `UDP Memcached`.|
    |Testgröße       | Mögliche Werte sind unter anderem `100K pps, 50 Mbps and 4 source IPs`, `200K pps, 100 Mbps and 8 source IPs`, `400K pps, 200Mbps and 16 source IPs`, `800K pps, 400 Mbps and 32 source IPs`.                                  |
    |Testdauer | Mögliche Werte sind unter anderem `10 Minutes`, `15 Minutes`, `20 Minutes`, `25 Minutes`, `30 Minutes`.|

Die Konfiguration sollte jetzt wie folgt aussehen:

![Beispielsimulation eines DDoS-Angriffs: BreakingPoint Cloud](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>Überwachen und Überprüfen

1. Melden Sie sich bei https://portal.azure.com an, und wechseln Sie zu Ihrem Abonnement.
1. Wählen Sie die öffentliche IP-Adresse aus, mit der Sie den Angriff getestet haben.
1. Wählen Sie unter **Überwachung** die Option **Metriken**.
1. Wählen Sie für **Metrik** die Option _Unter DDoS-Angriff oder nicht_ aus.

Sobald die Ressource angegriffen wird, sollte der Wert von **0** in **1** geändert werden, wie in der folgenden Abbildung gezeigt:

![Beispielsimulation eines DDoS-Angriffs: Portal](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

### <a name="breakingpoint-cloud-api-script"></a>BreakingPoint Cloud-API-Skript

Dieses [API-Skript](https://aka.ms/ddosbreakingpoint) kann verwendet werden, um DDoS-Tests zu automatisieren, indem es einmalig ausgeführt wird oder indem mithilfe von cron regelmäßige Tests geplant werden. Dies ist nützlich, um zu überprüfen, ob Ihre Protokollierung ordnungsgemäß konfiguriert ist und ob Erkennungs- und Reaktionsprozeduren wirksam sind. Für die Skripts ist ein Linux-Betriebssystem (getestet mit Ubuntu 18.04 LTS) und Python 3 erforderlich. Installieren Sie die erforderlichen Komponenten und den API-Client mithilfe des enthaltenen Skripts oder indem Sie die Dokumentation auf der [BreakingPoint Cloud](http://breakingpoint.cloud/)-Website verwenden.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Telemetriedaten zum DDoS-Schutz anzeigen und konfigurieren](telemetry.md).
- Erfahren Sie mehr zum [Anzeigen und Konfigurieren der DDoS-Diagnoseprotokollierung](diagnostic-logging.md).
- Erfahren Sie, wie Sie [DDoS Rapid Response einbinden](ddos-rapid-response.md).

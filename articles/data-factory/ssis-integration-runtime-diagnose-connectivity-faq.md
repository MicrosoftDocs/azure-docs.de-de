---
title: Verwenden des Features „Verbindung diagnostizieren“ in der SSIS Integration Runtime
description: Beheben Sie Verbindungsprobleme in der SSIS-Integration Runtime mithilfe des Features „Verbindung diagnostizieren“.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: ede36b20353f00ed9a4f80bec2d7bc5a3512a9ea
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637954"
---
# <a name="use-the-diagnose-connectivity-feature-in-the-ssis-integration-runtime"></a>Verwenden des Features „Verbindung diagnostizieren“ in der SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Es treten möglicherweise Fehler beim Ausführen von SSIS-Paketen (SQL Server Integration Services) in der SSIS-Integration Runtime auf. Diese Probleme treten besonders dann auf, wenn Ihre SSIS-Integration Runtime dem virtuellen Azure-Netzwerk beitritt.

Verwenden Sie zum Beheben von Konnektivitätsproblemen das Feature *Verbindung diagnostizieren* , um Verbindungen zu testen. Sie finden dieses Feature auf der Seite „Überwachung“ der SSIS-Integration Runtime im Azure Data Factory-Portal.

 ![Seite „Überwachung“: „Verbindung diagnostizieren“](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png)

 ![Seite „Überwachung“: „Verbindung testen“](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)

In den folgenden Abschnitten erfahren Sie mehr über die beim Testen von Verbindungen am häufigsten auftretenden Fehler. In jedem Abschnitt wird Folgendes beschrieben:

- Fehlercode
- Fehlermeldung
- Mögliche Ursachen des Fehlers
- Empfohlene Lösungen

## <a name="error-code-invalidinput"></a>Fehlercode: InvalidInput

- **Fehlermeldung** : „Überprüfen Sie, ob die Eingabe korrekt ist.“
- **Mögliche Ursache:** Ihre Eingabe ist falsch.
- **Empfehlung** : Überprüfen Sie die Eingabe.

## <a name="error-code-firewallornetworkissue"></a>Fehlercode: FirewallOrNetworkIssue

- **Fehlermeldung** : „Überprüfen Sie, ob dieser Port auf der Firewall/dem Server/der NSG geöffnet und das Netzwerk stabil ist.“
- **Mögliche Ursachen:**
  - Der Server öffnet den Port nicht.
  - Ihrer Netzwerksicherheitsgruppe wird der ausgehende Datenverkehr auf diesem Port verweigert.
  - Dieser Port wird von Ihrer NVA, von Azure Firewall oder der lokalen Firewall nicht geöffnet.
- **Empfehlungen:**
  - Öffnen Sie den Port auf dem Server.
  - Aktualisieren Sie die Netzwerksicherheitsgruppe, sodass ausgehender Datenverkehr über diesen Port zugelassen ist.
  - Öffnen Sie diesen Port auf der NVA, in Azure Firewall oder in der lokalen Firewall.

## <a name="error-code-misconfigureddnssettings"></a>Fehlercode: MisconfiguredDnsSettings

- **Fehlermeldung** : „Wenn Sie Ihren eigenen DNS-Server in dem VNET verwenden, das mit Ihrer Azure-SSIS IR verknüpft ist, überprüfen Sie, ob er Ihren Hostnamen auflösen kann.“
- **Mögliche Ursachen:**
  -  Es besteht ein Problem mit Ihrem benutzerdefinierten DNS.
  -  Sie verwenden keinen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) für Ihren privaten Hostnamen.
- **Empfehlungen:**
  -  Beheben Sie Ihr Problem mit dem benutzerdefinierten DNS, um sicherzustellen, dass er den Hostnamen auflösen kann.
  -  Verwenden Sie den vollqualifizierten Domänenname. Die Azure-SSIS IR fügt nicht automatisch Ihr DNS-Suffix an. Verwenden Sie beispielsweise **<Ihr_privater_Server>.contoso.com** anstelle von **<Ihr_privater_Server>** .

## <a name="error-code-servernotallowremoteconnection"></a>Fehlercode: ServerNotAllowRemoteConnection

- **Fehlermeldung** : „Überprüfen Sie, ob der Server TCP-Remoteverbindungen über diesen Port zulässt.“
- **Mögliche Ursachen:**
  -  Ihre Serverfirewall lässt keine TCP-Remoteverbindungen zu.
  -  Ihr Server ist nicht online.
- **Empfehlungen:**
  -  Lassen Sie TCP-Remoteverbindungen über die Serverfirewall zu.
  -  Starten Sie den Server.
   
## <a name="error-code-misconfigurednsgsettings"></a>Fehlercode: MisconfiguredNsgSettings

- **Fehlermeldung** : „Stellen Sie sicher, dass die NSG Ihres VNET ausgehenden Datenverkehr über diesen Port zulässt. Wenn Sie Azure ExpressRoute und/oder eine UDR verwenden, überprüfen Sie, ob dieser Port auf Ihrer Firewall/Ihrem Server geöffnet ist.“
- **Mögliche Ursachen:**
  -  Ihrer Netzwerksicherheitsgruppe wird der ausgehende Datenverkehr auf diesem Port verweigert.
  -  Dieser Port wird von Ihrer NVA, von Azure Firewall oder der lokalen Firewall nicht geöffnet.
- **Empfehlung:**
  -  Aktualisieren Sie die Netzwerksicherheitsgruppe, sodass ausgehender Datenverkehr über diesen Port zugelassen ist.
  -  Öffnen Sie diesen Port auf der NVA, in Azure Firewall oder in der lokalen Firewall.

## <a name="error-code-genericissues"></a>Fehlercode: GenericIssues

- **Fehlermeldung** : „Fehler bei Testverbindung aufgrund generischer Probleme.“
- **Mögliche Ursache:** Bei der Testverbindung ist ein allgemeines vorübergehendes Problem aufgetreten.
- **Empfehlung** : Wiederholen Sie die Testverbindung später. Wenden Sie sich an das Azure Data Factory-Supportteam, wenn auch bei der Wiederholung des Vorgangs Probleme auftreten.

## <a name="error-code-pspingexecutiontimeout"></a>Fehlercode: PSPingExecutionTimeout

- **Fehlermeldung** : „Timeout bei Testverbindung, versuchen Sie es später noch mal.“
- **Mögliche Ursache:** Timeout bei Testen der Konnektivität.
- **Empfehlung** : Wiederholen Sie die Testverbindung später. Wenden Sie sich an das Azure Data Factory-Supportteam, wenn auch bei der Wiederholung des Vorgangs Probleme auftreten.

## <a name="error-code-networkinstable"></a>Fehlercode: NetworkInstable

- **Fehlermeldung** : „Die Testverbindung ist wg. Netzwerkinstabilität unregelmäßig.“
- **Mögliche Ursache:** Vorübergehendes Netzwerkproblem.
- **Empfehlung** : Überprüfen Sie, ob das Server- oder Firewallnetzwerk stabil ist.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen eines SSIS-Projekts mit SQL Server Management Studio (SSMS)](/sql/integration-services/ssis-quickstart-deploy-ssms)
- [Ausführen eines SSIS-Pakets mit SQL Server Management Studio (SSMS)](/sql/integration-services/ssis-quickstart-run-ssms)
- [Planen der Ausführung von in Azure bereitgestellten SSIS-Paketen mit SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15)
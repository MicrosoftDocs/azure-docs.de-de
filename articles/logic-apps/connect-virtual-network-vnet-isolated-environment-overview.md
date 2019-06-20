---
title: Zugreifen auf virtuelle Azure-Netzwerke über Azure Logic Apps mit Integrationsdienstumgebungen (ISEs)
description: In dieser Übersicht wird beschrieben, wie Logik-Apps mit Integrationsdienstumgebungen (ISEs) auf virtuelle Azure-Netzwerke (VNETs) zugreifen können.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 1ef8c8eec3865f2a6e363e7da1dbda9504b81c05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65546446"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Zugreifen auf Ressourcen virtueller Azure-Netzwerke über Azure Logic Apps mit Integrationsdienstumgebungen (ISEs)

Manchmal benötigen Ihre Logik-Apps und Integrationskonten Zugriff auf gesicherte Ressourcen wie virtuelle Computer (VMs) und andere Systeme oder Dienste innerhalb eines [virtuellen Azure-Netzwerks](../virtual-network/virtual-networks-overview.md). Wenn Sie diesen Zugriff einrichten möchten, können Sie [eine *Integrationsdienstumgebung* (Integration Service Environment, ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) zum Ausführen Ihrer Logik-Apps und Erstellen Ihrer Integrationskonten erstellen.

Bei der Erstellung einer ISE stellt Azure eine private und isolierte Instanz des Logic Apps-Diensts in Ihrem virtuellen Azure-Netzwerk bereit. Diese private Instanz verwendet dedizierte Ressourcen wie z. B. Speicher und wird getrennt vom öffentlichen „globalen“ Logic Apps-Dienst ausgeführt. Die Trennung Ihrer isolierten privaten Instanz von der öffentlichen globalen Instanz trägt auch dazu bei, die Auswirkungen zu verringern, die andere Azure-Mandanten auf die Leistung Ihrer Apps haben könnten. Hierbei spricht man auch vom [„Noisy-Neighbor“-Problem](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

Wenn Sie nach dem Erstellen Ihrer ISE Ihre Logik-App oder Ihr Integrationskonto erstellen, können Sie Ihre ISE als Standort Ihrer Logik-App oder Ihres Integrationskontos auswählen:

![Auswählen der Integrationsdienstumgebung](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Ihre Logik-App kann nun unter Verwendung eines der folgenden Elemente direkt auf Systeme zugreifen, die sich innerhalb Ihres virtuellen Netzwerks befinden oder mit diesem verbunden sind:

* Ein Connector mit der Bezeichnung **ISE** für dieses System, z. B. SQL Server
* Ein integrierter Trigger oder eine integrierte Aktion mit der Bezeichnung **Core**, z. B. der HTTP-Trigger oder die HTTP-Aktion
* Ein benutzerdefinierter Connector

In dieser Übersicht wird detailliert beschrieben, wie eine ISE Logik-Apps und Integrationskonten ermöglicht, direkt auf Ihr virtuelles Azure-Netzwerk zuzugreifen. Außerdem werden die Unterschiede zwischen einer ISE und dem globalen Logic Apps-Dienst herausgestellt.

> [!NOTE]
> Für Logik-Apps, integrierte Trigger, integrierte Aktionen und Connectors, die in Ihrer ISE ausgeführt werden, gilt ein anderer als der nutzungsbasierte Tarif. Weitere Informationen hierzu finden Sie unter [Logic Apps – Preise](../logic-apps/logic-apps-pricing.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>„Isoliert“ im Vergleich zu „global“

Wenn Sie in Azure eine Integrationsdienstumgebung (ISE) erstellen, können Sie das virtuelle Azure-Netzwerk auswählen, in das Sie Ihre ISE *einfügen* möchten. Azure fügt dann eine private Instanz des Logic Apps-Diensts in Ihr virtuelles Netzwerk ein bzw. stellt diese im virtuellen Netzwerk bereit. Dadurch wird eine isolierte Umgebung erstellt, in der Sie Logik-Apps auf dedizierten Ressourcen erstellen und ausführen können. Wenn Sie Ihre Logik-App erstellen, wählen Sie Ihre ISE als Speicherort Ihrer App aus, wodurch die Logik-App direkten Zugriff auf Ihr virtuelles Netzwerk und die Ressourcen in diesem Netzwerk erhält.

Logik-Apps in einer Integrationsdienstumgebung bieten die gleiche Benutzeroberfläche und ähnliche Funktionen wie im globalen Logic Apps-Dienst. Sie können nicht nur die gleichen integrierten Trigger, integrierten Aktionen und Connectors im globalen Logic Apps-Dienst verwenden, sondern auch ISE-spezifische Connectors. Zum Beispiel bieten die folgenden Standardconnectors Versionen an, die in einer ISE ausgeführt werden können:

* Azure Blob Storage, File Storage und Table Storage
* Azure Queues, Azure Service Bus, Azure Event Hubs und IBM MQ
* FTP und SFTP-SSH
* SQL Server, SQL Data Warehouse, Azure Cosmos DB
* AS2, X12 und EDIFACT

Der Unterschied zwischen ISE- und Nicht-ISE-Connectors liegt darin, wo die Trigger und Aktionen ausgeführt werden:

* In Ihrer ISE werden die integrierten Trigger und Aktionen, z. B. HTTP, immer in derselben ISE wie Ihre Logik-App ausgeführt und zeigen die Bezeichnung **Core** an.

  ![Auswählen von integrierten Triggern und Aktionen mit der Bezeichnung „Core“](./media/connect-virtual-network-vnet-isolated-environment-overview/select-core-built-in-actions-triggers.png)

* Für Connectors, die in einer ISE ausgeführt werden, sind öffentlich gehostete Versionen im globalen Logic Apps-Dienst verfügbar. Bei Connectors, die zwei Versionen anbieten, werden Connectors mit der Bezeichnung **ISE** immer in der gleichen ISE wie Ihre Logik-App ausgeführt. Connectors ohne die Bezeichnung **ISE** werden im globalen Logic Apps-Dienst ausgeführt.

  ![Auswählen der ISE-Connectors](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

### <a name="access-to-on-premises-data-sources"></a>Zugriff auf lokale Datenquellen

Bei lokalen Systemen, die mit einem virtuellen Azure-Netzwerk verbunden sind, fügen Sie eine ISE in dieses Netzwerk ein, damit Ihre Logik-Apps direkt auf diese Systeme zugreifen können, indem sie eines der folgenden Elemente verwenden:

* Ein ISE-Connector mit Versionsangabe für dieses System, z. B. SQL Server
  
* HTTP-Aktion
  
* Benutzerdefinierter Connector

  * Wenn Sie über benutzerdefinierte Connectors verfügen, für die das lokale Datengateway erforderlich ist, und Sie haben diese Connectors außerhalb einer ISE erstellt, können Logik-Apps in einer ISE diese Connectors ebenfalls verwenden.
  
  * Benutzerdefinierte Connectors, die in einer ISE erstellt wurden, funktionieren nicht mit dem lokalen Datengateway. Diese Connectors können jedoch direkt auf lokale Datenquellen zugreifen, die mit dem virtuellen Netzwerk verbunden sind, das die ISE hostet. Deshalb benötigen Logik-Apps in einer ISE sehr wahrscheinlich das Datengateway nicht, wenn sie mit diesen Ressourcen kommunizieren.

Bei lokalen Systemen, die nicht mit einem virtuellen Netzwerk verbunden sind oder keine ISE-Connectors aufweisen, müssen Sie zuerst [das lokale Datengateway einrichten](../logic-apps/logic-apps-gateway-install.md), bevor Ihre Logik-Apps eine Verbindung zu diesen Systemen aufbauen können.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrationskonten mit ISE

In einer Integrationsdienstumgebung (ISE) haben Sie die Möglichkeit, Integrationskonten mit Logik-Apps zu verwenden. Diese Integrationskonten müssen jedoch auf *dieselbe ISE* wie die verknüpften Logik-Apps zugreifen. Logik-Apps in einer ISE können nur auf die Integrationskonten verweisen, die sich in derselben ISE befinden. Wenn Sie ein Integrationskonto erstellen, können Sie Ihre ISE als Speicherort für Ihr Integrationskonto auswählen.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [eine Verbindung mit virtuellen Azure-Netzwerken über isolierte Logik-Apps herstellen können](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).
* Erfahren Sie mehr über [Azure Virtual Network](../virtual-network/virtual-networks-overview.md).
* Erfahren Sie mehr über die [Integration virtueller Netzwerke für Azure-Dienste](../virtual-network/virtual-network-for-azure-services.md).

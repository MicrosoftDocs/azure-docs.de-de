---
title: Herstellen einer Verbindung mit virtuellen Azure-Netzwerken in Azure Logic Apps über eine Integrationsdienstumgebung (ISE)
description: Erstellen Sie eine Integrationsdienstumgebung (Integration Service Environment, ISE), damit Logik-Apps und Integrationskonten auf virtuelle Azure-Netzwerke (VNETs) zugreifen und dabei trotzdem privat und vom öffentlichen oder globalen Azure-Dienst isoliert bleiben können.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 05/20/2019
ms.openlocfilehash: bd1f06c93a75673f86f0c52f78cad8a60f7a1a1e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65961453"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Herstellen einer Verbindung mit virtuellen Azure-Netzwerken in Azure Logic Apps mithilfe einer Integrationsdienstumgebung

Für Szenarios, in denen Ihre Logik-Apps und Integrationskonten Zugriff auf ein [virtuelles Azure-Netzwerk](../virtual-network/virtual-networks-overview.md) benötigen, erstellen Sie eine [*Integrationsdienstumgebung* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Eine Integrationsdienstumgebung ist eine private und isolierte Umgebung, die dedizierten Speicher und andere Ressourcen verwendet, die vom öffentlichen oder „globalen“ Logic Apps-Dienst getrennt bleiben. Diese Trennung trägt auch dazu bei, jegliche Auswirkungen anderer Azure-Mandanten auf die Leistung Ihrer Apps zu verringern. Ihre ISE wird in Ihr virtuelles Azure-Netzwerk *eingefügt*. Daraufhin wird der Logic Apps-Dienst in Ihrem virtuellen Netzwerk bereitgestellt. Wählen Sie diese Integrationsdienstumgebung beim Erstellen einer Logik-App oder eines Integrationskontos als Standort aus. Ihre Logik-App bzw. Ihr Integrationskonto kann dann direkt auf Ressourcen wie virtuelle Computer (VMs), Server, Systeme und Dienste in Ihrem virtuellen Netzwerk zugreifen.

![Auswählen der Integrationsdienstumgebung](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

In diesem Artikel wird gezeigt, wie Sie die folgenden Aufgaben ausführen:

* Stellen Sie sicher, dass alle notwenigen Ports in einem virtuellen Netzwerk offen sind, damit der Datenverkehr durch Ihre Integrationsdienstumgebung über Subnetze in das virtuelle Netzwerk übertragen werden kann.

* Erstellen Ihrer Integrationsdienstumgebung.

* Erstellen einer Logik-App, die in Ihrer Integrationsdienstumgebung ausgeführt werden kann.

* Erstellen eines Integrationskontos für Ihre Logik-Apps in Ihrer Integrationsdienstumgebung.

Weitere Informationen zu Integrationsdienstumgebungen finden Sie unter [Zugriff auf Azure Virtual Network-Ressourcen aus Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>.

  > [!IMPORTANT]
  > Für Logik-Apps, integrierte Trigger, integrierte Aktionen und Connectors, die in Ihrer ISE ausgeführt werden, gilt ein anderer als der nutzungsbasierte Tarif. Weitere Informationen hierzu finden Sie unter [Logic Apps – Preise](../logic-apps/logic-apps-pricing.md).

* Ein [virtuelles Azure-Netzwerk](../virtual-network/virtual-networks-overview.md). Wenn Sie kein virtuelles Netzwerk besitzen, erfahren Sie, wie Sie [ein virtuelles Azure-Netzwerk erstellen](../virtual-network/quick-create-portal.md). 

  * Ihr virtuelles Netzwerk muss vier *leere* Subnetze aufweisen, um Ressourcen in Ihrer ISE bereitstellen und erstellen zu können. Sie können diese Subnetze im Voraus erstellen oder warten, bis Sie Ihre ISE erstellt haben, in der Sie Subnetze parallel erstellen können. Erfahren Sie mehr über die [Voraussetzungen für Subnetze](#create-subnet). 
  
    > [!NOTE]
    > Wenn Sie [ExpressRoute](../expressroute/expressroute-introduction.md) verwenden, das eine private Verbindung mit Microsoft Cloud Services bereitstellt, müssen Sie [eine Routentabelle](../virtual-network/manage-route-table.md) erstellen, die die folgende Route enthält, und diese Tabelle mit jedem von Ihrer ISE verwendeten Subnetz verknüpfen:
    > 
    > **Name**: <*Routenname*><br>
    > **Adresspräfix**: 0.0.0.0/0<br>
    > **Nächster Hop:** Internet

  * Achten Sie darauf, [dass Ihr virtuelles Netzwerk diese Ports zur Verfügung stellt](#ports), damit Ihre ISE ordnungsgemäß funktioniert und der Zugriff darauf gewährleistet ist.

* Wenn Sie benutzerdefinierte DNS-Server für Ihr virtuelles Azure-Netzwerk verwenden möchten, [richten Sie diese Server gemäß dieser Schritte ein](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md), bevor Sie Ihre ISE in Ihrem virtuellen Netzwerk bereitstellen. Andernfalls müssen Sie bei jeder Änderung Ihres DNS-Servers auch Ihre Integrationsdienstumgebung neu starten, was eine Funktion ist, die in der öffentlichen Vorschau der Integrationsdienstumgebung verfügbar ist.

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="ports"></a>

## <a name="check-network-ports"></a>Überprüfen von Netzwerkports

Wenn Sie eine Integrationsdienstumgebung mit einem virtuellen Netzwerk verwenden, besteht ein häufiges Einrichtungsproblem darin, dass ein oder mehrere Ports blockiert sind. Die Connectors, die Sie zum Herstellen von Verbindungen zwischen Ihrer Integrationsdienstumgebung und dem Zielsystem verwenden, können außerdem eigene Portanforderungen aufweisen. Wenn Sie beispielsweise über den FTP-Connector mit einem FTP-System kommunizieren, stellen Sie sicher, dass der Port, den Sie auf diesem FTP-System verwenden, wie z.B. Port 21 zum Senden von Befehlen, verfügbar ist.

Sie können [Netzwerksicherheitsgruppen](../virtual-network/security-overview.md), [indem Sie den Datenverkehr in Subnetzen filtern](../virtual-network/tutorial-filter-network-traffic.md), um den Datenverkehr in den Subnetzen des virtuellen Netzwerks zu steuern, in denen Sie die Integrationsdienstumgebung bereitstellen. In Ihrer Integrationsdienstumgebung müssen aber bestimmte Ports zu dem virtuellen Netzwerk, das Netzwerksicherheitsgruppen verwendet, offen sein. Auf diese Weise bleibt Ihre Integrationsdienstumgebung im Zugriff und kann ordnungsgemäß funktionieren, sodass Sie den Zugriff auf Ihre Integrationsdienstumgebung nicht verlieren. Andernfalls, wenn erforderlichen Ports nicht verfügbar sind, funktioniert Ihre Integrationsdienstumgebung nicht mehr.

In diesen Tabellen werden die Ports in Ihrem virtuellen Netzwerk beschrieben, die Ihre Integrationsdienstumgebung verwendet, und wo diese Ports verwendet werden. Die [Resource Manager-Diensttags](../virtual-network/security-overview.md#service-tags) stellen eine Gruppe von IP-Adresspräfixen dar, deren Aufgabe es ist, die Komplexität bei der Erstellung von Sicherheitsregeln zu verringern.

> [!IMPORTANT]
> Für die interne Kommunikation in Ihren Subnetzen ist es für die ISE erforderlich, dass Sie alle Ports in diesen Subnetzen öffnen.

| Zweck | Direction | Ports | Quelldiensttag | Zieldiensttag | Notizen |
|---------|-----------|-------|--------------------|-------------------------|-------|
| Datenverkehr aus Azure Logic Apps | Ausgehend | 80 und 443 | VirtualNetwork | Internet | Der Port hängt vom externen Dienste ab, mit dem der Logic Apps-Dienst kommuniziert. |
| Azure Active Directory | Ausgehend | 80 und 443 | VirtualNetwork | AzureActiveDirectory | |
| Azure Storage-Abhängigkeit | Ausgehend | 80 und 443 | VirtualNetwork | Storage | |
| Kommunikation zwischen Subnetzen | Ein- und ausgehend | 80 und 443 | VirtualNetwork | VirtualNetwork | Für die Kommunikation zwischen Subnetzen |
| Datenverkehr zu Azure Logic Apps | Eingehend | 443 | Internet  | VirtualNetwork | Die IP-Adresse des Computers oder Diensts, der einen Anforderungstrigger oder Webhook aufruft, der in Ihrer Logik-App vorhanden ist. Wenn Sie diesen Port schließen oder blockieren, werden HTTP-Aufrufe Ihrer Logik-Apps mit Anforderungstriggern verhindert.  |
| Ausführungsverlauf einer Logik-App | Eingehend | 443 | Internet  | VirtualNetwork | Die IP-Adresse des Computers, über den Sie den Ausführungsverlauf der Logik-App anzeigen. Wenn Sie diesen Port schließen oder blockieren, können Sie den Ausführungsverlauf zwar anzeigen, aber Sie können weder Ein- noch Ausgaben für die Schritte im Ausführungsverlauf anzeigen. |
| Verbindungsverwaltung | Ausgehend | 443 | VirtualNetwork  | Internet | |
| Veröffentlichen von Diagnoseprotokollen und Metriken | Ausgehend | 443 | VirtualNetwork  | AzureMonitor | |
| Kommunikation über Azure Traffic Manager | Eingehend | 443 | AzureTrafficManager | VirtualNetwork | |
| Logic Apps-Designer: dynamische Eigenschaften | Eingehend | 454 | Internet  | VirtualNetwork | Anforderungen werden von den [eingehenden IP-Adressen des Logic Apps-Zugriffsendpunkts in dieser Region gesendet](../logic-apps/logic-apps-limits-and-config.md#inbound). |
| Abhängigkeit von der App Service-Verwaltung | Eingehend | 454 und 455 | AppServiceManagement | VirtualNetwork | |
| Bereitstellen von Connectors | Eingehend | 454 und 3443 | Internet  | VirtualNetwork | Zum Bereitstellen und Aktualisieren von Connectors erforderlich. Wenn Sie diesen Port schließen oder blockieren, führt dies zu Fehlern bei ISE-Bereitstellungen, und es können keine Connectorupdates oder -fixes durchgeführt werden. |
| Azure SQL-Abhängigkeiten | Ausgehend | 1433 | VirtualNetwork | SQL |
| Azure Resource Health | Ausgehend | 1886 | VirtualNetwork | Internet | Zum Veröffentlichen des Integritätsstatus in Resource Health |
| API Management: Verwaltungsendpunkt | Eingehend | 3443 | APIManagement  | VirtualNetwork | |
| Abhängigkeit von Richtlinie zum Anmelden bei Event Hub und Überwachungs-Agent | Ausgehend | 5672 | VirtualNetwork  | EventHub | |
| Zugriff auf Azure Cache for Redis-Instanzen zwischen Rolleninstanzen | Eingehend <br>Ausgehend | 6379 – 6383 | VirtualNetwork  | VirtualNetwork | Damit die ISE mit Azure Cache for Redis funktioniert, müssen Sie die [Ports für ausgehenden und eingehenden Datenverkehr öffnen, die in den häufig gestellten Fragen zu Azure Cache for Redis beschrieben werden](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Azure Load Balancer | Eingehend | * | AzureLoadBalancer | VirtualNetwork |  |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Erstellen Ihrer Integrationsdienstumgebung

Befolgen Sie zum Erstellen Ihrer Integrationsdienstumgebung diese Schritte:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im Azure-Hauptmenü **Ressource erstellen** aus.
Geben Sie in das Suchfeld „Integrationsdienstumgebung“ als Ihren Filter ein.

   ![Neue Ressource erstellen](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Wählen Sie im Erstellungsbereich der Integrationsdienstumgebung **Erstellen** aus.

   ![Auswählen von „Erstellen“](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Geben Sie diese Details für Ihre Umgebung an, und wählen Sie dann **Bewerten + erstellen** aus, z. B.:

   ![Angeben von Umgebungsdetails](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Eigenschaft | Erforderlich | Value | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Abonnement** | Ja | <*Name des Azure-Abonnements*> | Das für Ihre Umgebung zu verwendende Azure-Abonnement |
   | **Ressourcengruppe** | Ja | <*Name der Azure-Ressourcengruppe*> | Die Azure-Ressourcengruppe, in der Sie Ihre Umgebung erstellen möchten. |
   | **Name der Integrationsdienstumgebung** | Ja | <*Umgebungsname*> | Der Name für Ihre Umgebung |
   | **Location** | Ja | <*Azure-Datencenterregion*> | Die Azure-Datencenterregion, in der Sie Ihre Umgebung bereitstellen. |
   | **Zusätzliche Kapazität** | Ja | 0 bis 10 | Die Anzahl der für diese ISE-Ressource zu verwendenden zusätzlichen Verarbeitungseinheiten. Weitere Informationen zum Hinzufügen von Kapazität nach dem Erstellen finden Sie im Abschnitt [Hinzufügen von ISE-Kapazität](#add-capacity). |
   | **Virtuelles Netzwerk** | Ja | <*Azure-virtual-network-name*> | Das virtuelle Azure-Netzwerk, in das Sie Ihre Umgebung einfügen möchten, damit Logik-Apps in dieser Umgebung auf Ihr virtuelles Netzwerk zugreifen können. Wenn Sie nicht über ein Netzwerk verfügen, [erstellen Sie zunächst ein virtuelles Azure-Netzwerk](../virtual-network/quick-create-portal.md). <p>**Wichtig**: Sie können diese Einfügung *nur* einmalig durchführen, wenn Sie Ihre ISE erstellen. |
   | **Subnetze** | Ja | <*subnet-resource-list*> | Eine ISE erfordert vier *leere* Subnetze zum Erstellen von Ressourcen in Ihrer Umgebung. Um jedes Subnetz zu erstellen, [führen Sie die Schritte unter dieser Tabelle aus](#create-subnet).  |
   |||||

   <a name="create-subnet"></a>

   **Erstellen eines Subnetzes**

   Ihre ISE erfordert vier *leere* Subnetze, die an keinen Dienst delegiert wurden, um eine Ressource in Ihrer Umgebung zu erstellen. 
   Sie können diese Subnetzadressen *nicht mehr ändern*, nachdem Sie Ihre Umgebung erstellt haben. Jedes Subnetz muss diese Kriterien erfüllen:

   * Einen Namen, der mit einem alphabetischen Zeichen oder Unterstrich beginnt und keines der folgenden Zeichen beinhaltet: `<`, `>`, `%`, `&`, `\\`, `?`, `/`

   * Das Format [Classless Inter-Domain Routing (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) und einen Class B-Adressraum

   * Es muss mindestens `/27` im Adressbereich verwenden, weil jedes Subnetz *mindestens* 32 Adressen aufweisen muss. Beispiel:

     * `10.0.0.0/27` hat 32 Adressen, da 2<sup>(32 – 27)</sup> 2<sup>5</sup> oder 32 ist.

     * `10.0.0.0/24` hat 256 Adressen, da 2<sup>(32 – 24)</sup> 2<sup>8</sup> oder 256 ist.

     * `10.0.0.0/28` hat nur 16 Adressen und ist somit zu klein, da 2<sup>(32 – 28)</sup> 2<sup>4</sup> oder 16 ist.

     Weitere Informationen zum Berechnen der Adressen finden Sie unter [Übersicht für IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Wenn Sie [ExpressRoute](../expressroute/expressroute-introduction.md) verwenden, müssen Sie daran denken, [eine Routentabelle](../virtual-network/manage-route-table.md) zu erstellen, die die folgende Route enthält, und diese Tabelle mit jedem von Ihrer ISE verwendeten Subnetz verknüpfen:

     **Name**: <*Routenname*><br>
     **Adresspräfix**: 0.0.0.0/0<br>
     **Nächster Hop:** Internet

   1. Wählen Sie unter der Liste **Subnetze** den Eintrag **Subnetzkonfiguration verwalten** aus.

      ![Verwalten der Subnetzkonfiguration](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. Wählen Sie im Bereich **Subnetze** den Eintrag **Subnetz** aus.

      ![Hinzufügen des Subnetzes](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Geben Sie im Bereich **Subnetz hinzufügen** diese Informationen an.

      * **Name**: Der Name für Ihr Subnetz.
      * **Adressbereich (CIDR-Block)** : Der Bereich Ihres Subnetzes in Ihrem virtuellen Netzwerk und im CIDR-Format

      ![Hinzufügen von Subnetzdetails](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Wählen Sie **OK** aus, wenn Sie fertig sind.

   1. Wiederholen Sie diese Schritte für drei weitere Subnetze.

      > [!NOTE]
      > Wenn die Subnetze, die Sie zu erstellen versuchen, nicht gültig sind, wird im Azure-Portal eine Meldung angezeigt, der Fortschritt aber nicht behindert.

1. Nachdem Azure Ihre ISE-Informationen erfolgreich überprüft hat, wählen Sie **Erstellen** aus, z. B.:

   ![Wählen Sie nach der erfolgreicher Überprüfung „Erstellen“ aus.](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure beginnt mit der Bereitstellung Ihrer Umgebung, aber dieser Prozess *könnte* bis zu zwei Stunden dauern, bevor er abgeschlossen ist. 
   Um den Bereitstellungsstatus zu überprüfen, wählen Sie in Ihrer Azure-Symbolleiste das Symbol „Benachrichtigungen“ aus, wodurch der Benachrichtigungsbereich geöffnet wird.

   ![Überprüfen des Bereitstellungsstatus](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Wenn die Bereitstellung erfolgreich abgeschlossen wurde, zeigt Azure diese Benachrichtigung an:

   ![Bereitstellung erfolgreich](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   Beachten Sie andernfalls die Anweisungen auf dem Azure-Portal zur Problembehandlung bei der Bereitstellung.

   > [!NOTE]
   > Wenn die Bereitstellung fehlschlägt oder Sie Ihre ISE löschen, kann es bis zu einer Stunde dauern, bis Azure Ihre Subnetze freigibt. Daher müssen Sie möglicherweise warten, bevor Sie diese Subnetze in einer anderen ISE wiederverwenden können. 
   >
   > Wenn Sie Ihr virtuelles Netzwerk löschen, dauert es in der Regel bis zu zwei Stunden, bis Azure Ihre Subnetze freigibt, dieser Vorgang kann aber auch länger dauern. 
   > Stellen Sie beim Löschen virtueller Netzwerke sicher, dass keine Ressourcen mehr verbunden sind. Beachten Sie hierzu die Anleitung [Löschen eines virtuellen Netzwerks](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Um Ihre Umgebung anzuzeigen, wählen Sie **Zu Ressource wechseln** aus, wenn Azure nach Abschluss der Bereitstellung nicht automatisch zu Ihrer Umgebung wechselt.  

Weitere Informationen zum Erstellen von Subnetzen finden Sie unter [Hinzufügen von Subnetzen virtueller Netzwerke](../virtual-network/virtual-network-manage-subnet.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Erstellen einer Logik-App – Integrationsdienstumgebung

Um Logik-Apps zu erstellen, die in Ihrer Integrationsdienstumgebung ausgeführt werden, [erstellen Sie Ihre Logik-Apps auf die übliche Weise](../logic-apps/quickstart-create-first-logic-app-workflow.md), außer wenn Sie die Eigenschaft **Standort** festlegen, wählen Sie Ihre Integrationsdienstumgebung im Abschnitt **Integrationsdienstumgebungen** aus, z. B.:

  ![Auswählen der Integrationsdienstumgebung](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

Informationen zu den Unterschieden, wie Trigger und Aktionen funktionieren und wie sie bezeichnet werden, wenn Sie eine Integrationsdienstumgebung verwenden, im Vergleich zum globalen Logic Apps-Dienst, finden Sie unter [„Isoliert“ gegenüber „Global“ in der Übersicht zur Integrationsdienstumgebung](connect-virtual-network-vnet-isolated-environment-overview.md#difference).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Erstellen des Integrationskontos – Integrationsdienstumgebung

Wenn Sie ein Integrationskonto mit Logik-Apps in einer Integrationsdienstumgebung verwenden möchten, muss dieses Integrationskonto *dieselbe Umgebung* wie die Logik-Apps verwenden. Logik-Apps in einer Integrationsdienstumgebung können nur auf Integrationskonten in derselben Integrationsdienstumgebung verweisen.

Um ein Integrationskonto zu erstellen, dass eine Integrationsdienstumgebung verwendet, [erstellen Sie Ihr Integrationskonto auf die übliche Weise](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), außer wenn Sie die Eigenschaft **Standort** festlegen, wählen Sie Ihre Integrationsdienstumgebung im Abschnitt **Integrationsdienstumgebungen** aus, z. B.:

![Auswählen der Integrationsdienstumgebung](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Hinzufügen von ISE-Kapazität

Ihre ISE-Basiseinheit hat eine feste Kapazität. Wenn Sie mehr Durchsatz benötigen, können Sie weitere Skalierungseinheiten hinzufügen. Sie können basierend auf Leistungsmetriken oder einer bestimmten Anzahl an zusätzlichen Verarbeitungseinheiten die Kapazität automatisch skalieren. Wenn Sie die automatische Skalierung basierend auf Metriken durchführen möchten, können Sie sich für verschiedene Kriterien entscheiden und Schwellenwerte für diese Kriterien festlegen.

1. Suchen Sie im Azure-Portal nach Ihrer ISE.

1. Wählen Sie im Hauptmenü Ihrer ISE **Übersicht** aus, um Nutzungs- und Leistungsmetriken für Ihre ISE anzuzeigen.

   ![Anzeigen der ISE-Nutzung](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. Wählen Sie unter **Einstellungen** **Horizontal hochskalieren**, um die automatische Skalierung einzurichten. Wählen Sie auf der Registerkarte **Konfigurieren** **Autoskalierung aktivieren** aus.

   ![Für die automatische Skalierung aktivieren](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. Geben Sie unter **Name der Einstellung für die Autoskalierung** einen Namen für Ihre Einstellung ein.

1. Wählen Sie im Bereich **Standard** entweder **Basierend auf einer Metrik skalieren** oder **Auf eine bestimmte Anzahl von Instanzen skalieren** aus.

   * Geben Sie eine Anzahl der Verarbeitungseinheiten zwischen 0 und 10 ein, wenn Sie sich für die instanzbasierte Option entscheiden.

   * Wenn Sie die metrikbasierte Option verwenden, befolgen Sie die nachstehenden Schritte:

     1. Wählen Sie im Abschnitt **Regeln** die Option **Regel hinzufügen** aus.

     1. Richten Sie im Bereich **Horizontal hochskalieren** Ihre Kriterien und die entsprechenden Maßnahmen beim Auslösen der Regel ein.

     1. Klicken Sie auf **Hinzufügen**, wenn Sie fertig sind.

1. Speichern Sie Ihre Änderungen, wenn Sie mit den Einstellungen für die automatische Skalierung fertig sind.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Azure Virtual Network](../virtual-network/virtual-networks-overview.md).
* Erfahren Sie mehr über die [Integration virtueller Netzwerke für Azure-Dienste](../virtual-network/virtual-network-for-azure-services.md).

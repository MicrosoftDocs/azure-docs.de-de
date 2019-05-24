---
title: Zugreifen auf lokale Datenquellen in Azure Logic Apps | Microsoft-Dokumentation
description: Herstellen einer Verbindung mit lokalen Datenquellen in Logik-Apps durch Erstellen eines lokalen Datengateways
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 0580fe09c2cb6569724a9b4365233a3142645a47
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546271"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Herstellen einer Verbindung mit lokalen Datenquellen in Azure Logic Apps

Um in Ihren Logik-Apps auf lokale Datenquellen zuzugreifen, erstellen Sie eine lokale Datengatewayressource im Azure-Portal. Ihre Logik-Apps können dann die [lokalen Connectors](../logic-apps/logic-apps-gateway-install.md#supported-connections) verwenden. In diesem Artikel wird gezeigt, wie Sie Ihre Azure-Gatewayressource erstellen, *nachdem* Sie [das Gateway auf Ihren lokalen Computer heruntergeladen und installiert haben](../logic-apps/logic-apps-gateway-install.md). 

> [!TIP]
> Erwägen Sie zum Herstellen einer Verbindung mit virtuellen Azure-Netzwerken stattdessen die Erstellung einer [*Integrationsdienstumgebung*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) (Integration Service Environment, ISE). 

Informationen zum Verwenden des Gateways mit anderen Diensten finden Sie in den folgenden Artikeln:

* [Microsoft Power BI | Lokales Datengateway](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow | Lokales Datengateway](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft Power Apps lokales Datengateway](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services | Lokales Datengateway](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>Voraussetzungen

* Sie müssen [das Datengateway bereits heruntergeladen und auf einem lokalen Computer installiert](../logic-apps/logic-apps-gateway-install.md) haben.

* Ihre Gatewayinstallation ist nicht bereits einer Gatewayressource in Azure zugeordnet. Sie können Ihre Gatewayinstallation nur mit einer einzigen Gatewayressource verknüpfen, was der Fall ist, wenn Sie die Gatewayressource erstellen und Ihre Gatewayinstallation auswählen. Diese Verknüpfung macht die Gatewayinstallation für andere Ressourcen nicht mehr verfügbar.

* Wenn Sie sich beim Azure-Portal anmelden und die Gatewayressource erstellen, müssen Sie dasselbe Anmeldekonto verwenden, das Sie zuvor zum [Installieren des lokalen Datengateways](../logic-apps/logic-apps-gateway-install.md#requirements) verwendet haben. Zudem ist dasselbe [Azure-Abonnement](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access) erforderlich, dass zum Installieren des Gateways benutzt wurde. Wenn Sie noch kein Azure-Abonnement haben, <a href="https://azure.microsoft.com/free/" target="_blank">melden Sie sich für ein kostenloses Azure-Konto an</a>.

* Um die Gatewayressource über das Azure-Portal erstellen und verwalten zu können, muss Ihr [Windows-Dienstkonto](../logic-apps/logic-apps-gateway-install.md#windows-service-account) mindestens über die Berechtigung **Mitwirkender** verfügen. Das lokale Datengateway wird als Windows-Dienst ausgeführt und ist so eingerichtet, dass es `NT SERVICE\PBIEgwService` für die Anmeldeinformationen des Windows-Diensts verwendet. 

  > [!NOTE]
  > Das Windows-Dienstkonto ist weder mit dem Konto, das zum Herstellen von Verbindungen mit den lokalen Datenquellen verwendet wird, noch mit dem Geschäfts-, Schul- oder Unikonto identisch, das Sie zum Anmelden bei Clouddiensten verwenden.

## <a name="download-and-install-gateway"></a>Herunterladen und Installieren des Gateways

Bevor Sie mit den Schritten in diesem Artikel fortfahren können, muss das Gateway auf einem lokalen Computer heruntergeladen und installiert sein.
Führen Sie – sofern noch nicht geschehen – die Schritte zum [Herunterladen und Installieren des lokalen Datengateways](../logic-apps/logic-apps-gateway-install.md) aus. 

<a name="create-gateway-resource"></a>

## <a name="create-azure-resource-for-gateway"></a>Erstellen von Azure-Ressourcen für Gateways

Nachdem Sie das Gateway auf einem lokalen Computer installiert haben, können Sie eine Azure-Ressource für Ihr Gateway erstellen. In diesem Schritt wird Ihre Gatewayressource auch Ihrem Azure-Abonnement zugeordnet.

1. Melden Sie sich beim <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> an. Achten Sie darauf, dass Sie dieselbe Azure-Geschäfts- oder -Schul-E-Mail-Adresse verwenden, mit der Sie das Gateway installiert haben.

2. Wählen Sie im Azure-Hauptmenü **Ressource erstellen** > 
**Integration** > **Lokales Datengateway** aus.

   ![Nach „Lokales Datengateway“ suchen](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. Geben Sie auf der Seite **Verbindungsgateway erstellen** die folgenden Informationen für Ihre Gatewayressource an:

   | Eigenschaft | BESCHREIBUNG | 
   |----------|-------------|
   | **Name** | Der Name für Ihre Gatewayressource | 
   | **Abonnement** | Der Name Ihres Azure-Abonnements, das mit dem Abonnement für Ihre Logik-App identisch sein muss. Das standardmäßige Abonnement basiert auf dem Azure-Konto, das Sie zum Anmelden verwendet haben. | 
   | **Ressourcengruppe** | Der Name der [Azure-Ressourcengruppe](../azure-resource-manager/resource-group-overview.md), die zum Organisieren verwandter Ressourcen verwendet wird. | 
   | **Location** | Azure schränkt diesen Standort auf die Region ein, die während der [Gatewayinstallation](../logic-apps/logic-apps-gateway-install.md) für den Gatewayclouddienst ausgewählt wurde. <p>**Hinweis**: Stellen Sie sicher, dass der Standort der Gatewayressource mit dem Standort des Gatewayclouddiensts übereinstimmt. Andernfalls kann es sein, dass Ihre Gatewayinstallation im nächsten Schritt nicht in der Liste der installierten Gateways zur Auswahl angezeigt wird. Sie können unterschiedliche Regionen für Ihre Gatewayressource und Ihre Logik-App verwenden. | 
   | **Installationsname** | Wenn Ihre Gatewayinstallation noch nicht ausgewählt ist, wählen Sie das Gateway aus, das Sie zuvor installiert haben. | 
   | | | 

   Beispiel: 

   ![Angeben von Details zum Erstellen Ihres lokalen Datengateways](./media/logic-apps-gateway-connection/createblade.png)

4. Um die Gatewayressource Ihrem Azure-Dashboard hinzuzufügen, wählen Sie **An Dashboard anheften** aus. Wenn Sie fertig sind, wählen Sie **Erstellen** aus.

   Um Ihr Gateway jederzeit suchen oder anzeigen zu können, wählen Sie im Azure-Hauptmenü die Option **Alle Dienste** aus. 
   Geben Sie im Suchfeld den Begriff „lokale Datengateways“ ein, und wählen Sie dann **Lokale Datengateways** aus.

   ![Nach „Lokale Datengateways“ suchen](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Herstellen einer Verbindung mit lokalen Daten

Nachdem Sie die Gatewayressource erstellt und Ihr Azure-Abonnement mit dieser Ressource verknüpft haben, können Sie jetzt mit dem Gateway eine Verbindung zwischen Ihrer Logik-App und Ihrer lokalen Datenquelle herstellen.

1. Erstellen oder öffnen Sie im Azure-Portal Ihre Logik-App im Logik-App-Designer.

2. Fügen Sie einen Connector hinzu, der lokale Verbindungen unterstützt, z.B. **SQL Server**.

3. Richten Sie jetzt die Verbindung ein:

   1. Wählen Sie **Verbinden über lokales Datengateway**. 

   2. Wählen Sie die für **Gateways** die Gatewayressource, die Sie zuvor erstellt haben. 

      Der Standort für die Gatewayverbindung muss sich zwar in derselben Region befinden wie Ihre Logik-App, Sie können aber ein Gateway auswählen, das sich in einer anderen Region befindet.

   3. Geben Sie einen eindeutigen Verbindungsnamen und die weiteren erforderlichen Informationen an. 

      Ein eindeutiger Verbindungsname vereinfacht später ein Erkennen dieser Verbindung, insbesondere wenn Sie mehrere Verbindungen herstellen. Sofern erforderlich, geben Sie auch die qualifizierte Domäne für Ihren Benutzernamen an.
   
      Beispiel: 

      ![Erstellen der Verbindung zwischen der Logik-App und dem Datengateway](./media/logic-apps-gateway-connection/blankconnection.png)

   4. Wenn Sie fertig sind, wählen Sie **Erstellen** aus. 

Die Gatewayverbindung kann nun für Ihre Logik-App verwendet werden.

## <a name="edit-connection"></a>Bearbeiten der Verbindung

Nach dem Sie eine Gatewayverbindung für Ihre Logik-App erstellt haben, kann es sein, dass Sie die Einstellungen für diese Verbindung später aktualisieren möchten.

1. Suchen Sie Ihre Gatewayverbindung:

   * Um alle API-Verbindungen nur für Ihre Logik-App zu suchen, wählen Sie im Menü Ihrer Logik-App unter **Entwicklungstools** die Option **API-Verbindungen** aus. 
   
     ![Navigieren zu Ihrer Logik-App, Auswählen von „API-Verbindungen“](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * So suchen Sie alle API-Verbindungen, die mit Ihrem Azure-Abonnement verknüpft sind: 

     * Wechseln Sie über das Azure-Hauptmenü zu **Alle Dienste** > **Web** > **API-Verbindungen**. 
     * Wechseln Sie alternativ über das Azure-Hauptmenü zu **Alle Ressourcen**.

2. Wählen Sie die gewünschte Gatewayverbindung und dann **API-Verbindung bearbeiten** aus.

   > [!TIP]
   > Sollten Ihre Aktualisierungen nicht wirksam werden, [beenden Sie den Gateway-Windows-Dienst, und starten Sie ihn neu](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Löschen der Gatewayressource

Um eine andere Ressource zu erstellen, Ihr Gateway mit einer anderen Ressource zu verknüpfen oder die Gatewayressource zu entfernen, können Sie die Gatewayressource löschen, ohne dass sich dies auf die Gatewayinstallation auswirkt. 

1. Wechseln Sie über das Azure-Hauptmenü zu **Alle Ressourcen**. 

2. Suchen Sie Ihre Gatewayressource, und wählen Sie sie aus.

3. Wenn nicht bereits ausgewählt, wählen Sie in Ihrem Gatewayressourcenmenü **Lokales Datengateway** aus. 

4. Wählen Sie in der Ressourcensymbolleiste die Option **Löschen** aus.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* [Sichern Ihrer Logik-Apps](./logic-apps-securing-a-logic-app.md)
* [Allgemeine Beispiele und Szenarien für Logik-Apps](./logic-apps-examples-and-scenarios.md)

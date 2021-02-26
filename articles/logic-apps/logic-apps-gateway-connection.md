---
title: Zugriff auf lokale Datenquellen
description: Herstellen einer Verbindung mit lokalen Datenquellen in Azure Logic Apps durch Erstellen einer Datengatewayressource in Azure
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 356e63bb0a749ad0f41d886e75971e9b05c7f9dc
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218993"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Herstellen einer Verbindung mit lokalen Datenquellen in Azure Logic Apps

Nachdem Sie [das *lokale Datengateway* auf einem lokalen Computer installiert haben](../logic-apps/logic-apps-gateway-install.md), müssen Sie für Ihre Gatewayinstallation eine Gatewayressource in Azure erstellen, um aus Ihren Logik-Apps lokal auf Datenquellen zugreifen zu können. Anschließend können Sie diese Gatewayressource in den Triggern und Aktionen auswählen, die Sie für die [lokalen Connectors](../connectors/apis-list.md#on-premises-connectors) verwenden möchten, die in Azure Logic Apps verfügbar sind. Azure Logic Apps unterstützt Lese- und Schreibvorgänge über das Datengateway. Allerdings besitzen diese Vorgänge [Limits hinsichtlich Ihrer Nutzlastgröße](/data-integration/gateway/service-gateway-onprem#considerations).

In diesem Artikel wird gezeigt, wie Sie Ihre Azure-Gatewayressource für ein zuvor [installiertes Gateway auf Ihren lokalen Computer](../logic-apps/logic-apps-gateway-install.md) erstellen. Weitere Informationen zum Gateway finden Sie unter [So funktioniert das Gateway](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Wenn direkt auf lokale Ressourcen in virtuellen Azure-Netzwerken zugegriffen werden soll, ohne das Gateway verwenden zu müssen, können Sie stattdessen eine [*Integrationsdienstumgebung*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) in Betracht ziehen. 

Informationen zum Verwenden des Gateways mit anderen Diensten finden Sie in den folgenden Artikeln:

* [Microsoft Power Automate für das lokale Datengateway](/power-automate/gateway-reference)
* [Microsoft Power BI | Lokales Datengateway](/power-bi/service-gateway-onprem)
* [Lokales Microsoft Power Apps-Datengateway](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services | Lokales Datengateway](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Unterstützte Datenquellen

In Azure Logic Apps unterstützt das lokale Datengateway [lokale Connectors](../connectors/apis-list.md#on-premises-connectors) für die folgenden Datenquellen:

* BizTalk Server 2016
* Dateisystem
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle-Datenbank
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Sie können auch [benutzerdefinierte Connectors](../logic-apps/custom-connector-overview.md) erstellen, die mithilfe von REST oder SOAP über HTTP oder HTTPS eine Verbindung mit Datenquellen herstellen. Obwohl das Gateway selbst keine zusätzlichen Kosten verursacht, gilt das [Logic Apps-Preismodell](../logic-apps/logic-apps-pricing.md) für diese Connectors und andere Vorgänge in Azure Logic Apps.

## <a name="prerequisites"></a>Voraussetzungen

* Sie haben das [lokale Datengateway bereits auf einem lokalen Computer installiert](../logic-apps/logic-apps-gateway-install.md). Diese Gatewayinstallation muss vorhanden sein, bevor Sie eine Gatewayressource erstellen können, die mit dieser Installation verknüpft ist.

* Sie verfügen über [dasselbe Azure-Konto und Abonnement](../logic-apps/logic-apps-gateway-install.md#requirements), das Sie für Ihre Gatewayinstallation verwendet haben. Dieses Azure-Konto muss zu einem einzigen [Azure Active Directory-Mandanten oder -Verzeichnis (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology) gehören. Dasselbe Azure-Konto und Abonnement muss zum Erstellen Ihrer Gatewayressource in Azure verwendet werden. Der Grund dafür ist, dass nur der Gatewayadministrator die Gatewayressource in Azure erstellen kann. Dienstprinzipale werden aktuell nicht unterstützt.

  * Wenn Sie in Azure eine Gatewayressource erstellen, wählen Sie eine Gatewayinstallation aus, die ausschließlich mit Ihrer Gatewayressource verknüpft wird. Eine Gatewayressource kann nur mit einer einzigen Gatewayinstallation verknüpft sein. Gatewayinstallationen, die bereits anderen Gatewayressourcen zugeordnet sind, können nicht ausgewählt werden.

  * Ihre Logik-App und Gatewayressource müssen nicht zum selben Azure-Abonnement gehören. In Triggern und Aktionen, bei denen Sie die Gatewayressource verwenden können, können Sie ein anderes Azure-Abonnement auswählen, das über eine Gatewayressource verfügt, aber nur, wenn sich dieses Abonnement im selben Azure AD-Mandanten oder Verzeichnis wie Ihre Logik-App befindet. Sie müssen außerdem über Administratorberechtigungen für das Gateway verfügen, das ein anderer Administrator für Sie einrichten kann. Weitere Informationen finden Sie unter [Datengateway: Automatisierung mithilfe von PowerShell – Teil 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) und [PowerShell: Datengateway – Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser).

    > [!NOTE]
    > Derzeit ist es nicht möglich, eine Gatewayressource oder -installation über mehrere Abonnements hinweg zu teilen. Informationen zum Übermitteln von Produktfeedback finden Sie im [Microsoft Azure-Feedbackforum](https://feedback.azure.com/forums/34192--general-feedback).

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Erstellen einer Azure-Gatewayressource

Nachdem Sie das Gateway auf einem lokalen Computer installiert haben, erstellen Sie eine Azure-Ressource für Ihr Gateway.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) mit dem gleichen Azure-Konto an, das beim Installieren des Gateways verwendet wurde.

1. Geben Sie im Suchfeld des Azure-Portals den Begriff „lokales Datengateway“ ein, und wählen Sie dann **Lokale Datengateways** aus.

   ![Nach „Lokales Datengateway“ suchen](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. Wählen Sie unter **Lokale Datengateways** die Option **Hinzufügen** aus.

   ![Hinzufügen neuer Azure-Ressource für das Datengateway](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. Geben Sie unter **Verbindungsgateway erstellen** die folgenden Informationen für Ihre Gatewayressource an. Wählen Sie **Erstellen**, wenn Sie fertig sind.

   | Eigenschaft | BESCHREIBUNG |
   |----------|-------------|
   | **Ressourcenname** | Geben Sie einen Namen für Ihre Gatewayressource an, der nur Buchstaben, Ziffern, Bindestriche (`-`), Unterstriche (`_`), Klammern (`(`, `)`) und Punkte (`.`) enthalten darf. |
   | **Abonnement** | Wählen Sie das Azure-Abonnement für das Azure-Konto aus, das für die Gatewayinstallation verwendet wurde. Das standardmäßige Abonnement basiert auf dem Azure-Konto, das Sie zum Anmelden verwendet haben. |
   | **Ressourcengruppe** | Die [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md), die Sie verwenden möchten. |
   | **Location** | Die gleiche Region oder der gleiche Standort, der während der [Gatewayinstallation](../logic-apps/logic-apps-gateway-install.md) für den Gatewayclouddienst ausgewählt wurde. Andernfalls wird Ihre Gatewayinstallation nicht in der Liste **Installationsname** angezeigt. Der Standort Ihrer Logik-App kann sich vom Standort Ihrer Gatewayressource unterscheiden. |
   | **Installationsname** | Wählen Sie eine Gatewayinstallation aus, die in der Liste nur angezeigt wird, wenn die folgenden Bedingungen erfüllt sind: <p><p>– Die Gatewayinstallation verwendet dieselbe Region wie die Gatewayressource, die Sie erstellen möchten. <br>– Die Gatewayinstallation ist nicht mit einer anderen Azure-Gatewayressource verknüpft. <br>– Die Gatewayinstallation ist mit demselben Azure-Konto verknüpft, das Sie zum Erstellen der Gatewayressource verwenden. <br>– Ihr Azure-Konto gehört zu einem einzigen [Azure Active Directory-Mandanten oder -Verzeichnis (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology) und ist dasselbe Konto, das für die Gatewayinstallation verwendet wurde. <p><p>Weitere Informationen finden Sie im Abschnitt [Häufig gestellte Fragen](#faq). |
   |||

   Im folgenden Beispiel wird eine Gatewayinstallation gezeigt, die sich in derselben Region wie die Gatewayressource befindet und mit demselben Azure-Konto verknüpft ist:

   ![Angeben von Details zum Erstellen der Datengatewayressource](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Herstellen einer Verbindung mit lokalen Daten

Nachdem Sie die Gatewayressource erstellt und Ihr Azure-Abonnement mit dieser Ressource verknüpft haben, können Sie jetzt mit dem Gateway eine Verbindung zwischen Ihrer Logik-App und Ihrer lokalen Datenquelle herstellen.

1. Erstellen oder öffnen Sie im Azure-Portal Ihre Logik-App im Logik-App-Designer.

1. Fügen Sie einen Connector hinzu, der lokale Verbindungen unterstützt, z.B. **SQL Server**.

1. Wählen Sie **Verbinden über lokales Datengateway**.

1. Wählen Sie unter **Gateway** in der Liste **Abonnement** Ihr Azure-Abonnement aus, das über die gewünschte Gatewayressource verfügt.

   Ihre Logik-App und Gatewayressource müssen nicht zum selben Azure-Abonnement gehören. Sie können unter anderen Azure-Abonnements auswählen, die jeweils über eine Gatewayressource verfügen, aber nur, wenn sich diese Abonnements im selben Azure AD-Mandanten oder Verzeichnis wie Ihre Logik-App befinden und Sie über Administratorberechtigungen für das Gateway verfügen, das ein anderer Administrator für Sie einrichten kann. Weitere Informationen finden Sie unter [Datengateway: Automatisierung mithilfe von PowerShell – Teil 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) und [PowerShell: Datengateway – Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser).
  
1. Wählen Sie in der Liste **Verbindungsgateway**, in der die verfügbaren Gatewayressourcen in Ihrem ausgewählten Abonnement angezeigt werden, die gewünschte Gatewayressource aus. Jede Gatewayressource ist mit einer einzelnen Gatewayinstallation verknüpft.

   > [!NOTE]
   > Die Liste der Gateways enthält Gatewayressourcen in anderen Regionen, da sich der Standort Ihrer Logik-App vom Standort Ihrer Gatewayressource unterscheiden kann. 

1. Geben Sie einen eindeutigen Verbindungsnamen und andere erforderliche Informationen an, die von der zu erstellenden Verbindung abhängig sind.

   Ein eindeutiger Verbindungsname vereinfacht später das Identifizieren dieser Verbindung. Dies gilt insbesondere dann, wenn Sie mehrere Verbindungen erstellen. Sofern erforderlich, geben Sie auch die qualifizierte Domäne für Ihren Benutzernamen an.

   Beispiel:

   ![Erstellen der Verbindung zwischen der Logik-App und dem Datengateway](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Wählen Sie **Erstellen**, wenn Sie fertig sind.

Die Gatewayverbindung kann nun für Ihre Logik-App verwendet werden.

## <a name="edit-connection"></a>Bearbeiten der Verbindung

Zum Aktualisieren der Einstellungen für eine Gatewayverbindung können Sie die Verbindung bearbeiten.

1. Um alle API-Verbindungen nur für Ihre Logik-App zu suchen, wählen Sie im Menü Ihrer Logik-App unter **Entwicklungstools** die Option **API-Verbindungen** aus.

   ![Wählen Sie im Menü Ihrer Logik-App „API-Verbindungen“ aus.](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Wählen Sie die gewünschte Gatewayverbindung und dann **API-Verbindung bearbeiten** aus.

   > [!TIP]
   > Sollten Ihre Aktualisierungen nicht wirksam werden, [beenden Sie das Gateway-Windows-Dienstkonto für Ihre Gatewayinstallation](../logic-apps/logic-apps-gateway-install.md#restart-gateway), und starten Sie es dann neu.

So suchen Sie alle API-Verbindungen, die mit Ihrem Azure-Abonnement verknüpft sind:

* Wählen Sie im Menü des Azure-Portals **Alle Dienste** > **Web** > **API-Verbindungen** aus.
* Sie können im Menü des Azure-Portals auch **Alle Ressourcen** auswählen. Legen Sie Filter **Typ** auf **API-Verbindung** fest.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Löschen der Gatewayressource

Um eine andere Gatewayressource zu erstellen, Ihre Gatewayinstallation mit einer anderen Gatewayressource zu verknüpfen oder die Gatewayressource zu entfernen, können Sie die Gatewayressource löschen, ohne dass sich dies auf die Gatewayinstallation auswirkt.

1. Wählen Sie im Menü des Azure-Portals die Option **Alle Ressourcen** aus, oder suchen Sie **Alle Ressourcen** auf einer beliebigen Seite, und wählen Sie die Option aus. Suchen Sie Ihre Gatewayressource, und wählen Sie sie aus.

1. Wenn nicht bereits ausgewählt, wählen Sie in Ihrem Gatewayressourcenmenü **Lokales Datengateway** aus. Wählen Sie auf der Gatewayressourcen-Symbolleiste die Option **Löschen** aus.

   Beispiel:

   ![Löschen der Gatewayressource in Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**F:** Warum wird meine Gatewayinstallation nicht angezeigt, wenn ich die Gatewayressource in Azure erstelle? <br/>
**A:** Dieses Problem kann folgende Ursachen haben:

* Bei Ihrem Azure-Konto handelt es sich nicht um dasselbe Konto, das für die Gatewayinstallation auf Ihrem lokalen Computer verwendet wurde. Überprüfen Sie, ob Sie mit derselben Identität beim Azure-Portal angemeldet sind, die für die Gatewayinstallation verwendet wurde. Nur der Gatewayadministrator kann die Gatewayressource in Azure erstellen. Dienstprinzipale werden aktuell nicht unterstützt.

* Ihr Azure-Konto gehört nicht zu einem einzigen [Azure AD-Mandanten oder -Verzeichnis](../active-directory/fundamentals/active-directory-whatis.md#terminology). Überprüfen Sie, ob Sie denselben Azure AD-Mandanten oder dasselbe Azure AD-Verzeichnis verwenden, den bzw. das Sie während der Gatewayinstallation verwendet haben.

* Ihre Gatewayressource und die Gatewayinstallation befinden sich nicht in derselben Region. Der Standort Ihrer Logik-App kann jedoch vom Standort Ihrer Gatewayressource abweichen.

* Ihre Gatewayinstallation ist bereits einer anderen Gatewayressource zugeordnet. Jede Gatewayressource kann nur mit einer Gatewayinstallation verknüpft sein, die wiederum nur mit einem Azure-Konto und einem Abonnement verknüpft ist. Gatewayinstallationen, die bereits anderen Gatewayressourcen zugeordnet sind, können also nicht ausgewählt werden. Diese Installationen werden nicht in der Liste **Installationsname** angezeigt.

  Sehen Sie sich zum Überprüfen der Gatewayregistrierungen im Azure-Portal alle Azure-Ressourcen mit dem Ressourcentyp **Lokale Datengateways** für *alle* Ihre Azure-Abonnements an. Informationen zum Aufheben der Verknüpfung der Gatewayinstallation mit der anderen Gatewayressource finden Sie unter [Löschen der Gatewayressource](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Nächste Schritte

* [Sichern Ihrer Logik-Apps](./logic-apps-securing-a-logic-app.md)
* [Allgemeine Beispiele und Szenarien für Logik-Apps](./logic-apps-examples-and-scenarios.md)

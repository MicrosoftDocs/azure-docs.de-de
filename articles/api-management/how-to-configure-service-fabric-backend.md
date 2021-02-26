---
title: Einrichten eines Service Fabric-Back-Ends in Azure API Management | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie über das Azure-Portal ein Service Fabric-Back-End in Azure API Management erstellen.
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: f6474dbd02c501612b951ddae490385a5d843fbf
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99499430"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Einrichten eines Service Fabric-Back-Ends in API Management über das Azure-Portal

In diesem Artikel wird beschrieben, wie Sie im Azure-Portal einen [Service Fabric](../service-fabric/service-fabric-api-management-overview.md)-Dienst als benutzerdefiniertes API-Back-End konfigurieren. Zu Demonstrationszwecken wird gezeigt, wie Sie einen einfachen zustandslosen zuverlässigen ASP.NET Core-Dienst als Service Fabric-Back-End einrichten.

Hintergrundinformationen finden Sie unter [Back-Ends in API Management](backends.md).

## <a name="prerequisites"></a>Voraussetzungen

Für die Konfiguration eines Beispieldiensts in einem Service Fabric-Cluster unter Windows als benutzerdefiniertes Back-End gelten folgende Voraussetzungen:

* **Windows-Entwicklungsumgebung:** Installieren Sie [Visual Studio 2019](https://www.visualstudio.com) und die Workloads **Azure-Entwicklung**, **ASP.NET und Webentwicklung** und **Plattformübergreifende .NET Core-Entwicklung**. Richten Sie dann eine [.NET-Entwicklungsumgebung](../service-fabric/service-fabric-get-started.md) ein.

* **Service Fabric-Cluster:** Siehe [Tutorial: Bereitstellen eines Service Fabric-Clusters mit Windows in einem virtuellen Azure-Netzwerk](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md). Sie können einen Cluster mit einem vorhandenen X.509-Zertifikat erstellen oder zu Testzwecken ein neues selbstsigniertes Zertifikat erstellen. Der Cluster wird in einem virtuellen Netzwerk erstellt.

* **Service Fabric-Beispiel-App:** Erstellen Sie eine Web-API-App, und stellen Sie sie wie unter [Integrieren von API Management in Service Fabric in Azure](../service-fabric/service-fabric-tutorial-deploy-api-management.md) beschrieben im Service Fabric-Cluster bereit.

    Mit diesen Schritten wird mithilfe der Standardvorlage für Web-API-Projekte ein einfacher zustandsloser zuverlässiger ASP.NET Core-Dienst erstellt. Später machen Sie den HTTP-Endpunkt für diesen Dienst über Azure API Management verfügbar.

    Notieren Sie den Anwendungsnamen, z. B. `fabric:/myApplication/myService`. 

* **API Management-Instanz:** eine vorhandene oder neue API Management-Instanz im **Premium**- oder **Developer**-Tarif und in der gleichen Region wie der Service Fabric-Cluster. Wenn dies erforderlich ist, [erstellen Sie eine API Management-Instanz](get-started-create-service-instance.md).

* **Virtuelles Netzwerk:** Fügen Sie die API Management-Instanz in dem virtuellen Netzwerk hinzu, das Sie für den Service Fabric-Cluster erstellt haben. Für API Management ist ein dediziertes Subnetz im virtuellen Netzwerk erforderlich.

  Die Schritte zum Aktivieren der Verbindung mit einem virtuellen Netzwerk für die API Management-Instanz finden Sie unter [Verwenden von Azure API Management mit virtuellen Netzwerken](api-management-using-with-vnet.md).

## <a name="create-backend---portal"></a>Erstellen eines Back-Ends über das Portal

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>Hinzufügen des Service Fabric-Clusterzertifikats in API Management

Das Service Fabric-Clusterzertifikat wird in einem Azure-Schlüsseltresor gespeichert und verwaltet, der dem Cluster zugeordnet ist. Fügen Sie dieses Zertifikat Ihrer API Management-Instanz als Clientzertifikat hinzu.

Die Schritte zum Hinzufügen eines Zertifikats zu einer API Management-Instanz finden Sie unter [Sichern von Back-End-Diensten über eine Clientzertifikatauthentifizierung in Azure API Management](api-management-howto-mutual-certificates.md). 

> [!NOTE]   
> Es wird empfohlen, das Zertifikat durch Verweisen auf das Schlüsseltresorzertifikat in API Management hinzuzufügen. 

### <a name="add-service-fabric-backend"></a>Hinzufügen eines Service Fabric-Back-Ends

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer API Management-Instanz.
1. Wählen Sie unter **APIs** die Optionen **Back-Ends** >  **+ Hinzufügen** aus.
1. Geben Sie einen Namen für das Back-End und optional eine Beschreibung ein.
1. Wählen Sie unter **Typ** den Typ **Service Fabric** aus.
1. Geben Sie unter **Laufzeit-URL** den Namen des Service Fabric-Back-End-Diensts ein, an den API Management Anforderungen weiterleiten soll. Beispiel: `fabric:/myApplication/myService`. 
1. Geben Sie unter **Maximum number of partition resolution retries** (Maximale Anzahl der Wiederholungsversuche für Partitionsauflösung) eine Zahl zwischen 0 und 10 ein.
1. Geben Sie den Verwaltungsendpunkt des Service Fabric-Clusters ein. Dieser Endpunkt ist die URL des Clusters am Port `19080`, z. B. `https://mysfcluster.eastus.cloudapp.azure.com:19080`.
1. Wählen Sie unter **Clientzertifikat** das Service Fabric-Clusterzertifikat aus, das Sie der API Management-Instanz im vorherigen Abschnitt hinzugefügt haben.
1. Geben Sie unter **Management endpoint authorization method** (Autorisierungsmethode für Verwaltungsendpunkt) einen Fingerabdruck oder Servernamen eines X509-Zertifikats ein, das im Verwaltungsdienst des Service Fabric-Clusters für die TLS-Kommunikation verwendet wird.
1. Aktivieren Sie die Einstellungen **Validate certificate chain** (Zertifikatkette überprüfen) und **Validate certificate name** (Zertifikatname überprüfen).
1. Geben Sie unter **Authorization credentials** (Anmeldeinformationen für die Autorisierung) gegebenenfalls die Anmeldeinformationen zur Verbindung mit dem konfigurierten Back-End-Dienst in Service Fabric an. Für die hier verwendete Beispiel-App sind keine Anmeldeinformationen für die Autorisierung erforderlich.
1. Wählen Sie **Erstellen** aus.

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Erstellen eines Service Fabric-Back-Ends":::

## <a name="use-the-backend"></a>Verwenden des Back-Ends

Zur Verwendung eines benutzerdefinierten Back-Ends verweisen Sie mit der Richtlinie [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) darauf. Mit dieser Richtlinie wird die Basis-URL des Standard-Back-Ends einer eingehenden API-Anforderung in ein angegebenes Back-End transformiert, in diesem Fall in das Service Fabric-Back-End. 

Die Richtlinie `set-backend-service` kann bei einer vorhandenen API nützlich sein, um eine eingehende Anforderung in ein anderes Back-End zu transformieren als in den API-Einstellungen angegeben ist. Zu Demonstrationszwecken erstellen Sie in diesem Artikel eine Test-API und legen die Richtlinie so fest, dass API-Anforderungen an das Service Fabric-Back-End weitergeleitet werden. 

### <a name="create-api"></a>API erstellen

Führen Sie die Schritte unter [Manuelles Hinzufügen einer API](add-api-manually.md) aus, um eine leere API zu erstellen.

* Lassen Sie in den API-Einstellungen das Feld **Webdienst-URL** leer.
* Fügen Sie ein **API-URL-Suffix** ein, z. B. *fabric*.

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="Erstellen einer leeren API":::

### <a name="add-get-operation-to-the-api"></a>Hinzufügen eines GET-Vorgangs zur API

Wie unter [Bereitstellen eines Service Fabric-Back-End-Diensts](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service) gezeigt, unterstützt der ASP.NET Core-Beispieldienst, der im Service Fabric-Cluster bereitgestellt wird, einen einzelnen HTTP GET-Vorgang für den URL-Pfad`/api/values`.

Die Standardantwort für diesen Pfad ist ein JSON-Array von zwei Zeichenfolgen:

```json
["value1", "value2"]
```

Um die Integration von API Management in den Cluster zu testen, fügen Sie der API den entsprechenden GET-Vorgang für den Pfad `/api/values` hinzu:

1. Wählen Sie die API aus, die Sie im vorherigen Schritt erstellt haben.
1. Klicken Sie auf **+ Vorgang hinzufügen**.
1. Geben Sie im Fenster **Front-End** die folgenden Werte ein, und wählen Sie **Speichern** aus.

     | Einstellung             | Wert                             | 
    |---------------------|-----------------------------------|
    | **Anzeigename**    | *Test backend*                       |  
    | **URL** | GET                               | 
    | **URL**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="Hinzufügen des GET-Vorgangs zur API":::

### <a name="configure-set-backend-policy"></a>Konfigurieren der Richtlinie `set-backend`

Fügen Sie der Test-API die Richtlinie [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) hinzu.

1. Wählen Sie auf der Registerkarte **Design** im Bereich **Eingehende Verarbeitung** das Symbol für den Code-Editor ( **</>** ) aus. 
1. Positionieren Sie den Cursor im **&lt;inbound&gt;** -Element.
1. Fügen Sie die folgende Richtlinienanweisung hinzu. Geben Sie in `backend-id` den Namen Ihres Service Fabric-Back-Ends ein.

   `sf-resolve-condition` ist eine Wiederholungsbedingung für den Fall, dass die Clusterpartition nicht aufgelöst wird. Die Anzahl der Wiederholungsversuche wurde beim Konfigurieren des Back-Ends festgelegt.

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. Wählen Sie **Speichern** aus.

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="Konfigurieren der Richtlinie „set-backend-service“":::

### <a name="test-backend-api"></a>Testen der Back-End-API

1. Wählen Sie auf der Registerkarte **Test** den **GET**-Vorgang aus, den Sie im vorherigen Abschnitt erstellt haben.
1. Klicken Sie auf **Senden**.

Wenn die HTTP-Antwort ordnungsgemäß konfiguriert ist, werden ein HTTP-Erfolgscode und der vom Back-End des Service Fabric-Diensts zurückgegebene JSON-Code angezeigt.

:::image type="content" source="media/backends/test-backend-service.png" alt-text="Testen des Service Fabric-Back-Ends":::

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Richtlinien konfigurieren](api-management-advanced-policies.md), um Anforderungen an ein Back-End weiterzuleiten.
* Back-Ends können auch über die [REST-API](/rest/api/apimanagement/2020-06-01-preview/backend), mit [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend) oder [Azure Resource Manager-Vorlagen](../service-fabric/service-fabric-tutorial-deploy-api-management.md) in API Management konfiguriert werden.


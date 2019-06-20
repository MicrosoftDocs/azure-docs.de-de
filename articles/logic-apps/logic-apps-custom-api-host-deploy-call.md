---
title: Bereitstellen und Aufrufen von Web- und REST-APIs über Azure Logic Apps | Microsoft-Dokumentation
description: Bereitstellen und Aufrufen von Web- und REST-APIs für Workflows für die Systemintegration in Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, stepsic, LADocs
ms.topic: article
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.date: 05/26/2017
ms.openlocfilehash: a9049ba1fbd7d3bdce061d277f6a7a02d9b1e4b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60740384"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Bereitstellen und Aufrufen benutzerdefinierter APIs über Workflows in Azure Logic Apps

Nachdem Sie den Vorgang [Benutzerdefinierte APIs erstellen](./logic-apps-create-api-app.md) für die Verwendung in Workflows der Logik-App durchgeführt haben, müssen Sie Ihre APIs bereitstellen, bevor Sie sie aufrufen können. Sie können Ihre APIs als [Web-Apps](../app-service/overview.md) bereitstellen, sollten jedoch in Erwägung ziehen, Ihre APIs als [API-Apps](../app-service/app-service-web-tutorial-rest-api.md) bereitzustellen. Dadurch wird das Erstellen, Hosten und Nutzen der APIs in der Cloud und lokal vereinfacht. Sie müssen keinen Code in Ihren APIs ändern. Stellen Sie einfach Ihren Code für eine API-App bereit. Sie können Ihre APIs in [Azure App Service](../app-service/overview.md) hosten, einem PaaS-Angebot (Platform-as-a-Service), das ein einfaches API-Hosting mit hoher Skalierbarkeit ermöglicht.

Und obwohl Sie alle APIs in einer Logik-App aufrufen können, sollten Sie für optimale Ergebnisse [OpenAPI-Metadaten (zuvor Swagger)](https://swagger.io/specification/) hinzufügen, die Ihre API-Vorgänge und -Parameter beschreiben. Mit dieser OpenAPI-Datei funktioniert Ihre API besser und lässt sich leichter in Logik-Apps integrieren.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Bereitstellen der API als Web-App oder API-App

Bevor Sie Ihre benutzerdefinierte API über eine Logik-App aufrufen können, müssen Sie die API im Azure App Service als Web-App oder API-App bereitstellen. Zudem müssen Sie die Eigenschaften der API-Definition festlegen und die [Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS)](../app-service/overview.md) für Ihre Web-App oder API-App einschalten, damit die OpenAPI-Datei für den Designer für Logik-Apps lesbar ist.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) Ihre Web-App oder API-App aus.

2. Wählen Sie auf dem geöffneten App-Menü unter **API** die Option **API-Definition** aus. Legen Sie den **Speicherort der API-Definition** auf die URL Ihrer OpenAPI-Datei „swagger.json“ fest.

   In der Regel wird die URL im folgenden Format dargestellt:`https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Verknüpfung mit der OpenAPI-Datei für Ihre benutzerdefinierte API](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Wählen Sie unter **API** den Eintrag **CORS** aus. Legen Sie die CORS-Richtlinie für **Zulässige Ursprünge** auf **'*'** (alle zulassen) fest.

   Diese Einstellung lässt Anforderungen vom Logik-App-Designer zu.

   ![Zulassen von Anforderungen des Logik-App-Designers an Ihre benutzerdefinierte API](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Weitere Informationen finden Sie unter [Hosten einer RESTful-API mit CORS in Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Aufrufen der benutzerdefinierten API über Workflows der Logik-App

Nachdem Sie die Eigenschaften der API-Definition und CORS eingerichtet haben, sollten die Auslöser und Aktionen Ihrer benutzerdefinierten API für Sie verfügbar sein, um in den Workflow Ihrer Logik-App aufgenommen zu werden. 

*  Durchsuchen Sie Ihre Abonnementwebsites im Logik-App-Designer, um Websites mit OpenAPI-URLs anzuzeigen.

*  Verwenden Sie die [Aktion „HTTP + Swagger“ ](../connectors/connectors-native-http-swagger.md), um verfügbare Aktionen und Eingaben durch Verweis auf ein OpenAPI-Dokument anzuzeigen.

*  Sie können über die [HTTP-Aktion](../connectors/connectors-native-http.md) jederzeit eine Anforderung erstellen, um alle APIs aufzurufen (einschließlich der APIs, die über kein OpenAPI-Dokument verfügen bzw. keines verfügbar machen).

## <a name="next-steps"></a>Nächste Schritte

* [Custom connector overview (Übersicht über benutzerdefinierte Connectors)](../logic-apps/custom-connector-overview.md)
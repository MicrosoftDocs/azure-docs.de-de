---
title: OpenAPI-Metadaten in Azure Functions
description: Übersicht über OpenAPI-Unterstützung in Azure Functions
ms.topic: conceptual
ms.date: 03/23/2017
ms.openlocfilehash: 499d4f685e3802fcc37c8a3050ae367207f192d2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000203"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>OpenAPI 2.0-Metadatenunterstützung in Azure Functions (Vorschauversion)
Metadatenunterstützung für OpenAPI 2.0 (ehemals Swagger) in Azure Functions ist eine Vorschaufunktion, mit der Sie eine OpenAPI 2.0-Definition in einer Funktions-App schreiben können. Diese Datei kann dann mit der Funktions-App gehostet werden.

> [!IMPORTANT]
> Das OpenAPI-Vorschaufeature ist aktuell nur in der 1.x-Runtime verfügbar. Informationen zum Erstellen einer 1.x-Funktions-App [finden Sie hier](./functions-versions.md#creating-1x-apps).

[OpenAPI-Metadaten](https://swagger.io/) ermöglichen, dass eine Funktion, die eine REST-API hostet, von vielen anderen Softwareprodukten verwendet werden kann. Diese Software enthält Microsoft-Angebote wie PowerApps und die [API-Apps-Funktion von Azure App Service](../app-service/overview.md), Drittanbieter-Entwicklertools wie [Postman](https://www.getpostman.com/docs/importing_swagger) sowie [viele weitere Pakete](https://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Es empfiehlt sich, mit dem [Erste-Schritte-Tutorial](./functions-openapi-definition.md) zu beginnen und dann zu diesem Dokument zurückzukehren, um das Wissen über bestimmte Features zu vertiefen.

## <a name="enable-openapi-definition-support"></a><a name="enable"></a>Aktivieren der Unterstützung für eine OpenAPI-Definition
Sie können alle OpenAPI-Einstellungen auf der Seite **API-Definition** in den **Plattformfunktionen** der Funktions-App konfigurieren.

> [!NOTE]
> Das Funktionen-API-Definitionsfeature wird für die Betaruntime zurzeit nicht unterstützt.

Um das Generieren einer gehosteten OpenAPI-Definition und einer Schnellstartdefinition zu ermöglichen, legen Sie **API-Definitionsquelle** auf **Funktion (Vorschau)** fest. **Externe URL** ermöglicht, dass Sie in Ihrer Funktion eine OpenAPI-Definition verwenden, die andernorts gehostet wird.

## <a name="generate-a-swagger-skeleton-from-your-functions-metadata"></a><a name="generate-definition"></a>Generieren eines Swagger-Gerüsts aus den Metadaten Ihrer Funktion
Eine Vorlage kann beim Schreiben Ihrer ersten OpenAPI-Definition hilfreich sein. Das Feature für Definitionsvorlagen erstellt eine spärlich konfigurierte OpenAPI-Definition anhand aller Metadaten in der Datei „function.json“ für jede Ihrer HTTP-Triggerfunktionen. Sie müssen weitere Informationen zu Ihrer API aus der [OpenAPI-Spezifikation](https://swagger.io/specification/), eintragen, z.B. Vorlagen für Anforderungen (request) und Antworten (response).

Schrittweise Anweisungen finden Sie im [Erste-Schritte-Tutorial](./functions-openapi-definition.md).

### <a name="available-templates"></a><a name="templates"></a>Verfügbare Vorlagen

|Name| BESCHREIBUNG |
|:-----|:-----|
|Generated Definition|Eine OpenAPI-Definition mit sämtlichen Informationen, die aus den vorhandenen Metadaten den Funktion abgeleitet werden können.|

### <a name="included-metadata-in-the-generated-definition"></a><a name="quickstart-details"></a>Enthaltene Metadaten in der generierten Definition

In der folgenden Tabelle sind die Azure-Portaleinstellungen und die entsprechenden Daten aus „function.json“ aufgeführt, wie sie dem generierten Swagger-Gerüst zugeordnet sind.

|Swagger.json|Portal-Benutzeroberfläche|Function.json|
|:----|:-----|:-----|
|[Host](https://swagger.io/specification/#fixed-fields-15)|**Funktionen-App-Einstellungen** > **App Service-Einstellungen** > **Übersicht** > **URL**|*Nicht vorhanden*
|[Paths](https://swagger.io/specification/#paths-object-29)|**Integrieren** > **Ausgewählte HTTP-Methoden**|Bindungen: Route
|[Path Item](https://swagger.io/specification/#path-item-object-32)|**Integrieren** > **Routenvorlage**|Bindungen: Methoden
|[Sicherheit](https://swagger.io/specification/#security-scheme-object-112)|**Schlüssel**|*Nicht vorhanden*|
|operationID*|**„Route“ und „Zulässige Verben“**|„Route“ und „Allowed Verbs“|

\*Die Vorgangs-ID ist nur für das Integrieren in PowerApps und Flow erforderlich.
> [!NOTE]
> Die Erweiterung „x-ms-summary“ stellt einen Anzeigenamen in Logic Apps, PowerApps und Flow bereit.
>
> Weitere Informationen finden Sie unter [Anpassen der Swagger-Definition für PowerApps](/connectors/custom-connectors/openapi-extensions).

## <a name="use-cicd-to-set-an-api-definition"></a><a name="CICD"></a>Verwenden von CI/CD, um eine API-Definition festzulegen

 Sie müssen das Hosten von API-Definitionen im Portal aktivieren, bevor Sie es ermöglichen, Ihre API-Definition aus der Quellcodeverwaltung ändern zu können. Befolgen Sie diese Anweisungen:

1. Navigieren Sie zu **API-Definition (Vorschau)** in den Einstellungen der Funktions-App.
   1. Legen Sie **API-Definitionsquelle** auf **Funktion** fest.
   1. Klicken Sie auf **API-Definitionsvorlage generieren** und anschließend auf **Speichern**, um eine Vorlagendefinition zu erstellen, die Sie später ändern können.
   1. Notieren Sie sich die URL und den Schlüssel der API-Definition.
1. [Einrichten von Continuous Integration/Continuous Deployment (CI/CD)](./functions-continuous-deployment.md#requirements-for-continuous-deployment).
2. Ändern Sie „swagger.json“ in der Quellcodeverwaltung unter „\site\wwwroot\.azurefunctions\swagger\swagger.json“.

Nun werden Änderungen an „swagger.json“ in Ihrem Repository von Ihrer Funktions-App unter der URL und mit dem Schlüssel der API-Definition gehostet, die Sie sich in Schritt 1.c notiert haben.

## <a name="next-steps"></a>Nächste Schritte
* [Erste-Schritte-Tutorial](./functions-openapi-definition.md). Probieren Sie unsere exemplarische Vorgehensweise aus, um eine OpenAPI-Definition in Aktion zu sehen!
* [Azure Functions-GitHub-Repository](https://github.com/Azure/Azure-Functions/). Testen Sie das Functions-Repository, und geben Sie uns Feedback zur Vorschauversion der Unterstützung von API-Definitionen! Erstellen Sie ein GitHub-Problem für jeden Punkt, für den Sie eine Aktualisierung wünschen.
* [Entwicklerreferenz zu Azure Functions](functions-reference.md). Informieren Sie sich über das Programmieren von Funktionen und das Festlegen von Triggern und Bindungen.

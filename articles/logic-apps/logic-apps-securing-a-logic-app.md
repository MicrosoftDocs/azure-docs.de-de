---
title: Sicherer Zugriff auf Azure Logic Apps | Microsoft-Dokumentation
description: Hinzufügen von Sicherheit für Azure Logic Apps, einschließlich Triggern, Eingaben und Ausgaben, Parametern und anderer Dienste
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.topic: article
ms.date: 02/05/2019
ms.openlocfilehash: 31b18e2dfbad1f572b5c9b0ae59f5e076db7cde2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60995678"
---
# <a name="secure-access-in-azure-logic-apps"></a>Schützen des Zugriffs in Azure Logic Apps

Für diese Elemente in Ihrer Logik-App können Sie den Zugriff schützen:

* [Anforderung oder Webhooktrigger](#secure-triggers)
* [Vorgänge wie das Verwalten, Bearbeiten oder Anzeigen](#secure-operations) Ihrer Logik-App
* [Eingaben und Ausgaben](#secure-run-history) aus dem Ausführungsverlauf Ihrer Logik-App
* [Aktionsparameter und Eingaben](#secure-action-parameters)
* [Dienste, die Anforderungen](#secure-requests) von Ihrer Logik-App erhalten

<a name="secure-triggers"></a>

## <a name="secure-access-to-request-triggers"></a>Sichern des Zugriffs zum Anfordern von Triggern

Wenn Ihre Logik-App einen auf HTTP-Anforderungen basierenden Trigger verwendet, z.B. einen der Trigger [Anforderung](../connectors/connectors-native-reqres.md) oder [Webhook](../connectors/connectors-native-webhook.md), können Sie den Zugriff einschränken, sodass nur autorisierte Clients die Logik-App starten können. Alle von einer Logik-App empfangenen Anforderungen werden mit dem Secure Sockets Layer-Protokoll (SSL) verschlüsselt und gesichert. Hier finden Sie verschiedene Möglichkeiten, wie Sie den Zugriff auf diesen Triggertyp absichern können:

* [Generieren von Shared Access Signatures](#sas)
* [Beschränken eingehender IP-Adressen](#restrict-incoming-ip-addresses)
* [Hinzufügen von Azure Active Directory, OAuth oder anderen Sicherheitsfeatures](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures"></a>Generieren von Shared Access Signatures

Jeder Anforderungsendpunkt einer Logik-App enthält in der URL des Endpunkts eine [Shared Access Signature (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md). Jede URL enthält die Abfrageparameter `sp`, `sv` und `sig`.

* `sp` gibt Berechtigungen an, die den zulässigen HTTP-Methoden zugeordnet sind.
* `sv` gibt die Version zum Generieren der Signatur an.
* `sig` dient zum Authentifizieren des Zugriffs auf den Trigger.

Die Signatur wird mit dem SHA256-Algorithmus mit einem geheimen Zugriffsschlüssel für alle URL-Pfade und -Eigenschaften generiert. Der geheime Schlüssel wird nie verfügbar gemacht oder veröffentlicht. Er bleibt verschlüsselt und wird mit der Logik-App gespeichert. Die Logik-App autorisiert nur Trigger, die eine gültige, mit dem geheimen Schlüssel erstellte Signatur enthalten. 

Hier finden Sie weitere Informationen zum Absichern des Zugriffs mit einer Shared Access Signature:

* [Erneutes Generieren von Zugriffsschlüsseln](#access-keys)
* [Erstellen von ablaufenden Rückruf-URLs](#expiring-urls)
* [Erstellen von URLs mit Primär- oder Sekundärschlüssel](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Erneutes Generieren von Zugriffsschlüsseln

Verwenden Sie die Azure-REST-API oder das Azure-Portal, um jederzeit einen neuen sicheren Schlüssel zu generieren. Alle zuvor generierten URLs, die den alten Schlüssel verwenden, werden ungültig und sind nicht mehr berechtigt, die Logik-App auszulösen. Die URLs, die Sie nach der erneuten Generierung abrufen, werden mithilfe des neuen Zugriffsschlüssels signiert.

1. Öffnen Sie im Azure-Portal die Logik-App mit dem Schlüssel, den Sie erneut generieren möchten.

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Zugriffsschlüssel** aus.

1. Wählen Sie den Schlüssel aus, den Sie erneut generieren möchten, und schließen Sie den Vorgang ab.

<a name="expiring-urls"></a>

#### <a name="create-callback-urls-with-expiration-dates"></a>Erstellen von Rückruf-URLs mit einem Ablaufdatum

Wenn Sie die URL eines anforderungsbasierten Triggerendpunkts für andere Parteien freigeben, können Sie nach Bedarf Rückruf-URLs mit bestimmten Schlüsseln und Ablaufdaten generieren. Sie können dann nahtlos Schlüssel bereitstellen oder den Zugriff für das Auslösen Ihrer Logik-App auf einen bestimmten Zeitraum einschränken. Über die [Logic Apps-REST-API](https://docs.microsoft.com/rest/api/logic/workflowtriggers) können Sie ein Ablaufdatum für eine URL angeben, z.B.:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

Schließen Sie mithilfe einer JSON-Datumszeichenfolge die `NotAfter`-Eigenschaft in den Textkörper ein. Diese Eigenschaft gibt eine Rückruf-URL zurück, die nur bis zum Datum und der Uhrzeit in `NotAfter` gültig ist.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Erstellen von URLs mit einem primären oder sekundären geheimen Schlüssel

Beim Generieren oder Auflisten von Rückruf-URLs für anforderungsbasierte Trigger können Sie auch den Schlüssel zum Signieren der URL angeben. Sie können mit der [Logic Apps-REST-API](https://docs.microsoft.com/rest/api/logic/workflowtriggers) eine mit einem bestimmten Schlüssel signierte URL generieren, z.B.:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

Schließen Sie in den Textkörper die `KeyType`-Eigenschaft als `Primary` oder als `Secondary` ein. Diese Eigenschaft gibt eine URL zurück, die mit dem angegebenen sicheren Schlüssel signiert ist.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>Beschränken eingehender IP-Adressen

Im Zusammenhang mit der Shared Access Signature empfiehlt es sich, die Clients, die Ihre Logik-App aufrufen können, einzuschränken.  
Beispiel: Wenn Sie den Anforderungsendpunkt mit Azure API Management verwalten, können Sie für die Logik-App festlegen, dass sie Anforderungen nur von der IP-Adresse der API Management-Instanz annimmt. 

#### <a name="set-ip-ranges---azure-portal"></a>Festlegen von IP-Adressbereichen – Azure-Portal

Um diese Einschränkung im Azure-Portal einzurichten, wechseln Sie zu den Einstellungen Ihrer Logik-App: 

1. Öffnen Sie die Logik-App im Azure-Portal im Logik-App-Designer. 

1. Wählen Sie im Menü Ihrer Logik-App unter **Einstellungen** die Option **Workfloweinstellungen** aus.

1. Wählen Sie unter **Konfiguration der Zugriffssteuerung** > 
**Zulässige eingehende IP-Adressen** die Option **Spezifische IP-Bereiche** aus.

1. Geben Sie unter **IP-Bereiche für Trigger** die IP-Adressbereiche an, die der Trigger akzeptiert. Gültige IP-Adressbereiche verwenden die Formate *x.x.x.x/x* oder *x.x.x.x-x.x.x.x*. 

Wenn Sie Ihre Logik-App nur als geschachtelte Logik-App auslösen möchten, wählen Sie in der Liste **Zulässige eingehende IP-Adressen** die Option **Nur andere Logik-Apps** aus. Diese Option schreibt ein leeres Array in Ihre Logik-App-Ressource, damit nur Anrufe vom Logic Apps-Dienst (übergeordnete Logik-Apps) die geschachtelte Logik-App auslösen können.

> [!NOTE]
> Unabhängig von der IP-Adresse können Sie eine Logik-App, die einen anforderungsbasierten Trigger hat, mithilfe von `/triggers/{triggerName}/run` über die Azure-REST-API oder über API Management weiterhin ausführen. In diesem Szenario ist jedoch weiterhin eine Authentifizierung über die Azure-REST-API erforderlich, und alle Ereignisse werden im Azure-Überwachungsprotokoll angezeigt. Achten Sie darauf, die Richtlinien für die Zugriffssteuerung entsprechend festzulegen.

#### <a name="set-ip-ranges---logic-app-deployment-template"></a>Festlegen von IP-Adressbereichen – Bereitstellungsvorlage für Logik-Apps

Wenn Sie Logik-App-Bereitstellungen mit einer [Azure Resource Manager-Bereitstellungsvorlage](../logic-apps/logic-apps-create-deploy-template.md) automatisieren, können Sie die IP-Adressbereiche in dieser Vorlage festlegen, z.B.:

``` json
{
   "properties": {
      "definition": {},
      "parameters": {},
      "accessControl": {
         "triggers": {
            "allowedCallerIpAddresses": [
               {
                  "addressRange": "192.168.12.0/23"
               },
               {
                  "addressRange": "2001:0db8::/64"
               }
            ]
         }
      }
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Hinzufügen von Azure Active Directory, OAuth oder anderen Sicherheitsfeatures

Um Ihrer Logik-App weitere Autorisierungsprotokolle hinzuzufügen, können Sie [Azure API Management](https://azure.microsoft.com/services/api-management/) verwenden. Dieser Dienst bietet umfassende Überwachung, Sicherheit, Richtlinien und Dokumentation für alle Endpunkte. Er bietet Ihnen außerdem die Möglichkeit, Ihre Logik-App als API verfügbar zu machen. API Management kann einen öffentlichen oder privaten Endpunkt für die Logik-App verfügbar machen. Dadurch können Azure Active Directory, OAuth, Zertifikate oder andere Sicherheitsstandards genutzt werden. Wenn API Management eine Anforderung empfängt, sendet der Dienst die Anforderung an Ihre Logik-App und setzt dazu alle erforderlichen Transformationen oder Einschränkungen um. Damit nur API Management Ihre Logik-App auslösen kann, können Sie die Einstellungen für den eingehenden IP-Bereich Ihrer Logik-App anpassen. 

<a name="secure-operations"></a>

## <a name="secure-access-to-logic-app-operations"></a>Sichern des Zugriffs auf Logik-App-Vorgänge

Damit nur bestimmte Benutzer oder Gruppen Vorgänge mit Ihrer Logik-App ausführen können, beschränken Sie den Zugriff auf Vorgänge wie das Verwalten, Bearbeiten und Anzeigen. Logic Apps unterstützt [rollenbasierte Zugriffssteuerung (RBAC) von Azure](../role-based-access-control/role-assignments-portal.md), die Sie anpassen oder integrierten Rollen für Mitglieder in Ihrem Abonnement zuzuweisen können, z.B.:

* [Logik-App-Mitwirkender:](../role-based-access-control/built-in-roles.md#logic-app-contributor) Benutzer können Ihre Logik-App anzeigen, bearbeiten und aktualisieren. 

* [Logik-App-Operator:](../role-based-access-control/built-in-roles.md#logic-app-operator) Benutzer können Ihre Logik-App und den Ausführungsverlauf anzeigen und Ihre Logik-App aktivieren oder deaktivieren. Diese Rolle kann Ihre Logik-App nicht bearbeiten oder aktualisieren.

Um zu verhindern, dass andere Personen Ihre Logik-App ändern oder löschen, können Sie [Azure-Ressourcensperren](../azure-resource-manager/resource-group-lock-resources.md) verwenden. Diese Funktion hilft dabei, andere Personen am Ändern oder Löschen von Produktionsressourcen zu hindern.

<a name="secure-run-history"></a>

## <a name="secure-access-to-logic-app-run-history"></a>Sichern des Zugriffs auf den Verlauf von Logik-Apps

Um Inhalte zu schützen, die als Eingaben oder Ausgaben früherer Ausführungen weitergegeben wurden, können Sie den Zugriff auf bestimmte IP-Adressbereiche beschränken. Diese Funktion bietet Ihnen eine bessere Zugriffssteuerung. Alle Daten in der Ausführung einer Logik-App werden bei der Übertragung und im Ruhezustand verschlüsselt. Wenn Sie den Ausführungsverlauf einer Logik-App anfordern, authentifiziert Logic Apps die Anforderung und stellt Links zu den Eingaben und Ausgaben der Anforderungen sowie die Antworten im Workflow Ihrer Logik-App bereit. Sie können diese Links schützen, sodass dieser Inhalt nur bei Anforderungen von einer bestimmten IP-Adresse zurückgegeben wird. Beispielsweise können Sie auch eine IP-Adresse wie `0.0.0.0-0.0.0.0` angeben, damit niemand auf die Eingaben und Ausgaben zugreifen kann. Nur Personen mit Administratorberechtigungen können diese Einschränkung entfernen und erhalten damit die Möglichkeit für Just-In-Time-Zugriff auf Workflowinhalte.

### <a name="set-ip-ranges---azure-portal"></a>Festlegen von IP-Adressbereichen – Azure-Portal

Um diese Einschränkung im Azure-Portal einzurichten, wechseln Sie zu den Einstellungen Ihrer Logik-App:

1. Öffnen Sie die Logik-App im Azure-Portal im Logik-App-Designer. 

1. Wählen Sie im Menü Ihrer Logik-App unter **Einstellungen** die Option **Workfloweinstellungen** aus.

1. Wählen Sie unter **Konfiguration der Zugriffssteuerung** > 
   **Zulässige eingehende IP-Adressen** die Option **Spezifische IP-Bereiche** aus.

1. Geben Sie unter **IP-Bereiche für Inhalte** die IP-Adressbereiche an, die auf Inhalte von Eingaben und Ausgaben zugreifen können. 
   Gültige IP-Adressbereiche verwenden die Formate *x.x.x.x/x* oder *x.x.x.x-x.x.x.x*. 

### <a name="set-ip-ranges---logic-app-deployment-template"></a>Festlegen von IP-Adressbereichen – Bereitstellungsvorlage für Logik-Apps

Wenn Sie Logik-App-Bereitstellungen mit einer [Azure Resource Manager-Bereitstellungsvorlage](../logic-apps/logic-apps-create-deploy-template.md) automatisieren, können Sie die IP-Adressbereiche in dieser Vorlage festlegen, z.B.:

``` json
{
   "properties": {
      "definition": {},
      "parameters": {},
      "accessControl": {
         "contents": {
            "allowedCallerIpAddresses": [
               {
                  "addressRange": "192.168.12.0/23"
               },
               {
                  "addressRange": "2001:0db8::/64"
               }
            ]
         }
      }
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="secure-action-parameters"></a>

## <a name="secure-action-parameters-and-inputs"></a>Sichere Aktionsparameter und Eingaben

Wenn Sie die Bereitstellung in unterschiedlichen Umgebungen durchführen, empfiehlt es sich, bestimmte Elemente in der Workflowdefinition Ihrer Logik-App zu parametrisieren. Auf diese Weise können Sie Eingaben basierend auf den verwendeten Umgebungen bereitstellen und vertrauliche Informationen schützen. Wenn Sie z.B. HTTP-Aktionen mit [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication) authentifizieren, definieren und sichern Sie die Parameter, die die Client-ID und das Clientgeheimnis für die Authentifizierung verwenden. Für diese Parameter enthält die Definition der Logik-App einen eigenen `parameters`-Abschnitt.
Um zur Laufzeit auf Parameterwerte zuzugreifen, können Sie den Ausdruck `@parameters('parameterName')` verwenden, der durch die [Definitionssprache für Workflows](https://aka.ms/logicappsdocs) bereitgestellt wird. 

Um Parameter und Werte zu schützen, die beim Bearbeiten der Logik-App oder Anzeigen des Ausführungsverlaufs nicht angezeigt werden sollen, können Sie Parameter des Typs `securestring` definieren und bei Bedarf codieren. Parameter dieses Typs werden nicht mit der Ressourcendefinition zurückgegeben und sind beim Anzeigen der Ressource nach der Bereitstellung nicht zugänglich.

> [!NOTE]
> Wenn Sie einen Parameter in einem Anforderungsheader oder -text verwenden, kann dieser Parameter beim Zugreifen auf den Ausführungsverlauf der Logik-App und die ausgehende HTTP-Anforderung sichtbar sein. Achten Sie auch darauf, die Richtlinien für den Zugriff auf Inhalte entsprechend festzulegen.
> Autorisierungsheader sind nie über Eingaben oder Ausgaben sichtbar. Wenn hier ein Geheimnis verwendet wird, ist dieses daher nicht abrufbar.

Weitere Informationen zum Sichern von Parametern in Logik-App-Definitionen finden Sie weiter unten auf dieser Seite unter [Sichern von Parametern in Logik-App-Definitionen](#secure-parameters-workflow).

Wenn Sie Bereitstellungen mit [Azure Resource Manager-Bereitstellungsvorlagen](../azure-resource-manager/resource-group-authoring-templates.md#parameters) automatisieren, können Sie in diesen Vorlagen auch gesicherte Parameter verwenden. Beispielsweise können Sie Parameter zum Abrufen von KeyVault-Geheimnissen verwenden, wenn Sie die Logik-App erstellen. Die Definition der Bereitstellungsvorlage enthält einen eigenen `parameters`-Abschnitt, der vom `parameters`-Abschnitt der Logik-App getrennt ist. Weitere Informationen zum Sichern von Parametern in Bereitstellungsvorlagen finden Sie weiter unten auf dieser Seite unter [Sichern von Parametern in Bereitstellungsvorlagen](#secure-parameters-deployment-template).

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-logic-app-definitions"></a>Sichern von Parametern in Logik-App-Definitionen

Verwenden Sie zum Schützen vertraulicher Informationen in der Workflowdefinition der Logik-App gesicherte Parameter, sodass diese Informationen nach dem Speichern der Logik-App nicht sichtbar sind. Angenommen, Sie verwenden beispielsweise den Authentifizierungstyp `Basic` in der Definition einer HTTP-Aktion. Das folgende Beispiel enthält einen `parameters`-Abschnitt, in dem die Parameter für die Aktionsdefinition definiert werden, und einen `authentication`-Abschnitt, in dem Werte für die Parameter `username` und `password` akzeptiert werden. Um Werte für diese Parameter anzugeben, können Sie eine separate Parameterdatei verwenden, z.B.:

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('usernameParam')",
               "password": "@parameters('passwordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "passwordParam": {
         "type": "securestring"
      },
      "userNameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

Wenn Sie Geheimnisse verwenden, können Sie diese Geheimnisse bei der Bereitstellung mithilfe des [Azure Resource Manager-Schlüsseltresors](../azure-resource-manager/resource-manager-keyvault-parameter.md) abrufen.

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-deployment-templates"></a>Sichern von Parametern in Azure Resource Manager-Bereitstellungsvorlagen

Das folgende Beispiel zeigt eine Resource Manager-Bereitstellungsvorlage, in der mehrere Laufzeitparameter vom Typ `securestring` verwendet werden:

* `armTemplatePasswordParam` als Eingabe für den Parameter `logicAppWfParam` der Logik-App-Definition

* `logicAppWfParam` als Eingabe für die HTTP-Aktion mit Standardauthentifizierung

Das Beispiel enthält einen inneren `parameters`-Abschnitt, der zur Workflowdefinition der Logik-App gehört, und einen äußeren `parameters`-Abschnitt, der zu der Bereitstellungsvorlage gehört. Um die Umgebungswerte für Parameter anzugeben, können Sie eine separate Parameterdatei verwenden. 

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "logicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "armTemplatePasswordParam": {
         "type": "securestring"
      },
      "logicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('logicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('logicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('usernameParam')",
                           "password": "@parameters('logicAppWfParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "logicAppWfParam": {
                     "type": "securestring"
                  },
                  "userNameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "logicAppWfParam": {
                  "value": "[parameters('armTemplatePasswordParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

Wenn Sie Geheimnisse verwenden, können Sie diese Geheimnisse bei der Bereitstellung mithilfe des [Azure Resource Manager-Schlüsseltresors](../azure-resource-manager/resource-manager-keyvault-parameter.md) abrufen.

<a name="secure-requests"></a>

## <a name="secure-access-to-services-receiving-requests"></a>Sichern des Zugriffs auf Dienste, die Anforderungen empfangen

Hier finden Sie einige Möglichkeiten, einen beliebigen Endpunkt zu schützen, auf den Ihre Logik-App Zugriff benötigt und an den sie Anforderungen sendet.

### <a name="add-authentication-on-outbound-requests"></a>Hinzufügen von Authentifizierung für ausgehende Anforderungen

Bei der Arbeit mit einer Aktion vom Typ HTTP, HTTP + Swagger (offene API) oder Webhook können Sie der von Ihrer Logik-App gesendeten Anforderung eine Authentifizierung hinzufügen. Beispielsweise können Sie eine grundlegende Authentifizierung, eine Zertifikatauthentifizierung oder eine Azure Active Directory-Authentifizierung verwenden. Weitere Informationen finden Sie unter [Authentifizieren von Triggern oder Aktionen](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

### <a name="restrict-access-to-logic-app-ip-addresses"></a>Einschränken des Zugriffs auf IP-Adressen von Logik-Apps

Alle Aufrufe von Logik-Apps stammen basierend auf der Region von bestimmten IP-Adressen. Sie können Filter hinzufügen, die Anforderungen nur von diesen IP-Adressen akzeptieren. Weitere Informationen zu diesen IP-Adressen finden Sie unter [Grenzwerte und Konfiguration für Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

### <a name="secure-on-premises-connectivity"></a>Absichern der lokalen Konnektivität

Azure Logic Apps bietet eine Integration mit diesen Diensten, um eine sichere und zuverlässige lokale Kommunikation bereitzustellen.

#### <a name="on-premises-data-gateway"></a>Lokales Datengateway

Viele verwaltete Connectors für Azure Logic Apps bieten sichere Verbindungen mit lokalen Systemen wie dem Dateisystem, SQL, SharePoint, DB2 und anderen. Das Gateway sendet Daten von lokalen Quellen durch verschlüsselte Kanäle über Azure Service Bus. Der gesamte Datenverkehr stammt als sicherer ausgehender Datenverkehr vom Gateway-Agent. Erfahren Sie, [wie das lokale Datengateway funktioniert](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Azure API Management

[Azure API Management](https://azure.microsoft.com/services/api-management/) bietet lokale Konnektivitätsoptionen wie die Integration von Site-to-Site-VPN und ExpressRoute für geschützte Proxys und die Kommunikation mit lokalen Systemen. Im Logik-App-Designer können Sie innerhalb des Workflows Ihrer Logik-App eine API auswählen, die über API Management verfügbar gemacht wird, und so schnellen Zugriff auf lokale Systeme bereitstellen.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer Bereitstellungsvorlage](logic-apps-create-deploy-template.md)  
* [Fehlerbehandlung](logic-apps-exception-handling.md)  
* [Überwachen von Logik-Apps](logic-apps-monitor-your-logic-apps.md)  
* [Diagnostizieren von Fehlern und Problemen bei Logik-Apps](logic-apps-diagnosing-failures.md)  

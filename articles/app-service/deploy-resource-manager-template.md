---
title: Anleitung zur Bereitstellung von Apps mit Vorlagen – Azure App Service | Microsoft-Dokumentation
description: Empfehlungen zum Erstellen von Azure Resource Manager-Vorlagen zum Bereitstellen von Web-Apps
services: app-service
documentationcenter: app-service
author: tfitzmac
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: d8fa8b216ca6044adefe1398b58f5d14630540e0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66137163"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Anleitung zum Bereitstellen von Web-Apps mit Azure Resource Manager-Vorlagen

Dieser Artikel enthält Empfehlungen zum Erstellen von Azure Resource Manager-Vorlagen für die Bereitstellung von Azure App Service-Lösungen. Diese Empfehlungen helfen Ihnen dabei, häufige Probleme zu vermeiden.

## <a name="define-dependencies"></a>Definieren von Abhängigkeiten

Das Definieren von Abhängigkeiten für Web-Apps erfordert Kenntnisse in Bezug auf die Interaktion der Ressourcen innerhalb einer Web-App. Wenn Sie die Abhängigkeiten in der falschen Reihenfolge angeben, treten unter Umständen Bereitstellungsfehler auf, oder es entsteht eine Racebedingung, die die Bereitstellung stoppt.

> [!WARNING]
> Wenn Sie eine MSDeploy-Websiteerweiterung in die Vorlage einfügen, müssen Sie alle Konfigurationsressourcen als von der MSDeploy-Ressource abhängig festlegen. Änderungen an der Konfiguration führen zu einem asynchronen Neustart der Website. Indem Sie die Konfigurationsressourcen von MSDeploy abhängig machen, stellen Sie sicher, dass MSDeploy abgeschlossen wird, bevor die Website neu gestartet wird. Ohne diese Abhängigkeiten wird die Website eventuell während des Bereitstellungsprozesses von MSDeploy neu gestartet. Eine Beispielvorlage finden Sie unter [WordPress-Vorlage mit Web Deploy-Abhängigkeit](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

Die folgende Abbildung zeigt die Reihenfolge der Abhängigkeiten für verschiedene App Service-Ressourcen:

![Web-App-Abhängigkeiten](media/web-sites-rm-template-guidance/web-dependencies.png)

Sie stellen Ressourcen in der folgenden Reihenfolge bereit:

**Ebene 1**
* App Service-Plan.
* Alle anderen verwandten Ressourcen, z.B. Datenbanken oder Speicherkonten.

**Ebene 2**
* Web-App: Hängt vom App Service-Plan ab.
* Azure Application Insights-Instanz, die auf die Serverfarm ausgerichtet ist: Hängt vom App Service-Plan ab.

**Ebene 3**
* Quellcodeverwaltung: Hängt von der Web-App ab.
* MSDeploy-Websiteerweiterung: Hängt von der Web-App ab.
* Application Insights-Instanz, die auf die Serverfarm ausgerichtet ist: Hängt von der Web-App ab.

**Ebene 4**
* App Service Certificate: Hängt von der Quellcodeverwaltung oder MSDeploy ab, falls vorhanden. Hängt andernfalls von der Web-App ab.
* Konfigurationseinstellungen (Verbindungszeichenfolgen, Werte in „web.config“, App-Einstellungen): Hängen von der Quellcodeverwaltung oder von MSDeploy ab, falls vorhanden. Hängen andernfalls von der Web-App ab.

**Ebene 5**
* Hostnamenbindungen: Hängen vom Zertifikat ab, falls vorhanden. Hängen andernfalls von einer Ressource auf höherer Ebene ab.
* Websiteerweiterungen: Hängen von den Konfigurationseinstellungen ab, falls vorhanden. Hängen andernfalls von einer Ressource auf höherer Ebene ab.

In der Regel enthält Ihre Lösung nur einige dieser Ressourcen und Ebenen. Bei fehlenden Ebenen ordnen Sie die Ressourcen auf niedrigerer Ebene der nächsthöheren Ebene zu.

Das folgende Beispiel zeigt einen Teil einer Vorlage. Der Wert der Verbindungszeichenfolgen-Konfiguration hängt von der MSDeploy-Erweiterung ab. Die MS Deploy-Erweiterung hängt von der Web-App und der Datenbank ab. 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Ein lauffähiges Beispiel, das den oben stehenden Code verwendet, finden Sie unter [Vorlage: Erstellen einer einfachen Umbraco-Web-App](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>Suchen von Informationen zu MS Deploy-Fehlern

Wenn Ihre Resource Manager-Vorlage MS Deploy verwendet, sind die Bereitstellungsfehlermeldungen möglicherweise schwer verständlich. Probieren Sie die folgenden Schritte aus, um nach einer fehlerhaften Bereitstellung weitere Informationen zu erhalten:

1. Wechseln Sie zur [Kudu-Konsole](https://github.com/projectkudu/kudu/wiki/Kudu-console) der Website.
2. Navigieren Sie zum Ordner „D:\home\LogFiles\SiteExtensions\MSDeploy“.
3. Suchen Sie nach den Dateien „appManagerStatus.xml“ und „appManagerLog.xml“. Die erste Datei protokolliert den Status. Die zweite Datei protokolliert Informationen zum Fehler. Wenn Ihnen der Fehler nicht klar ist, können Sie die Informationen bei Nachfragen im Forum einfügen.

## <a name="choose-a-unique-web-app-name"></a>Auswählen eines eindeutigen Web-App-Namens

Der Name Ihrer Web-App muss global eindeutig sein. Sie können eine Namenskonvention verwenden, die höchstwahrscheinlich eindeutig ist, oder Sie können beim Erstellen eines eindeutigen Namens die [uniqueString-Funktion](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) verwenden.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Bereitstellen eines Web-App-Zertifikats aus Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wenn Ihre Vorlage eine [Microsoft.Web/certificates](/azure/templates/microsoft.web/certificates)-Ressource für die SSL-Bindung enthält und das Zertifikat in einer Key Vault-Instanz gespeichert ist, müssen Sie sicherstellen, dass die App Service-Identität auf das Zertifikat zugreifen kann.

In der globalen Azure-Umgebung hat der App Service-Dienstprinzipal die ID **abfa0a7c-a6b6-4736-8310-5855508787cd**. Verwenden Sie Folgendes, um für den App Service-Dienstprinzipal den Zugriff auf Key Vault zu gewähren:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

In Azure Government hat der App Service-Dienstprinzipal die ID **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Verwenden Sie diese ID im vorherigen Beispiel.

Wählen Sie in Ihrer Key Vault-Instanz die Optionen **Zertifikate** und **Generieren/importieren**, um das Zertifikat hochzuladen.

![Importieren des Zertifikats](media/web-sites-rm-template-guidance/import-certificate.png)

Geben Sie in Ihrer Vorlage den Namen des Zertifikats für `keyVaultSecretName` an.

Eine Beispielvorlage finden Sie unter [Deploy a Web App certificate from Key Vault secret and use it for creating SSL binding](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault) (Bereitstellen eines Web-App-Zertifikats über das Key Vault-Geheimnis und Verwenden zum Erstellen der SSL-Bindung).

## <a name="next-steps"></a>Nächste Schritte

* Ein Tutorial zum Bereitstellen von Web-Apps mit einer Vorlage finden Sie unter [Vorhersagbares Bereitstellen von Microservices in Azure](deploy-complex-application-predictably.md).
* Informationen zur JSON-Syntax und zu den Eigenschaften von Ressourcentypen in Vorlagen finden Sie in der [Azure Resource Manager-Vorlagenreferenz](/azure/templates/).

---
title: 'Schnellstart: Erstellen und Verwalten von Ressourcen in Azure Communication Services'
titleSuffix: An Azure Communication Services quickstart
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Ihre erste Azure Communication Services-Ressource erstellen und verwalten.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-azp-net
ms.openlocfilehash: a93ac3b5d988be33c0f27726a75b1006f990d1da
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886108"
---
# <a name="quickstart-create-and-manage-communication-services-resources"></a>Schnellstart: Erstellen und Verwalten einer Communication Services-Ressource

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Führen Sie erste Schritte mit Azure Communication Services aus, indem Sie Ihre erste Communication Services-Ressource bereitstellen. Communication Services-Ressourcen können über das Azure-Portal oder mit der .NET-Verwaltungsclientbibliothek bereitgestellt werden. Mithilfe der Verwaltungsclientbibliothek können Sie Ihre Ressource und Schnittstellen mit [Azure Resource Manager](../../azure-resource-manager/management/overview.md), dem Bereitstellungs- und Verwaltungsdienst von Azure, erstellen, konfigurieren, aktualisieren und löschen. Alle in den Clientbibliotheken verfügbaren Funktionen sind auch im Azure-Portal verfügbar. 

> [!WARNING]
> Beachten Sie, dass die Verfügbarkeit von Communication Services während der öffentlichen Vorschauphase auf die geografische Region „USA“ beschränkt ist. Beachten Sie zudem, dass während der öffentlichen Vorschauphase keine Kommunikationsressourcen in ein anderes Abonnement übertragen werden können.

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/create-resource-azp.md)]
::: zone-end

::: zone pivot="platform-net"
[!INCLUDE [.NET](./includes/create-resource-net.md)]
::: zone-end

## <a name="access-your-connection-strings-and-service-endpoints"></a>Zugreifen auf die Verbindungszeichenfolgen und Dienstendpunkte

Mit Verbindungszeichenfolgen können Communication Services-Clientbibliotheken eine Verbindung mit Azure herstellen und sich bei Azure authentifizieren. Sie können auf die Communication Services-Verbindungszeichenfolgen und -Dienstendpunkte über das Azure-Portal oder programmgesteuert mit Azure Resource Manager-APIs zugreifen. 

Navigieren Sie zur Communication Services-Ressource, wählen Sie im Navigationsmenü **Schlüssel** aus, und kopieren Sie die Werte **Verbindungszeichenfolge** oder **Endpunkt** zur Verwendung durch die Communication Services-Clientbibliotheken. Beachten Sie, dass Sie Zugriff auf Primär- und Sekundärschlüssel haben. Dies kann in Szenarien nützlich sein, in denen Sie für einen Drittanbieter oder eine Stagingumgebung temporären Zugriff auf Ihre Communication Services-Ressourcen bereitstellen möchten.

:::image type="content" source="./media/key.png" alt-text="Screenshot: Communication Services-Seite „Schlüssel“":::

## <a name="store-your-connection-string"></a>Speichern der Verbindungszeichenfolge

Communication Services-Clientbibliotheken verwenden Verbindungszeichenfolgen, um an Communication Services gesendete Anforderungen zu autorisieren. Es gibt mehrere Möglichkeiten, die Verbindungszeichenfolge zu speichern:

* Eine Anwendung, die auf dem Desktop oder auf einem Gerät ausgeführt wird, kann die Verbindungszeichenfolge in der Datei **app.config** oder **web.config** speichern. Fügen Sie die Verbindungszeichenfolge dem Abschnitt **AppSettings** in diesen Dateien hinzu.
* Eine Anwendung, die in einer Azure App Service-Instanz ausgeführt wird, kann die Verbindungszeichenfolge in den [App Service-Anwendungseinstellungen](../../app-service/configure-common.md) speichern. Fügen Sie die Verbindungszeichenfolge dem Abschnitt **Verbindungszeichenfolgen** auf der Registerkarte „Anwendungseinstellungen“ im Portal hinzu.
* Sie können die Verbindungszeichenfolge in [Azure Key Vault](../../data-factory/store-credentials-in-key-vault.md) speichern.
* Wenn Sie Ihre Anwendung lokal ausführen, können Sie die Verbindungszeichenfolge in einer Umgebungsvariablen speichern.

### <a name="store-your-connection-string-in-an-environment-variable"></a>Speichern der Verbindungszeichenfolge in einer Umgebungsvariablen

Öffnen Sie zum Konfigurieren einer Umgebungsvariablen ein Konsolenfenster, und wählen Sie über die folgenden Registerkarten Ihr Betriebssystem aus. Ersetzen Sie `<yourconnectionstring>` durch Ihre Verbindungszeichenfolge.

#### <a name="windows"></a>[Windows](#tab/windows)

Öffnen Sie ein Konsolenfenster, und geben Sie den folgenden Befehl ein:

```console
setx COMMUNICATION_SERVICES_CONNECTION_STRING "<yourconnectionstring>"
```

Nachdem Sie die Umgebungsvariable hinzugefügt haben, müssen Sie unter Umständen alle ausgeführten Programme neu starten, von denen die Umgebungsvariable gelesen werden muss, z.B. das Konsolenfenster. Wenn Sie beispielsweise Visual Studio als Editor verwenden, müssen Sie Visual Studio neu starten, bevor Sie das Beispiel ausführen.

#### <a name="macos"></a>[macOS](#tab/unix)

Bearbeiten Sie die Datei vom Typ **.zshrc**, und fügen Sie die Umgebungsvariable hinzu:

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

Führen Sie nach dem Hinzufügen der Umgebungsvariablen im Konsolenfenster `source ~/.zshrc` aus, damit die Änderungen wirksam werden. Wenn Sie die Umgebungsvariable bei geöffneter IDE erstellt haben, muss der Editor, die IDE oder die Shell unter Umständen geschlossen und erneut geöffnet werden, um auf die Variable zuzugreifen.

#### <a name="linux"></a>[Linux](#tab/linux)

Bearbeiten Sie **.bash_profile**, und fügen Sie die Umgebungsvariable hinzu:

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

Führen Sie nach dem Hinzufügen der Umgebungsvariablen im Konsolenfenster `source ~/.bash_profile` aus, damit die Änderungen wirksam werden. Wenn Sie die Umgebungsvariable bei geöffneter IDE erstellt haben, muss der Editor, die IDE oder die Shell unter Umständen geschlossen und erneut geöffnet werden, um auf die Variable zuzugreifen.

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Communication Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

Wenn bei der Ressourcenlöschung der Ressource Telefonnummern zugewiesen waren, werden die Telefonnummern automatisch zum gleichen Zeitpunkt aus der Ressource freigegeben. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Communication Services-Ressource
> * Konfigurieren von Ressourcengeografie und -tags
> * Zugreifen auf die Schlüssel für diese Ressource
> * Löschen der Ressource

> [!div class="nextstepaction"]
> [Erstellen Ihrer ersten Benutzerzugriffstokens](access-tokens.md)
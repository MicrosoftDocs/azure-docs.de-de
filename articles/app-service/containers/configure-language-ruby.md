---
title: Konfigurieren von Ruby-Apps – Azure App Service
description: In diesem Tutorial werden die Optionen zum Erstellen und Konfigurieren von Ruby-Apps für Azure App Service für Linux beschrieben.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: astay;cephalin;kraigb
ms.custom: seodec18
ms.openlocfilehash: 412efac3742acf7ad1cdc3d08f9d90c4d39bad3e
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956119"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Konfigurieren einer Linux-Ruby-App für Azure App Service

In diesem Artikel erfahren Sie, wie Ruby-Apps mit [Azure App Service](app-service-linux-intro.md) ausgeführt werden und wie Sie das Verhalten von App Service bei Bedarf anpassen können. Ruby-Apps müssen mit allen erforderlichen [pip](https://pypi.org/project/pip/)-Modulen bereitgestellt werden.

Dieser Leitfaden enthält wichtige Konzepte und Anleitungen für Ruby-Entwickler, die in App Service einen integrierten Linux-Container verwenden. Falls Sie Azure App Service noch nicht verwendet haben, sollten Sie zunächst die [Ruby-Schnellstartanleitung](quickstart-ruby.md) und das [Tutorial „Ruby mit PostgreSQL“](tutorial-ruby-postgres-app.md) durcharbeiten.

## <a name="show-ruby-version"></a>Anzeigen der Ruby-Version

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um die aktuelle Ruby-Version anzuzeigen:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um alle unterstützten Ruby-Versionen anzuzeigen:

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Sie können auch eine nicht unterstützte Version von Ruby ausführen, indem Sie stattdessen Ihr eigenes Containerimage erstellen. Weitere Informationen finden Sie unter [Verwenden eines benutzerdefinierten Docker-Images für Web-App für Container](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Festlegen der Ruby-Version

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um die Ruby-Version auf „2.3“ festzulegen:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Bei Fehlern der folgenden Art zur Bereitstellungszeit:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> oder
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Dies bedeutet, dass sich die in Ihrem Projekt konfigurierte Ruby-Version von der Version unterscheidet, die im ausgeführten Container installiert ist (im obigen Beispiel: `2.3.3`). Überprüfen Sie im obigen Beispiel sowohl *Gemfile* als auch *.ruby-version*. Vergewissern Sie sich, dass die Ruby-Version nicht festgelegt bzw. auf die Version festgelegt ist, die im ausgeführten Container installiert ist (im obigen Beispiel: `2.3.3`).

## <a name="access-environment-variables"></a>Zugreifen auf Umgebungsvariablen

In App Service können Sie [App-Einstellungen außerhalb Ihres App-Codes festlegen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings). Anschließend können Sie darauf unter Verwendung des Standardmusters [ENV['<path-name>']](https://ruby-doc.org/core-2.3.3/ENV.html) zugreifen. Verwenden Sie beispielsweise den folgenden Code, um auf eine App-Einstellung namens `WEBSITE_SITE_NAME` zuzugreifen:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Anpassen der Bereitstellung

Beim Bereitstellen eines [Git-Repositorys](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) oder [ZIP-Pakets](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) mit aktivierten Buildprozessen führt die Bereitstellungs-Engine (Kudu) nach der Bereitstellung standardmäßig automatisch die folgenden Schritte aus:

1. Überprüfen, ob eine *Gemfile* vorhanden ist
1. Führen Sie `bundle clean`aus. 
1. Führen Sie `bundle install --path "vendor/bundle"`aus.
1. Ausführen von `bundle package`, um Gems im Ordner „vendor/cache“ zu verpacken

### <a name="use---without-flag"></a>Verwenden des Flags „--without“

Legen Sie zum Ausführen von `bundle install` mit dem Flag [--without](https://bundler.io/man/bundle-install.1.html) die [App-Einstellung](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `BUNDLE_WITHOUT` auf eine durch Trennzeichen getrennte Liste mit Gruppen fest. Mit dem folgenden Befehl wird beispielsweise `development,test` festgelegt.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Wenn diese Einstellung definiert ist, führt die Bereitstellungs-Engine `bundle install` mit `--without $BUNDLE_WITHOUT` aus.

### <a name="precompile-assets"></a>Vorkompilieren von Ressourcen

Mit den Schritten nach der Bereitstellung werden standardmäßig keine Ressourcen vorkompiliert. Legen Sie die [App-Einstellung](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `ASSETS_PRECOMPILE` auf `true` fest, um die Vorkompilierung von Ressourcen zu aktivieren. Anschließend wird nach Abschluss der Schritte nach der Bereitstellung der Befehl `bundle exec rake --trace assets:precompile` ausgeführt. Beispiel: 

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Weitere Informationen finden Sie unter [Bereitstellen von statischen Ressourcen](#serve-static-assets).

## <a name="customize-start-up"></a>Anpassen des Startvorgangs

Standardmäßig wird der Rails-Server vom Ruby-Container in der folgenden Reihenfolge gestartet (weitere Informationen unter [start-up script](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh) (Startskript)):

1. Generieren Sie einen [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security)-Wert, falls er noch nicht vorhanden ist. Dieser Wert ist erforderlich, damit die App im Produktionsmodus ausgeführt werden kann.
1. Legen Sie die Umgebungsvariable `RAILS_ENV` auf `production` fest.
1. Löschen Sie alle *PID*-Dateien im Verzeichnis *tmp/pids*, die bei der vorherigen Ausführung eines Rails-Servers erstellt wurden.
1. Überprüfen Sie, ob alle Abhängigkeiten installiert sind. Falls nicht, sollten Sie versuchen, Gems über das lokale Verzeichnis *vendor/cache* zu installieren.
1. Führen Sie `rails server -e $RAILS_ENV`aus.

Sie können den Startvorgang auf folgende Weise anpassen:

- [Bereitstellen von statischen Ressourcen](#serve-static-assets)
- [Ausführen in einem anderen Modus als dem Produktionsmodus](#run-in-non-production-mode)
- [Manuelles Festlegen von „secret_key_base“](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Bereitstellen von statischen Ressourcen

Der Rails-Server im Ruby-Container wird standardmäßig im Produktionsmodus ausgeführt, und [es wird vorausgesetzt, dass die Ressourcen vorkompiliert und von ihrem Webserver bereitgestellt werden](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Sie müssen zwei Schritte ausführen, um statische Ressourcen über den Rails-Server bereitzustellen:

- **Vorkompilieren der Ressourcen** - [Führen Sie das Vorkompilieren der Ressourcen lokal durch](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation), und stellen Sie sie manuell bereit. Sie können die Verarbeitung aber auch der Bereitstellungs-Engine überlassen (siehe [Vorkompilieren von Ressourcen](#precompile-assets)).
- **Aktivieren der Bereitstellung von statischen Dateien**: Legen Sie zum Bereitstellen von statischen Ressourcen aus dem Ruby-Container die `RAILS_SERVE_STATIC_FILES`[App-Einstellung `RAILS_SERVE_STATIC_FILES`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) auf `true` fest. Beispiel: 

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Ausführen in einem anderen Modus als dem Produktionsmodus

Der Rails-Server wird standardmäßig im Produktionsmodus ausgeführt. Legen Sie für die Ausführung im Entwicklungsmodus die [App-Einstellung](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `RAILS_ENV` auf `development` fest.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Diese Einstellung allein führt aber nur dazu, dass der Rails-Server im Entwicklungsmodus gestartet wird. Hierbei werden nur localhost-Anforderungen akzeptiert, und der Zugriff von außerhalb des Containers ist nicht möglich. Legen Sie die [App-Einstellung](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `APP_COMMAND_LINE` auf `rails server -b 0.0.0.0` fest, damit Anforderungen von Remoteclients akzeptiert werden. Mit dieser App-Einstellung können Sie im Ruby-Container einen benutzerdefinierten Befehl ausführen. Beispiel: 

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secretkeybase-manually"></a>Manuelles Festlegen von „secret_key_base“

Legen Sie die [App-Einstellung](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `SECRET_KEY_BASE` mit dem gewünschten Wert fest, um Ihren eigenen `secret_key_base`-Wert zu verwenden, anstatt von App Service einen Wert generieren zu lassen. Beispiel: 

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Zugreifen auf Diagnoseprotokolle

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Öffnen einer SSH-Sitzung im Browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Rails-App mit PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [App Service unter Linux – Häufig gestellte Fragen](app-service-linux-faq.md)
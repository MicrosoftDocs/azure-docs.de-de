---
title: Häufig gestellte Fragen zum Ausführen integrierter Container
description: Hier finden Sie Antworten auf häufig gestellte Fragen zu den integrierten Linux-Containern in Azure App Service.
keywords: Azure App Service, Web-App, FAQ, Linux, OSS, Web-App für Container, mehrere Container, Multicontainer
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: e2ab39f090124aaf590798795f446bda5b0fe48a
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008553"
---
# <a name="azure-app-service-on-linux-faq"></a>Häufig gestellte Fragen (FAQ) zu Azure App Service unter Linux

Mit dem Release von App Service unter Linux erweitern wir unsere Plattform um Features und nehmen Verbesserungen an ihr vor. Dieser Artikel enthält Antworten auf Fragen, die uns kürzlich von Kunden gestellt wurden.

Wenn Sie eine Frage haben, schreiben Sie einen Kommentar zu diesem Artikel.

## <a name="built-in-images"></a>Integrierte Images

**Ich möchte die integrierten Docker-Container nutzen, die von der Plattform bereitgestellt werden. Wo finde ich diese Dateien?**

Sie finden alle Docker-Dateien auf [GitHub](https://github.com/azure-app-service). Sie finden alle Docker-Container auf [Docker Hub](https://hub.docker.com/u/appsvc/).

<a id="#startup-file"></a>

**Welche Werte sind beim Konfigurieren des Laufzeitstapels im Abschnitt „Startdatei“ anzugeben?**

| Stapel           | Erwarteter Wert                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | der Befehl, mit dem Ihre JAR-App gestartet wird (z. B. `java -jar /home/site/wwwroot/app.jar --server.port=80`) |
| Tomcat          | der Speicherort eines Skripts zum Ausführen aller erforderlichen Konfigurationen (z. B. `/home/site/deployments/tools/startup_script.sh`)          |
| Node.js         | die PM2-Konfigurationsdatei oder Ihre Skriptdatei                                |
| .NET Core       | der Name der kompilierten DLL-Datei als `dotnet <myapp>.dll`                                 |
| Ruby            | das Ruby-Skript, mit dem Sie Ihre App initialisieren möchten                     |

Diese Befehle oder Skripts werden ausgeführt, nachdem der integrierte Docker-Container gestartet ist, aber bevor der Code Ihrer Anwendung gestartet wird.

## <a name="management"></a>Verwaltung

**Was geschieht, wenn ich auf die Schaltfläche „Neu starten“ im Azure-Portal klicke?**

Diese Aktion ist mit dem Docker-Neustart identisch.

**Kann ich über Secure Shell (SSH) eine Verbindung mit dem virtuellen Computer des App-Containers herstellen?**

Ja, Sie können dazu die Website für die Quellcodeverwaltung (Source Control Management, SCM) verwenden.

> [!NOTE]
> Sie können auch direkt über SSH, SFTP oder Visual Studio Code (zum Livedebuggen von Node.js-Apps) eine Verbindung vom lokalen Entwicklungscomputer zum App-Container herstellen. Weitere Informationen finden Sie unter [Remotedebuggen und SSH in App Service unter Linux](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html).
>

**Wie kann ich einen Linux-App Service-Plan über ein SDK oder eine Azure Resource Manager-Vorlage erstellen?**

Legen Sie das App-Dienstfeld **reserviert** auf *true* fest.

## <a name="continuous-integration-and-deployment"></a>Continuous Integration und Continuous Deployment

**Meine Web-App verwendet nach dem Aktualisieren des Images auf Docker Hub noch ein altes Docker-Containerimage. Unterstützen Sie Continuous Integration/Deployment von benutzerdefinierten Containern?**

Ja. Informationen zum Einrichten von Continuous Integration/Continuous Deployment für Azure Container Registry oder DockerHub finden Sie unter [Continuous Deployment mit Web-App für Container](./deploy-ci-cd-custom-container.md) (in englischer Sprache). Bei privaten Registrierungen können Sie den Container aktualisieren, indem Sie Ihre Web-App beenden und dann wieder starten. Sie können auch eine Dummyeinstellung in der Anwendung ändern oder hinzufügen, um die Aktualisierung Ihres Containers zu erzwingen.

**Werden Stagingumgebungen unterstützt?**

Ja.

**Kann ich *WebDeploy/MSDeploy* zur Bereitstellung meiner Web-App verwenden?**

Ja, Sie müssen die App-Einstellung `WEBSITE_WEBDEPLOY_USE_SCM` auf *false* festlegen.

**Bei der Git-Bereitstellung meiner Anwendung tritt ein Fehler auf, wenn ich eine Linux-Web-App verwende. Wie kann ich dieses Problem umgehen?**

Wenn bei der Git-Bereitstellung in Ihrer Linux-Web-App ein Fehler auftritt, stellen Sie Anwendungscodes wie folgt bereit:

- Verwenden Sie das Feature für Continuous Delivery (Vorschauversion): Sie können den Quellcode Ihrer App in einem Azure DevOps-Git-Repository oder -GitHub-Repository speichern, um Azure Continuous Delivery zu verwenden. Weitere Informationen finden Sie unter [Konfigurieren von Continuous Delivery für die Linux-Web-App](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/) (in englischer Sprache).

- Verwenden Sie die [API für die ZIP-Bereitstellung](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file):Stellen Sie zur Verwendung dieser API [eine SSH-Verbindung mit Ihrer Web-App](configure-linux-open-ssh-session.md) her, und navigieren Sie zu dem Ordner, in dem der Code bereitgestellt werden soll. Führen Sie den folgenden Code aus:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Wenn eine Fehlermeldung angezeigt wird, dass der Befehl `curl` nicht gefunden wird, müssen Sie curl unbedingt mithilfe von `apt-get install curl` installieren, bevor Sie den Befehl `curl` ausführen.

## <a name="language-support"></a>Sprachunterstützung

**Ich möchte in meiner Node.js-Anwendung Websockets verwenden. Muss ich bestimmte Einstellungen oder Konfigurationen festlegen?**

Ja, deaktivieren Sie in Ihrem serverseitigen Node.js-Code `perMessageDeflate`. Verwenden Sie beispielsweise den folgenden Code, wenn Sie socket.io nutzen:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Unterstützen Sie nicht kompilierte .NET Core-Apps?**

Ja.

**Unterstützen Sie Composer als Abhängigkeits-Manager für PHP-Apps?**

Ja. Bei einer Git-Bereitstellung erkennt Kudu, dass Sie eine PHP-Anwendung bereitstellen (weil eine composer.lock-Datei existiert), und löst eine Composerinstallation aus.

## <a name="custom-containers"></a>Benutzerdefinierte Container

**Ich verwende meinen eigenen benutzerdefinierten Container. Ich möchte, dass die Plattform eine SMB-Freigabe im Verzeichnis `/home/` bereitstellt.**

Wenn die Einstellung `WEBSITES_ENABLE_APP_SERVICE_STORAGE`**nicht angegeben** oder auf *TRUE* festgelegt ist, wird das Verzeichnis `/home/` nicht über Skalierungsinstanzen **freigegeben** und geschriebene Dateien werden **über Neustarts hinweg beibehalten**. Das explizite Festlegen von `WEBSITES_ENABLE_APP_SERVICE_STORAGE` auf *FALSE* deaktiviert die Bereitstellung.

**Mein benutzerdefinierter Container benötigt für den Start sehr lange, und die Plattform startet den Container neu, bevor er den Startvorgang abgeschlossen hat.**

Sie können den Zeitraum konfigurieren, den die Plattform vor dem Neustarten Ihres Containers wartet. Legen Sie hierzu die App-Einstellung `WEBSITES_CONTAINER_START_TIME_LIMIT` auf den gewünschten Wert fest. Der Standardwert ist 230 Sekunden, und der Maximalwert ist 1800 Sekunden.

**Welches Format hat die Server-URL der privaten Registrierung?**

Geben Sie die vollständige Registrierungs-URL samt `http://` oder `https://` ein.

**Welches Format hat der Imagename in der Option zur privaten Registrierung?**

Fügen Sie den vollständigen Namen hinzu, einschließlich der URL der privaten Registrierung (Beispiel: myacr.azurecr.io/dotnet:latest). Namen von Images, die einen benutzerdefinierten Port verwenden, [können nicht über das Portal eingegeben werden](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Verwenden Sie das [Befehlszeilentool](/cli/azure/webapp/config/container#az-webapp-config-container-set)`az`, um `docker-custom-image-name` festzulegen.

**Kann ich mehr als einen Port in meinem benutzerdefinierten Containerimage verfügbar machen?**

Es kann nur ein Port verfügbar gemacht werden.

**Kann ich meinen eigenen Speicher verwenden?**

Ja, [Bring Your Own Storage](./configure-connect-to-azure-storage.md) ist als Vorschauversion verfügbar.

**Warum kann ich über die SCM-Website das Dateisystem meines benutzerdefinierten Containers nicht durchsuchen und keine Prozesse ausführen?**

Die SCM-Website wird in einem separaten Container ausgeführt. Sie können das Dateisystem oder aktive Prozesse des App-Containers nicht überprüfen.

**Mein benutzerdefinierter Container lauscht auf einen anderen Port als Port 80. Wie kann ich meine App so konfigurieren, dass Anforderungen an diesen Port weitergeleitet werden?**

Die Porterkennung erfolgt automatisch. Sie können auch eine Anwendungseinstellung mit dem Namen *WEBSITES_PORT* und für diese den Wert der erwarteten Portnummer angeben. Zuvor wurde von der Plattform die App-Einstellung *PORT* verwendet. Es ist geplant, diese App-Einstellung einzustellen und ausschließlich *WEBSITES_PORT* zu verwenden.

**Muss ich HTTPS in meinem benutzerdefinierten Container implementieren?**

Nein. Die Plattform handhabt die HTTPS-Beendigung an den freigegebenen Front-Ends.

## <a name="multi-container-with-docker-compose"></a>Mehrere Container mit Docker Compose

**Wie kann ich die Azure Container Registry (ACR) für die Verwendung mehrerer Container konfigurieren?**

Um ACR mit mehreren Containern zu verwenden, müssen **alle Containerimages** auf dem gleichen ACR-Registrierungsserver gehostet sein. Sobald sie sich auf dem gleichen Registrierungsserver befinden, müssen Sie Anwendungseinstellungen erstellen und dann die Konfigurationsdatei von Docker Compose mit den Namen der ACR-Images aktualisieren.

Erstellen Sie die folgenden Anwendungseinstellungen:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (vollständige URL, Bsp.: `https://<server-name>.azurecr.io`)
- DOCKER_REGISTRY_SERVER_PASSWORD (Aktivieren des Administratorzugriffs in den ACR-Einstellungen)

Verweisen Sie in der Konfigurationsdatei auf Ihr ACR-Image, wie im folgenden Beispiel:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Wie finde ich heraus, auf welchen Container aus dem Internet zugegriffen werden kann?**

- Nur ein Container kann für den Zugriff geöffnet sein
- Nur Port 80 und 8080 sind zugänglich (verfügbar gemachte Ports)

Anhand dieser Regeln können Sie bestimmen, auf welchen Container zugegriffen werden kann, anhand der Rangfolge aufgelistet:

- Die Anwendungseinstellung `WEBSITES_WEB_CONTAINER_NAME` ist auf den Containernamen festgelegt
- Der erste Container, der Port 80 oder 8080 definiert
- Wenn keiner der oben genannten Punkte zutrifft, kann auf den in der Datei zuerst definierten (verfügbar gemachten) Container zugegriffen werden


## <a name="web-sockets"></a>Websockets

Websockets werden für Linux-Apps unterstützt.

> [!IMPORTANT]
> Bei Free-App Service-Plänen werden Websockets aktuell nicht für Linux-Apps unterstützt. Wir arbeiten daran, dass diese Einschränkung schon bald nicht mehr gilt. Bei Free-App Service-Plänen sollen in Zukunft bis zu fünf Websocketverbindungen unterstützt werden.

## <a name="pricing-and-sla"></a>Preise und SLA

**Wie erfolgt die Abrechnung für den nun allgemein verfügbaren Dienst?**

Die Preise variieren abhängig von der SKU und Region. Einzelheiten finden Sie auf unserer Seite mit der Preisübersicht: [App Service – Preise](https://azure.microsoft.com/pricing/details/app-service/linux/)

## <a name="other-questions"></a>Andere Fragen

**Was bedeutet „Die angeforderte Funktion ist in der Ressourcengruppe nicht verfügbar“?**

Diese Meldung wird möglicherweise angezeigt, wenn Sie eine Web-App mit Azure Resource Manager (ARM) erstellen. Aufgrund einer aktuellen Beschränkung können Sie für dieselbe Ressourcengruppe keine Windows- und Linux-Apps in derselben Region kombinieren.

**Welche Zeichen werden in den Namen von Anwendungseinstellungen unterstützt?**

Sie können nur Buchstaben (A-Z, a-Z), Zahlen (0-9) und Unterstrich (_) für Anwendungseinstellungen verwenden.

**Wo kann ich neue Funktionen beantragen?**

Sie können Ihre Idee im [Web-Apps-Feedbackforum](https://aka.ms/webapps-uservoice) einreichen. Fügen Sie im Titel Ihrer Idee „[Linux]“ hinzu.

## <a name="next-steps"></a>Nächste Schritte

- [Was ist Azure App Service unter Linux?](overview.md#app-service-on-linux)
- [Einrichten von Stagingumgebungen in Azure App Service](deploy-staging-slots.md)
- [Continuous Deployment mit Web-App für Container](./deploy-ci-cd-custom-container.md)

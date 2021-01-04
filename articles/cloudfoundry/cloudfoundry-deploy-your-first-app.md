---
title: Bereitstellen Ihrer ersten App in Cloud Foundry in Microsoft Azure
description: Bereitstellen einer Anwendung in Cloud Foundry in Azure
author: seanmck
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: c8afc4f15f421a7b60b95d09b954e7742c24c66e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95519675"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Bereitstellen Ihrer ersten App in Cloud Foundry in Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) ist eine beliebte Open-Source-Anwendungsplattform , die in Microsoft Azure verfügbar ist. In diesem Artikel wird beschrieben, wie Sie in einer Azure Umgebung eine Anwendung in Cloud Foundry bereitstellen und verwalten.

## <a name="create-a-cloud-foundry-environment"></a>Erstellen einer Cloud Foundry-Umgebung

Es gibt mehrere Optionen zum Erstellen einer Cloud Foundry-Umgebung in Azure:

- Nutzen Sie das [Pivotal Cloud Foundry-Angebot][pcf-azuremarketplace] im Azure Marketplace, um eine Standardumgebung zu erstellen, die PCF Operations Manager und Azure Service Broker umfasst. Eine umfassende Anleitung für die Bereitstellung des Marketplace-Angebots finden Sie in der [Pivotal-Dokumentation][pcf-azuremarketplace-pivotaldocs].
- Erstellen Sie eine benutzerdefinierte Umgebung, indem Sie [Pivotal Cloud Foundry manuell bereitstellen][pcf-custom].
- [Stellen Sie die Cloud Foundry-Open-Source-Pakete direkt bereit][oss-cf-bosh], indem Sie einen [BOSH](https://bosh.io)-Director einrichten. Dies ist ein virtueller Computer, der die Bereitstellung der Cloud Foundry-Umgebung koordiniert.

> [!IMPORTANT] 
> Wenn Sie Pivotal Cloud Foundry (PCF) über Azure Marketplace bereitstellen, notieren Sie sich den Wert von SYSTEMDOMAINURL und die Anmeldeinformationen für den Administrator, die für den Zugriff auf Pivotal Apps Manager erforderlich sind. Beides wird im Bereitstellungsleitfaden in Marketplace beschrieben. Die Angaben sind zum Durchführen dieses Tutorials erforderlich. Für Marketplace-Bereitstellungen hat SYSTEMDOMAINURL das Format `https://system.*ip-address*.cf.pcfazure.com`.

## <a name="connect-to-the-cloud-controller"></a>Herstellen einer Verbindung mit dem Cloud Controller

Der Cloud Controller ist der primäre Einstiegspunkt in eine Cloud Foundry-Umgebung für die Bereitstellung und Verwaltung von Anwendungen. Die Core Cloud Controller-API (CCAPI) ist eine REST-API, auf die über verschiedene Tools zugegriffen werden kann. In diesem Fall interagieren wir mit der API über die [Cloud Foundry-Befehlszeilenschnittstelle][cf-cli]. Die Befehlszeilenschnittstelle kann unter Linux, macOS oder Windows installiert werden. Wenn Sie sie nicht installieren möchten, steht sie auch in der [Azure Cloud Shell][cloudshell-docs] vorinstalliert zur Verfügung.

Um sich anzumelden, stellen Sie `api` der SYSTEMDOMAINURL voran, die Sie bei der Marketplace-Bereitstellung erhalten haben. Da bei der Standardbereitstellung ein selbst signiertes Zertifikat verwendet wird, müssen Sie auch den Schalter `skip-ssl-validation` einschließen.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Sie werden aufgefordert, sich beim Cloud Controller anzumelden. Verwenden Sie die Administratoranmeldeinformationen, die Sie bei den Schritten zur Bereitstellung über Marketplace erhalten haben.

Cloud Foundry bietet *Orgs* (Organisationen) und *Spaces* (Bereiche) als Namespaces zum Isolieren der Teams und Umgebungen in einer gemeinsam genutzten Umgebung. Die Marketplace-Bereitstellung von PCF umfasst die Standardorganisation *system* und einen Satz von Spaces, die für die Basiskomponenten wie den Dienst für die automatische Skalierung und den Azure Service Broker erstellt werden. Wählen Sie einstweilen den Space *system* aus.


## <a name="create-an-org-and-space"></a>Erstellen einer Organisation und eines Space

Wenn Sie `cf apps` eingeben, werden verschiedene Systemanwendungen angezeigt, die im Space „system“ innerhalb der Organisation „system“ bereitgestellt wurden. 

Sie sollten die Organisation *system* für Systemanwendungen reserviert lassen. Erstellen Sie deshalb eine Organisation und einen Space für unsere Beispielanwendung.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Verwenden Sie den Befehl „target“, um zur neuen Organisation und zum neuen Space zu wechseln:

```bash
cf target -o testorg -s dev
```

Wenn Sie nun eine Anwendung bereitstellen, wird sie automatisch in der neuen Organisation und im neuen Space erstellt. Um zu bestätigen, dass es in der neuen Organisation und im neuen Space keine Apps gibt, geben Sie erneut `cf apps` ein.

> [!NOTE] 
> Weitere Informationen zu Organisationen und Spaces und ihrer Nutzung für die rollenbasierte Zugriffssteuerung von Cloud Foundry (Cloud Foundry RBAC) finden Sie in der [Cloud Foundry-Dokumentation][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Bereitstellen von Anwendungen

Lassen Sie uns eine Cloud Foundry-Beispielanwendung namens „Hello Spring Cloud“ verwenden, die in Java geschrieben ist und auf [Spring Framework](https://spring.io) und [Spring Boot](https://projects.spring.io/spring-boot/) basiert.

### <a name="clone-the-hello-spring-cloud-repository"></a>Klonen des Repositorys von Hello Spring Cloud

Die Beispielanwendung Hello Spring Cloud ist auf GitHub verfügbar. Klonen Sie sie in Ihre Umgebung, und wechseln Sie zum neuen Verzeichnis:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Erstellen der Anwendung

Erstellen Sie Anwendung mit [Apache Maven](https://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Bereitstellen der Anwendung mit cd push

Sie können die meisten Anwendungen in Cloud Foundry mit dem Befehl `push` bereitstellen:

```bash
cf push
```

Wenn Sie den Befehl *push* für eine Anwendung aufrufen, erkennt Cloud Foundry den Typ der Anwendung (in diesem Fall eine Java-App) und ihre Abhängigkeiten (in diesem Fall das Spring Framework). Anschließend werden alle für die Ausführung Ihres Codes erforderlichen Elemente in einem eigenständigen Containerimage gepackt, das als *Droplet* bezeichnet wird. Schließlich plant Cloud Foundry die Anwendung auf einem der verfügbaren Computer in Ihrer Umgebung und erstellt eine URL, über die sie erreicht werden kann und die Sie in der Ausgabe des Befehls finden.

![Ausgabe des Befehls cf push][cf-push-output]

Um die Anwendung Hello Spring Cloud anzuzeigen, öffnen Sie die bereitgestellte URL in Ihrem Browser:

![Standardbenutzeroberfläche für Hello Spring Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Weitere Informationen dazu, was bei Ausführung von `cf push` passiert, finden Sie unter [How Applications Are Staged][cf-push-docs] (Wie das Staging für Anwendungen erfolgt) in der Cloud Foundry-Dokumentation.

## <a name="view-application-logs"></a>Anzeigen von Anwendungsprotokollen

Sie können die Cloud Foundry CLI verwenden, um Protokolle für eine Anwendung anhand ihres Namens anzuzeigen:

```bash
cf logs hello-spring-cloud
```

Standardmäßig verwendet der Befehl „logs“ *tail*, wodurch neue Protokolle angezeigt werden, während sie geschrieben werden. Damit neue Protokolle angezeigt werden, aktualisieren Sie die App Hello Spring Cloud im Browser.

Um Protokolle anzuzeigen, die bereits geschrieben wurden, fügen Sie den Schalter `recent` hinzu:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Skalieren der Anwendung

Standardmäßig erstellt `cf push` nur eine einzelne Instanz der Anwendung. Um Hochverfügbarkeit zu gewährleisten und ein Aufskalieren für einen höheren Durchsatz zu ermöglichen, werden in der Regel mehrere Instanzen Ihrer Anwendungen ausgeführt. Mit dem Befehl `scale` können Sie bereits bereitgestellte Anwendungen mühelos aufskalieren:

```bash
cf scale -i 2 hello-spring-cloud
```

Bei Anwenden des Befehls `cf app` auf die Anwendung wird deutlich, dass Cloud Foundry eine weitere Instanz der Anwendung erstellt. Nach dem Start der Anwendung startet Cloud Foundry automatisch den Lastenausgleich von Datenverkehr für diese.


## <a name="next-steps"></a>Nächste Schritte

- [Lesen der Cloud Foundry-Dokumentation][cloudfoundry-docs]
- [Einrichten des Azure DevOps Services-Plug-Ins für Cloud Foundry][vsts-plugin]
- [Konfigurieren von Microsoft Log Analytics Nozzle für Cloud Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: ../cloud-shell/overview.md
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: https://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png
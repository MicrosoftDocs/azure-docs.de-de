---
title: Arbeiten mit Node.js-Modulen
description: Erfahren Sie, wie Sie mit Node.js-Modulen im Zusammenhang mit Azure App Service oder Cloud Services arbeiten.
services: ''
documentationcenter: nodejs
author: rloutlaw
manager: rloutlaw
editor: ''
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: routlaw
ms.openlocfilehash: f61f08f32ebb2b721846d3c3017405af99421104
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95542217"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Verwenden von Node.js-Modulen mit Azure-Anwendungen
Dieses Dokument bietet Anweisungen zur Verwendung von Node.js-Modulen mit Anwendungen, die auf Azure gehostet werden. Sie erfahren, wie Sie sicherstellen können, dass eine Anwendung eine bestimmte Version eines Moduls sowie systemeigene Module von Azure verwendet.

Wenn Sie sich bereits mit Node.js-Modulen, **package.json**- und **npm-shrinkwrap.json**-Dateien auskennen, erhalten Sie im Folgenden eine kurze Zusammenfassung der in diesem Artikel diskutierten Inhalte:

* Azure App Service versteht **package.json**- und **npm-shrinkwrap.json**-Dateien und kann Module basierend auf den Einträgen in diesen Dateien installieren.

* Azure Cloud Services erfordern, dass alle Module auf der Entwicklungsumgebung installiert sind und das Verzeichnis **node\_modules** als Teil des Bereitstellungspakets enthalten ist. Sie können die Unterstützung für die Installation von Modulen mithilfe der Dateien **package.json** oder **npm-shrinkwrap.json** auf Cloud Services aktivieren, diese Konfiguration erfordert jedoch die Anpassung der Standardskripts, die von Cloud Services-Projekten verwendet werden. Ein Beispiel für das Konfigurieren dieser Umgebung finden Sie unter [Azure Startup task to run npm install to avoid deploying node modules](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown) (Azure-Starttask zum Ausführen von „npm install“, um die Bereitstellung von Knotenmodulen zu vermeiden).

> [!NOTE]
> Azure Virtual Machines werden in diesem Artikel nicht behandelt, da die Bereitstellung auf einem virtuellen Computer vom Betriebssystem abhängig ist, das von dem virtuellen Computer gehostet wird.
>
>

## <a name="nodejs-modules"></a>Node.js-Module
Module sind ladbare JavaScript-Pakete, die bestimmte Funktionen für die Anwendung bieten. Module werden normalerweise über das Befehlszeilentool **npm** installiert. Einige Module (wie das http-Modul) werden jedoch als Teil des Node.js-Pakets bereitgestellt.

Wenn Module installiert werden, werden diese im Verzeichnis **node\_modules** im Stamm der Anwendungsverzeichnisstruktur gespeichert. Jedes Modul im Verzeichnis **node\_modules** behält sein eigenes Verzeichnis, das alle Module enthält, von denen es abhängt. Dieses Verhalten wiederholt sich für jedes Modul auf der Abhängigkeitskette. Durch diese Umgebung kann jedes installierte Modul seine eigenen Versionsanforderungen für die Module haben, von denen es abhängt. Dies kann jedoch zu einer sehr großen Verzeichnisstruktur führen.

Wenn das Verzeichnis **node\_modules** als Teil der Anwendung bereitgestellt wird, vergrößert dies die Bereitstellung im Vergleich zur Verwendung einer Datei **package.json** oder **npm-shrinkwrap.json**. Dies garantiert jedoch, dass die Versionen der in der Produktion verwendeten Module identisch mit denen der Module aus der Entwicklung sind.

### <a name="native-modules"></a>Systemeigene Module
Auch wenn die meisten Module einfache Nur-Text-JavaScript-Dateien sind, gibt es auch Module, die plattformspezifische Binärimages sind. Diese Module werden bei der Installation gebildet, was normalerweise mit Python und node-gyp erfolgt. Da Azure Cloud Services auf den Ordner **node\_modules** angewiesen sind, der als Teil der Anwendung bereitgestellt wird, sollte ein systemeigenes Modul, das als Teil der installierten Module enthalten ist, in einem Clouddienst funktionieren, solange dieser auf einem Windows-Entwicklungssystem erstellt wurde.

Azure App Service unterstützt nicht alle nativen Module, sodass beim Kompilieren dieser Module unter sehr spezifischen Voraussetzungen möglicherweise ein Fehler auftritt. Während einige beliebte Module wie MongoDB optionale native Abhängigkeiten besitzen, aber problemlos ohne diese funktionieren, erwiesen sich zwei Problemumgehungen bei fast allen zurzeit verfügbaren nativen Modulen als sehr erfolgreich:

* Führen Sie **npm install** auf einem Windows-Computer aus, auf dem alle Voraussetzungen für das jeweilige systemeigene Modul installiert sind. Stellen Sie anschließend den erstellten Ordner **node\_modules** als Teil der Anwendung in Azure App Service bereit.

  * Überprüfen Sie vor dem Kompilieren, dass die Architektur der lokalen Installation von Node.js übereinstimmt und dass die Version so nah wie möglich an der in Azure verwendeten ist (die aktuellen Werte können zur Laufzeit mit den Eigenschaften **process.arch** und **process.version** geprüft werden).

* Azure App Service kann für die Ausführung benutzerdefinierter Bash- oder Shell-Skripts während der Bereitstellung konfiguriert werden. So haben Sie die Möglichkeit, benutzerdefinierte Befehle auszuführen und exakt zu konfigurieren, wie **npm install** ausgeführt wird. Ein Video, in dem das Konfigurieren dieser Umgebung veranschaulicht wird, finden Sie unter [Benutzerdefinierte Website-Bereitstellungsskripts mit Kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Verwenden einer package.json-Datei

Mit der Datei **package.json** können Sie die von der Anwendung benötigten Abhängigkeiten auf oberster Ebene angeben, sodass die Hostingplattform die Abhängigkeiten installieren kann. Somit muss der Ordner **node\_modules** nicht als Teil der Bereitstellung enthalten sein. Nachdem die Anwendung bereitgestellt wurde, wird der Befehl **npm install** verwendet, um die Datei **package.json** zu analysieren und alle aufgelisteten Abhängigkeiten zu installieren.

Während der Entwicklung können Sie bei der Installation von Modulen die Parameter **--save**, **--save-dev** oder **--save-optional** verwenden, um automatisch einen Eintrag für das Modul zur Datei **package.json** hinzuzufügen. Weitere Informationen hierzu finden Sie unter [npm-install](https://docs.npmjs.com/cli/install).

Ein potenzielles Problem bei der Datei **package.json** besteht darin, dass nur die Version für die Abhängigkeiten auf oberster Ebene angegeben wird. Manche installierten Module geben die Version der Module an, von denen sie abhängen, andere nicht. Somit ist es möglich, dass Sie eine andere Abhängigkeitskette erhalten als die in der Entwicklung verwendete.

> [!NOTE]
> Falls die Datei <b>package.json</b> auf ein natives Modul verweist, wird Ihnen bei der Bereitstellung auf Azure App Service u.U. ein Fehler ähnlich im folgenden Beispiel angezeigt, wenn die Anwendung mithilfe von Git veröffentlicht wird:
>
> npm ERR! module-name@0.6.0 install: 'node-gyp configure build'
>
> npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1
>
>

### <a name="using-a-npm-shrinkwrapjson-file"></a>Verwenden einer npm-shrinkwrap.json-Datei
Die Datei **npm-shrinkwrap.json** ist ein Versuch, die Modulversionsbegrenzungen der Datei **package.json** zu umgehen. Während die Datei **package.json** nur Versionen der Module auf oberster Ebene enthält, enthält die Datei **npm-shrinkwrap.json** die Versionsanforderungen für die vollständige Modulabhängigkeitskette.

Wenn die Anwendung für die Produktion bereit ist, können Sie die Versionsanforderungen sperren und eine Datei **npm-shrinkwrap.json** über den Befehl **npm shrinkwrap** erstellen. Dieser Befehl verwendet die derzeit im Ordner **node\_modules** installierten Versionen und speichert sie in der Datei **npm-shrinkwrap.json**. Nachdem die Anwendung zur Hostingumgebung bereitgestellt wurde, wird der Befehl **npm install** verwendet, um die Datei **npm-shrinkwrap.json** zu analysieren und alle aufgelisteten Abhängigkeiten zu installieren. Weitere Informationen finden Sie unter [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Falls die Datei <b>npm-shrinkwrap.json</b> auf ein natives Modul verweist, wird bei der Bereitstellung auf Azure App Service ein Fehler ähnlich dem im folgenden Beispiel angezeigt, wenn die Anwendung mithilfe von Git veröffentlicht wird:
>
> npm ERR! module-name@0.6.0 install: 'node-gyp configure build'
>
> npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1
>
>

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie wissen, wie Sie Node.js-Module mit Azure verwenden, erfahren Sie nun, wie Sie [die Node.js-Version angeben](https://github.com/squillace/nodejs-microservice), [eine Node.js-Web-App erstellen und bereitstellen](app-service/quickstart-nodejs.md) und [die Azure-Befehlszeilenschnittstelle für Mac und Linux verwenden](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

Weitere Informationen finden Sie im [Node.js Developer Center](/azure/developer/javascript/).

[specify the Node.js version]: ./app-service/overview.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
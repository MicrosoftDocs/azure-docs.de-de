---
title: Bereitstellung virtueller Azure-Computer mit Chef | Microsoft Docs
description: Hier erfahren Sie, wie Sie Chef zur automatisierten Bereitstellung und Konfiguration virtueller Computer in Microsoft Azure verwenden.
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: jeconnoc
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: diviso
ms.openlocfilehash: 9cb7172fb529d8f0cd8650db7c06a78176ef342d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64729553"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatisieren der Bereitstellung virtueller Azure-Computer mit Chef
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Chef ist ein hervorragendes Tool zur Automatisierung und für Konfigurationen mit gewünschten Zuständen.

Mit der aktuellen Veröffentlichung der Cloud-API bietet Chef eine nahtlose Integration in Azure und Ihnen die Möglichkeit, den Konfigurationsstatus über einen einzelnen Befehl bereitzustellen und zu implementieren.

Anhand dieses Artikels richten Sie die Chef-Umgebung für die Bereitstellung virtueller Azure-Computer ein. Zudem werden Sie schrittweise durch die Erstellung einer Richtlinie und die anschließende Bereitstellung dieses „Cookbooks“ auf einem virtuellen Azure-Computer geführt.

## <a name="chef-basics"></a>Grundlagen zu Chef
Machen Sie sich zuerst [mit den grundlegenden Konzepten von Chef vertraut](https://www.chef.io/chef).

Das folgende Diagramm zeigt die allgemeine Chef-Architektur:

![][2]

Die Architektur von Chef setzt sich aus drei Hauptkomponenten zusammen: Chef-Server, Chef-Client (Knoten) und Chef-Arbeitsstation.

Der Chef-Server ist der Verwaltungspunkt, für den es zwei Optionen gibt: eine gehostete oder eine lokale Lösung.

Der Chef-Client (Knoten) ist der Agent, der sich auf den Servern befindet, die Sie verwalten.

Die Chef-Arbeitsstation ist der Name für die Administrator-Arbeitsstation, auf der Sie Richtlinien erstellen und Verwaltungsbefehle ausführen, sowie für das Softwarepaket mit Chef-Tools.

Im Allgemeinen wird der Ort, an dem Sie Aktionen ausführen als _Ihre Arbeitsstation_ und das Softwarepaket als _Chef-Arbeitsstation_ bezeichnet.
Beispiel: Sie laden den Befehl „knife“ als Teil der _Chef-Arbeitsstation_ herunter, führen knife-Befehler jedoch über _Ihre Arbeitsstation_ aus, um die Infrastruktur zu verwalten.

Chef verwendet auch die Konzepte von „Cookbooks“ und „Recipes“, die im Grunde die Richtlinien sind, die wir definieren und auf die Server anwenden.

## <a name="preparing-your-workstation"></a>Vorbereiten der Arbeitsstation

Bereiten Sie zunächst Ihre Arbeitsstation vor, indem Sie ein Verzeichnis zum Speichern von Chef-Konfigurationsdateien und Rezeptbüchern (Cookbooks) erstellen.

Erstellen Sie ein Verzeichnis namens „C:\chef“.

Laden Sie Ihre Azure PowerShell-[Veröffentlichungseinstellungen](https://docs.microsoft.com/dynamics-nav/how-to--download-and-import-publish-settings-and-subscription-information) herunter.

## <a name="setup-chef-server"></a>Einrichten des Chef-Servers

Dieser Leitfaden setzt voraus, dass Sie sich für Hosted Chef registrieren.

Wenn Sie noch keinen Chef-Server verwenden, haben Sie folgende Möglichkeiten:

* Registrieren Sie sich für [Hosted Chef](https://manage.chef.io/signup). Dies ist die schnellste Möglichkeit zum Einstieg in Chef.
* Installieren Sie einen eigenständigen Chef-Server auf einem Linux-basierten Computer. Folgen Sie dazu den [Installationsanweisungen](https://docs.chef.io/install_server.html) in der [Chef-Dokumentation](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Erstellen eines verwalteten Chef-Kontos

Registrieren Sie sich [hier](https://manage.chef.io/signup) für ein gehostetes Chef-Konto.

Während der Registrierung werden Sie aufgefordert, eine neue Organisation zu erstellen.

![][3]

Sobald Ihre Organisation erstellt wurde, laden Sie das Starterkit herunter.

![][4]

> [!NOTE]
> Wenn Sie eine Meldung mit dem Hinweis erhalten, dass Ihre Schlüssel zurückgesetzt werden, ist es in Ordnung, wenn Sie fortfahren, da wir noch keine Infrastruktur konfiguriert haben.
>

Die ZIP-Datei dieses Starterkits enthält die Konfigurationsdateien und den Benutzerschlüssel für Ihre Organisation im Verzeichnis `.chef`.

Die Datei `organization-validator.pem` muss separat heruntergeladen werden, da es sich dabei um einen privaten Schlüssel handelt und private Schlüssel nicht auf dem Chef-Server gespeichert werden dürfen. Wählen Sie unter [Chef Manage](https://manage.chef.io/) die Option „Reset Validation Key“ (Validierungsschlüssel zurücksetzen) aus. Damit wird eine separat herunterladbare Datei bereitgestellt. Speichern Sie die Datei unter „C:\chef“.

### <a name="configuring-your-chef-workstation"></a>Konfigurieren der Chef-Arbeitsstation

Extrahieren Sie den Inhalt der Datei „chef-starter.zip“ im Ordner „C:\chef“.

Kopieren Sie alle Dateien unter „chef-starter\chef-repo\.chef“ in das Verzeichnis „C:\chef“.

Kopieren Sie die Datei `organization-validator.pem` in „C:\chef“, falls sie unter „C:\Downloads“ gespeichert wurde.

Ihr Verzeichnis sollte in etwa wie folgt aussehen:

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

Das Stammverzeichnis „C:\chef“ sollte jetzt fünf Dateien und vier Verzeichnisse (einschließlich des leeren Verzeichnisses „chef-repo“) enthalten.

### <a name="edit-kniferb"></a>Bearbeiten von „knife.rb“

Die PEM-Dateien enthalten die privaten Schlüssel für die Organisation und Administration für die Kommunikation, während die Datei „knife.rb“ die knife-Konfiguration enthält. Wir müssen die Datei knife.rb bearbeiten.

Öffnen Sie die Datei „knife.rb“ in einem Editor Ihrer Wahl. Die unveränderte Datei sollte etwa so aussehen:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Fügen Sie der Datei „knife.rb“ die folgenden Informationen:

validation_client_name   "myorg-validator"

validation_key           "#{current_dir}/myorg.pem"

Fügen Sie zudem die folgende Zeile hinzu, die den Namen Ihrer Azure-Datei für die Veröffentlichungseinstellungen widerspiegelt.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Ändern Sie „cookbook_path“, indem Sie „/../“ aus dem Pfad entfernen, damit dieser wie folgt aussieht:

    cookbook_path  ["#{current_dir}/cookbooks"]

Diese Zeilen stellen sicher, dass Knife auf „C:\chef\cookbooks“ verweist und bei Azure-Vorgängen unsere Azure-Datei mit den Veröffentlichungseinstellungen verwendet.

Die Datei „knife.rb“ sollte in etwa wie im folgenden Beispiel aussehen:

![][6]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

```rb
knife.rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "mynode"
client_key               "#{current_dir}/user.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
validation_client_name   "myorg-validator"
validation_key           ""#{current_dir}/myorg.pem"
cookbook_path            ["#{current_dir}/cookbooks"]
knife[:azure_publish_settings_file] = "yourfilename.publishsettings"
```

## <a name="install-chef-workstation"></a>Installieren der Chef-Arbeitsstation

Als Nächstes wird die Chef-Arbeitsstation [heruntergeladen und installiert](https://downloads.chef.io/chef-workstation/).
Installieren Sie die Chef-Arbeitsstation am standardmäßigen Speicherort. Diese Installation kann einige Minuten dauern.

Auf dem Desktop wird „CW PowerShell“ angezeigt. Dabei handelt es sich um eine Umgebung, die mit dem Tool geladen wurde, das Sie für die Interaktion mit den Chef-Produkten benötigen. CW PowerShell macht neue Ad-hoc-Befehle wie `chef-run` sowie herkömmliche Chef-CLI-Befehle wie `chef` verfügbar. Mit `chef -v` zeigen Sie Ihre installierte Version der Chef-Arbeitsstation und der Chef-Tools an. Die Version Ihrer Arbeitsstation können Sie ebenfalls überprüfen, indem Sie „About Chef Workstation“ (Über Chef Workstation) in der Chef Workstation-App auswählen.

`chef --version` sollte etwa Folgendes zurückgeben:

```
Chef Workstation: 0.2.29
  chef-run: 0.2.2
  Chef Client: 14.6.47x
  delivery-cli: master (6862f27aba89109a9630f0b6c6798efec56b4efe)
  berks: 7.0.6
  test-kitchen: 1.23.2
  inspec: 3.0.12
```

> [!NOTE]
> Die Reihenfolge der Pfade ist wichtig! Wenn die "opscode"-Pfade nicht in der richtigen Reihenfolge angegeben sind, können Probleme auftreten.
>

Starten Sie die Arbeitsstation neu, bevor Sie fortfahren.

### <a name="install-knife-azure"></a>Installieren von Knife Azure

In diesem Tutorial wird davon ausgegangen, dass Sie Azure Resource Manager für die Interaktion mit Ihrem virtuellen Computer verwenden.

Installieren Sie die Azure Knife-Erweiterung. Dadurch erhält Knife das Azure-Plug-In.

Führen Sie den folgenden Befehl aus:

    chef gem install knife-azure ––pre

> [!NOTE]
> Das Argument „-pre“ stellt sicher, dass Sie die neueste RC-Version des Azure-Plug-Ins für Knife erhalten, das Zugriff auf die neuesten APIs bietet.
>
>

Voraussichtlich werden gleichzeitig auch eine Reihe von Abhängigkeiten installiert.

![][8]

Führen Sie den folgenden Befehl aus, um sich zu vergewissern, dass alles korrekt konfiguriert ist:

    knife azure image list

Wenn alles richtig konfiguriert ist, wird eine Liste der verfügbaren Azure-Images angezeigt.

Herzlichen Glückwunsch. Die Arbeitsstation ist eingerichtet!

## <a name="creating-a-cookbook"></a>Erstellen eines Cookbooks

Ein Cookbook wird von Chef dazu verwendet, um eine Reihe von Befehlen zu definieren, die auf dem verwalteten Client ausgeführt werden sollen. Die Cookbook-Erstellung ist unkompliziert. Verwenden Sie einfach den Befehl **chef generate cookbook** zum Erstellen der Cookbook-Vorlage. Dieses Cookbook ist für einen Webserver vorgesehen, der automatisch IIS bereitstellt.

Führen Sie im Verzeichnis „C:\Chef“ den folgenden Befehl aus:

    chef generate cookbook webserver

Mit diesem Befehl werden eine Reihe von Dateien im Verzeichnis C:\Chef\cookbooks\webserver generiert. Als Nächstes definieren Sie den Satz von Befehlen, die der Chef-Client auf dem verwalteten virtuellen Computer ausführen soll.

Die Befehle werden in der Datei default.rb gespeichert. In dieser Datei definieren Sie eine Reihe von Befehlen, die IIS installieren, starten und eine Vorlagendatei in den Ordner „wwwroot“ kopieren.

Ändern Sie die Datei „C:\chef\cookbooks\webserver\recipes\default.rb“, und fügen Sie die folgenden Zeilen hinzu:

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

Speichern Sie die Datei, sobald Sie fertig sind.

## <a name="creating-a-template"></a>Erstellen einer Vorlage
In diesem Schritt generieren Sie eine Vorlagendatei, die als Seite „default.html“ verwendet wird.

Führen Sie den folgenden Befehl aus, um die Vorlage zu generieren:

    chef generate template webserver Default.htm

Navigieren Sie zur Datei `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb`. Fügen Sie der Datei etwas einfachen HTML-Code (Hello World) hinzu, und speichern Sie sie.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Hochladen des Cookbooks auf den Chef-Server
In diesem Schritt erstellen Sie eine Kopie des Cookbooks, das Sie auf dem lokalen Computer erstellt haben, und laden sie auf den von Chef gehosteten Server hoch. Sobald es hochgeladen ist, wird das Cookbook auf der Registerkarte **Richtlinie** angezeigt.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Bereitstellen eines virtuellen Computers mit Knife Azure
Stellen Sie einen virtuellen Azure-Computer bereit, und wenden Sie das Cookbook „Webserver“ an, das den IIS-Webdienst und die Standardwebseite installiert.

Verwenden Sie hierzu den Befehl **knife azure server create** .

Im Anschluss sehen Sie ein Beispiel für den Befehl.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Die Parameter sind selbsterklärend. Ersetzen Sie Ihre bestimmten Variablen, und führen Sie den Befehl dann aus.

> [!NOTE]
> Über die Befehlszeile automatisiere ich mithilfe des Parameters „–tcp-endpoints“ auch die Filterregeln für mein Endpunktnetzwerk. Ich habe die Ports 80 und 3389 geöffnet, um den Zugriff auf meine Webseite und die RDP-Sitzung zu gestatten.
>
>

Nachdem Sie den Befehl ausgeführt haben, wechseln Sie zum Azure-Portal, wo Sie die Bereitstellung des virtuellen Computers verfolgen können.

![][13]

Anschließend erscheint die Eingabeaufforderung.

![][10]

Nach Abschluss der Bereitstellung sollten Sie über Port 80 eine Verbindung mit dem Webdienst herstellen können, da Sie den Port beim Bereitstellen des virtuellen Computers mit dem Knife Azure-Befehl geöffnet haben. Da dieser virtuelle Computer der einzige in diesem Clouddienst ist, verknüpfen Sie ihn mit der URL des Clouddiensts.

![][11]

In diesem Beispiel wird kreativer HTML-Code verwendet.

Denken Sie daran, dass Sie die Verbindung auch im Azure-Portal per RDP-Sitzung über den Port 3389 herstellen können.

Vielen Dank! Starten Sie noch heute mit Azure, um Ihre Infrastruktur als Code umzusetzen!

<!--Image references-->
[2]: media/chef-automation/2.png
[3]: media/chef-automation/3.png
[4]: media/chef-automation/4.png
[5]: media/chef-automation/5.png
[6]: media/chef-automation/6.png
[7]: media/chef-automation/7.png
[8]: media/chef-automation/8.png
[9]: media/chef-automation/9.png
[10]: media/chef-automation/10.png
[11]: media/chef-automation/11.png
[13]: media/chef-automation/13.png


<!--Link references-->

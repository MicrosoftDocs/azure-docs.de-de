---
title: Azure Service Fabric Docker Compose-Bereitstellung (Vorschau)
description: Azure Service Fabric akzeptiert das Docker Compose-Format für eine einfachere Orchestrierung vorhandener Container mithilfe von Service Fabric. Diese Unterstützung befindet sich derzeit in der Vorschauphase.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: aljo, subramar
ms.openlocfilehash: da86ed9a3e6979bd1dc05aef6ef70c7b8533a8c1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60948833"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Docker Compose-Bereitstellungsunterstützung in Azure Service Fabric (Vorschau)

Docker verwendet die Datei [„docker-compose.yml“](https://docs.docker.com/compose) zum Definieren von Anwendungen mit mehreren Containern. Damit Kunden, die mit Docker vertraut sind, vorhandene Containeranwendungen einfacher in Azure Service Fabric orchestrieren können, haben wir eine Unterstützung für die Vorschauversion der Docker Compose-Bereitstellung nativ in die Plattform integriert. Service Fabric akzeptiert `docker-compose.yml`-Dateien ab Version 3 (und höher). 

Da diese Unterstützung als Vorschauversion verfügbar ist, wird nur ein Teil der Compose-Direktiven unterstützt. So werden beispielsweise Anwendungsupgrades nicht unterstützt. Sie können jedoch jederzeit Anwendungen entfernen und bereitstellen, statt sie zu aktualisieren.

Wenn Sie diese Vorschauversion verwenden möchten, erstellen Sie Ihren Cluster mindestens mit der Version 5.7 der Service Fabric-Laufzeit über das Azure-Portal (zusammen mit dem entsprechenden SDK). 

> [!NOTE]
> Das Feature befindet sich derzeit in der Vorschauphase und wird in Produktionsumgebungen nicht unterstützt.
> Die folgenden Beispiele basieren auf der Runtimeversion 6.0 und der SDK-Version 2.8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Bereitstellen einer Docker Compose-Datei in Service Fabric

Die folgenden Befehle erstellen eine Service Fabric-Anwendung (die den Namen `fabric:/TestContainerApp` hat), die wie jede andere Service Fabric-Anwendung überwacht und verwaltet werden kann. Sie können den jeweiligen Anwendungsnamen für Integritätsabfragen verwenden.
Service Fabric erkennt „DeploymentName“ als Bezeichner der Compose-Bereitstellung.

### <a name="use-powershell"></a>Verwenden von PowerShell

Erstellen Sie eine Service Fabric-Docker Compose-Bereitstellung aus einer „docker-compose.yml“-Datei, indem Sie den folgenden Befehl in PowerShell ausführen:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` und `RegistryPassword` verweisen auf den Benutzernamen und das Kennwort der Containerregistrierung. Nachdem Sie die Bereitstellung fertiggestellt haben, können Sie deren Status mit folgendem Befehl überprüfen:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Um die Compose-Bereitstellung über PowerShell zu löschen, verwenden Sie den folgenden Befehl:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Verwenden Sie den folgenden Befehl, um ein Compose-Bereitstellungsupgrade über PowerShell zu starten:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Verwenden Sie den folgenden Befehl, um ein Compose-Bereitstellungsupgrade über PowerShell zurückzusetzen:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

Nachdem das Upgrade akzeptiert wurde, kann der Upgradestatus mit dem folgenden Befehl nachverfolgt werden:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Verwenden der Azure Service Fabric CLI (sfctl)

Verwenden Sie alternativ den folgenden Service Fabric CLI-Befehl:

```azurecli
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Nachdem Sie die Bereitstellung erstellt haben, können Sie deren Status mit folgendem Befehl überprüfen:

```azurecli
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Verwenden Sie den folgenden Befehl, um die Compose-Bereitstellung zu löschen:

```azurecli
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Verwenden Sie den folgenden Befehl, um ein Compose-Bereitstellungsupgrade zu starten:

```azurecli
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Verwenden Sie den folgenden Befehl, um ein Compose-Bereitstellungsupgrade zurückzusetzen:

```azurecli
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

Nachdem das Upgrade akzeptiert wurde, kann der Upgradestatus mit dem folgenden Befehl nachverfolgt werden:

```azurecli
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Unterstützte Compose-Direktiven

Diese Vorschauversion unterstützt nur einen Teil der verfügbaren Konfigurationsoptionen im Compose V3-Format, einschließlich der folgenden Grundtypen:

* Dienste > Bereitstellen > Replikate
* Dienste > Bereitstellen > Platzierung > Einschränkungen
* Dienste > Bereitstellen > Ressourcen > Grenzwerte
    * -cpu-shares
    * -memory
    * -memory-swap
* Dienste > Befehle
* Dienste > Umgebung
* Dienste > Ports
* Dienste > Image
* Dienste > Isolation (nur für Windows)
* Dienste > Protokollieren > Treiber
* Dienste > Logging > Treiber > Optionen
* Volumen & Bereitstellen > Volumen

Der Cluster muss für das Erzwingen von Ressourcengrenzwerten wie in [Service Fabric-Ressourcengovernance](service-fabric-resource-governance.md) beschrieben eingerichtet werden. Alle anderen Docker Compose-Direktiven werden in dieser Vorschauversion nicht unterstützt.

### <a name="ports-section"></a>Abschnitt „Ports“

Geben Sie entweder das HTTP- oder HTTPS-Protokoll im Abschnitt „Ports“ an, das vom Listener für den Service Fabric-Dienst verwendet wird. Dadurch wird sichergestellt, dass das Endpunktprotokoll ordnungsgemäß mit dem Naming Service veröffentlicht wird, damit der Reverseproxy die Anforderungen weiterleiten kann:
* Geben Sie **/http** an, um zu unsicheren Service Fabric Compose-Diensten weiterzuleiten. Beispiel: - **"80:80/http"** .
* Geben Sie **/https** an, um zu sicheren Service Fabric Compose-Diensten weiterzuleiten. Beispiel: - **"443:443/https"** .

> [!NOTE]
> Die Syntax der Portabschnitte für /http und /https ist spezifisch für Service Fabric, um die ordnungsgemäße URL für den Service Fabric Listener zu registrieren.  Wenn die Docker Compose-Dateisyntax programmgesteuert überprüft wird, kann dies zu einem Validierungsfehler führen.

## <a name="servicednsname-computation"></a>ServiceDnsName-Berechnung

Wenn der in der Compose-Datei angegebene Dienstname ein vollqualifizierter Domänenname ist (d.h., er enthält einen Punkt [.]), lautet der in Service Fabric registrierte DNS-Name `<ServiceName>` einschließlich des Punkts. Andernfalls wird jedes Pfadsegment im Anwendungsnamen eine Domänenbezeichnung im DNS-Namen des Diensts. Dabei wird das erste Pfadsegment die Bezeichnung für die Domäne der obersten Ebene.

Wenn also der angegebene Anwendungsname z.B. `fabric:/SampleApp/MyComposeApp` lautet, wird `<ServiceName>.MyComposeApp.SampleApp` als DNS-Name registriert.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Compose-Bereitstellung (Instanzdefinition) im Vergleich zum Service Fabric-App-Modell (Typdefinition)

Eine „docker-compose.yml“-Datei beschreibt einen bereitstellbaren Satz von Containern, einschließlich ihrer Eigenschaften und Konfigurationen.
Die Datei kann z.B. Umgebungsvariablen und Ports enthalten. Bereitstellungsparameter wie Platzierungsbeschränkungen, Ressourcengrenzwerte und DNS-Namen können ebenfalls in der Datei „docker-compose.yml“ angegeben werden.

Das [Service Fabric-Anwendungsmodell](service-fabric-application-model.md) verwendet Dienst- und Anwendungstypen. Dabei können Sie über viele Anwendungsinstanzen desselben Typs verfügen. Sie können beispielsweise eine Anwendungsinstanz pro Kunde haben. Dieses typbasierte Modell unterstützt die Registrierung mehrerer Versionen desselben Anwendungstyps in der Runtime.

Für Kunde A kann z.B. eine Anwendung mit Typ 1.0 von AppTypeA instanziiert werden, während für Kunde B eine andere Anwendung mit demselben Typ und derselben Version instanziiert wird. Die Applikationstypen werden im Applikationsmanifest festgelegt. Bei der Erstellung der Anwendung werden auch der Applikationsname und die Bereitstellungsparameter angegeben.

Obwohl dieses Modell Flexibilität bietet, planen wir auch die Unterstützung eines einfacheren, instanzbasierten Bereitstellungsmodells, bei dem sich die Typen implizit aus der Manifestdatei ergeben. In diesem Modell erhält jede Anwendung ein eigenes unabhängiges Manifest. Eine Vorschau dieser Vorgehensweise bieten wir durch die Unterstützung von „docker-compose.yml“-Dateien, einem instanzenbasierten Bereitstellungsformat.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Service Fabric-Anwendungsmodell](service-fabric-application-model.md).
* [Erste Schritte mit der Service Fabric CLI](service-fabric-cli.md)

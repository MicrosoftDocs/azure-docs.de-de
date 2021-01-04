---
title: Verwalten von Apps für mehrere Umgebungen
description: Azure Service Fabric-Anwendungen können in Clustern ausgeführt werden, die eine Größe von einem Computer bis zu Tausenden von Computern aufweisen. In einigen Fällen möchten Sie die Anwendung für diese verschiedenen Umgebungen unterschiedlich konfigurieren. In diesem Artikel wird beschrieben, wie unterschiedliche Anwendungsparameter pro Umgebung definiert werden.
ms.topic: conceptual
ms.date: 02/23/2018
ms.openlocfilehash: 51bc77abca8ce2b3878df1c1859203618d3e2e27
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574444"
---
# <a name="manage-applications-for-multiple-environments"></a>Verwalten von Anwendungen für mehrere Umgebungen

Mit Azure Service Fabric-Clustern können Sie Cluster mit einem oder mehreren Tausend Computern erstellen. In den meisten Fällen müssen Sie Ihre Anwendung unter verschiedenen Clusterkonfigurationen bereitstellen: in Ihrem lokalen Entwicklungscluster, in einem gemeinsam verwendeten Entwicklungscluster und in Ihrem Produktionscluster. Alle diese Cluster gelten als unterschiedliche Umgebungen, in denen Ihr Code ausgeführt werden muss. Anwendungsbinärdateien können ohne Änderungen in diesem breiten Spektrum ausgeführt werden. Häufig möchten Sie die Anwendung jedoch anders konfigurieren.

Betrachten Sie zwei einfache Beispiele:
  - Der Dienst lauscht an einem festgelegten Port, der jedoch in den verschiedenen Umgebungen unterschiedlich sein muss.
  - Sie müssen in den verschiedenen Umgebungen unterschiedliche Bindungsanmeldeinformationen für eine Datenbank angeben.

## <a name="specifying-configuration"></a>Angeben der Konfiguration

Die Konfiguration, die Sie bereitstellen, kann in zwei Kategorien unterteilt werden:

- Konfiguration der Dienstausführung
  - Dazu gehören z.B. die Portnummer für einen Endpunkt oder die Anzahl der Instanzen eines Diensts.
  - Diese Konfiguration wird in der Manifestdatei der Anwendung oder des Diensts angegeben.
- Konfiguration für den Anwendungscode
  - Dazu gehören z.B. Bindungsinformationen für eine Datenbank.
  - Diese Konfiguration kann über Konfigurationsdateien oder Umgebungsvariablen bereitgestellt werden.

> [!NOTE]
> Nicht alle Attribute in der Manifestdatei der Anwendung oder des Diensts unterstützen Parameter.
> In solchen Fällen müssen Sie die Zeichenfolgen im Rahmen des Bereitstellungsworkflows ersetzen. In Azure DevOps können Sie eine Erweiterung wie „Replace Tokens: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens“ verwenden. In Jenkins könnten Sie auch eine Skriptaufgabe ausführen, um die Werte zu ersetzen.
>

## <a name="specifying-parameters-during-application-creation"></a>Angeben von Parametern während der Anwendungserstellung

Sie können beim Erstellen von benannten Anwendungsinstanzen in Service Fabric Parameter übergeben. Die Vorgehensweise hängt davon ab, wie Sie die Anwendungsinstanz erstellen.

  - In PowerShell akzeptiert das Cmdlet [`New-ServiceFabricApplication`](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) die Anwendungsparameter als Hashtabelle.
  - Bei sfctl akzeptiert der Befehl [`sfctl application create`](./service-fabric-sfctl-application.md#sfctl-application-create) Parameter als JSON-Zeichenfolge. Das Skript „install.sh“ verwendet sfctl.
  - Visual Studio stellt Ihnen im Ordner „Parameters“ im Anwendungsprojekt eine Reihe von Parameterdateien zur Verfügung. Diese Parameterdateien werden bei der Veröffentlichung aus Visual Studio heraus unter Verwendung von Azure DevOps Services oder Azure DevOps Server verwendet. In Visual Studio werden die Parameterdateien an das Skript „Deploy-FabricApplication.ps1“ übergeben.

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln wird gezeigt, wie einige der hier beschriebenen Konzepte verwendet werden:

- [Angeben von Umgebungsvariablen für Dienste in Service Fabric](service-fabric-how-to-specify-environment-variables.md)
- [Angeben der Portnummer eines Diensts mithilfe von Parametern in Service Fabric](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Parametrisieren von Konfigurationsdateien](service-fabric-how-to-parameterize-configuration-files.md)

- [Referenz zu Umgebungsvariablen](service-fabric-environment-variables-reference.md)

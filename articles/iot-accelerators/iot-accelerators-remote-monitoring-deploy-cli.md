---
title: 'Bereitstellen der Remoteüberwachungslösung mithilfe der CLI: Azure | Microsoft-Dokumentation'
description: In dieser Schrittanleitung wird gezeigt, wie Sie mithilfe der CLI den Solution Accelerator für die Remoteüberwachung bereitstellen.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: ea96b2b996ea79efacdcda50c6370f25e26e0aa2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447011"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Bereitstellen des Solution Accelerators für die Remoteüberwachung mithilfe der CLI

In dieser Schrittanleitung wird gezeigt, wie Sie den Solution Accelerator für die Remoteüberwachung bereitstellen. Sie stellen die Lösung über die Befehlszeilenschnittstelle bereit. Sie können die Lösung auch über die webbasierte Benutzeroberfläche auf azureiotsolutions.com bereitstellen. Informationen zu dieser Möglichkeit finden Sie im Schnellstart [Ausprobieren einer cloudbasierten Lösung für die Remoteüberwachung](quickstart-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>Voraussetzungen

Für die Bereitstellung des Solution Accelerators für die Remoteüberwachung benötigen Sie ein aktives Azure-Abonnement.

Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

Zum Ausführen der Befehlszeilenschnittstelle muss [Node.js](https://nodejs.org/) auf Ihrem lokalen Computer installiert sein.

## <a name="install-the-cli"></a>Installieren der Befehlszeilenschnittstelle

Führen Sie zum Installieren der Befehlszeilenschnittstelle den folgenden Befehl in der Befehlszeilenumgebung aus:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Anmelden bei der Befehlszeilenschnittstelle

Damit Sie den Solution Accelerator bereitstellen können, müssen Sie sich über die CLI bei Ihrem Azure-Abonnement anmelden:

```cmd/sh
pcs login
```

Befolgen Sie die Anweisungen auf dem Bildschirm, um den Anmeldevorgang abzuschließen.

## <a name="deployment-options"></a>Bereitstellungsoptionen

Beim Bereitstellen des Solution Accelerators sind mehrere Optionen verfügbar, mit denen der Bereitstellungsvorgang konfiguriert wird:

| Option | Werte | BESCHREIBUNG |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | Eine Bereitstellung des Typs _basic_ ist für Test- und Demonstrationszwecke vorgesehen. Dabei werden alle Microservices auf einem einzelnen virtuellen Computer bereitgestellt. Eine Bereitstellung des Typs _Standard_ ist für die Produktion vorgesehen. Die Microservices werden auf mehreren virtuellen Computern bereitgestellt. Ein _lokale_ Bereitstellung konfiguriert einen Docker-Container für die Ausführung der Microservices auf Ihrem lokalen Computer und verwendet Azure-Clouddienste wie Storage und Cosmos DB. |
| Laufzeit | `dotnet`, `java` | Wählt die Implementierung der Sprache für die Microservices aus. |

Informationen zur Verwendung der lokalen Bereitstellung finden Sie unter [Lokales Bereitstellen des Solution Accelerators für die Remoteüberwachung](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="basic-and-standard-deployments"></a>Bereitstellungen vom Typ „Basic“ und „Standard“

In diesem Abschnitt werden die wichtigsten Unterschiede zwischen einer Basic- und einer Standard-Bereitstellung zusammengefasst.

### <a name="basic"></a>Basic

Sie können eine Basic-Bereitstellung über [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) oder die Befehlszeilenschnittstelle ausführen.

Die Basic-Bereitstellung ist auf die Demonstration der Lösung ausgelegt. Zur Kostensenkung werden alle Microservices auf einem einzelnen virtuellen Computer bereitgestellt. Diese Bereitstellung nutzt keine produktionsbereite Architektur.

Bei einer Bereitstellung vom Typ „Basic“ werden die folgenden Dienste in Ihrem Azure-Abonnement erstellt:

| Count | Resource                       | Type         | Verwendung |
|-------|--------------------------------|--------------|----------|
| 1     | [Virtueller Linux-Computer](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 v2  | Hosten von Microservices |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1: Tarif „Standard“ | Geräteverwaltung und Kommunikation |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Speichern von Konfigurationsdaten, Regeln, Warnungen und anderer Cold Storage |  
| 1     | [Azure Storage-Konto](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Speicher für VM und Streamingprüfpunkte |
| 1     | [Web Application](https://azure.microsoft.com/services/app-service/web/)        |                 | Hosten der Front-End-Webanwendung |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Verwalten von Benutzeridentitäten und Sicherheit |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Anzeigen von Ressourcenspeicherorten |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 Einheiten              | Aktivieren von Echtzeitanalysen |
| 1     | [Azure Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Umfangreiche Bereitstellung von Geräten |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1-Einheit              | Speicher für Nachrichtendaten und ermöglicht eine detaillierte Telemetrieanalyse |

### <a name="standard"></a>Standard

Sie können eine standardmäßige Bereitstellung nur über die Befehlszeilenschnittstelle ausführen.

Eine Standard-Bereitstellung ist für eine Produktionsumgebung geeignet und kann von einem Entwickler angepasst und erweitert werden. Wenn Sie eine produktionsbereite, auf Skalierbarkeit und Erweiterbarkeit ausgelegte Architektur anpassen möchten, verwenden Sie die Bereitstellungsoption „Standard“. Anwendungsmicroservices werden als Docker-Container erstellt und mithilfe von Azure Kubernetes Service bereitgestellt. Die Microservices werden vom Kubernetes-Orchestrator bereitgestellt, skaliert und verwaltet.

Bei einer Bereitstellung vom Typ „Standard“ werden die folgenden Dienste in Ihrem Azure-Abonnement erstellt:

| Count | Resource                                     | SKU/Größe      | Verwendung |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Nutzen Sie einen vollständig verwalteten Orchestrierungsdienst für Kubernetes-Container mit standardmäßig drei Agents.|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2: Tarif „Standard“ | Geräteverwaltung und -steuerung |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Speichern von Konfigurationsdaten und Gerätetelemetrie wie Regeln, Warnungen und Meldungen |
| 5     | [Azure-Speicherkonten](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | Vier für VM-Speicher, eins für die Streamingprüfpunkte |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Application Gateway über SSL |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Verwalten von Benutzeridentitäten und Sicherheit |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Anzeigen von Ressourcenspeicherorten |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 Einheiten              | Aktivieren von Echtzeitanalysen |
| 1     | [Azure Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Umfangreiche Bereitstellung von Geräten |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1-Einheit              | Speicher für Nachrichtendaten und ermöglicht eine detaillierte Telemetrieanalyse |

> [!NOTE]
> Preisinformationen für diese Dienste finden Sie unter [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing). Ausführliche Informationen zu Verbrauch und Abrechnung für Ihr Abonnement finden Sie im [Azure-Portal](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Bereitstellen des Solution Accelerators

Bereitstellungsbeispiele:

### <a name="example-deploy-net-version"></a>Beispiel: Bereitstellen der .NET-Version

Das folgende Beispiel veranschaulicht die Bereitstellung der grundlegenden .NET-Version des Solution Accelerators für die Remoteüberwachung:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Beispiel: Bereitstellen der Java-Version

Das folgende Beispiel veranschaulicht die Bereitstellung der Java-Standardversion des Solution Accelerators für die Remoteüberwachung:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>Optionen des Befehls „pcs“

Beim Ausführen des Befehls `pcs` zum Bereitstellen einer Lösung müssen Sie folgende Elemente angeben:

- Einen Namen für die Lösung. Dieser Name muss eindeutig sein.
- Das zu verwendende Azure-Abonnement.
- Einen Speicherort.
- Anmeldeinformationen für die virtuellen Computer, auf denen die Microservices gehostet werden. Sie können diese Anmeldeinformationen verwenden, um zur Problembehandlung auf die virtuellen Computer zuzugreifen.

Nach Abschluss des Befehls `pcs` wird die URL des neuen Solution Accelerators angezeigt. Mit dem Befehl `pcs` wird auch die Datei `{deployment-name}-output.json` mit Informationen (etwa dem Namen der erstellten IoT Hub-Instanz) erstellt.

Wenn Sie weitere Informationen zu den Befehlszeilenparametern benötigen, führen Sie den folgenden Befehl aus:

```cmd/sh
pcs -h
```

Weitere Informationen zur Befehlszeilenschnittstelle finden Sie unter [How to use the CLI](https://github.com/Azure/pcs-cli/blob/master/README.md) (Verwenden der Befehlszeilenschnittstelle).

## <a name="next-steps"></a>Nächste Schritte

In dieser Schrittanleitung wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Konfigurieren des Solution Accelerators
> * Bereitstellen des Solution Accelerators
> * Anmelden beim Solution Accelerator

Nach Bereitstellung der Remoteüberwachungslösung können Sie sich als Nächstes [mit den Funktionen des Lösungsdashboards vertraut machen](./quickstart-remote-monitoring-deploy.md).

<!-- Next how-to guides in the sequence -->

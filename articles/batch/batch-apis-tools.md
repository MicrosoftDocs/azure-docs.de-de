---
title: APIs und Tools für Entwickler – Azure Batch | Microsoft-Dokumentation
description: Es werden die APIs und Tools beschrieben, die zum Entwickeln von Lösungen mit dem Azure Batch-Dienst verfügbar sind.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 912e3342b4e8031b4404dffb56f1add2cc705f8e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60721758"
---
# <a name="overview-of-batch-apis-and-tools"></a>Übersicht über Batch-APIs und -Tools

Die Verarbeitung paralleler Workloads mit Azure Batch wird üblicherweise programmgesteuert mit einer der Batch-APIs durchgeführt. Ihre Clientanwendung oder Ihr Dienst kann mithilfe der Batch-APIs mit dem Batch-Dienst kommunizieren. Mithilfe der Batch-APIs können Sie Pools mit Computeknoten (virtuelle Computer oder Clouddienste) erstellen und verwalten. Anschließend können Sie die Ausführung von Aufträgen und Aufgaben auf diesen Knoten planen. 

Sie können größere Workloads für Ihr Unternehmen auf effiziente Weise verarbeiten oder ein Dienst-Front-End für Ihre Kunden bereitstellen, damit sie Aufträge und Aufgaben – bedarfsgesteuert oder nach Zeitplan – auf einem, Hunderten oder Tausenden von Knoten ausführen können. Sie können Azure Batch auch als Teil eines größeren Workflows nutzen und mithilfe von Tools wie [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json) verwalten.

> [!TIP]
> Wenn Sie ein besseres Verständnis der Batch-API-Funktionen entwickeln möchten, können Sie sich [Übersicht über Batch-Features für Entwickler](batch-api-basics.md)durchlesen.
> 
> 

## <a name="azure-accounts-for-batch-development"></a>Azure-Konten für die Batch Entwicklung
Beim Entwickeln von Batch-Lösungen verwenden Sie die folgenden Konten in Ihrem Azure-Abonnement:

* **Batch-Konto:** Azure Batch-Ressourcen (einschließlich Pools, Computeknoten, Aufträgen und Aufgaben) werden einem [Azure Batch-Konto](batch-api-basics.md#account) zugeordnet. Wenn Ihre Anwendung eine Anforderung an den Batch-Dienst richtet, wird die Anforderung anhand des Azure Batch-Kontonamens, der URL des Kontos und entweder eines Zugriffsschlüssels oder eines Azure Active Directory-Tokens authentifiziert. Die [Erstellung eines Batch-Kontos](batch-account-create-portal.md) kann über das Azure-Portal oder programmgesteuert erfolgen.
* **Speicherkonto:** Batch enthält integrierte Unterstützung zum Verwenden von Dateien in [Azure Storage][azure_storage]. In nahezu jedem Batch-Szenario wird Azure Blob Storage für das Staging der Programme, die von Ihren Aufgaben ausgeführt werden, und für die dabei verarbeiteten Daten sowie für die Speicherung der generierten Ausgabedaten verwendet. Informationen zu den Optionen für Speicherkonten in Batch finden Sie unter [Entwickeln von parallelen Computelösungen in größerem Umfang mit Batch](batch-api-basics.md#azure-storage-account).

## <a name="batch-service-apis"></a>Batch-Dienst-APIs

Ihre Anwendungen und Dienste können direkte REST-API-Aufrufe ausgeben oder Ihre Azure Batch-Workloads unter Verwendung einer oder mehrerer der folgenden Clientbibliotheken ausführen und verwalten:

| API | API-Referenz | Download | Tutorial | Codebeispiele | Weitere Informationen |
| --- | --- | --- | --- | --- | --- |
| **Batch REST (in englischer Sprache)** |[docs.microsoft.com][batch_rest] |– |- |- | [Unterstützte Versionen](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet][api_net_nuget] |[Tutorial](tutorial-parallel-dotnet.md) |[GitHub][api_sample_net] | [Anmerkungen zu dieser Version](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[docs.microsoft.com][api_python] |[PyPI][api_python_pypi] |[Tutorial](tutorial-parallel-python.md)|[GitHub][api_sample_python] | [Infodatei](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[docs.microsoft.com][api_nodejs] |[npm][api_nodejs_npm] |[Tutorial](batch-nodejs-get-started.md) |- | [Infodatei](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[docs.microsoft.com][api_java] |[Maven][api_java_jar] |- |[Infodatei][api_sample_java] | [Infodatei](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>Batch Management-APIs

Die Azure Resource Manager-APIs für Batch ermöglichen den programmgesteuerten Zugriff auf Batch-Konten. Mit diesen APIs können Sie Batch-Konten, Kontingente, Anwendungspakete und andere Ressourcen über den Microsoft.Batch-Anbieter programmgesteuert verwalten.  

| API | API-Referenz | Download | Tutorial | Codebeispiele |
| --- | --- | --- | --- | --- |
| **Batch Management – REST** |[docs.microsoft.com][api_rest_mgmt] |– |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Management .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet][api_net_mgmt_nuget] | [Tutorial](batch-management-dotnet.md) |[GitHub][api_sample_net] |
| **Batch Management – Python** |[docs.microsoft.com][api_python_mgmt] |[PyPI][api_python_mgmt_pypi] |- |- |
| **Batch Management – Node.js** |[docs.microsoft.com][api_nodejs_mgmt] |[npm][api_nodejs_mgmt_npm] |- |- | 
| **Batch Management – Java** |- |[Maven][api_java_mgmt_jar] |- |- |
## <a name="batch-command-line-tools"></a>Batch-Befehlszeilentools

Diese Befehlszeilentools bieten die gleiche Funktionalität wie die Batch-Dienst- und Batch Management-APIs: 

* [Batch PowerShell-Cmdlets][batch_ps]: Mit den Azure Batch-Cmdlets im [Azure PowerShell](/powershell/azure/overview)-Modul können Sie Batch-Ressourcen mit PowerShell verwalten.
* [Azure CLI](/cli/azure): Die Azure CLI ist ein plattformübergreifendes Toolset mit Shellbefehlen für die Interaktion mit vielen Azure-Diensten, z.B. dem Batch-Dienst und dem Batch Management-Dienst. Weitere Informationen zur Verwendung der Azure CLI mit Batch finden Sie unter [Verwalten von Batch-Ressourcen mit der Azure CLI](batch-cli-get-started.md).

## <a name="other-tools-for-application-development"></a>Weitere Tools für die Anwendungsentwicklung

Folgende weitere Tools können beim Entwickeln und Debuggen Ihrer Batch-Anwendungen und -Dienste hilfreich sein:

* [Azure-Portal][portal]: Über das Azure-Portal können Sie Batch-Pools, -Aufträge und -Aufgaben erstellen, überwachen und löschen. Sie können die Statusinformationen für diese und andere Ressourcen beim Ausführen der Aufträge anzeigen und sogar Dateien von den Computeknoten in Ihre Pools herunterladen. Beispielsweise können Sie bei der Problembehandlung die Datei `stderr.txt` einer fehlerhaften Aufgabe herunterladen. Außerdem können Sie RDP-Dateien (Remotedesktop) herunterladen, die sich zum Anmelden an Computeknoten eignen.
* [Azure Batch Explorer][batch_labs]: Batch Explorer (früher: BatchLabs) ist ein kostenloses eigenständiges Clienttool mit zahlreichen Features, das Sie beim Erstellen, Debuggen und Überwachen von Azure Batch-Anwendungen unterstützt. Ein Installationspaket für Mac, Linux oder Windows können Sie [hier](https://azure.github.io/BatchExplorer/) herunterladen.
* [Azure Batch Shipyard:](https://github.com/Azure/batch-shipyard) Batch Shipyard ist ein Tool, das Sie beim Bereitstellen, Ausführen und Überwachen der containerbasierten Batchverarbeitung und von HPC-Workloads in Azure Batch unterstützt.
* [Azure Storage-Explorer:][storage_explorer] Dies ist zwar kein Azure Batch-Tool, aber der Storage-Explorer ist ein weiteres wertvolles Tool, das Sie beim Entwickeln und Debuggen Ihrer Batch-Lösungen einsetzen können.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- Weitere Informationen zum Protokollieren von Ereignissen in der Batch-Anwendung finden Sie unter [Protokollereignisse für die Diagnoseauswertung und -überwachung von Batch-Lösungen](batch-diagnostics.md). Eine Referenz zu Ereignissen, die vom Batch-Dienst ausgelöst werden, finden Sie unter [Batch-Analysen](batch-analytics.md).
- Informationen zu Umgebungsvariablen für Serverknoten finden Sie unter [Umgebungsvariablen für Azure Batch-Computeknoten](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich die [Übersicht über Batch-Features für Entwickler](batch-api-basics.md) an. Diese enthält wichtige Informationen für Entwickler, die sich auf die Verwendung von Batch vorbereiten möchten. Der Artikel enthält ausführliche Informationen zu Batch-Dienstressourcen wie Pools, Knoten, Aufträgen und Aufgaben sowie zu den zahlreichen API-Funktionen, die beim Erstellen der Batch-Anwendung verwendet werden können.
* [Erste Schritte mit der Azure Batch-Bibliothek für .NET](tutorial-parallel-dotnet.md) erfahren Sie, wie Sie mit C# und der Batch .NET-Bibliothek eine einfache Workload mit einem allgemeinen Batch-Workflow ausführen. Es stehen auch eine [Python-Version](tutorial-parallel-python.md) und ein [Node.js-Tutorial](batch-nodejs-get-started.md) zur Verfügung.
* Laden Sie die [Codebeispiele unter GitHub][github_samples] herunter, um sich zu informieren, wie C# und Python mit Batch verknüpft werden können, um Beispielworkloads zu planen und zu verarbeiten.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: /java/api/overview/azure/batch
[api_java_mgmt]: /java/api/overview/azure/batch/managementapi
[api_java_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_java_mgmt_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: /dotnet/api/overview/azure/batch/
[api_net_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[api_rest_mgmt]: /rest/api/batchmanagement/
[api_net_mgmt]: /dotnet/api/overview/azure/batch/management
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: /javascript/api/overview/azure/batch/client
[api_nodejs_mgmt]: /javascript/api/overview/azure/batch/management
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_nodejs_mgmt_npm]: https://www.npmjs.com/package/azure-arm-batch
[api_python]: /python/api/overview/azure/batch/client
[api_python_mgmt]: /python/api/overview/azure/batch/management
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_python_mgmt_pypi]: https://pypi.python.org/pypi/azure-mgmt-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/module/az.batch/
[batch_rest]: /rest/api/batchservice/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_labs]: https://azure.github.io/BatchExplorer/
[storage_explorer]: https://storageexplorer.com/
[portal]: https://portal.azure.com

---
title: Azure Service Fabric-Umgebungsvariablen
description: Erfahren Sie mehr über Umgebungsvariablen in Azure Service Fabric. Enthält einen Verweis auf eine vollständige Liste der Variablen und ihrer Verwendungsmöglichkeiten.
ms.topic: reference
ms.date: 12/07/2017
ms.openlocfilehash: b70249daa439b5a631b5a84b10c47f082ce75985
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574580"
---
# <a name="service-fabric-environment-variables"></a>Service Fabric-Umgebungsvariablen

Service Fabric verfügt über Umgebungsvariablen, die für jede Dienstinstanz festgelegt werden. Im Folgenden finden Sie die vollständige Liste der Umgebungsvariablen:

| Umgebungsvariable                         | BESCHREIBUNG                                                            | Beispiel                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Der Fabric-URI-Name der Anwendung                                 | fabric:/MeineAnwendung                                                |
| Fabric_CodePackageName                       | Name des Codepakets, zu dem der Prozess gehört              | Code                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*Name_Dienstendpunkt*     | IP-Adresse oder FQDN des Endpunkts                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*Name_Dienstendpunkt*              | Portnummer für den Endpunkt                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Protokollordner                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MeinAnwendungstyp_App12\\\\log      |
| Fabric_Folder_App_Temp                       | Ordner für temporäre Dateien                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MeinAnwendungstyp_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Arbeitsordner                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MeinAnwendungstyp_App12\\\\work     |
| Fabric_Folder_Application                    | Stammordner der Anwendung                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MeinAnwendungstyp_App12             |
| Fabric_IsContainerHost                       | Boolescher Wert, der angibt, ob der Prozess ein Container ist                   | false                                                                |
| Fabric_NodeId                                | Knoten-ID des Knotens, auf dem der Prozess ausgeführt wird                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | IP-Adresse oder FQDN des Knotens wie in der Manifestdatei des Clusters | „localhost“ oder 10.0.0.1                                                |
| Fabric_NodeName                              | Name des Knotens, auf dem der Prozess ausgeführt wird                          | _Node_0                                                              |
| Fabric_ServiceName                           | Der Fabric-URI-Name des Diensts, wenn dieser im ExclusiveProcess-Modus gehostet wird. Dieser Variablenwert ist nur verfügbar, wenn Sie den Dienst mit „ServicePackageActivationMode ExclusiveProcess“ erstellen.  | fabric:/MyApplication/MyService                                               |
| Fabric_ServicePackageActivationId            | Aktivierungs-ID des Dienstpakets                                         | Eine GUID                                                               |
| Fabric_ServicePackageName                    | Name des Dienstpakets, zu dem der Prozess gehört                     | Web1Pkg                                                              |

Interne Umgebungsvariablen, die von der Service Fabric-Runtime verwendet werden:

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName

---
title: Allgemeine FabricClient-Ausnahmen | Microsoft Docs
description: Beschreibt die allgemeinen Ausnahmen und Fehler, die von den FabricClient-APIs beim Ausführen der Anwendungs- und Clusterverwaltungsvorgänge ausgelöst werden können.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: bb821313-b221-479f-b08e-36cf07e60a07
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/20/2018
ms.author: oanapl
ms.openlocfilehash: 5bf17f4ced6bb01d8b62b6fa40ed1aeffe6f712f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60946540"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Allgemeine Ausnahmen und Fehler bei der Arbeit mit FabricClient-APIs
Mit [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient)-APIs können Cluster- und Anwendungsadministratoren administrative Aufgaben für eine Service Fabric-Anwendung, einen Service Fabric-Dienst oder einen Service Fabric-Cluster ausführen. Dazu zählen z. B. Anwendungsbereitstellung, Upgrade und Entfernung, Überprüfung der Integrität eines Clusters oder Testen eines Diensts. Anwendungsentwickler und Clusteradministratoren können die FabricClient-APIs verwenden, um Tools zum Verwalten der Service Fabric-Cluster und -Anwendungen zu entwickeln.

Viele verschiedene Vorgänge können mit FabricClient ausgeführt werden.  Jede Methode kann Ausnahmen für Fehler aufgrund von falschen Eingaben, Laufzeitfehlern oder vorübergehenden Infrastrukturproblemen auslösen.  Bitte entnehmen Sie der API-Referenzdokumentation, welche Ausnahmen von bestimmten Methoden ausgelöst werden. Einige Ausnahmen können jedoch von verschiedenen [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient)-APIs ausgelöst werden. Die folgende Tabelle enthält die Ausnahmen, die für alle FabricClient-APIs gelten.

| Ausnahme | Grund für Auslösung |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |Das [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient)-Objekt befindet sich im geschlossenen Zustand. Löschen Sie das [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient)-Objekt, das Sie verwenden, und instanziieren Sie ein neues [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient)-Objekt. |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |Beim Vorgang ist ein Timeout aufgetreten. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) wird zurückgegeben, wenn die Ausführung des Vorgangs länger dauert, als in MaxOperationTimeout vorgegeben. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |Die Zugriffsüberprüfung für den Vorgang ist fehlgeschlagen. „E_ACCESSDENIED“ wird zurückgegeben. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |Beim Ausführen des Vorgangs ist ein Laufzeitfehler aufgetreten. Jede der FabricClient-Methoden kann potenziell [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) auslösen. Die [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode)-Eigenschaft gibt die genaue Ursache der Ausnahme an. Fehlercodes sind in der [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode)-Enumeration definiert. |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |Der Vorgang ist aufgrund eines vorübergehenden Fehlerzustands beliebiger Art fehlgeschlagen. Beispielsweise kann ein Vorgang fehlschlagen, da ein Quorum von Replikaten vorübergehend nicht erreichbar ist. Vorübergehende Ausnahmen entsprechen fehlgeschlagenen Vorgängen, die wiederholt werden können. |

Einige häufige [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode)-Fehler, die in einer [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) zurückgegeben werden können:

| Error | Bedingung |
| --- |:--- |
| CommunicationError |Ein Kommunikationsfehler verursachte das Fehlschlagen des Vorgangs – wiederholen Sie den Vorgang. |
| InvalidCredentialType |Der Anmeldeinformationstyp ist ungültig. |
| InvalidX509FindType |Der X509FindType ist ungültig. |
| InvalidX509StoreLocation |Der X509-Speicherort ist ungültig. |
| InvalidX509StoreName |Die X509-Speichername ist ungültig. |
| InvalidX509Thumbprint |Die X509-Zertifikatfingerabdruck-Zeichenfolge ist ungültig. |
| InvalidProtectionLevel |Die Schutzebene ist ungültig. |
| InvalidX509Store |Der X509-Zertifikatspeicher kann nicht geöffnet werden. |
| InvalidSubjectName |Der Antragstellername ist ungültig. |
| InvalidAllowedCommonNameList |Das Format der Zeichenfolge der Liste allgemeiner Namen ist ungültig. Es sollte eine durch Trennzeichen getrennte Liste sein. |


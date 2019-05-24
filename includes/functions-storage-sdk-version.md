---
title: Includedatei
description: Includedatei
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: f8733ef907b8f31ace7ea72f705ba1b37d1adece
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132389"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Azure Storage SDK-Version in Functions 1.x

In Functions 1.x verwenden die Storage-Trigger und -Bindungen Version 7.2.1 des Azure Storage SDK (NuGet-Paket [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1)). Wenn Sie auf eine andere Version des Storage SDK verweisen und in Ihrer Funktionssignatur an einen Storage SDK-Typ binden, meldet die Functions-Runtime möglicherweise, dass Bindungen an diesen Typ nicht möglich sind. Die Lösung besteht darin, sicherzustellen, dass Ihr Projekt auf [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) verweist.

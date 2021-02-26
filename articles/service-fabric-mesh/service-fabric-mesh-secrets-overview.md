---
title: Speichern und Verwenden von Azure Service Fabric Mesh-Anwendungsgeheimnissen
description: Service Fabric Mesh unterstützt Geheimnisse als Azure-Ressourcen. Im Folgenden erfahren Sie, wie Sie Geheimnisse mit Ihren Service Fabric Mesh-Anwendungen speichern und verwalten können.
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 85b4bb6ee4d76a7115c9ebdd1466049afe6683c6
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625531"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric Mesh-Anwendungsgeheimnisse

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen werden nicht mehr über die Service Fabric Mesh-API gestattet. Unterstützung für vorhandene Bereitstellungen wird bis zum 28. April 2021 fortgesetzt.
> 
> Einzelheiten finden Sie unter [Einstellung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Service Fabric Mesh unterstützt Geheimnisse als Azure-Ressourcen. Bei einem Service Fabric Mesh-Geheimnis kann es sich um beliebige sensible Informationen in Textform handeln, z.B. Speicherverbindungszeichenfolgen, Kennwörter oder andere Werte, die sicher gespeichert und übertragen werden sollten.

![Übersicht über Mesh-Geheimnisse][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Mesh-Geheimnisressourcen
Ein Mesh-Anwendungsgeheimnis umfasst Folgendes:
* Eine Ressource **Geheimnisse**, bei der es sich um einen Container zum Speichern von Textgeheimnissen handelt. In der Ressource **Geheimnisse** enthaltene Geheimnisse werden auf sichere Weise gespeichert und übertragen.
* Mindestens eine Ressource vom Typ **Geheimnisse/Werte**, die im Ressourcencontainer **Geheimnisse** gespeichert ist. Jede Ressource **Geheimnisse/Werte** verfügt über eine Versionsnummer.

## <a name="next-steps"></a>Nächste Schritte 
Weitere Informationen zu Service Fabric Mesh-Geheimnissen finden Sie in den folgenden Artikeln:
- [Verwalten von Service Fabric Mesh-Anwendungsgeheimnissen](service-fabric-mesh-howto-manage-secrets.md)
- [Einführung in das Service Fabric-Ressourcenmodell](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png

---
title: Parametrisieren von Konfigurationsdateien in Azure Service Fabric | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Konfigurationsdateien in Service Fabric parametrisieren.
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: 3d03ca5cec2cef67862c2678b3b0a8f17b413787
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60482436"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Parametrisieren von Konfigurationsdateien in Service Fabric

In diesem Artikel erfahren Sie, wie Sie eine Konfigurationsdatei in Service Fabric parametrisieren.  Wenn Sie noch nicht mit den grundlegenden Konzepten der Verwaltung von Anwendungen für mehrere Umgebungen vertraut sind, lesen Sie [Verwalten von Anwendungen für mehrere Umgebungen](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Verfahren für das Parametrisieren von Konfigurationsdateien

In diesem Beispiel überschreiben Sie einen Konfigurationswert, indem Sie Parameter in der Anwendungsbereitstellung verwenden.

1. Öffnen Sie die Datei *\<MyService>\PackageRoot\Config\Settings.xml* in Ihrem Dienstprojekt.
1. Legen Sie den Namen und den Wert eines Konfigurationsparameters fest, z.B. Cachegröße 25, indem Sie den folgenden XML-Code hinzufügen:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Speichern und schließen Sie die Datei.
1. Öffnen Sie die Datei *\<MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml*.
1. Deklarieren Sie in der Datei „ApplicationManifest.xml“ einen Parameter und Standardwert im `Parameters`-Element.  Es wird empfohlen, dass der Name des Parameters den Namen des Diensts enthält (z.B. „MyService“).

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. Fügen Sie im Abschnitt `ServiceManifestImport` der Datei „ApplicationManifest.xml“ ein `ConfigOverride`-Element hinzu, in dem auf das Konfigurationspaket, den Abschnitt und den Parameter verwiesen wird.

   ```xml
    <ConfigOverrides>
      <ConfigOverride Name="Config">
          <Settings>
            <Section Name="MyConfigSection">
                <Parameter Name="CacheSize" Value="[MyService_CacheSize]" />
            </Section>
          </Settings>
      </ConfigOverride>
    </ConfigOverrides>
   ```

> [!NOTE]
> Wenn Sie ein ConfigOverride-Element hinzugefügt haben, wählt Service Fabric immer die Anwendungsparameter oder den Standardwert aus, der im Anwendungsmanifest angegeben ist.
>
>

## <a name="next-steps"></a>Nächste Schritte
Informationen zu anderen App-Verwaltungsfunktionen in Visual Studio finden Sie unter [Verwalten Ihrer Service Fabric-Anwendungen in Visual Studio](service-fabric-manage-application-in-visual-studio.md).
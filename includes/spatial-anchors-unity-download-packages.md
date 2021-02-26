---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: f6c2780ccbb914228a9870cb3b5fe4b0e3d0b214
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569427"
---
Damit Sie die erforderlichen Pakete herunterladen können, muss <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> installiert sein.

Führen Sie den folgenden Befehl aus. Ersetzen Sie dabei `<version_number>` durch die Version der Azure Spatial Anchors-Instanz, die Sie in den aktuellen Ordner herunterladen möchten:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM
```

> [!NOTE]
> Führen Sie zum Auflisten der verfügbaren Versionen des Azure Spatial Anchors-Pakets den folgenden Befehl aus:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM versions
> ```

Das Azure Spatial Anchors-Hauptpaket wird in den Ordner heruntergeladen, in dem Sie den Befehl ausgeführt haben.

Wiederholen Sie diesen Schritt, um das Paket für alle Plattformen (Android/iOS/HoloLens) herunterzuladen, die Sie in Ihrem Projekt unterstützen möchten.

| Plattform | Paketname                                    |
|----------|-------------------------------------------------|
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<version_number> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<version_number>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<version_number> |
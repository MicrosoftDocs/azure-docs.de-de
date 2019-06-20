---
title: Installieren von Paketen in einem Jupyter-Notebook auf Azure
description: Installieren von Python-, R- und F#-Paketen aus einem Jupyter-Notebook, das auf Azure ausgeführt wird.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 6f089c12-128b-4dbd-96e3-1320d37eeba4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 504158f248cde3a399475cdec99de3e6a4ebfcc5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60598011"
---
# <a name="install-packages-from-within-a-notebook"></a>Installieren von Paketen aus einem Notebook

Zwar können Sie die [Umgebung für Ihr Notebook auf Projektebene konfigurieren](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), es kann aber sinnvoll sein, Pakete direkt innerhalb eines einzelnen Notebooks zu installieren.

Aus dem Notebook installierte Pakete betreffen nur die aktuelle Serversitzung. Paketinstallationen bleiben beim Herunterfahren des Servers nicht erhalten.

## <a name="python"></a>Python

Pakete können in Python wahlweise mit Pip oder mit Conda mithilfe von Befehlen innerhalb von Codezellen installiert werden:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Wenn die Befehlsausgabe darauf schließen lässt, dass der Anforderung bereits entsprochen wird, enthält Azure Notebooks das fragliche Paket möglicherweise bereits standardmäßig. Möglicherweise wurde das Paket auch über einen [Einrichtungsschritt der Projektumgebung](configure-manage-azure-notebooks-projects.md#configure-the-project-environment) installiert.

## <a name="r"></a>R

Pakete können in R von CRAN oder GitHub mithilfe der Funktion `install.packages` in einer Codezelle installiert werden:

```r
install.packages("package_name")
```

Mithilfe der devtools-Bibliothek können auch Vorabversionen oder sonstige Entwicklungspakete von GitHub installiert werden:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

Pakete können in F# von [nuget.org](https://www.nuget.org) durch Aufrufen des Paket-Abhängigkeits-Managers aus Codezellen installiert werden. Laden Sie zunächst den Paket-Manager:

```fsharp
#load "Paket.fsx"
```

Installieren Sie anschließend Pakete:

```fsharp
Paket.Package
[ "MathNet.Numerics"
"MathNet.Numerics.FSharp"
]
```

## <a name="next-steps"></a>Nächste Schritte

- [Gewusst wie: Konfigurieren und Verwalten von Projekten](configure-manage-azure-notebooks-projects.md)
- [Gewusst wie: Zeigen einer Bildschirmpräsentation](present-jupyter-notebooks-slideshow.md)

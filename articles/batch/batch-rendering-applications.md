---
title: Renderinganwendungen
description: Mit Azure Batch können beliebige Renderinganwendungen verwendet werden. In Azure Marketplace-VM-Images sind gängige Anwendungen jedoch bereits vorinstalliert.
ms.date: 02/01/2021
ms.topic: how-to
ms.openlocfilehash: 4eeb87c866858f2f1733b81e3e2154f4521e94bc
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430027"
---
# <a name="pre-installed-applications-on-batch-rendering-vm-images"></a>Vorinstallierte Anwendungen in VM-Images für das Batch-Rendering

Mit Azure Batch können beliebige Renderinganwendungen verwendet werden. In Azure Marketplace-VM-Images sind gängige Anwendungen jedoch bereits vorinstalliert.

Für vorinstallierte Renderinganwendungen ist ggf. Lizenzierung mit nutzungsbasierter Zahlung verfügbar. Bei der Erstellung eines Batch-Pools können die erforderlichen Anwendungen angegeben werden, und die Kosten des virtuellen Computers sowie der Anwendungen werden pro Minute abgerechnet. Anwendungspreise sind auf der [Seite mit den Preisen für Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering) aufgeführt.

Einige Anwendungen unterstützen nur Windows, die meisten werden jedoch unter Windows und Linux unterstützt.

## <a name="applications-on-centos-7-rendering-image"></a>Anwendungen für CentOS 7-Renderingimage

Die folgende Liste gilt für Renderingimages von CentOS 7.6, Version 1.1.6.

* Autodesk Maya I/O 2017 Update 5 (Cut 201708032230)
* Autodesk Maya I/O 2018 Update 2 (Cut 201711281015)
* Autodesk Maya I/O 2019 Update 1
* Autodesk Arnold for Maya 2017 (Arnold-Version 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Arnold for Maya 2018 (Arnold-Version 5.3.1.1) MtoA-3.2.1.1-2018
* Autodesk Arnold for Maya 2019 (Arnold-Version 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray for Maya 2017 (Version 3.60.04)
* Chaos Group V-Ray for Maya 2018 (Version 3.60.04)
* Blender (2.68)
* Blender (2.8)

## <a name="applications-on-latest-windows-server-rendering-image"></a>Anwendungen für aktuelles Windows Server-Renderingimage

Die folgende Liste gilt für das Windows Server-Renderingimage mit der Version 1.5.0:

* Autodesk Maya I/O 2020 Update 4.4
* Autodesk 3ds Max I/O 2021 Update 3
* Autodesk Arnold for Maya 2020 (Arnold-Version 6.1.0.1) MtoA-4.1.1.1-2020
* Autodesk Arnold for 3ds Max 2021 (Arnold-Version 6.1.0.1) MAXtoA-4.2.2.20-2021
* Chaos Group V-Ray for Maya 2020 (Version 5.00.21)
* Chaos Group V-Ray for 3ds Max 2021 (Version 5.00.05)
* Blender (2.79)
* Blender (2.80)
* AZ 10

> [!IMPORTANT]
> Um V-Ray mit Maya außerhalb der [Azure Batch-Erweiterungsvorlagen](https://github.com/Azure/batch-extension-templates) auszuführen, starten Sie `vrayses.exe`, bevor Sie das Rendering ausführen. Wenn Sie „vrayses.exe“ außerhalb der Vorlagen starten möchten, können Sie den folgenden Befehl verwenden: `%MAYA_2020%\vray\bin\vrayses.exe"`.
>
> Ein Beispiel finden Sie im Starttask der [Vorlage „Maya und V-Ray“](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) auf GitHub.

## <a name="applications-on-previous-windows-server-rendering-images"></a>Anwendungen für frühere Windows Server-Renderingimages

Die folgende Liste betrifft Renderingimages von Windows Server 2016, Version 1.3.8.

* Autodesk Maya I/O 2017 Update 5 (Version 17.4.5459)
* Autodesk Maya I/O 2018 Update 6 (Version 18.4.0.7622)
* Autodesk Maya I/O 2019
* Autodesk 3ds Max I/O 2018 Update 4 (Version 20.4.0.4254)
* Autodesk 3ds Max I/O 2019 Update 1 (Version 21.2.0.2219)
* Autodesk 3ds Max I/O 2020 Update 2
* Autodesk Arnold for Maya 2017 (Arnold-Version 5.3.0.2) MtoA-3.2.0.2-2017
* Autodesk Arnold for Maya 2018 (Arnold-Version 5.3.0.2) MtoA-3.2.0.2-2018
* Autodesk Arnold for Maya 2019 (Arnold-Version 5.3.0.2) MtoA-3.2.0.2-2019
* Autodesk Arnold for 3ds Max 2018 (Arnold-Version 5.3.0.2) (Version 1.2.926)
* Autodesk Arnold for 3ds Max 2019 (Arnold-Version 5.3.0.2) (Version 1.2.926)
* Autodesk Arnold for 3ds Max 2020 (Arnold-Version 5.3.0.2) (Version 1.2.926)
* Chaos Group V-Ray for Maya 2017 (Version 4.12.01)
* Chaos Group V-Ray for Maya 2018 (Version 4.12.01)
* Chaos Group V-Ray for Maya 2019 (Version 4.04.03)
* Chaos Group V-Ray for 3ds Max 2018 (Version 4.20.01)
* Chaos Group V-Ray for 3ds Max 2019 (Version 4.20.01)
* Chaos Group V-Ray for 3ds Max 2020 (Version 4.20.01)
* Blender (2.79)
* Blender (2.80)
* AZ 10

Die folgende Liste betrifft Renderingimages von Windows Server 2016, Version 1.3.7.

* Autodesk Maya I/O 2017 Update 5 (Version 17.4.5459)
* Autodesk Maya I/O 2018 Update 4 (Version 18.4.0.7622)
* Autodesk 3ds Max I/O 2019 Update 1 (Version 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (Version 20.4.0.4254)
* Autodesk Arnold for Maya 2017 (Arnold-Version 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018 (Arnold-Version 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold for 3ds Max 2018 (Arnold-Version 5.0.2.4) (Version 1.2.926)
* Autodesk Arnold for 3ds Max 2019 (Arnold-Version 5.0.2.4) (Version 1.2.926)
* Chaos Group V-Ray for Maya 2018 (Version 3.52.03)
* Chaos Group V-Ray for 3ds Max 2018 (Version 3.60.02)
* Chaos Group V-Ray for Maya 2019 (Version 3.52.03)
* Chaos Group V-Ray for 3ds Max 2019 (Version 4.10.01)
* Blender (2.79)

> [!NOTE]
> Mit Chaos Group V-Ray für 3ds Max 2019 (Version 4.10.01) werden nicht abwärtskompatible Änderungen an V-Ray eingeführt. Wenn Sie die vorherige Version (Version 3.60.02) einsetzen möchten, verwenden Sie Renderingimages mit Windows Server 2016, Version 1.3.2.

## <a name="next-steps"></a>Nächste Schritte

Damit Sie die Rendering-VM-Images verwenden können, müssen Sie sie beim Erstellen eines Pools in der Poolkonfiguration angeben. Informationen finden Sie unter [Azure Batch rendering capabilities](./batch-rendering-functionality.md) (Renderingfunktionen von Azure Batch).

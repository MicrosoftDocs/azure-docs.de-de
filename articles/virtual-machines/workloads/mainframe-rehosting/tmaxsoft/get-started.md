---
title: Erste Schritte mit TmaxSoft OpenFrame auf Azure Virtual Machines
description: Hosten Sie Ihre IBM z/OS-Mainframeworkloads mithilfe der TmaxSoft OpenFrame-Umgebung auf Azure Virtual Machines (VMs) neu.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.openlocfilehash: 215a17ced6be4cc8792ac1a06115450bfbccac99
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963265"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Erste Schritte mit TmaxSoft OpenFrame in Azure

Mithilfe von TmaxSoft OpenFrame können Sie Ihre vorhandenen Mainframeressourcen nach Microsoft Azure verlagern. Diese beliebte Lösung für das erneute Hosting schafft eine Emulationsumgebung in Azure, die es Ihnen ermöglicht, Anwendungen schnell zu migrieren. Es ist keine Neuformatierung erforderlich.

## <a name="openframe-rehosting-environment"></a>OpenFrame-Umgebung für erneutes Hosting

Richten Sie in Azure eine OpenFrame-Umgebung für Entwicklungs-, Demo-, Test- oder Produktionsworkloads ein. Wie die folgende Abbildung zeigt, umfasst OpenFrame mehrere Komponenten, die die Emulationsumgebung für Mainframes in Azure bilden. OpenFrame-Onlinedienste ersetzen z.B. die Mainframemiddleware wie IBM Customer Information Control System (CICS). OpenFrame Batch mit der Komponente TJES ersetzt das Job Entry Subsystem (JES) des IBM-Mainframes. OpenFrame Batch mit der Komponente TJES ersetzt das Job Entry Subsystem (JES) des IBM-Mainframes. 

![Prozess für erneutes Hosting](media/openframe-01.png)

> [!NOTE]
> Um die OpenFrame-Umgebung in Azure auszuführen, benötigen Sie eine gültige Produktlizenz oder eine Testlizenz von TmaxSoft.

## <a name="openframe-components"></a>OpenFrame-Komponenten

Die folgenden Komponenten sind Teil der OpenFrame-Umgebung in Azure:

- **Migrationstools**, einschließlich OFMiner, einer Lösung, die die Mainframeressourcen analysiert und dann in Azure migriert.
- **Compiler**, einschließlich OFCOBOL, einem Compiler, der die COBOL-Programme des Mainframes interpretiert, OFPLI zur Interpretation der PL/I-Programme des Mainframes und OFASM, einem Compiler, der die Assemblerprogramme des Mainframes interpretiert.
- **Front-End-Komponenten**, einschließlich Java Enterprise User Solution (JEUS), eines Webanwendungsservers, der für Java Enterprise Edition 6 zertifiziert ist, und der OpenFrame-Gatewaykomponente, die einen Listener der Version 3270 bereitstellt.
- **Anwendungsumgebung**. OpenFrame Base ist die Middleware, die das gesamte System verwaltet. OpenFrame Server Type C (OSC) ersetzt die Middleware des Mainframes und IBM CICS.
- **Relationale Datenbank**, wie z.B. Tibero (siehe die Abbildung), Oracle Database, Microsoft SQL Server, IBM Db2, oder MySQL. Die OpenFrame-Anwendungen verwenden das ODBC-Protokoll (Open Database Connectivity), um mit der Datenbank zu kommunizieren.
- **Sicherheit** über TACF, ein Dienstmodul, das den Benutzerzugriff auf Systeme und Ressourcen steuert. 
- **OFManager** ist eine Lösung, die die Betriebs- und Verwaltungsfunktionen von OpenFrame in der Webumgebung bereitstellt.

![OpenFrame-Architektur](media/openframe-02.png)

## <a name="next-steps"></a>Nächste Schritte

- [Install TmaxSoft OpenFrame on Azure (Installieren von TmaxSoft OpenFrame in Azure)](./install-openframe-azure.md)

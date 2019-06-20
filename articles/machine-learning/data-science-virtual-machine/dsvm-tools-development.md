---
title: Entwicklungstools für die Data Science-VM – Azure | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Tools und integrierten Entwicklungsumgebungen, die in Data Science Virtual Machine vorinstalliert sind.
keywords: Data Science-Tools, virtuelle Computer für Data Science, Tools für Data Science, Linux Data Science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: c4e101e45dc1641c5731e246f7fdeeb37ebc0823
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864419"
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Entwicklungstools auf der Data Science-VM

Die Data Science-VM (DSVM) bündelt mehrere beliebte Tools und IDEs zu einer produktiven Umgebung für Entwickler. Im Anschluss sind einige der Tools aufgeführt, die auf der DSVM zur Verfügung stehen: 

## <a name="visual-studio-2019"></a>Visual Studio 2019  

|    |           |
| ------------- | ------------- |
| Was ist das?   | Allgemeine IDE      |
| Unterstützte DSVM-Versionen      | Windows      |
| Typische Verwendung      | Softwareentwicklung    |
| Konfiguration/Installation auf der DSVM      | Data Science-Workload (Python- und R-Tools), Azure-Workload (Hadoop, Data Lake), Node.js, SQL Server-Tools, [Azure Machine Learning für Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Verwendung/Ausführung      | Desktopverknüpfung (`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`)    |
| Verwandte Tools auf der DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Was ist das?   | Allgemeine IDE      |
| Unterstützte DSVM-Versionen      | Windows, Linux     |
| Typische Verwendung      | Code-Editor und Git-Integration   |
| Verwendung/Ausführung      | Desktopverknüpfung (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) unter Windows, Desktopverknüpfung oder Terminal (`code`) unter Linux    |
| Verwandte Tools auf der DSVM      |     Visual Studio 2019, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| Was ist das?   | Client-IDE für R    |
| Unterstützte DSVM-Versionen      | Windows, Linux      |
| Typische Verwendung      |  R-Entwicklung     |
| Verwendung/Ausführung      | Desktopverknüpfung (`C:\Program Files\RStudio\bin\rstudio.exe`) unter Windows, Desktopverknüpfung (`/usr/bin/rstudio`) unter Linux      |
| Verwandte Tools auf der DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio Server 

|    |           |
| ------------- | ------------- |
| Was ist das?   | Webbasierte IDE für R    |
| Unterstützte DSVM-Versionen      | Linux      |
| Typische Verwendung      |  R-Entwicklung     |
| Verwendung/Ausführung      | Aktivieren Sie den Dienst mit _systemctl enable rstudio-server_, und starten Sie dann den Dienst mit _systemctl start rstudio-server_. Anschließend können Sie sich bei RStudio Server unter „http:\//ihre-vm-ip:8787“ anmelden.       |
| Verwandte Tools auf der DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Was ist das?   | Client-IDE für Julia   |
| Unterstützte DSVM-Versionen      | Windows, Linux      |
| Typische Verwendung      |  Julia-Entwicklung     |
| Verwendung/Ausführung      | Desktopverknüpfung (`C:\JuliaPro-0.5.1.1\Juno.bat`) unter Windows, Desktopverknüpfung (`/opt/JuliaPro-VERSION/Juno`) unter Linux      |
| Verwandte Tools auf der DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>PyCharm

|    |           |
| ------------- | ------------- |
| Was ist das?   | Client-IDE für Python    |
| Unterstützte DSVM-Versionen      | Linux      |
| Typische Verwendung      |  Python-Entwicklung     |
| Verwendung/Ausführung      | Desktopverknüpfung (`/usr/bin/pycharm`) unter Linux      |
| Verwandte Tools auf der DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |



## <a name="powerbi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| Was ist das?   | Interaktives Tool für Datenvisualisierung und BI    |
| Unterstützte DSVM-Versionen      | Windows  |
| Typische Verwendung      |  Datenvisualisierung und Dashboarderstellung   |
| Verwendung/Ausführung      | Desktopverknüpfung (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Verwandte Tools auf der DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |


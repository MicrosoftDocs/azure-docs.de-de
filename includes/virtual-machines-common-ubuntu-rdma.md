---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d41b86b902d9a58b144e251e6922fbd95d459031
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016125"
---
1. Installieren von dapl, rdmacm, ibverbs und mlx4

   ```bash
   sudo apt-get update

   sudo apt-get install libdapl2 libmlx4-1

   ```

2. Aktivieren Sie RDMA in „/etc/waagent.conf“, indem Sie die folgenden Konfigurationszeilen auskommentieren. Sie benötigen Root-Zugriff zum Bearbeiten dieser Datei.
  
   ```
   OS.EnableRDMA=y

   OS.UpdateRdmaDriver=y
   ```

3. Fügen Sie die folgenden Speichereinstellungen in der Datei „/etc/security/limits.conf“ in KB hinzu, oder ändern Sie sie. Sie benötigen Root-Zugriff zum Bearbeiten dieser Datei. Zu Testzwecken können Sie „memlock“ auf unbegrenzt festlegen. Beispiel: `<User or group name>   hard    memlock   unlimited`.

   ```
   <User or group name> hard    memlock <memory required for your application in KB>

   <User or group name> soft    memlock <memory required for your application in KB>
   ```
  
4. Installieren Sie die Intel MPI-Bibliothek. Sie können die Bibliothek von Intel [erwerben und herunterladen](https://software.intel.com/intel-mpi-library/) oder die [kostenlose Evaluierungsversion](https://registrationcenter.intel.com/en/forms/?productid=1740) herunterladen.

   ```bash
   wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
   ```
 
   Es werden nur Intel MPI 5.x-Runtimes unterstützt.
 
   Die Installationsschritte finden Sie im [Installationshandbuch für die Intel MPI-Bibliothek](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Aktivieren Sie ptrace für Nicht-Root-Nicht-Debugger-Prozesse (erforderlich für die aktuelle Version von Intel MPI).
 
   ```bash
   echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
   ```

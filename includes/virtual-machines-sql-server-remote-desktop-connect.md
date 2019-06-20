---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: fe5daa38c43723c85fb464e191ee4a3e85700e0b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178047"
---
1. Wenn der virtuelle Azure-Computer erstellt wurde und ausgeführt wird, klicken Sie im Azure-Portal auf das Symbol für virtuelle Computer, um Ihre virtuellen Computer anzuzeigen.

1. Klicken Sie auf die Auslassungspunkte **...** für den neuen virtuellen Computer.

1. Klicken Sie auf **Verbinden**.

   ![Im Portal eine Verbindung mit dem virtuellen Computer herstellen](./media/virtual-machines-sql-server-remote-desktop-connect/azure-virtual-machine-connect.png)

1. Öffnen Sie die **RDP**-Datei, die vom Browser für den virtuellen Computer heruntergeladen wird.

1. Sie werden von der Remotedesktopverbindung informiert, dass der Herausgeber dieser Remoteverbindung nicht identifiziert werden kann. Klicken Sie auf **Verbinden** , um den Vorgang fortzusetzen.

1. Klicken Sie im Dialogfeld **Windows-Sicherheit** auf **Anderes Konto verwenden**. Möglicherweise müssen Sie auf **More choices** (Weitere Optionen) klicken, um diese Option anzuzeigen. Geben Sie den Benutzernamen und das Kennwort an, den bzw. das Sie bei der Erstellung des virtuellen Computers konfiguriert haben. Sie müssen einen umgekehrten Schrägstrich vor dem Benutzernamen einfügen.

   ![Remotedesktopauthentifizierung](./media/virtual-machines-sql-server-remote-desktop-connect/remote-desktop-connect.png)

1. Klicken Sie auf **OK**, um eine Verbindung herzustellen.

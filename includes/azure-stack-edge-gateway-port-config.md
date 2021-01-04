---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/16/2019
ms.author: alkohli
ms.openlocfilehash: 417fbdea3f46dfb3e90ab4890cec5e88c5aa4e07
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523796"
---
| Port-Nr.| ein oder aus | Portbereich| Erforderlich | Notizen |
|---------|-----------|-----------|----------|-------|
| TCP 80 (HTTP)|aus|WAN |Nein|Der ausgehende Port wird für den Internetzugriff zum Abrufen von Updates verwendet. <br>Der ausgehende Webproxy kann vom Benutzer konfiguriert werden. |
| TCP 443 (HTTPS)|aus|WAN|Ja|Der ausgehende Port wird für den Zugriff auf Daten in der Cloud verwendet.<br>Der ausgehende Webproxy kann vom Benutzer konfiguriert werden.|
| UDP 123 (NTP)|aus|WAN|In einigen Fällen<br>Siehe Hinweise|Dieser Port ist nur dann erforderlich, wenn Sie einen internetbasierten NTP-Server verwenden.  |   
| UDP 53 (DNS)|aus|WAN|In einigen Fällen<br>Siehe Hinweise|Dieser Port ist nur dann erforderlich, wenn Sie einen internetbasierten DNS-Server verwenden.<br>Es wird empfohlen, den lokalen DNS-Server zu verwenden. |
| TCP 5985 (WinRM)|Aus/Ein|LAN|In einigen Fällen<br>Siehe Hinweise|Dieser Port ist erforderlich, um eine Verbindung mit dem Gerät per Remote-PowerShell über HTTP herzustellen.  |
| UDP 67 (DHCP)|aus|LAN|In einigen Fällen<br>Siehe Hinweise|Dieser Port ist nur dann erforderlich, wenn Sie einen lokalen DHCP-Server verwenden.  |
| TCP 80 (HTTP)|Aus/Ein|LAN|Ja|Dies ist der eingehende Port für die lokale Benutzeroberfläche auf dem Gerät für die lokale Verwaltung. <br>Beim Zugriff auf die lokale Benutzeroberfläche über HTTP erfolgt automatisch eine Umleitung auf HTTPS.  |
| TCP 443 (HTTPS)|Aus/Ein|LAN|Ja|Dies ist der eingehende Port für die lokale Benutzeroberfläche auf dem Gerät für die lokale Verwaltung. Dieser Port wird auch verwendet, um Azure Resource Manager mit den gerätelokalen APIs zu verbinden, um den Blobspeicher über REST-APIs und mit dem Sicherheitstokendienst (STS) zu verbinden, um sich über Zugriffs- und Aktualisierungstoken zu authentifizieren.|
| TCP 445 (SMB)|In|LAN|In einigen Fällen<br>Siehe Hinweise|Dieser Port ist nur dann erforderlich, wenn Sie eine Verbindung über SMB herstellen. |
| TCP 2049 (NFS)|In|LAN|In einigen Fällen<br>Siehe Hinweise|Dieser Port ist nur dann erforderlich, wenn Sie eine Verbindung über NFS herstellen. |



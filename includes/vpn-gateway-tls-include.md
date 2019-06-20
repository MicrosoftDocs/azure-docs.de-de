---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/27/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0d5c3b55d20be19d4aeb92b82d6e44d417259a7b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177917"
---
1. Öffnen Sie eine Eingabeaufforderung mit erhöhten Rechten, indem Sie mit der rechten Maustaste auf **Eingabeaufforderung** klicken und die Option **Als Administrator ausführen** wählen.
2. Führen Sie an der Eingabeaufforderung die folgenden Befehle aus:

   ```
   reg add HKLM\SYSTEM\CurrentControlSet\Services\RasMan\PPP\EAP\13 /v TlsVersion /t REG_DWORD /d 0xfc0
   reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp" /v DefaultSecureProtocols /t REG_DWORD /d 0xaa0
   if %PROCESSOR_ARCHITECTURE% EQU AMD64 reg add "HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp" /v DefaultSecureProtocols /t REG_DWORD /d 0xaa0
   ```

3. Installieren Sie die folgenden Updates:
  
   * [KB3140245](https://www.catalog.update.microsoft.com/search.aspx?q=kb3140245)
   * [KB2977292](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2977292)

4. Starten Sie den Computer neu.
5. Stellen Sie eine VPN-Verbindung her.

> [!NOTE]
> Sie müssen den oben aufgeführten Registrierungsschlüssel festlegen, wenn Sie eine ältere Version von Windows 10 (10240) ausführen.
>

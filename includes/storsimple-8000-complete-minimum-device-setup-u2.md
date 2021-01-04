---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 867cdc97ff91d5932230b733dee4d7660d499c39
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027524"
---
#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>So führen Sie die mindestens erforderliche StorSimple-Gerätekonfiguration aus

   > [!NOTE]
   > Nach Abschluss der minimalen Gerätekonfiguration kann der Gerätename nicht mehr geändert werden.
   
1. Wählen Sie auf dem Blatt **Geräte** in der tabellarischen Geräteliste Ihr Gerät aus, und klicken Sie darauf. Das Gerät befindet sich im Status **Zur Einrichtung bereit**. Das Blatt **Gerät konfigurieren** wird geöffnet.

     ![Netzwerkschnittstellen für die minimale Konfiguration von StorSimple-Geräten](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig1.png)

2. Gehen Sie auf dem Blatt **Gerät konfigurieren** wie folgt vor:
   
   1. Geben Sie einen **Anzeigenamen** für Ihr Gerät an. Der Standardgerätename enthält Informationen zum Gerätemodell und zur Seriennummer. Sie können einen Anzeigenamen zuweisen, der bis zu 64 Zeichen lang ist, um Ihr Gerät zu verwalten.
   2. Legen Sie die **Zeitzone** basierend auf dem geografischen Standort fest, an dem das Gerät bereitgestellt wird. Ihr Gerät verwendet diese Zeitzone für alle geplanten Vorgänge.
   3. Unter den **DATA 0-Einstellungen**:

       1. Ihre DATA 0-Netzwerkschnittstelle wird als aktiviert angezeigt, und die Netzwerkeinstellungen (IP-Adresse, Subnetz, Gateway) werden über den Setup-Assistenten konfiguriert. DATA 0 ist außerdem automatisch für die Cloud und für iSCSI aktiviert.

       2. Geben Sie die festen IP-Adressen für Controller 0 und Controller 1 an. **Die festen IP-Adressen des Controllers müssen freie IP-Adressen in dem Subnetz sein, auf das die IP-Adresse des Geräts zugreifen kann.** Wenn die DATA 0-Schnittstelle für IPv4 konfiguriert wurde, müssen die festen IP-Adressen im IPv4-Format angegeben werden. Wenn Sie ein Präfix für die IPv6-Konfiguration angegeben haben, werden die festen IP-Adressen in diesen Feldern automatisch mit Daten aufgefüllt.

            ![Netzwerkschnittstellen für die minimale Konfiguration von StorSimple-Geräten 2](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig2.png)

            Die festen IP-Adressen für den Controller werden für die Bereitstellung der Updates für das Gerät sowie für die Garbage Collection verwendet. Aus diesem Grund müssen die festen IP-Adressen routingfähig sein und eine Verbindung mit dem Internet herstellen können. Mit dem [Test-HcsmConnection][Test]-Cmdlet können Sie feststellen, ob die festen IP-Adressen Ihres Controllers routingfähig sind. Im folgenden Beispiel sind feste Controller-IP-Adressen dargestellt, die ins Internet weitergeleitet werden und auf die Microsoft Update-Server zugreifen können.

            ![Test-HcsmConnection mit Darstellung routingfähiger IP-Adressen](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig3.png)

1. Klicken Sie auf **OK**. Die Gerätekonfiguration wird gestartet. Wenn die Gerätekonfiguration abgeschlossen ist, erhalten Sie eine entsprechende Benachrichtigung. Der Gerätestatus ändert sich auf dem Blatt **Geräte** in **Online**.

    ![Netzwerkschnittstellen für die minimale Konfiguration von StorSimple-Geräten 3](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig4.png)

<!--Link reference-->
[Test]: /previous-versions/windows/powershell-scripting/dn715782(v=wps.630)
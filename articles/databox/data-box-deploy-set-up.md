---
title: Tutorial zum Einrichten der Azure Data Box | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie Ihr Azure Data Box-Gerät verkabeln, eine Verbindung mit Azure Data Box herstellen und Azure Data Box einschalten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: ac87d5040cd572635d81be51308f48a57ddd38e3
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335467"
---
::: zone target="docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>Tutorial: Verkabeln und Herstellen einer Verbindung mit der Azure Data Box

::: zone-end

::: zone target="chromeless"

## <a name="cable-and-connect-to-your-device"></a>Verkabeln und Herstellen einer Verbindung mit Ihrem Gerät

::: zone-end

::: zone target="docs"

In diesem Tutorial wird beschrieben, wie Sie Ihre Azure Data Box verkabeln, verbinden und einschalten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Verkabeln der Data Box
> * Herstellen einer Verbindung mit der Data Box

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

1. Sie haben die Bestellung für Azure Data Box aufgegeben.
    - Weitere Informationen zu Importbestellungen finden Sie unter [Tutorial: Bestellen von Azure Data Box](data-box-deploy-ordered.md).
    - Weitere Informationen zu Exportbestellungen finden Sie unter [Tutorial: Bestellen von Azure Data Box](data-box-deploy-export-ordered.md).
1. Sie haben Ihre Data Box erhalten, und die Bestellung wird im Portal mit dem Status **Übermittelt** angezeigt. 
    - Die Klarsichthülle, die am Gerät unter dem eigentlichen Etikett angebracht ist, enthält ein Adressetikett. Bewahren Sie dieses Etikett sicher auf, da Sie es für den Rückversand benötigen.
    - In einigen Regionen in Europa wird das Gerät unter Umständen in einer Kiste ausgeliefert. Packen Sie das Gerät aus, und behalten Sie die Kiste für die Rücksendung.
1. Sie haben sich mit den [Sicherheitsrichtlinien für die Data Box](data-box-safety.md) vertraut gemacht.
1. Sie haben ein geerdetes Netzkabel zur Verwendung mit dem 100-TB-Speichergerät erhalten.
1. Sie verfügen über einen Hostcomputer, der zum Kopieren von Daten auf Ihre Data Box (Importauftrag) oder zum Kopieren von Daten von Ihrer Data Box (Exportauftrag) verwendet wird. Für Ihren Hostcomputer müssen die folgenden Bedingungen erfüllt sein:
    - Es muss ein [unterstütztes Betriebssystem](data-box-system-requirements.md) ausgeführt werden.
    - Er muss mit einem Hochgeschwindigkeitsnetzwerk verbunden sein. Mindestens eine 10-GbE-Verbindung wird dringend empfohlen. Falls keine 10-GbE-Verbindung verfügbar ist, kann eine 1-GbE-Datenverbindung verwendet werden, wodurch aber die Geschwindigkeit der Kopiervorgänge leidet. 
1. Sie verfügen über eine geeignete ebene Fläche, auf der Sie die Data Box aufstellen können. Wenn Sie das Gerät in einem standardmäßigen Rackregal einbauen möchten, benötigen Sie einen 7HE-Steckplatz in Ihrem Rack im Rechenzentrum. Sie können das Gerät waagerecht oder senkrecht in das Rack einbauen.
1. Sie haben die folgenden Kabel zur Hand, um Ihre Data Box mit dem Hostcomputer zu verbinden.
    - Mindestens ein SFP+-Twinax-Kupferkabel oder SFP+-Glasfaserkabel mit 10 GbE (zur Verwendung mit den Netzwerkschnittstellen DATA 1 und DATA 2). Die Data Box verfügt über die Netzwerkschnittstelle „Mellanox ConnectX®-3 Pro EN Dual-Port 10GBASE-T Adapters w/ PCI Express 3.0“, sodass mit dieser Schnittstelle kompatible Kabel verwendet werden können. Bei internen Tests wurde beispielsweise ein Kabel vom Typ „CISCO SFP-H10GB-CU3M 10GBASE-CU TWINAX SFP +3M“ verwendet. Weitere Informationen finden Sie in der [Liste der unterstützten Kabel und Switches von Mellanox](https://www.mellanox.com/pdf/firmware/ConnectX3-FW-2_42_5000-release_notes.pdf).
    - Ein Netzwerkkabel des Typs RJ-45 CAT 6 (zur Verwendung mit der Netzwerkschnittstelle MGMT)
    - Ein Netzwerkkabel des Typs RJ-45 CAT 6A oder RJ-45 CAT 6 (zur Verwendung mit der Netzwerkschnittstelle DATA 3, die mit 10 GBit/s bzw. 1 GBit/s konfiguriert ist)

## <a name="cable-your-device"></a>Verkabeln des Geräts

Führen Sie die folgenden Schritte aus, um das Gerät zu verkabeln.

1. Überprüfen Sie das Gerät auf Anzeichen einer Manipulation oder andere erkennbare Schäden. Fahren Sie nicht mit den nächsten Schritten fort, wenn das Gerät manipuliert wurde oder stark beschädigt ist. Wenden Sie sich umgehend an den Microsoft-Support. Er wird gemeinsam mit Ihnen ermitteln, ob das Gerät in einwandfreiem Zustand ist und verwendet werden kann oder ein Ersatzgerät geliefert werden muss.
2. Transportieren Sie das Gerät an den Ort, an dem Sie es einschalten möchten. Stellen Sie das Gerät auf eine ebene Fläche. Das Gerät kann auch in einem standardmäßigen Rackregal aufgestellt werden.
3. Schließen Sie die Netz- und Netzwerkkabel an. Die folgende Abbildung zeigt die Rückwandplatine eines verbundenen Geräts für eine allgemeine Konfiguration. Abhängig von Ihrer Umgebung können Sie auch eine andere [Verkabelungsoption](data-box-cable-options.md) wählen.
    
    ![Verkabelte Rückwandplatine der Data Box](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. Schließen Sie das Netzkabel an den gekennzeichneten Netzeingang an. Das andere Ende des Netzkabels muss an eine Stromversorgungseinheit angeschlossen werden.
    2. Verbinden Sie ein Ende des RJ-45 CAT 6-Kabels mit dem MGMT-Port und das andere Ende mit einem Laptop.            
    3. Verbinden Sie ein Ende des RJ-45 CAT 6A-Kabels mit dem DATA 3-Port. DATA 3 wird mit 10 GbE konfiguriert, wenn Sie das RJ-45 CAT 6A-Kabel für die Verbindung verwenden, und mit 1 GbE, wenn Sie das RJ-45 CAT 6-Kabel verwenden.
    4. Schließen Sie abhängig von den Netzwerkschnittstellen, die Sie für die Datenübertragung nutzen möchten, bis zu zwei SFP+-Twinax-Kupferkabel oder SFP+-Glasfaserkabel mit 10 GbE an die Anschlüsse „DATA 1“ und „DATA 2“ an. 
    5. Die anderen Enden der Kabel von den Datenports werden über einen 10-GbE-Switch mit dem Hostcomputer verbunden.

4. Machen Sie den Netzschalter auf dem Bedienfeld des Geräts ausfindig. Schalten Sie das Gerät ein.

    ![Netzschalter der Data Box](media/data-box-deploy-set-up/data-box-powered-door-open.png)

::: zone-end

::: zone target="chromeless"

Nachdem Sie das Gerät erhalten haben, müssen Sie es verkabeln und eine Verbindung mit Ihrem Gerät herstellen. 

## <a name="cable-your-device"></a>Verkabeln des Geräts

1. Wenn es Anzeichen gibt, dass das Gerät manipuliert oder beschädigt wurde, fahren Sie nicht fort. Wenden Sie sich an den Microsoft-Support mit der Bitte, Ihnen ein Austauschgerät zu senden.
2. Bevor Sie Ihr Gerät verkabeln, vergewissern Sie sich, dass Sie die folgenden Kabel haben:
    
    - (Im Lieferumfang) geerdetes Netzkabel mit einer Nennstromstärke von mindestens 10 A mit einem IEC60320 C-13-Stecker an einem Ende zum Anschluss an das Gerät.
    - Ein Netzwerkkabel des Typs RJ-45 CAT 6 (zur Verwendung mit der Netzwerkschnittstelle MGMT)
    - Zwei SFP+ Twinax-Kupferkabel mit 10 GbE (zur Verwendung mit den 10-GBit/s-Netzwerkschnittstellen DATA 1 und DATA 2)
    - Ein Netzwerkkabel des Typs RJ-45 CAT 6A oder RJ-45 CAT 6 (zur Verwendung mit der Netzwerkschnittstelle DATA 3, die mit 10 GBit/s bzw. 1 GBit/s konfiguriert ist)

3. Nehmen Sie das Gerät heraus, und stellen Sie es auf eine ebene Fläche. 
    
4. Verkabeln Sie das Gerät wie unten gezeigt.  

    ![Verkabelte Rückwandplatine der Data Box](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. Schließen Sie das Netzkabel an das Gerät an.
    2. Verwenden Sie das RJ-45 CAT 6 -Netzwerkkabel, um Ihren Hostcomputer mit dem Verwaltungsport (MGMT) des Geräts zu verbinden. 
    3. Verwenden Sie das SFP+ Twinax-Kupferkabel, um mindestens eine Netzwerkschnittstelle, DATA 1 oder DATA 2, mit 10 GBit/s (bevorzugt gegenüber 1 GBit/s) für die Datenübertragung anzuschließen. 
    4. Schalten Sie das Gerät ein. Der Netzschalter befindet sich auf der Vorderseite des Geräts.

::: zone-end

::: zone target="docs"


## <a name="connect-to-your-device"></a>Herstellen der Verbindung mit dem Gerät

Führen Sie die folgenden Schritte aus, um Ihr Gerät über die lokale Webbenutzeroberfläche und die Benutzeroberfläche des Portals einzurichten.

1. Konfigurieren Sie den Ethernet-Adapter auf dem Laptop, über den Sie eine Verbindung mit dem Gerät herstellen, mit der statischen IP-Adresse 192.168.100.5 und dem Subnetz 255.255.255.0. 
2. Stellen Sie eine Verbindung mit dem MGMT-Port des Geräts her, und greifen Sie über „https\://192.168.100.10“ auf die lokale Webbenutzeroberfläche des Geräts zu. Dies kann nach dem Einschalten des Geräts bis zu 5 Minuten dauern.
3. Klicken Sie auf **Details** und anschließend auf **Webseite trotzdem laden**.

   ![Herstellen einer Verbindung mit der lokalen Webbenutzeroberfläche](media/data-box-deploy-set-up/data-box-connect-local-web-ui.png)

4. Eine Seite **Anmelden** für die lokale Webbenutzeroberfläche wird angezeigt. Stellen Sie sicher, dass die Seriennummer des Geräts in der Benutzeroberfläche des Portals und der lokalen Webbenutzeroberfläche übereinstimmt. Das Gerät ist zu diesem Zeitpunkt gesperrt.
5. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
6. Laden Sie die Anmeldeinformationen für das Gerät aus dem Portal herunter. Navigieren Sie zu **Allgemein > Gerätedetails**. Kopieren Sie das **Gerätekennwort**. Das Gerätekennwort ist mit einer bestimmten Bestellung im Portal verknüpft. 

    ![Abrufen der Geräteanmeldeinformationen](media/data-box-deploy-set-up/data-box-device-credentials.png)
    
    
7. Geben Sie das Gerätekennwort an, das Sie im vorherigen Schritt aus dem Azure-Portal kopiert haben, um sich bei der lokalen Webbenutzeroberfläche des Geräts anzumelden. Klicken Sie auf **Anmelden**.
8. Überprüfen Sie im **Dashboard** , ob die Netzwerkschnittstellen konfiguriert sind. 
   - Falls DHCP in Ihrer Umgebung aktiviert ist, werden Netzwerkschnittstellen automatisch konfiguriert. 
   - Wenn DHCP nicht aktiviert ist, wechseln Sie zu **Netzwerkschnittstellen festlegen** , und weisen Sie ggf. statische IP-Adressen zu.

     ![Gerätedashboard](media/data-box-deploy-set-up/data-box-dashboard-1.png)

Nach dem Konfigurieren der Datennetzwerkschnittstellen können Sie auch die IP-Adresse einer der Schnittstellen (DATA 1 bis DATA 3) verwenden, um auf die lokale Webbenutzeroberfläche unter `https://<IP address of a data network interface>` zuzugreifen. 

Sobald die Einrichtung des Geräts abgeschlossen ist, können Sie eine Verbindung mit den Gerätefreigaben herstellen und die Daten kopieren. 

::: zone-end

::: zone target="chromeless"

## <a name="connect-your-device"></a>Verbinden Ihres Geräts

1. Zum Abrufen des Gerätekennworts wechseln Sie im [Azure-Portal](https://portal.azure.com) zu **Allgemein > Gerätedetails**.
2. Weisen Sie dem Ethernet-Adapter auf dem Computer, mit dem Sie sich mit der Data Box verbinden, die statische IP-Adresse 192.168.100.5 und das Subnetz 255.255.255.255.0 zu. Greifen Sie unter `https://192.168.100.10` auf die lokale Webbenutzeroberfläche des Geräts zu. Der Verbindungsaufbau kann nach dem Einschalten des Geräts bis zu 5 Minuten dauern. 
3. Melden Sie sich mit dem aus dem Azure-Portal abgerufenen Kennwort an. Sie sehen eine Fehlermeldung, die auf ein Problem mit dem Sicherheitszertifikat der Website hinweist. Befolgen Sie die browserspezifischen Anweisungen, um zur Webseite zu gelangen.
4. Standardmäßig werden die Netzwerkeinstellungen für die Datenschnittstelle mit 10 Gbit/s (oder 1 Gbit/s) als DHCP konfiguriert. Bei Bedarf können Sie diese Schnittstelle als statisch konfigurieren und eine IP-Adresse angeben. 

::: zone-end


::: zone target="docs"

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zu Azure Data Box-Themen erhalten, darunter die folgenden:

> [!div class="checklist"]
> * Verkabeln der Data Box
> * Herstellen einer Verbindung mit der Data Box

Im nächsten Tutorial erfahren Sie, wie Sie Daten kopieren:

> [!div class="nextstepaction"]
> [Tutorial: Kopieren von Daten in eine Azure Data Box über SMB](./data-box-deploy-copy-data.md)

oder

> [!div class="nextstepaction"]
> [Tutorial: Kopieren von Daten aus Azure Data Box über SMB (Vorschau)](./data-box-deploy-export-copy-data.md)

::: zone-end


---
title: Konfigurieren von privaten IP-Adressen für virtuelle Computer (klassisch) – Azure-Portal | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie private IP-Adressen für virtuelle Computer (klassisch) über das Azure-Portal konfigurieren.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 72d1c4d2ea3adf7d8751adfbb013435f8f2530f0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125745"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Konfigurieren von privaten IP-Adressen für einen virtuellen Computer (klassisch) über das Azure-Portal

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Dieser Artikel gilt für das klassische Bereitstellungsmodell. Sie können [eine statische private IP-Adresse auch im Ressourcen-Manager-Bereitstellungsmodell verwalten](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

In den folgenden Beispielschritten wird vorausgesetzt, dass bereits eine einfache Umgebung erstellt wurde. Wenn Sie die in diesem Dokument aufgeführten Schritte ohne Veränderungen ausführen möchten, erstellen Sie zunächst eine Testumgebung, wie unter [Erstellen eines VNets](virtual-networks-create-vnet-classic-pportal.md)beschrieben.

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Angeben einer statischen privaten IP-Adresse beim Erstellen eines virtuellen Computers
Um in einem VNet mit dem Namen *TestVNet* im Subnetz *FrontEnd* einen virtuellen Computer mit dem Namen *DNS01* und der statischen privaten IP-Adresse *192.168.1.101* zu erstellen, führen Sie die folgenden Schritte aus:

1. Navigieren Sie in einem Browser zu https://portal.azure.com, und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.
2. Wählen Sie **NEU** > **Compute** > **Windows Server 2012 R2 Datacenter**. Beachten Sie, dass in der Liste **Bereitstellungsmodell auswählen** bereits **Klassisch** angezeigt wird. Wählen Sie anschließend die Option **Erstellen**.
   
    ![Erstellen einer VM im Azure-Portal](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. Geben Sie unter **VM erstellen** den Namen des zu erstellenden virtuellen Computers (in diesem Szenario *DNS01*), das lokale Administratorkonto und das Kennwort ein.
   
    ![Erstellen einer VM im Azure-Portal](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Wählen Sie **Optionale Konfiguration** > **Netzwerk** > **Virtual Network** und anschließend **TestVNet**. Wenn **TestVNet** nicht verfügbar ist, stellen Sie sicher, dass Sie den Speicherort *USA (Mitte)* verwenden und dass die am Anfang dieses Artikels beschriebene Testumgebung erstellt wurde.
   
    ![Erstellen einer VM im Azure-Portal](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. Stellen Sie unter **Netzwerk** sicher, dass das Subnetz *FrontEnd* ausgewählt ist, und wählen Sie anschließend **IP-Adressen**. Wählen Sie unter **IP-Adresszuweisung** die Option **Statisch**, und geben Sie dann im Feld **IP-Adressen** den Wert *192.168.1.101* ein, wie nachfolgend dargestellt.
   
    ![Erstellen einer VM im Azure-Portal](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Wählen Sie unter **IP-Adressen** die Option **OK**, unter **Netzwerk** erneut **OK** und unter **Optionale Konfiguration** noch einmal **OK**.
7. Wählen Sie unter **VM erstellen** die Option **Erstellen**. Beachten Sie die Kachel, die im unteren Bereich des Dashboards angezeigt wird:
   
    ![Erstellen einer VM im Azure-Portal](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Abrufen der Informationen zur statischen privaten IP-Adresse für einen virtuellen Computer
Um die Informationen zur statischen privaten IP-Adresse für den virtuellen Computer anzuzeigen, der mit den obigen Schritten erstellt wurde, führen Sie die folgenden Schritte aus.

1. Wählen Sie im Azure-Portal die Option **ALLE DURCHSUCHEN** > **Virtuelle Computer (Klassisch)**  > **DNS01All** > **Alle Einstellungen** > **IP-Adressen**, um die Informationen zur IP-Adresszuweisung und IP-Adresse anzuzeigen, wie nachfolgend dargestellt.
   
    ![Erstellen einer VM im Azure-Portal](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Entfernen einer statischen privaten IP-Adresse von einem virtuellen Computer

Wählen Sie unter **IP-Adressen** rechts von **IP-Adresszuweisung** die Option **Dynamisch** und dann die Option **Speichern** und abschließend **Ja**. Dies ist in der folgenden Abbildung dargestellt:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Hinzufügen einer statischen privaten IP-Adresse zu einem vorhandenen virtuellen Computer

1. Wählen Sie unter **IP-Adressen** wie oben gezeigt rechts von **IP-Adresszuweisung** die Option **Statisch**.
2. Geben Sie unter **IP-Adresse** den Wert *192.168.1.101* ein, und wählen Sie dann **Speichern** und **Ja**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Festlegen von IP-Adressen innerhalb des Betriebssystems

Es wird davon abgeraten, die private IP-Adresse, die dem virtuellen Azure-Computer innerhalb des Betriebssystems einer VM zugewiesen ist, statisch zuzuweisen, sofern dies nicht erforderlich ist. Wenn Sie die private IP-Adresse innerhalb des Betriebssystems manuell festlegen, sollten Sie sicherstellen, dass es sich um dieselbe Adresse wie die private IP-Adresse handelt, die dem virtuellen Azure-Computer zugewiesen ist. Andernfalls kann die Konnektivität mit dem virtuellen Computer verloren gehen. Die öffentliche IP-Adresse sollte niemals manuell einem virtuellen Azure-Computer innerhalb des Betriebssystems des virtuellen Computers zugewiesen werden.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [reservierte öffentliche IP-Adressen](virtual-networks-reserved-public-ip.md) .
* Erfahren Sie mehr über [öffentliche IP-Adressen auf Instanzebene (ILPIP)](virtual-networks-instance-level-public-ip.md) .
* Lesen Sie die Informationen zu [REST-APIs für reservierte IP-Adressen](https://msdn.microsoft.com/library/azure/dn722420.aspx).


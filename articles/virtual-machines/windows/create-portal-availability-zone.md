---
title: Erstellen eines virtuellen Windows-Computers in einer Zone mit dem Azure-Portal | Microsoft Docs
description: Erstellen eines virtuellen Windows-Computers in einer Verfügbarkeitszone mit dem Azure-Portal
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: e813b26a91d25fbaa1298acd455f27d2cac705f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61403639"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Erstellen eines virtuellen Windows-Computers in einer Verfügbarkeitszone mit dem Azure-Portal

In diesem Artikel werden die Schritte zum Erstellen eines virtuellen Computers in einer Azure-Verfügbarkeitszone mithilfe des Azure-Portals erläutert. Eine [Verfügbarkeitszone](../../availability-zones/az-overview.md) ist eine physisch separate Zone in einer Azure-Region. Verwenden Sie Verfügbarkeitszonen, um Ihre Apps und Daten vor einem unwahrscheinlichen Fehler oder Ausfall eines gesamten Rechenzentrums zu schützen.

Um eine Verfügbarkeitszone verwenden zu können, muss der virtuelle Computer in einer [unterstützten Azure-Region](../../availability-zones/az-overview.md#services-support-by-region) erstellt werden.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure 

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Klicken Sie im Azure-Portal links oben auf **Ressource erstellen**.

2. Wählen Sie **Compute** und dann **Windows Server 2016 Datacenter**. 

3. Geben Sie die Informationen zum virtuellen Computer ein. Der hier eingegebene Benutzername und das Kennwort werden zum Anmelden am virtuellen Computer verwendet. Das Kennwort muss mindestens zwölf Zeichen lang sein und die [definierten Anforderungen an die Komplexität](faq.md#what-are-the-password-requirements-when-creating-a-vm) erfüllen. Wählen Sie einen Speicherort wie z.B. „USA, Osten 2“, der Verfügbarkeitszonen unterstützt. Klicken Sie zum Abschluss auf **OK**.

    ![Eingeben grundlegender Informationen zu Ihrem virtuellen Computer im Portalblatt](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Wählen Sie eine Größe für den virtuellen Computer. Wählen Sie eine empfohlene Größe aus, oder filtern Sie basierend auf Features. Vergewissern Sie sich, dass die Größe in der Zone verfügbar ist, die Sie verwenden möchten.

    ![VM-Größe auswählen](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. Wählen Sie unter **Einstellungen** > **Hochverfügbarkeit** eine der nummerierten Zonen aus der Dropdownliste **Verfügbarkeitszone** aus, behalten Sie die restlichen Standardeinstellungen bei, und klicken Sie auf **OK**.

    ![Auswählen einer Verfügbarkeitszone](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Klicken Sie auf der Zusammenfassungsseite auf **Erstellen**, um die Bereitstellung des virtuellen Computers zu starten.

7. Die VM wird im Dashboard des Azure-Portals angeheftet. Nach Abschluss der Bereitstellung wird automatisch die VM-Zusammenfassung geöffnet.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Bestätigen der Zone für verwalteten Datenträger und IP-Adresse

Bei der Bereitstellung des virtuellen Computers in einer Verfügbarkeitszone wird in der gleichen Verfügbarkeitszone ein verwalteter Datenträger bereitgestellt. Standardmäßig wird in dieser Zone ebenfalls eine öffentliche IP-Adresse erstellt.

Sie können die Zoneneinstellungen für diese Ressourcen im Portal überprüfen.  

1. Klicken Sie auf **Ressourcengruppen** und dann auf den Namen der Ressourcengruppe für den virtuellen Computer, z.B. *myResourceGroup*.

2. Klicken Sie auf den Namen der Datenträgerressource. Die Seite **Übersicht** enthält Details über den Speicherort und die Verfügbarkeitszone der Ressource.

    ![Verfügbarkeitszone für den verwalteten Datenträger](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Klicken Sie auf den Namen der öffentlichen IP-Adressressource. Die Seite **Übersicht** enthält Details über den Speicherort und die Verfügbarkeitszone der Ressource.

    ![Verfügbarkeitszone für IP-Adresse](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie einen virtuellen Computer in einer Verfügbarkeitszone erstellen. Weitere Informationen zu Regionen und Verfügbarkeit für virtuelle Azure-Computer finden Sie [hier](regions-and-availability.md).

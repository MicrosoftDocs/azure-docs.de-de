---
title: Erstellen eines Benutzer-VM-Images für den Azure Marketplace
description: Listet die Schritte und Referenzen auf, die zum Erstellen eines Benutzer-VM-Images erforderlich sind.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 0005ab517d38903b87889b67449569495e396265
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938332"
---
# <a name="create-a-user-vm-image"></a>Erstellen eines Benutzer-VM-Images

In diesem Artikel sind die beiden allgemeinen Schritte beschrieben, die erforderlich sind, um ein nicht verwaltetes Image aus einer generalisierten VHD zu erstellen.  Über Referenzen werden Sie durch die einzelnen Schritte geführt: Erfassen des Images und Generalisieren des Images.


## <a name="capture-the-vm-image"></a>Erfassen des VM-Images

Befolgen Sie die Anweisungen im folgenden Artikel zum Erfassen der VM, die Ihrer Zugriffsweise entspricht:

-  PowerShell: [Erstellen eines verwalteten Images eines generalisierten virtuellen Computers in Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure-Befehlszeilenschnittstelle: [Vorgehensweise zum Erstellen eines Image von einem virtuellen Computer oder einer VHD](../../../virtual-machines/linux/capture-image.md)
-  API: [VMs – Erfassen](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Generalisieren des VM-Images

Da Sie das Benutzerimage aus einer zuvor generalisierten virtuellen Festplatte generiert haben, sollte das Image ebenfalls generalisiert werden.  Wählen Sie aus der folgenden Liste denjenigen Artikel aus, der Ihrer Zugriffsweise entspricht.  (Möglicherweise haben Sie die Festplatte bereits beim Erfassen generalisiert.)

-  PowerShell: [Generalisieren des virtuellen Computers](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure-Befehlszeilenschnittstelle: [Schritt 2: Erstellen des VM-Image](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [VMs – Generalisieren](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Nächste Schritte

Als Nächstes [erstellen Sie ein Zertifikat](cpp-create-key-vault-cert.md) und speichern dieses in einem neuen Azure Key Vault.  Dieses Zertifikat ist erforderlich, um eine sichere WinRM-Verbindung mit dem virtuellen Computer herzustellen.

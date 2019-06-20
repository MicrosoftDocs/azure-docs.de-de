---
title: Verwenden eines Katalogs mit freigegebenen Images in Azure Lab Services | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie ein Labkonto für die Verwendung eines Katalogs mit freigegebenen Images konfigurieren, sodass ein Benutzer ein Image für andere freigeben und ein anderer Benutzer mithilfe dieses Images eine Vorlage für virtuelle Computer im Lab erstellen kann.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 8d8b6fffe197d4180b091518dcd1615d0e0b9d19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65412855"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Verwenden eines Katalogs mit freigegebenen Images in Azure Lab Services
In diesem Artikel wird gezeigt, wie Lehrkräfte/Labadministratoren ein VM-Vorlagenimage speichern können, damit es von anderen wiederverwendet werden kann. Diese Images werden in einem [Katalog mit freigegebenen Images](../../virtual-machines/windows/shared-image-galleries.md) von Azure gespeichert. Als Erstes fügt der Labadministrator einen vorhandenen Katalog mit freigegebenen Images an das Labkonto an. Nachdem der Katalog mit freigegebenen Images angefügt wurde, können in Labs, die unter dem Labkonto erstellt wurden, Images in dem Katalog mit freigegebenen Images gespeichert werden. Andere Lehrkräfte können dieses Image im Katalog mit freigegebenen Images auswählen, um eine Vorlage für ihre Klassen zu erstellen. 

## <a name="prerequisites"></a>Voraussetzungen
- Erstellen Sie mithilfe von [Azure PowerShell](../../virtual-machines/windows/shared-images.md) oder über die [Azure-Befehlszeilenschnittstelle](../../virtual-machines/linux/shared-images.md) einen Katalog mit freigegebenen Images.
- Sie haben den Katalog mit freigegebenen Images an das Lab-Konto angefügt. Schrittweise Anweisungen finden Sie unter [Anfügen oder Trennen eines Katalogs mit freigegebenen Images](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Speichern eines Images im Katalog mit freigegebenen Images
Nachdem ein Katalog mit freigegebenen Images angefügt wurde, kann eine Lehrkraft ein Image im Katalog mit freigegebenen Images speichern oder in ihn hochladen, das dann von anderen Lehrkräften wiederverwendet werden kann. Anweisungen zum Hochladen eines Images in den Katalog mit freigegebenen Images finden Sie unter [Übersicht über den Katalog mit freigegebenen Images](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Zurzeit unterstützt die Benutzeroberfläche (UI) für Classroom Labs das Speichern eines Lab-Images im Katalog mit freigegebenen Images nicht. 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Verwenden eines Images aus dem Katalog mit freigegebenen Images
Lehrer/Dozenten können beim Erstellen eines neuen Labs ein benutzerdefiniertes Image aus dem Katalog mit freigegebenen Images als Vorlage auswählen.

![Verwenden eines VM-Images aus dem Katalog](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Katalogen mit freigegebenen Images finden Sie in der [Übersicht über den Katalog mit freigegebenen Images](../../virtual-machines/windows/shared-image-galleries.md).

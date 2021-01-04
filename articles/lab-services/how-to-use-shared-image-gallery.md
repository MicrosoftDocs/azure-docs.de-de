---
title: Verwenden eines Katalogs mit freigegebenen Images in Azure Lab Services | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie ein Labkonto für die Verwendung eines Katalogs mit freigegebenen Images konfigurieren, sodass ein Benutzer ein Image für andere freigeben und ein anderer Benutzer mithilfe dieses Images eine Vorlage für virtuelle Computer im Lab erstellen kann.
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: d9f4e75163f591680cc8f85ac42c1b6ada5f2365
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647765"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Verwenden eines Katalogs mit freigegebenen Images in Azure Lab Services
In diesem Artikel wird erläutert, wie Lehrer/Dozenten und Lab-Administratoren ein VM-Vorlagenimage in einem [Katalog mit freigegebenen Images](../virtual-machines/windows/shared-image-galleries.md) speichern können, damit es von anderen Benutzern zum Erstellen von Labs verwendet werden kann. 

> [!IMPORTANT]
> Bei Verwendung einer Shared Image Gallery unterstützt Azure Lab Services nur Images mit weniger als 128 GB Betriebssystem-Speicherplatz. Images mit mehr als 128 GB Speicherplatz auf dem Datenträger oder mehreren Datenträgern werden während der Erstellung des Labs nicht in der Liste der Images virtueller Computer angezeigt.

## <a name="scenarios"></a>Szenarien
Im Folgenden sehen Sie ein paar der Szenarien, die von diesem Feature unterstützt werden: 

- Ein Lab-Kontoadministrator fügt dem Lab-Konto einen Katalog mit freigegebenen Images hinzu und lädt ein Image in den Katalog mit freigegebenen Images außerhalb des Kontexts eines Labs hoch. Danach können Lab-Ersteller dieses Image aus dem Katalog mit freigegebenen Images zum Erstellen von Labs verwenden. 
- Ein Lab-Kontoadministrator fügt dem Lab-Konto einen Katalogs mit freigegebenen Images an. Ein Lab-Ersteller (Kursleiter) speichert das angepasste Image seines Labs im Katalog mit freigegebenen Images. Andere Lab-Ersteller können dann dieses Image im Katalog mit freigegebenen Images auswählen, um eine Vorlage für ihre Labs zu erstellen. 

    Wenn ein Image in eine Shared Image Gallery gespeichert wird, repliziert Azure Lab Services das gespeicherte Image in andere Regionen, die in derselben [Geografie](https://azure.microsoft.com/global-infrastructure/geographies/) verfügbar sind. Dadurch wird sichergestellt, dass das Image für Labs verfügbar ist, die in anderen Regionen innerhalb derselben Geografie erstellt wurden. Das Speichern von Images in einer Shared Image Gallery führt zu zusätzlichen Kosten, einschließlich der Kosten für alle replizierten Images. Diese Kosten entstehen zusätzlich zu den Kosten für die Azure Lab Services-Nutzung. Weitere Informationen zu Preisen der Shared Image Gallery finden Sie unter [Shared Image Gallery – Preise]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).
    
## <a name="prerequisites"></a>Voraussetzungen
- Erstellen Sie mithilfe von [Azure PowerShell](../virtual-machines/shared-images-powershell.md) oder über die [Azure-Befehlszeilenschnittstelle](../virtual-machines/shared-images-cli.md) einen Katalog mit freigegebenen Images.
- Sie haben den Katalog mit freigegebenen Images an das Lab-Konto angefügt. Schrittweise Anweisungen finden Sie unter [Anfügen oder Trennen eines Katalogs mit freigegebenen Images](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Speichern eines Images im Katalog mit freigegebenen Images
Nachdem ein Katalog mit freigegebenen Images angefügt wurde, kann ein Lab-Kontoadministrator oder ein Lehrer/Dozent ein Image im Katalog mit freigegebenen Images speichern, das dann von anderen Lehrern/Dozenten wiederverwendet werden kann. 

1. Wählen Sie auf der Seite **Vorlage** für das Lab auf der Symbolleiste die Option **Export to Shared Image Gallery** (Nach Shared Image Gallery exportieren) aus.

    ![Schaltfläche „Image speichern“](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. Geben Sie im Dialogfeld **Export to Shared Image Gallery** (Nach Shared Image Gallery exportieren) einen **Namen für das Image** ein, und wählen Sie **Exportieren** aus. 

    ![Dialogfeld „Export to Shared Image Gallery“ (Nach Shared Image Gallery exportieren)](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. Der Fortschritt dieses Vorgangs wird auf der Seite **Vorlage** angezeigt. Dieser Vorgang kann einige Zeit dauern. 

    ![Aktiver Exportvorgang](./media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. War der Exportvorgang erfolgreich, wird die folgende Meldung angezeigt:

    ![Export erfolgreich](./media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    Nachdem Sie das Image in dem Katalog mit freigegebenen Images gespeichert haben, können Sie dieses Image beim Erstellen eines anderen Labs aus dem Katalog verwenden. Sie können ein Image auch außerhalb des Kontexts eines Labs in den Katalog mit freigegebenen Images hochladen. Weitere Informationen finden Sie unter [Katalog mit freigegebenen Images: Übersicht](../virtual-machines/shared-images-powershell.md). 

    > [!IMPORTANT]
    > Wenn Sie in Azure Lab Services ein [Vorlagenimage eines Labs in einem Katalog mit freigegebenen Images speichern](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery), wird das Image als ein **spezialisiertes Image** in den Katalog hochgeladen. Bei [spezialisierten Images](../virtual-machines/windows/shared-image-galleries.md#generalized-and-specialized-images) werden computerspezifische Informationen und Benutzerprofile beibehalten. Sie können weiterhin ein generalisiertes Image direkt in den Katalog außerhalb von Azure Lab Services hochladen.    

## <a name="use-an-image-from-the-shared-image-gallery"></a>Verwenden eines Images aus dem Katalog mit freigegebenen Images
Lehrer/Dozenten können beim Erstellen eines neuen Labs ein benutzerdefiniertes Image aus dem Katalog mit freigegebenen Images als Vorlage auswählen.

![Verwenden eines VM-Images aus dem Katalog](./media/how-to-use-shared-image-gallery/use-shared-image.png)

> [!NOTE]
> Sie können eine Vorlagen-VM basierend auf **generalisierten** und **spezialisierten** Images in Azure Lab Services erstellen. 


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Katalogen mit freigegebenen Images finden Sie in der [Übersicht über den Katalog mit freigegebenen Images](../virtual-machines/windows/shared-image-galleries.md).
---
title: Anfügen oder Trennen eines Katalogs mit freigegebenen Images in Azure Lab Services | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie einen freigegebenen Imagekatalog an ein Classroom-Lab in Azure Lab Services anfügen.
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: ae0870139d2320fa079f6705956e124f61479882
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660099"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Anfügen oder Trennen eines Katalogs mit freigegebenen Images in Azure Lab Services
Dieser Artikel zeigt, wie Sie einen Katalog mit freigegebenen Images einem Labkonto anfügen können. 

> [!NOTE]
> Wenn Sie in Azure Lab Services ein [Vorlagenimage eines Labs in einem Katalog mit freigegebenen Images speichern](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery), wird das Image als ein spezialisiertes Image in den Katalog hochgeladen. Bei [spezialisierten Images](../virtual-machines/windows/shared-image-galleries.md#generalized-and-specialized-images) werden computerspezifische Informationen und Benutzerprofile beibehalten. Sie können weiterhin ein generalisiertes Image direkt in den Katalog außerhalb von Azure Lab Services hochladen. 
>
> Ein Lab-Ersteller kann eine Vorlagen-VM basierend auf generalisierten und spezialisierten Images in Azure Lab Services erstellen. 

## <a name="scenarios"></a>Szenarien
Im Folgenden sehen Sie ein paar der Szenarien, die von diesem Feature unterstützt werden: 

- Ein Lab-Kontoadministrator fügt dem Lab-Konto einen Katalog mit freigegebenen Images hinzu und lädt ein Image in den Katalog mit freigegebenen Images außerhalb des Kontexts eines Labs hoch. Danach können Lab-Ersteller dieses Image aus dem Katalog mit freigegebenen Images zum Erstellen von Labs verwenden. 
- Ein Lab-Kontoadministrator fügt dem Lab-Konto einen Katalogs mit freigegebenen Images an. Ein Lab-Ersteller (Kursleiter) speichert das angepasste Image seines Labs im Katalog mit freigegebenen Images. Andere Lab-Ersteller können dann dieses Image im Katalog mit freigegebenen Images auswählen, um eine Vorlage für ihre Labs zu erstellen. 

    Wenn ein Image in eine Shared Image Gallery gespeichert wird, repliziert Azure Lab Services das gespeicherte Image in andere Regionen, die in derselben [Geografie](https://azure.microsoft.com/global-infrastructure/geographies/) verfügbar sind. Dadurch wird sichergestellt, dass das Image für Labs verfügbar ist, die in anderen Regionen innerhalb derselben Geografie erstellt wurden. Das Speichern von Images in einer Shared Image Gallery führt zu zusätzlichen Kosten, einschließlich der Kosten für alle replizierten Images. Diese Kosten entstehen zusätzlich zu den Kosten für die Azure Lab Services-Nutzung. Weitere Informationen zu Preisen der Shared Image Gallery finden Sie unter [Shared Image Gallery – Preise](../virtual-machines/windows/shared-image-galleries.md#billing).

> [!IMPORTANT]
> Bei Verwendung einer Shared Image Gallery unterstützt Azure Lab Services nur Images mit weniger als 128 GB Betriebssystem-Speicherplatz. Images mit mehr als 128 GB Speicherplatz auf dem Datenträger oder mehreren Datenträgern werden während der Erstellung des Labs nicht in der Liste der Images virtueller Computer angezeigt.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurieren zum Zeitpunkt der Labkontoerstellung
Wenn Sie ein Labkonto erstellen, können Sie dem Labkonto einen Katalog mit freigegebenen Images anfügen. Sie können entweder einen vorhandenen Katalog mit freigegebenen Images in der Dropdownliste auswählen oder einen neuen erstellen. Um einen Katalog mit freigegebenen Images zu erstellen und dem Labkonto anzufügen, wählen Sie **Neu erstellen** aus, geben einen Sie Namen für den Katalog ein, und geben Sie dann **OK** ein. 

![Konfigurieren eines Katalog mit freigegebenen Images zum Zeitpunkt der Labkontoerstellung](./media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Konfigurieren nach Erstellen des Labkontos
Nachdem das Labkonto erstellt ist, können Sie die folgenden Aufgaben ausführen:

- Erstellen und Anfügen eines Katalogs mit freigegebenen Images
- Anfügen eines Katalogs mit freigegebenen Images an ein Labkonto
- Trennen eines Katalogs mit freigegebenen Images von einem Labkonto

## <a name="create-and-attach-a-shared-image-gallery"></a>Erstellen und Anfügen eines Katalogs mit freigegebenen Images
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Menü links **Alle Dienste** aus. Wählen Sie im Abschnitt **DEVOPS** die Option **Lab-Dienste** aus. Wenn Sie das Sternchen (`*`) neben **Lab-Dienste** auswählen, wird die Option im linken Menü dem Abschnitt **FAVORITEN** hinzugefügt. Beim nächsten Mal wählen Sie **Lab-Dienste** unter **FAVORITEN** aus.

    ![Alle Dienste -> Lab-Dienste](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Wählen Sie Ihr Labkonto aus, um die Seite **Labkonto** anzuzeigen. 
4. Wählen Sie im linken Menü die Option **Katalog mit freigegebenen Images** und anschließend auf der Symbolleiste die Option **Erstellen** aus.  

    ![Katalog mit freigegebenen Images: Schaltfläche zum Erstellen](./media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. Geben Sie im Fenster **Katalog mit freigegebenen Images erstellen** einen **Namen** für den Katalog ein, und geben Sie dann **OK** ein. 

    ![Fenster „Katalog mit freigegebenen Images erstellen“](./media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services erstellt den Katalog mit freigegebenen Images und fügt ihn dem Labkonto an. Alle Labs, die unter diesem Labkonto erstellt wurden, haben Zugriff auf den angefügten Katalog mit freigegebenen Images. 

    ![Angefügter Katalog mit Images](./media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    Im unteren Bereich sehen Sie Images im Katalog mit freigegebenen Images. In diesem neuen Katalog sind keine Images vorhanden. Wenn Sie Images in den Katalog hochladen, sehen Sie sie auf dieser Seite.     

    Alle Images im angefügten Katalog mit freigegebenen Images werden standardmäßig aktiviert. Sie können Images aktivieren oder deaktivieren, indem Sie sie in der Liste auswählen und die Schaltfläche **Ausgewählte Images aktivieren** oder **Ausgewählte Images deaktivieren** verwenden.

## <a name="attach-an-existing-shared-image-gallery"></a>Anfügen eines vorhandenen Katalogs mit freigegebenen Images
Gehen Sie wie folgt vor, um einen vorhandenen Katalog mit freigegebenen Images einem Labkonto anzufügen. 

1. Wählen Sie auf der Seite **Labkonto** im linken Menü die Option **Katalog mit freigegebenen Images** und anschließend auf der Symbolleiste die Option **Anfügen** aus. 

    ![Katalog mit freigegebenen Images: Schaltfläche zum Hinzufügen](./media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Wählen Sie auf der Seite **Vorhandenen Katalog mit freigegebenen Images anfügen** Ihren Katalog mit freigegebenen Images und anschließend **OK** aus.

    ![Auswählen eines vorhandenen Katalogs](./media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Daraufhin wird der folgende Bildschirm angezeigt: 

    ![Eigener Katalog in der Liste](./media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    In diesem Beispiel enthält der Katalog mit freigegebenen Images noch keine Images.

    Die Azure Lab Services-Identität wird als Mitwirkender dem an das Lab angefügten Katalog mit freigegebenen Images hinzugefügt. Dadurch können Lehrer/Dozenten und IT-Administratoren VM-Images im Katalog mit freigegebenen Images speichern. Alle Labs, die unter diesem Labkonto erstellt wurden, haben Zugriff auf den angefügten Katalog mit freigegebenen Images. 

    Alle Images im angefügten Katalog mit freigegebenen Images werden standardmäßig aktiviert. Sie können Images aktivieren oder deaktivieren, indem Sie sie in der Liste auswählen und die Schaltfläche **Ausgewählte Images aktivieren** oder **Ausgewählte Images deaktivieren** verwenden. 

## <a name="detach-a-shared-image-gallery"></a>Trennen eines Katalogs mit freigegebenen Images
An ein Lab kann immer nur ein einzelner Katalog mit freigegebenen Images angefügt sein. Wenn Sie einen anderen Katalog mit freigegebenen Images anfügen möchten, müssen Sie zuerst den aktuellen Katalog trennen. Wenn Sie einen Katalog mit freigegebenen Images von Ihrem Lab trennen möchten, wählen Sie auf der Symbolleiste die Option **Trennen** aus, und bestätigen Sie den Vorgang. 

![Trennen des Katalogs mit freigegebenen Images von einem Labkonto](./media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Nächste Schritte
Informationen zum Speichern eines Labimages im Katalog mit freigegebenen Images oder zum Verwenden eines Images aus dem Katalog mit freigegebenen Images zum Erstellen einer VM finden Sie unter [Verwenden eines Katalogs mit freigegebenen Images in Azure Lab Services](how-to-use-shared-image-gallery.md).

Weitere allgemeine Informationen zu Katalogen mit freigegebenen Images finden Sie in der [Übersicht über den Katalog mit freigegebenen Images](../virtual-machines/windows/shared-image-galleries.md).
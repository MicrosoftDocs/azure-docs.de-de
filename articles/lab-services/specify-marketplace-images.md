---
title: Angeben von Marketplace-Images für ein Lab in Azure Lab Services
description: In diesem Artikel wird gezeigt, wie Sie die Marketplace-Images angeben, die Lab-Ersteller zum Erstellen von Labs in einem Lab-Konto in Azure Lab Services verwenden können.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5c81c8d7a15d67055729a29e98f5b7e30f3d0764
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434735"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Angeben von für Lab-Ersteller verfügbare Marketplace-Images
Als Lab-Kontobesitzer können Sie die Marketplace-Images festlegen, die Lab-Ersteller zum Erstellen von Labs im Lab-Konto verwenden können. 

## <a name="select-images-available-for-labs"></a>Auswählen von für Labs verfügbaren Images
Wählen Sie links im Menü die Option **Marketplace-Images** aus. Standardmäßig wird die vollständige Liste der Images (sowohl aktivierte als auch deaktivierte) angezeigt. Sie können die Liste filtern, um nur aktivierte bzw. deaktivierte Images anzuzeigen. Wählen Sie dazu in der Dropdownliste oben die Option **Enabled only**/**Disabled only** (Nur aktivierte/Nur deaktivierte). 
    
![Seite mit Marketplace-Images](./media/tutorial-setup-lab-account/marketplace-images-page.png)

Die Marketplace-Images, die in der Liste angezeigt werden, sind die einzigen, die die folgenden Bedingungen erfüllen:
    
- Erstellt einen einzelnen virtuellen Computer
- Verwendet den Azure Resource Manager zum Bereitstellen von virtuellen Computern
- Erfordert nicht den Erwerb eines zusätzlichen Lizenzplans

## <a name="disable-images-for-a-lab"></a>Deaktivieren von Images für ein Lab 
Um ein einzelnes Image für ein Lab zu deaktivieren, wählen Sie in der letzten Spalte die **Auslassungspunkte (...)** und dann **Image deaktivieren** aus. 

![Deaktivieren eines Images](./media/tutorial-setup-lab-account/disable-one-image.png) 

Aktivieren Sie wahlweise das Kontrollkästchen vor dem Imagenamen, und wählen Sie auf der Symbolleiste **Ausgewählte Images deaktivieren** aus. 

Um mehrere Images gleichzeitig zu deaktivieren, aktivieren Sie die Kontrollkästchen vor den Imagenamen, und wählen Sie auf der Symbolleiste **Ausgewählte Images deaktivieren** aus. 

![Deaktivieren mehrerer Images](./media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Aktivieren von Images für ein Lab
Um ein deaktiviertes Image zu aktivieren, wählen Sie in der letzten Spalte die **Auslassungspunkte (...)** und dann **Image aktivieren** aus. Aktivieren Sie wahlweise das Kontrollkästchen vor dem Imagenamen, und wählen Sie auf der Symbolleiste **Ausgewählte Images aktivieren** aus. 

Um mehrere Images gleichzeitig zu deaktivieren, aktivieren Sie die Kontrollkästchen vor den Imagenamen, und wählen Sie auf der Symbolleiste **Ausgewählte Images aktivieren** aus. 

## <a name="enable-images-at-the-time-of-lab-creation"></a>Aktivieren von Images zum Zeitpunkt der Laberstellung
Zum Zeitpunkt der Erstellung des Labs können Sie weitere Images aktivieren: 

1. Melden Sie sich bei der [Azure Lab Services-Website](https://labs.azure.com) mithilfe der Anmeldeinformationen eines **Lab-Kontobesitzers** an.
2. Wählen Sie das standardmäßige VM-Image oder den Pfeil nach unten aus. 
3. Wählen Sie **Weitere Imageoptionen aktivieren** aus. 

    ![Weitere Imageoptionen aktivieren](./media/specify-marketplace-images/enable-more-images-menu.png)
4. Befolgen Sie die Anleitung aus dem vorherigen Abschnitt, um die Images zu aktivieren, die Sie auswählen. 
5. Möglicherweise müssen Sie das Fenster **Neues Lab** schließen und erneut öffnen, um die im vorherigen Schritt ausgewählten Images anzuzeigen. 



## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

- [Erstellen und Verwalten von Labs als Labbesitzer](how-to-manage-classroom-labs.md)
- [Einrichten und Veröffentlichen von Vorlagen als Labbesitzer](how-to-create-manage-template.md)
- [Konfigurieren und Steuern der Nutzung eines Labs als Labbesitzer](how-to-configure-student-usage.md)
- [Zugreifen auf Labs als Labbenutzer](how-to-use-classroom-lab.md)

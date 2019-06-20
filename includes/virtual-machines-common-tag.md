---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: ccc2b574ea054a1b0ecf32a1e59691050fb66fcf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178031"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Markieren eines virtuellen Computers mittels Vorlagen
Zunächst sehen wir uns das Verwenden von Tags mithilfe von Vorlagen an. [Diese Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) versieht die folgenden Ressourcen mit Tags: Compute (virtueller Computer), Speicher (Storage-Konto) und Netzwerk (öffentliche IP-Adresse, virtuelles Netzwerk und Netzwerkschnittstelle). Diese Vorlage gilt für einen virtuellen Windows-Computer, kann aber für virtuelle Linux-Computer angepasst werden.

Klicken Sie unter dem **Vorlagenlink** auf die Schaltfläche [Bereitstellen in Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Dadurch werden Sie zum [Azure-Portal](https://portal.azure.com/) weitergeleitet, in dem Sie diese Vorlage bereitstellen können.

![Einfache Bereitstellung mit Tags](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Diese Vorlage umfasst die folgenden Tags: *Abteilung*, *Anwendung* und *Erstellt von*. Sie können diese Tags direkt in der Vorlage hinzufügen oder bearbeiten, wenn Sie andere Tagnamen wünschen.

![Azure-Tags in einer Vorlage](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Wie Sie sehen können, werden die Tags als Schlüssel-Wert-Paare definiert und durch einen Doppelpunkt (:) getrennt. Die Tags müssen im folgenden Format definiert werden:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Speichern Sie Vorlagendatei nach dem Bearbeiten mit den Tags Ihrer Wahl.

Anschließend können Sie im Abschnitt **Parameter bearbeiten** die Werte für die Tags ausfüllen.

![Bearbeiten von Tags im Azure-Portal](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Klicken Sie auf **Erstellen** , um diese Vorlage mit Ihren Tagwerten bereitzustellen.

## <a name="tagging-through-the-portal"></a>Tags über das Portal erstellen
Nachdem Sie Ihre Ressourcen mit Tags erstellt haben, können Sie sie im Portal anzeigen, hinzufügen und löschen.

Wählen Sie das Tag-Symbol, um die Tags anzuzeigen:

![Symbol für Tags im Azure-Portal](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Fügen Sie ein neues Tag über das Portal durch Definieren Ihres eigenen Schlüssels-Wert-Paares hinzu, und speichern Sie es.

![Hinzufügen eines neuen Tags im Azure-Portal](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Ihr neues Tag sollte jetzt in der Liste der Tags für die Ressource angezeigt werden.

![Neues gespeichertes Tag im Azure-Portal](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)


---
title: Konvertieren von Portalvorlagen in Vorlagenspezifikationen
description: Hier wird beschrieben, wie Sie eine vorhandene Vorlage im Azure-Portalkatalog in eine Vorlagenspezifikation konvertieren.
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: c59275fca1eb3037b48b7293fc9e507df46b7fcb
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555949"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>Konvertieren des Vorlagenkatalogs im Portal in Vorlagenspezifikationen

Das Azure-Portal bietet eine Möglichkeit, Azure Resource Manager-Vorlagen (ARM-Vorlagen) in Ihrem Konto zu speichern. [Vorlagenspezifikationen](template-specs.md) bieten allerdings eine einfachere Möglichkeit, um Ihre Vorlagen mit Benutzern in Ihrer Organisation zu teilen und mit anderen Vorlagen zu verknüpfen. In diesem Artikel wird gezeigt, wie Sie vorhandene Vorlagen im Vorlagenkatalog in Vorlagenspezifikationen konvertieren.

Um festzustellen, ob Sie Vorlagen konvertieren müssen, sehen Sie sich den [Vorlagenkatalog im Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems) an. Der Ressourcentyp dieser Vorlagen ist `Microsoft.Gallery/myareas/galleryitems`.

## <a name="convert-with-powershell-script"></a>Konvertieren mit einem PowerShell-Skript

Um das Konvertieren von Vorlagen im Vorlagenkatalog zu vereinfachen, verwenden Sie ein PowerShell-Skript aus dem Repository mit Azure-Schnellstartvorlagen. Wenn Sie das Skript ausführen, können Sie entweder eine neue Vorlagenspezifikation für jede Vorlage erstellen oder eine Vorlage herunterladen, mit der die Vorlagenspezifikation erstellt wird. Das Skript löscht die Vorlage nicht aus dem Vorlagenkatalog.

1. Kopieren Sie das [Migrationsskript](https://github.com/Azure/azure-quickstart-templates/blob/master/201-templatespec-migrate-create/Migrate-GalleryItems.ps1). Speichern Sie eine lokale Kopie mit dem Namen *Migrate-GalleryItems.ps1*.
1. Um neue Vorlagenspezifikationen zu erstellen, geben Sie Werte für die Parameter `-ResourceGroupName` und `-Location` an. 

   Legen Sie `ItemsToExport` auf `MyGalleryItems` fest, um Ihre Vorlagen zu exportieren. Legen Sie diese Option auf `AllGalleryItems` fest, um alle Vorlagen zu exportieren, auf die Sie Zugriff haben.

   Im folgenden Beispiel werden neue Vorlagenspezifikationen für jede Vorlage in einer Ressourcengruppe mit dem Namen **migratedRG** erstellt. Dieses Skript erstellt die Ressourcengruppe, wenn sie noch nicht vorhanden ist.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ResourceGroupName migratedRG -Location westus2 -ItemsToExport MyGalleryItems
   ```

1. Um Vorlagen herunterzuladen, die Sie zum Erstellen der Vorlagenspezifikationen verwenden können, geben Sie keine Werte für die Ressourcengruppe oder den Speicherort an. Geben Sie stattdessen `-ExportToFile` an. Die Vorlage ist nicht mit Ihrer Vorlage im Katalog identisch. Stattdessen enthält sie eine Vorlagenspezifikationenressource, mit der die Vorlagenspezifikation für die Vorlage erstellt wird.

   Im folgenden Beispiel werden die Vorlagen heruntergeladen, ohne dass Vorlagenspezifikationen erstellt werden.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ItemsToExport MyGalleryItems -ExportToFile
   ```

   Informationen zum Bereitstellen der Vorlage, mit der die Vorlagenspezifikation erstellt wird, finden Sie unter [Schnellstart: Erstellen und Bereitstellen von Vorlagenspezifikationen (Vorschau)](quickstart-create-template-specs.md).

Weitere Informationen zum Skript und den zugehörigen Parametern finden Sie unter [Create TemplateSpecs from Template Gallery Templates](https://github.com/Azure/azure-quickstart-templates/tree/master/201-templatespec-migrate-create) (Erstellen von Vorlagenspezifikationen aus Vorlagen aus dem Vorlagenkatalog).

## <a name="manually-convert-through-portal"></a>Manuelles Konvertieren über das Portal

Sie können Vorlagen aus dem Katalog manuell in neue Vorlagenspezifikationen kopieren.

1. Öffnen Sie im Portal [Vorlagen (Vorschau)](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems).
1. Wählen Sie die zu migrierende Vorlage aus.
1. Wählen Sie **Vorlage anzeigen** aus.
1. Kopieren Sie den Vorlageninhalt.
1. Suchen Sie über die Suchleiste des Portals nach **Vorlagenspezifikationen**. Wählen Sie diese Option aus.
1. Wählen Sie **Create template spec** (Vorlagenspezifikation erstellen) aus.
1. Geben Sie Werte für Name, Abonnement, Ressourcengruppe und Speicherort an.
1. Klicken Sie auf **Weiter: Vorlage bearbeiten**.
1. Fügen Sie für den Inhalt der Vorlage die Vorlage ein, die Sie aus dem Vorlagenkatalog kopiert haben.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Wenn die Überprüfung erfolgreich abgeschlossen ist, wählen Sie **Erstellen** aus.

Wenn Sie die Vorlagenspezifikation für andere Benutzer in Ihrer Organisation freigeben müssen, [legen Sie die rollenbasierte Zugriffssteuerung](../../role-based-access-control/tutorial-role-assignments-group-powershell.md) für die Gruppe oder die Benutzer fest, die Zugriff benötigen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Vorlagenspezifikationen finden Sie unter [Azure Resource Manager-Vorlagenspezifikationen (Vorschau)](template-specs.md).

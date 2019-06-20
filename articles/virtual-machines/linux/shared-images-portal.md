---
title: Erstellen von freigegebenen Azure-VM-Images für Linux über das Portal | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Azure-Portal VM-Images erstellen und freigeben.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 1b760612d8d9a5ed0817ce662ed190f3477cd125
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65235851"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Erstellen eines Katalogs mit freigegebenen Images über das Azure-Portal

Der [Katalog mit freigegebenen Images](shared-image-galleries.md) vereinfacht das Freigeben benutzerdefinierter Images in Ihrer Organisation. Benutzerdefinierte Images sind wie Marketplace-Images, Sie erstellen sie jedoch selbst. Benutzerdefinierte Images können zum Laden von Bereitstellungsaufgaben verwendet werden, z.B. zum Vorabladen von Anwendungen sowie für Anwendungskonfigurationen und andere Betriebssystemkonfigurationen. 

Der Katalog mit geteilten Images gestattet es Ihnen, Ihre benutzerdefinierten VM-Images mit anderen Personen in Ihrer Organisation, innerhalb einer oder zwischen Regionen, innerhalb eines AAD-Mandanten zu teilen. Wählen Sie aus, welche Images Sie teilen möchten, in welchen Regionen Sie sie verfügbar machen möchten, und mit wem Sie sie teilen möchten. Sie können mehrere Kataloge erstellen, damit Sie geteilte Images logisch gruppieren können. 

Der Katalog ist eine Ressource der obersten Ebene, die vollständige rollenbasierte Zugriffssteuerung (RBAC) bereitstellt. Images bieten Versionsverwaltung, und Sie können sich entschließen, jede Imageversion in eine andere Gruppe von Azure-Regionen zu replizieren. Der Katalog funktioniert nur mit verwalteten Images.

Die Funktion „Katalog mit freigegebenen Images“ verfügt über mehrere Ressourcentypen. Wir werden diese in diesem Artikel verwenden oder erstellen:

| Resource | BESCHREIBUNG|
|----------|------------|
| **Verwaltetes Image** | Dies ist ein Basisimage, das eigenständig oder zum Erstellen einer **Imageversionen** in einem Imagekatalog verwendet werden kann. Verwaltete Images werden aus generalisierten virtuellen Computern erstellt. Ein verwaltetes Image ist ein spezieller VHD-Typ, mit dem mehrere virtuelle Computer und jetzt auch Versionen von freigegebenen Images erstellt werden können. |
| **Imagekatalog** | Wie der Azure Marketplace ist ein **Imagekatalog** ein Repository zum Verwalten und Teilen von Images, aber Sie kontrollieren, wer Zugriff hat. |
| **Imagedefinition** | Abbilder sind innerhalb eines Katalogs definiert und enthalten intern Informationen über das Image und die Anforderungen für seine Verwendung. Dies schließt ein, ob das Image Windows oder Linux ist, Anmerkungen zu dieser Version und Anforderungen an den minimalen und maximalen Arbeitsspeicher. Es ist eine Definition eines Imagetyps. |
| **Imageversion** | Eine **Imageversion** ist, was Sie verwenden, um einen virtuellen Computer zu erstellen, wenn Sie einen Katalog verwenden. Sie können nach Bedarf mehrere Versionen eines Images für Ihre Umgebung haben. Wie bei einem verwalteten Image wird, wenn Sie eine **Imageversion** zum Erstellen einer VM verwenden, wird die Imageversion verwendet, um neue Datenträger für den virtuellen Computer zu erstellen. Imageversionen können mehrmals verwendet werden. |


## <a name="before-you-begin"></a>Voraussetzungen

Für das Beispiel in diesem Artikel muss ein verwaltetes Image vorhanden sein. Sie können im [Tutorial: Erstellen eines benutzerdefinierten Images eines virtuellen Azure-Computers mit Azure PowerShell](tutorial-custom-images.md) ggf. eines erstellen. Wenn das verwaltete Image einen Datenträger für Daten enthält, darf dieser nicht größer als 1 TB sein.

Ersetzen Sie beim Durcharbeiten dieses Artikels bei Bedarf den Namen der Ressourcengruppe und des virtuellen Computers.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms-from-an-image"></a>Erstellen von VMs aus Images

Wenn die Imageversion vollständig ist, können Sie neue VMs erstellen. 

Dieses Beispiel erstellt den virtuellen Computer *myVMfromImage* in *myResourceGroup* im Rechenzentrum *USA, Osten*.

1. Wählen Sie im Menü oben auf der Seite für Ihre Imageversion **VM erstellen** aus.
1. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, und geben Sie *myResourceGroup* als Namen ein.
1. Geben Sie unter **Name des virtuellen Computers** die Zeichenfolge *myVM* ein.
1. Wählen Sie als **Region** die Option *USA, Osten* aus.
1. Übernehmen Sie für **Verfügbarkeitsoptionen** den Standardwert *Keine Infrastrukturredundanz erforderlich*.
1. Der Wert für **Image** sollte automatisch ausgefüllt werden, wenn Sie auf der Seite für die Imageversion begonnen haben.
1. Wählen Sie für **Größe** in der Liste der verfügbaren Größen eine VM-Größe aus, und klicken Sie dann auf „Auswählen“.
1. Wählen Sie unter **Administratorkonto** eine der Optionen **Kennwort** oder **Öffentlicher SSH-Schlüssel** aus, und geben Sie dann Ihre Daten ein.
1. Wenn Sie Remotezugriff auf den virtuellen Computer zulassen möchten, wählen Sie unter **Öffentliche Eingangsports** die Option **Ausgewählte Ports zulassen** aus, und wählen Sie dann in der Dropdownliste **SSH (22)** aus. Wenn Sie keinen Remotezugriff auf den virtuellen Computer ermöglichen möchten, lassen Sie für ausgewählte **Öffentliche Eingangsports** die Option **Keine** ausgewählt.
1. Wählen Sie abschließend die Schaltfläche **Überprüfen + erstellen** im unteren Seitenbereich aus.
1. Nachdem die VM-Konfiguration erfolgreich überprüft wurde, wählen Sie im unteren Seitenbereich **Erstellen** aus, um die Bereitstellung zu starten.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ressourcengruppe, virtueller Computer und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie löschen. Wählen Sie dazu die Ressourcengruppe für den virtuellen Computer aus, klicken Sie auf **Löschen**, und bestätigen Sie den Namen der zu löschenden Ressourcengruppe.

Wenn Sie einzelne Ressourcen löschen möchten, müssen Sie diese in umgekehrter Reihenfolge löschen. Um beispielsweise eine Imagedefinition zu löschen, müssen Sie alle Imageversionen löschen, die aus diesem Image erstellt wurden.

## <a name="next-steps"></a>Nächste Schritte

Sie können auch mithilfe von Vorlagen eine Ressource im Katalog für freigegebene Images erstellen. Es stehen mehrere Azure-Schnellstartvorlagen zur Verfügung: 

- [Erstellen eines Katalogs mit freigegebenen Images](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Erstellen einer Imagedefinition in einem Katalog mit freigegebenen Images](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Erstellen einer Imageversion in einem Katalog mit freigegebenen Images](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Erstellen eines virtuellen Computers aus einer Imageversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Weitere Informationen zu Katalogen mit geteilten Images finden Sie in der [Übersicht](shared-image-galleries.md). Sollten Probleme auftreten, sehen Sie unter [Problembehandlung für Kataloge mit freigegebenen Images](troubleshooting-shared-images.md) nach.


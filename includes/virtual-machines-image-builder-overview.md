---
author: cynthn
ms.author: cynthn
ms.date: 04/30/2019
ms.topic: include
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: e1b3b5fe603072069cb3a19c7597fcc1872fefd7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178073"
---
Standardisierte VM-Images ermöglichen es Organisationen, in die Cloud zu migrieren und die Konsistenz der Bereitstellung sicherzustellen. Die Images beinhalten üblicherweise vordefinierte Sicherheits- und Konfigurationseinstellungen und die notwendige Software. Das Einrichten Ihrer eigenen Imaging-Pipeline erfordert Zeit, Infrastruktur und Setup, aber mit dem Azure VM Image Builder stellen Sie eine einfache Konfiguration zur Verfügung, die Ihr Image beschreibt, senden es an den Dienst, und das Image wird erstellt und verteilt.
 
Mit dem Azure VM Image Builder (Azure Image Builder) können Sie mit einem Windows- oder Linux-basierten Azure Marketplace-Image, vorhandenen benutzerdefinierten Images oder Red Hat Enterprise Linux (RHEL) ISO starten und beginnen, Ihre eigenen Anpassungen vorzunehmen. Da der Image Builder auf [HashiCorp Packer](https://packer.io/) aufbaut, können Sie auch Ihre vorhandenen Packer Shell Provisioner-Skripts importieren. Sie können auch angeben, wo Ihre Images gehostet werden sollen – im Azure-Katalog mit freigegebenen Images (virtual-machines-common-shared-image-galleries.md), als verwaltetes Image oder als VHD.

> [!IMPORTANT]
> Azure Image Builder ist derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Previewfunktionen

In der Vorschauversion werden diese Funktionen unterstützt:

- Erstellung von Golden Baseline-Images, die Ihre Mindestsicherheit und Unternehmenskonfigurationen umfassen und es den Abteilungen ermöglichen, sie weiter an ihre Bedürfnisse anzupassen.
- Patchen von vorhandenen Images: Image Builder ermöglicht es Ihnen, bestehende benutzerdefinierte Images kontinuierlich zu patchen.
- Die Integration mit dem Azure-Katalog mit freigegebenen Images ermöglicht es Ihnen, Images global zu verteilen, zu versionieren und zu skalieren, und bietet Ihnen ein System für die Imageverwaltung.
- Für die Integration in bestehende Image Buildpipelines, rufen Sie einfach Image Builder aus Ihrer Pipeline auf, oder verwenden Sie die einfache Vorschauversion der Azure DevOps-Task von Image Builder.
- Migrieren Sie eine bestehende Pipeline zur Imageanpassung zu Azure. Verwenden Sie Ihre vorhandenen Skripts, Befehle und Prozesse, um Images anzupassen.
- Verwenden Sie die Unterstützung von Red Hat für „Bring Your Own Subscription“ (BYOS, Nutzen des eigenen Abonnements). Erstellen Sie Red Hat Enterprise-Images zur Verwendung mit Ihren berechtigten, ungenutzten Red Hat-Abonnements.
- Erstellung von Images im VHD-Format.
 

## <a name="regions"></a>Regions
Der Azure Image Builder-Dienst wird in folgenden Regionen als Vorschauversion verfügbar sein. Images können außerhalb dieser Regionen verteilt werden.
- USA (Ost)
- USA (Ost) 2
- USA, Westen-Mitte
- USA (Westen)
- USA, Westen 2

## <a name="os-support"></a>Betriebssystemunterstützung
AIB unterstützt Basisbetriebssystem-Images aus dem Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6
- CentOS 7.6
- Windows 2016
- Windows 2019


## <a name="how-it-works"></a>So funktioniert's


![Schematische Darstellung von Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder.png)

Der Azure Image Builder ist ein vollständig verwalteter Azure-Dienst, der für einen Azure-Ressourcenanbieter zugänglich ist. Der Azure Image Builder-Prozess besteht aus drei Hauptteilen: Quelle, Anpassung und Verteilung. Diese werden in einer Vorlage dargestellt. Das folgende Diagramm zeigt die Komponenten, mit einigen ihrer Eigenschaften. 
 


**Image Builder-Prozess** 

![Schematische Darstellung des Azure Image Builder-Prozesses](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Erstellen Sie die Imagevorlage als eine JSON-Datei an. Diese.json-Datei enthält Informationen über die Imagequelle, Anpassungen und Verteilung. Im [GitHub-Repository für Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts) finden Sie zahlreiche Beispiele.
1. Durch Senden an den Dienst wird in der von Ihnen angegebenen Ressourcengruppe ein Image-Vorlagenartefakt erstellt. Im Hintergrund lädt der Image Builder das Quellimage oder ISO und bei Bedarf Skripts herunter. Diese werden in einer separaten Ressourcengruppe gespeichert, die automatisch in Ihrem Abonnement im folgenden Format erstellt wird: IT_<DestinationResourceGroup>_<TemplateName>. 
1. Sobald die Imagevorlage erstellt ist, können Sie mit den Zusammenstelle des Images beginnen. Im Hintergrund verwendet der Image Builder die Vorlagen- und Quelldateien, um eine VM, ein Netzwerk und einen Speicher in der Ressourcengruppe IT_<DestinationResourceGroup>_<TemplateName> zu erstellen.
1. Im Rahmen der Imageerstellung verteilt Image Builder das Bild entsprechend der Vorlage und löscht dann die zusätzlichen Ressourcen in der Ressourcengruppe IT_<DestinationResourceGroup>_<TemplateName>, die für den Prozess erstellt wurde.


## <a name="permissions"></a>Berechtigungen

Damit Azure VM Image Builder die Images entweder an die verwalteten Images oder an einen Azure-Katalog mit freigegebenen Images verteilen kann, müssen Sie die Berechtigungen „Mitwirkender“ für den Dienst „Azure Virtual Machine Image Builder“ (App-ID: cf32a0cc-373c-47c9-9156-0db11f6a6dfc) in den Ressourcengruppen angeben. 

Wenn Sie ein vorhandenes benutzerdefiniertes Image oder eine benutzerdefinierte Imageversion verwenden, benötigt der Azure Image Builder mindestens Lesezugriff auf diese Ressourcengruppen.

Sie können den Zugriff über die Azure-Befehlszeilenschnittstelle zuweisen:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Wenn das Dienstkonto nicht gefunden wird, kann dies bedeuten, dass das Abonnement, in dem Sie die Rollenzuweisung hinzufügen, noch nicht für den Ressourcenanbieter registriert ist.


## <a name="costs"></a>Kosten
Bei der Erstellung, dem Aufbau und der Speicherung von Images mit Azure Image Builder fallen einige Computing-, Netzwerk- und Speicherkosten an. Diese Kosten sind mit Kosten beim manuellen Erstellen von benutzerdefinierten Images vergleichbar. Für die Ressourcen werden Ihre üblichen Azure-Preisen abgerechnet. 

Während der Imagestellung werden Dateien heruntergeladen und in der `IT_<DestinationResourceGroup>_<TemplateName>`-Ressourcengruppe gespeichert. Dadurch werden die Speicherkosten reduziert. Sie können die Imagevorlage nach der Erstellung löschen, wenn sie nicht mehr benötigt wird.
 
Image Builder erstellt eine VM mit einer D1v2 VM-Größe und dem für die VM erforderlichen Speicher und Netzwerk. Diese Ressourcen werden für die Dauer des Erstellungsprozesses benötigt und werden gelöscht, sobald der Image Builder die Erstellung des Images abgeschlossen hat. 
 
Azure Image Builder verteilt das Image an die von Ihnen ausgewählten Regionen. Das könnte zu Kosten für ausgehenden Datenverkehr führen.
 
## <a name="next-steps"></a>Nächste Schritte 
 
Um den Azure Image Builder auszuprobieren, lesen Sie die Artikel zum Erstellen von [Linux](../articles/virtual-machines/linux/image-builder.md)- oder [Windows](../articles/virtual-machines/windows/image-builder.md)-Images.
 
 

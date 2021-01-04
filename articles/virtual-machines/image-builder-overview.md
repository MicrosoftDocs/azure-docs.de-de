---
title: Weitere Informationen zu Azure Image Builder (Vorschauversion)
description: Erfahren Sie mehr über Azure Image Builder für virtuelle Computer in Azure.
author: danielsollondon
ms.author: danis
ms.date: 05/02/2019
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: imaging
ms.reviewer: cynthn
ms.openlocfilehash: fb596352011bcce3130d22b7277444bc45679f4c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96839721"
---
# <a name="preview-azure-image-builder-overview"></a>Vorschau: Übersicht über Azure Image Builder

Standardisierte VM-Images ermöglichen es Organisationen, in die Cloud zu migrieren und die Konsistenz der Bereitstellung sicherzustellen. Die Images beinhalten üblicherweise vordefinierte Sicherheits- und Konfigurationseinstellungen und die notwendige Software. Das Einrichten Ihrer eigenen Imaging-Pipeline erfordert Zeit, Infrastruktur und Setup, aber mit dem Azure VM Image Builder stellen Sie eine einfache Konfiguration zur Verfügung, die Ihr Image beschreibt, senden es an den Dienst, und das Image wird erstellt und verteilt.
 
Mit dem Azure VM Image Builder (Azure Image Builder) können Sie mit einem Windows- oder Linux-basierten Azure Marketplace-Image, vorhandenen benutzerdefinierten Images oder Red Hat Enterprise Linux (RHEL) ISO starten und beginnen, Ihre eigenen Anpassungen vorzunehmen. Da der Image Builder auf [HashiCorp Packer](https://packer.io/) aufbaut, können Sie auch Ihre vorhandenen Packer Shell Provisioner-Skripts importieren. Sie können auch angeben, wo Ihre Images gehostet werden sollen: in [Azure Shared Image Gallery](shared-image-galleries.md), als verwaltetes Image oder als VHD.

> [!IMPORTANT]
> Azure Image Builder ist derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Previewfunktionen

In der Vorschauversion werden diese Funktionen unterstützt:

- Erstellung von Golden Baseline-Images, die Ihre Mindestsicherheit und Unternehmenskonfigurationen umfassen und es den Abteilungen ermöglichen, sie weiter an ihre Bedürfnisse anzupassen.
- Patchen von vorhandenen Images: Image Builder ermöglicht es Ihnen, bestehende benutzerdefinierte Images kontinuierlich zu patchen.
- Verbinden Sie Image Builder mit Ihren vorhandenen virtuellen Netzwerken, damit Sie eine Verbindung mit vorhandenen Konfigurationsservern (DSC, Chef, Puppet usw.), Dateifreigaben oder anderen routingfähigen Servern/Diensten herstellen können.
- Die Integration mit dem Azure-Katalog mit freigegebenen Images ermöglicht es Ihnen, Images global zu verteilen, zu versionieren und zu skalieren, und bietet Ihnen ein System für die Imageverwaltung.
- Für die Integration in bestehende Image Buildpipelines, rufen Sie einfach Image Builder aus Ihrer Pipeline auf, oder verwenden Sie die einfache Vorschauversion der Azure DevOps-Task von Image Builder.
- Migrieren Sie eine bestehende Pipeline zur Imageanpassung zu Azure. Verwenden Sie Ihre vorhandenen Skripts, Befehle und Prozesse, um Images anzupassen.
- Erstellung von Images im VHD-Format zur Unterstützung von Azure Stack.
 

## <a name="regions"></a>Regions
Der Azure Image Builder-Dienst wird in folgenden Regionen als Vorschauversion verfügbar sein. Images können außerhalb dieser Regionen verteilt werden.
- East US
- USA (Ost) 2
- USA, Westen-Mitte
- USA (Westen)
- USA, Westen 2
- Nordeuropa
- Europa, Westen

## <a name="os-support"></a>Betriebssystemunterstützung
AIB unterstützt Basisbetriebssystem-Images aus dem Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise (mehrere Sitzungen)/Professional
- Windows 2016
- Windows 2019

Unterstützung für RHEL ISOs wird nicht mehr geboten.

## <a name="how-it-works"></a>Funktionsweise

Der Azure Image Builder ist ein vollständig verwalteter Azure-Dienst, der für einen Azure-Ressourcenanbieter zugänglich ist. Der Azure Image Builder-Prozess besteht aus drei Hauptteilen: Quelle, Anpassung und Verteilung. Diese werden in einer Vorlage dargestellt. Das folgende Diagramm zeigt die Komponenten, mit einigen ihrer Eigenschaften. 
 

**Image Builder-Prozess** 

![Schematische Darstellung des Azure Image Builder-Prozesses](./media/image-builder-overview/image-builder-process.png)

1. Erstellen Sie die Imagevorlage als eine JSON-Datei an. Diese.json-Datei enthält Informationen über die Imagequelle, Anpassungen und Verteilung. Im [GitHub-Repository für Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts) finden Sie zahlreiche Beispiele.
1. Durch Senden an den Dienst wird in der von Ihnen angegebenen Ressourcengruppe ein Image-Vorlagenartefakt erstellt. Im Hintergrund lädt der Image Builder das Quellimage oder ISO und bei Bedarf Skripts herunter. Diese werden in einer separaten Ressourcengruppe gespeichert, die automatisch in Ihrem Abonnement im folgenden Format erstellt wird: IT_\<DestinationResourceGroup>_\<TemplateName>. 
1. Sobald die Imagevorlage erstellt ist, können Sie mit den Zusammenstelle des Images beginnen. Im Hintergrund verwendet der Image Builder die Vorlagen- und Quelldateien, um eine VM (Standardgröße: Standard_D1_v2), ein Netzwerk, eine öffentliche IP-Adresse, eine NSG und einen Speicher in der Ressourcengruppe IT_\<DestinationResourceGroup>_\<TemplateName> zu erstellen.
1. Im Rahmen der Imageerstellung verteilt Image Builder das Bild entsprechend der Vorlage und löscht dann die zusätzlichen Ressourcen in der Ressourcengruppe IT_\<DestinationResourceGroup>_\<TemplateName>, die für den Prozess erstellt wurde.


## <a name="permissions"></a>Berechtigungen
Wenn Sie sich für die (AIB) registrieren, wird dem AIB-Dienst die Berechtigung zum Erstellen, Verwalten und Löschen einer Stagingressourcengruppe (IT_ *) und zum Hinzufügen von Ressourcen erteilt, die für die Imageerstellung erforderlich sind. Dies erfolgt dadurch, dass im Rahmen einer erfolgreichen Registrierung ein AIB-Dienstprinzipalname (Service Principal Name, SPN) in Ihrem Abonnement verfügbar gemacht wird.

Damit Azure VM Image Builder Images an die verwalteten Images oder an eine Shared Image Gallery verteilen kann, müssen Sie eine benutzerseitig zugewiesene Azure-Identität erstellen, die über Berechtigungen zum Lesen und Schreiben von Images verfügt. Wenn Sie auf Azure Storage zugreifen, sind dafür Berechtigungen zum Lesen privater Container erforderlich.

Zunächst müssen Sie der Dokumentation zum [Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) folgen, um eine Identität zu erstellen.

Nachdem Sie über die Identität verfügen, müssen Sie ihr Berechtigungen erteilen. Dazu können Sie eine benutzerdefinierte Azure-Rollendefinition verwenden und ihr dann die benutzerseitig zugewiesene verwaltete Identität zur Verwendung zuweisen.

Berechtigungen werden ausführlicher [hier](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements) erläutert, und die Implementierung ist aus den Beispielen ersichtlich.

> [!Note]
> Bisher würden Sie für AIB den AIB-SPN verwenden und diesem Berechtigungen für die Imageressourcengruppen erteilen. Wir geben dieses Modell auf, um zukünftige Funktionen zu ermöglichen. Vom 26. Mai 2020 an akzeptiert der Image Builder keine Vorlagen mehr, die nicht über eine benutzerseitig zugewiesene Identität verfügen, vorhandene Vorlagen müssen mit einer [Benutzeridentität](./linux/image-builder-json.md) erneut an den Dienst übermittelt werden. In den Beispielen hier wurde bereits gezeigt, wie Sie eine benutzerseitig zugewiesene Identität erstellen und sie einer Vorlage hinzufügen. Weitere Informationen finden Sie in dieser [Dokumentation](https://github.com/danielsollondon/azvmimagebuilder#service-updates-and-latest-release-information) zu dieser Änderung und Updates zu Releases.

## <a name="costs"></a>Kosten
Bei der Erstellung, dem Aufbau und der Speicherung von Images mit Azure Image Builder fallen einige Computing-, Netzwerk- und Speicherkosten an. Diese Kosten sind mit Kosten beim manuellen Erstellen von benutzerdefinierten Images vergleichbar. Für die Ressourcen werden Ihre üblichen Azure-Preisen abgerechnet. 

Während der Imagestellung werden Dateien heruntergeladen und in der `IT_<DestinationResourceGroup>_<TemplateName>`-Ressourcengruppe gespeichert. Dadurch werden die Speicherkosten reduziert. Sie können die **Imagevorlage** nach der Erstellung löschen, wenn sie nicht mehr benötigt wird.
 
Image Builder erstellt eine VM mit einer D1v2 VM-Größe und dem für die VM erforderlichen Speicher und Netzwerk. Diese Ressourcen werden für die Dauer des Erstellungsprozesses benötigt und werden gelöscht, sobald der Image Builder die Erstellung des Images abgeschlossen hat. 
 
Azure Image Builder verteilt das Image an die von Ihnen ausgewählten Regionen. Das könnte zu Kosten für ausgehenden Datenverkehr führen.

## <a name="hyper-v-generation"></a>Hyper-V Generation
Image Builder unterstützt derzeit nativ nur die Erstellung von Images der Hyper-V-Generation 1 (Gen1) für Azure Shared Image Gallery (SIG) oder Managed Image. Wenn Sie Gen2-Images erstellen möchten, müssen Sie ein Gen2-Quellimage verwenden und an VHD verteilen. Danach müssen Sie ein verwaltetes Image von der VHD erstellen und es als Gen2-Image in SIG einfügen.
 
## <a name="next-steps"></a>Nächste Schritte 
 
Um den Azure Image Builder auszuprobieren, lesen Sie die Artikel zum Erstellen von [Linux](./linux/image-builder.md)- oder [Windows](./windows/image-builder.md)-Images.


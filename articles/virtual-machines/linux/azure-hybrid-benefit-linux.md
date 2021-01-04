---
title: Azure-Hybridvorteil und virtuelle Linux-Computer
description: Mit dem Azure-Hybridvorteil können Sie bei virtuellen Linux-Computern, die in Azure ausgeführt werden, Geld sparen.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: f4dce87533927eebef792987b87d148e6a5268b4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500613"
---
# <a name="azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Azure-Hybridvorteil – Anwendung bei virtuelle Linux-Computern

## <a name="overview"></a>Übersicht

Mit dem Azure-Hybridvorteil können Sie die virtuellen Computer (VMs) Ihrer lokalen RHEL- (Red Hat Enterprise Linux) und SLES-Server (SUSE Linux Enterprise Server) ganz einfach zu Azure migrieren, indem Sie Ihr eigenes, bereits vorhandenes Red Hat- oder SUSE-Softwareabonnement verwenden. Dank diesem Vorteil bezahlen Sie lediglich die Infrastrukturkosten Ihres virtuellen Computers, da die Softwaregebühr durch Ihr RHEL- bzw. SLES-Abonnement abgedeckt ist. Der Vorteil gilt für alle RHEL und SLES Marketplace-Images mit nutzungsbasierter Bezahlung.

> [!IMPORTANT]
> Der Azure-Hybridvorteil für Linux-VMs ist jetzt öffentlich verfügbar.


## <a name="benefit-description"></a>Beschreibung des Vorteils

Mit dem Azure-Hybridvorteil können Sie Ihre lokalen RHEL- und SLES-Server ganz einfach zu Azure migrieren, indem Sie die nutzungsbasierte Bezahlung (PAYG, Pay As You Go) von vorhandenen RHEL- und SLES-VMs in Azure in eine BYOS-Abrechnung (Bring Your Own Subscription) umwandeln. In der Regel wird für virtuelle Computer, die über PAYG-Images in Azure bereitgestellt werden, nicht nur eine Infrastrukturgebühr, sondern auch eine Softwaregebühr berechnet. Mit dem Azure-Hybridvorteil können virtuelle Computer mit nutzungsbasierter Bezahlung ohne erneute Bereitstellung auf ein BYOS-Abrechnungsmodell umgestellt werden, wodurch das Risiko eines Ausfalls vermieden wird.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Darstellung der Kosten für den Azure-Hybridvorteil auf virtuellen Linux-Computern.":::

Wenn Sie den Vorteil für einen virtuellen RHEL- oder SLES-Computer aktivieren, wird Ihnen keine zusätzliche Softwaregebühr berechnet, die normalerweise für einen virtuellen PAYG-Computer anfällt. Stattdessen wird für Ihren virtuellen Computer eine BYOS-Gebühr berechnet, die nur die Hardwaregebühr, jedoch keine Softwaregebühr beinhaltet.

Wenn Sie möchten, können Sie einen virtuellen Computer, für den der Vorteil aktiviert wurde, wieder auf ein PAYG-Abrechnungsmodell umstellen.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Umfang der Azure-Hybridvorteilsberechtigung für virtuelle Linux-Computer

Der Azure-Hybridvorteil ist für alle RHEL und SLES Marketplace-Images mit nutzungsbasierte Bezahlung verfügbar. Für RHEL oder SLES Marketplace-BYOS-Images oder benutzerdefinierte Images ist der Vorteil noch nicht verfügbar.

Die Hybridvorteile von reservierten Instanzen, Dedicated Hosts und SQL berechtigen nicht zum Azure-Hybridvorteil, wenn Sie bereits den Vorteil für virtuellen Linux-Computer nutzen.

## <a name="how-to-get-started"></a>Erste Schritte

### <a name="red-hat-customers"></a>Red Hat-Kunden

Der Azure-Hybridvorteil für RHEL ist für Kunden verfügbar, die über aktive/nicht genutzte RHEL-Abonnements verfügen, die für die Verwendung in Azure berechtigt sind und für die mindestens eines der Abonnements für die Verwendung in Azure mit dem [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)-Programm aktiviert ist. 

1.  Aktivieren Sie mindestens eines Ihrer berechtigten RHEL-Abonnements für die Verwendung in Azure über die [Red Hat Cloud Access-Benutzeroberfläche für Kunden](https://access.redhat.com/management/cloud).
1.  Die von Ihnen während des Red Hat Cloud Access-Aktivierungsprozesses bereitgestellten Azure-Abonnements sind dann berechtigt, den Azure-Hybridvorteil zu nutzen.
1.  Wenden Sie den Azure-Hybridvorteil auf alle Ihre vorhandenen RHEL-VMs mit nutzungsbasierter Zahlung sowie auf alle neuen RHEL-VMs an, die Sie auf Grundlage von Images im Azure Marketplace mit nutzungsbasierter Zahlung bereitstellen.
1.  Befolgen Sie die empfohlenen [weiteren Schritte](https://access.redhat.com/articles/5419341) für das Konfigurieren von Updatequellen für Ihre RHEL-VMs und für Compliancerichtlinien für RHEL-Abonnements.


### <a name="suse-customers"></a>SUSE-Kunden

1.    Registrieren Sie sich beim SUSE Public Cloud-Programm.
1.    Wenden Sie den Vorteil über die Azure CLI auf Ihre vorhandenen VMs an.
1.    Registrieren Sie die virtuellen Computer, auf die der Vorteil angewendet wird, bei einer separaten Quelle für Updates.


### <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Aktivieren und Deaktivieren des Vorteils über die Azure-Befehlszeilenschnittstelle

Mit dem Befehl „az vm update“ können Sie vorhandene virtuelle Computer aktualisieren. Führen Sie bei virtuellen RHEL-Computern den Befehl mit einem „--license-type“-Parameter von „RHEL_BYOS“ aus. Führen Sie bei virtuellen SLES-Computern den Befehl mit einem „--license-type“-Parameter von „SLES_BYOS“ aus.

#### <a name="cli-example-to-enable-the-benefit"></a>Beispiel für die Aktivierung des Vorteils über die Befehlszeilenschnittstelle:
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
#### <a name="cli-example-to-disable-the-benefit"></a>Beispiel für die Deaktivierung des Vorteils über die Befehlszeilenschnittstelle:
Verwenden Sie zum Deaktivieren des Vorteils den „license-type“-Wert „None“.
```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

#### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>Beispiel für die Aktivierung des Vorteils für eine Vielzahl von virtuellen Computern über die Befehlszeilenschnittstelle
Zum Aktivieren des Vorteils für eine Vielzahl von virtuellen Computern können Sie den `--ids`-Parameter in der Azure-Befehlszeilenschnittstelle verwenden.

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file containing a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

Anhand der folgenden Beispiele werden zwei Methoden zum Abrufen einer Liste mit Ressourcen-IDs vorgestellt: eine auf der Ressourcengruppenebene, die andere auf der Abonnementebene.
```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="check-ahb-status-of-a-vm"></a>Prüfen des AHB-Status eines virtuellen Computers
Es gibt zwei Möglichkeiten, den AHB-Status (Azure Hybrid Benefit, Azure-Hybridvorteil) einer VM anzuzeigen: über die Azure CLI und mithilfe von Azure Instance Metadata Service (Azure IMDS).


### <a name="azure-cli"></a>Azure CLI

Hierfür können Sie den Befehl `az vm get-instance-view` verwenden. Suchen Sie in der Antwort nach einem „licenseType“-Feld. Wenn das „licenseType“-Feld vorhanden ist und der Wert „RHEL_BYOS“ oder „SLES_BYOS“ lautet, ist für Ihren virtuellen Computer der Vorteil aktiviert.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure-Instanzmetadatendienst

Über den virtuellen Computer selbst können Sie die bestätigten IMDS-Metadaten abfragen, um den Wert für „licenseType“ für Ihren virtuellen Computer zu ermitteln. Wenn der „licenseType“-Wert „RHEL_BYOS“ oder „SLES_BYOS“ lautet, ist der Vorteil für Ihren virtuellen Computer aktiviert. Weitere Informationen zu bestätigten Metadaten finden Sie [hier](./instance-metadata-service.md#attested-data).

## <a name="compliance"></a>Compliance

### <a name="red-hat"></a>Red Hat

Kunden, die den Azure-Hybridvorteil für RHEL verwenden, stimmen den [rechtlichen Standardbedingungen](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) und den [Standarddatenschutzbestimmungen](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) für RHEL-Angebote im Azure Marketplace zu.

Kunden, die den Azure-Hybridvorteil für RHEL nutzen, haben drei Möglichkeiten, Softwareupdates und Patches für diese VMs bereitzustellen:

1.  [Red Hat-Updateinfrastruktur (RHUI)](../workloads/redhat/redhat-rhui.md) (Standardoption)
1.  Red Hat Satellite Server
1.  Red Hat Subscription Manager

Kunden, die sich für die RHUI-Option entschließen, können diese weiterhin als Hauptquelle für Updates für ihre RHEL-VMs mit Azure-Hybridvorteil nutzen, ohne RHEL-Abonnements an diese VMs anfügen zu müssen.  Kunden, die sich für die RHUI-Option entscheiden, sind dafür verantwortlich, für die RHEL-Abonnementscompliance zu sorgen.

Kunden, die sich für Red Hat Satellite-Server oder Red Hat Subscription Manager entscheiden, sollten die RHUI-Konfiguration entfernen und dann ein RHEL-Abonnement mit Cloud Access-Aktivierung an ihre RHEL-VMs mit Azure-Hybridvorteil anfügen.  

Weitere Informationen zur Red Hat-Abonnementscompliance, Softwareupdates und Quellen für RHEL-VMs mit Azure-Hybridvorteil finden Sie [hier](https://access.redhat.com/articles/5419341).

### <a name="suse"></a>SUSE

Wenn Sie den Azure-Hybridvorteil für Ihre virtuellen SLES-Computer verwenden möchten, müssen Sie sich zunächst beim SUSE Public Cloud-Programm registrieren. Weitere Informationen zum Programm finden Sie hier. Nachdem Sie SUSE-Abonnements erworben haben, müssen Sie Ihre virtuellen Computer mit diesen Abonnements bei Ihrer eigenen Quelle für Updates über SUSE Customer Center, den Subscription Management Tool-Server oder SUSE Manager registrieren.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen
*F: Kann ich den Lizenztyp „RHEL_BYOS“ mit einem SLES-Image oder umgekehrt verwenden?*

A: Nein, das ist nicht möglich. Wenn Sie versuchen, einen Lizenztyp einzugeben, der nicht mit der auf Ihrem virtuellen Computer ausgeführten Distribution übereinstimmt, werden die Metadaten für die Abrechnung nicht aktualisiert. Wenn Sie jedoch versehentlich den falschen Lizenztyp eingeben und Ihren virtuellen Computer dann noch mal mit dem richtigen Lizenztyp aktualisieren, wird der Vorteil dagegen aktiviert.

*F: Ich habe mich bei Red Hat Cloud Access registriert, kann aber den Vorteil auf meinen virtuellen RHEL-Computern dennoch nicht aktivieren. Wie gehe ich vor?*

A: Es kann einige Zeit in Anspruch nehmen, bis die Registrierung Ihres Red Hat Cloud Access-Abonnements von Red Hat an Azure weitergegeben wird. Wenn der Fehler nach einem Werktag weiterhin auftritt, wenden Sie sich an den Microsoft Support.

*F: Ich habe eine VM mit einem „Golden Image“ für RHEL BYOS bereitgestellt. Kann ich die Abrechnung für diese Images von BYOS zur nutzungsbasierten Bezahlung ändern?*

A: Nein, das ist nicht möglich. Der Azure-Hybridvorteil unterstützt eine Konvertierung nur für Images mit nutzungsbasierter Bezahlung.

*F: Ich habe eine VM mit einem „Golden Image“ für RHEL BYOS bereitgestellt. Kann ich die Abrechnung für diese Images von BYOS zur nutzungsbasierten Bezahlung ändern?*

A: Nein, das ist nicht möglich. Der Azure-Hybridvorteil unterstützt eine Konvertierung nur für Images mit nutzungsbasierter Bezahlung.

*F: Ich habe ein eigenes RHEL-Image aus meinem lokalen System (über Azure Migrate, ASR oder eine andere Methode) in Azure hochgeladen. Kann ich die Abrechnung für diese Images von BYOS zur nutzungsbasierten Bezahlung ändern?*

A: Nein, das ist nicht möglich. Der Azure-Hybridvorteil ist heute nur für RHEL- und SLES-Images vom Marketplace verfügbar. 

*F: Ich habe ein eigenes RHEL-Image aus meinem lokalen System (über Azure Migrate, ASR oder eine andere Methode) in Azure hochgeladen. Muss ich bestimmte Schritte ausführen, um vom Azure-Hybridvorteil profitieren zu können?*

A: Nein, das ist nicht erforderlich. Die von Ihnen hochgeladenen RHEL-Images gelten bereits als BYOS, und Ihnen werden nur die Kosten für die Azure-Infrastruktur in Rechnung gestellt. Sie sind für die Kosten der RHEL-Abonnements genauso verantwortlich wie für Ihre lokalen Umgebungen. 

*F: Kann ich den Azure-Hybridvorteil auf VMs verwenden, die mithilfe von RHEL- und SLES-SAP-Images vom Marketplace bereitgestellt wurden?*

Antwort: Ja, das ist möglich. Sie können den Lizenztyp „RHEL_BYOS“ für RHEL-VMs und den Typ „SLES_BYOS“ zum Konvertieren von VMs verwenden, die mithilfe von RHEL- bzw. SLES-SAP-Images vom Marketplace bereitgestellt wurden.

*F: Kann ich den Azure-Hybridvorteil in VM-Skalierungsgruppen für RHEL und SLES verwenden?*

A: Nein, das ist nicht möglich. VM-Skalierungsgruppen (VM Scale Sets, VMSSs) für RHEL und SLES sind derzeit nicht für den Azure-Hybridvorteil berechtigt.

*F: Kann ich den Azure-Hybridvorteil auf reservierten Instanzen für RHEL und SLES verwenden?*

A: Nein, das ist nicht möglich. Reservierte Instanzen (Reserved Instances, RIs) für RHEL und SLES sind derzeit nicht für den Azure-Hybridvorteil berechtigt.

*F: Kann ich den Azure-Hybridvorteil auf einer VM nutzen, die für SQL Server auf RHEL-Images bereitgestellt wurde?*

A: Nein, das ist nicht möglich. Hierfür ist keine Unterstützung geplant.
 

## <a name="common-issues"></a>Häufige Probleme
In diesem Abschnitt finden Sie eine Liste häufiger Probleme, die auftreten könnten, sowie Schritte zu deren Behebung.

| Fehler | Minderung |
| ----- | ---------- |
| „Die Aktion konnte nicht durchgeführt werden, da Red Hat Cloud Access laut unseren Datensätzen nicht für Ihr Azure-Abonnement aktiviert wurde.“ | Um den Vorteil mit virtuellen RHEL-Computern zu nutzen, müssen Sie zunächst Ihre Azure-Abonnements bei Red Hat Cloud Access registrieren. Weitere Informationen zum Registrieren von Azure-Abonnements für Red Hat Cloud Access finden Sie unter diesem Link.

## <a name="next-steps"></a>Nächste Schritte
* [Hier](/cli/azure/vm?preserve-view=true&view=azure-cli-latest) erhalten Sie Informationen zum Erstellen und Aktualisieren von VMs und zum Hinzufügen von Lizenztypen (RHEL_BYOS, SLES_BYOS) für den Azure-Hybridvorteil mit der Azure CLI.
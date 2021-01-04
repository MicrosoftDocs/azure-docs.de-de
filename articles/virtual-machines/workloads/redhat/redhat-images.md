---
title: Übersicht über Red Hat Enterprise Linux-Images in Azure
description: Erfahren Sie mehr über Red Hat Enterprise Linux-Images in Microsoft Azure.
author: asinn826
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 02/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 5cac21046b6b4afdefc4213f6a1fab438d62cca3
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957536"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Übersicht über Red Hat Enterprise Linux-Images

Dieser Artikel beschreibt die verfügbaren Red Hat Enterprise Linux-Images (RHEL) in Azure Marketplace sowie Richtlinien für die Benennung und Aufbewahrung.

Weitere Informationen zu Red Hat-Supportrichtlinien für alle RHEL-Versionen finden Sie unter [Red Hat Enterprise Linux Life Cycle (Red Hat Enterprise Linux-Lebenszyklus)](https://access.redhat.com/support/policy/updates/errata). Ausführliche Informationen zu Preisen finden Sie unter [Azure-Preisrechner](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> Für RHEL-Images, die derzeit auf dem Azure-Marketplace verfügbar sind, wird entweder das Lizenzierungsmodell Bring-Your-Own-Subscription (BYOS) oder die nutzungsbasierte Zahlung (Pay-As-You-Go) unterstützt. Der [Azure-Vorteil bei Hybridnutzung](../../windows/hybrid-use-benefit-licensing.md) und dynamische Wechsel zwischen BYOS und nutzungsbasierter Bezahlung werden nicht unterstützt. Für einen Wechsel des Lizenzierungsmodus ist eine erneute Bereitstellung der VM über das entsprechende Image erforderlich.

>[!NOTE]
> Erstellen Sie ein Supportticket bei Microsoft, wenn bei Ihnen in Azure Marketplace ein Problem mit RHEL-Images auftritt.

## <a name="view-images-available-in-azure"></a>Anzeigen von in Azure verfügbaren Images

Wenn Sie in Azure Marketplace nach „Red Hat“ suchen oder eine Ressource in der Benutzeroberfläche des Azure-Portals erstellen, wird eine Teilmenge der verfügbaren RHEL-Images angezeigt. Sie können den vollständigen Satz der verfügbaren VM-Images immer mit der Azure CLI, mit PowerShell oder mit der API abrufen.

Führen Sie den folgenden Befehl aus, um den vollständigen Satz von verfügbaren Red Hat-Images in Azure anzuzeigen:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Benennungskonvention

VM-Images in Azure sind nach Herausgeber, Angebot, SKU und Version organisiert. Die Kombination aus Herausgeber:Angebot:SKU:Version ist der Image-URN, durch den das zu verwendende Image eindeutig identifiziert wird.

Beispielsweise verweist `RedHat:RHEL:8-LVM:8.1.20200318` auf ein RHEL 8.1-Image mit LVM-Partitionierung (Logical Volume Manager), das am 18. März 2020 erstellt wurde.

Ein Beispiel zum Erstellen einer RHEL 8.1-VM wird hier gezeigt.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>Der Moniker „latest“

Die Azure-REST-API ermöglicht die Verwendung des Monikers „latest“ für die Version anstelle der genauen Version. Mit „latest“ wird das neueste verfügbare Image für die angegebene Kombination aus Herausgeber, Angebot und SKU bereitgestellt.

Beispielsweise verweist `RedHat:RHEL:8-LVM:latest` auf das neueste verfügbare Image der RHEL 8-Familie mit LVM-Partitionierung.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> Im Allgemeinen folgt der Vergleich von Versionen zum Ermitteln der neuesten Version den Regeln der [CompareTo](/dotnet/api/system.version.compareto?view=netcore-3.1#system_version_compareto_system_version_)-Methode.
Der Vergleich der Imageversion erfolgt durch Vergleichen der Werte als [Version](/dotnet/api/system.version.-ctor?view=netframework-4.8)-Objekt, nicht als Zeichenfolge.

## <a name="rhel-6-image-types"></a>RHEL 6-Imagetypen

Für RHEL 6.x-Images stehen die in der folgenden Tabelle gezeigten Imagetypen zur Verfügung.

|Herausgeber | Angebot | SKU-Wert | Version | Details
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Nebenversion (z. B. 6.9) | Verkettete Werte der RHEL-Nebenversion und des Veröffentlichungsdatums (z. B. 6.9.2018010506) | Alle standardmäßigen RHEL 6.x-Images befolgen diese Konvention.
|RedHat | rhel-byos | rhel-raw69 | Verkettete Werte der RHEL-Nebenversion und des Veröffentlichungsdatums (z. B. 6.9.20181023) | Dieses Image ist ein RHEL 6.9-BYOS-Image.
|RedHat | RHEL | RHEL-SAP-APPS | Verkettete Werte der RHEL-Nebenversion und des Veröffentlichungsdatums (z. B. 6.8.2017053118) | Dieses Image ist ein RHEL 6.8-Image für SAP-Anwendungen. Es ist berechtigt, auf SAP-Anwendungsrepositorys sowie auf RHEL-Basisrepositorys zuzugreifen.
|RedHat | RHEL | RHEL-SAP-HANA | Verkettete Werte der RHEL-Nebenversion und des Veröffentlichungsdatums (z. B. 6.7.2017053121) | Dieses Image ist ein RHEL 6.7-Image für SAP HANA. Es ist berechtigt, auf SAP HANA-Anwendungsrepositorys sowie auf RHEL-Basisrepositorys zuzugreifen.

## <a name="rhel-7-image-types"></a>RHEL 7-Imagetypen

Für RHEL 7.x-Images gibt es ein paar andere Imagetypen. In der folgenden Tabelle sind die verschiedenen Imagetypen aufgeführt, die angeboten werden. Um eine vollständige Liste anzuzeigen, verwenden Sie den Azure CLI-Befehl `az vm image list --publisher redhat --all`.

>[!NOTE]
> Sofern nicht anders angegeben, sind alle Images mit LVM partitioniert und stellen Verbindungen mit regulären RHEL-Repositorys her. Das heißt, die Repositorys sind keine EUS (Extended Update Support) und keine Update Services für SAP (E4S). In der Zukunft sollen nur noch mit LVM partitionierte Images veröffentlicht werden. Wir berücksichtigen jedoch Ihr Feedback zu dieser Entscheidung. Weitere Informationen zu EUS und Update Services für SAP finden Sie unter [Red Hat Enterprise Linux-Lebenszyklus](https://access.redhat.com/support/policy/updates/errata).

|Herausgeber | Angebot | SKU-Wert | Version | Details
|----------|-------|------------|---------|--------
|RedHat | RHEL | Nebenversion (z. B. 7.6) | Verkettete Werte der RHEL-Nebenversion und des Veröffentlichungsdatums (z. B. 7.6.2019102813) | Images, die vor April 2019 veröffentlicht wurden, sind an standardmäßige RHEL-Repositorys angefügt. Images, die nach April 2019 veröffentlicht wurden, werden an EUS-Repositorys von Red Hat angefügt, um das Sperren einer bestimmten Nebenversion zu ermöglichen. Kunden, die reguläre Repositorys verwenden möchten, sollten die Images verwenden, die im SKU-Wert „7-LVM“ oder „7-RAW“ enthalten (weitere Informationen folgen). Images von RHEL 7.7 und höher sind mit LVM partitioniert. Alle anderen Images dieser Kategorie sind rohpartitioniert.
|RedHat | RHEL | 7-RAW | Verkettete Werte der RHEL-Nebenversion und des Veröffentlichungsdatums (z. B. 7.6.2019102813) | Diese Images werden rohpartitioniert (es wurden beispielsweise keine logischen Volumes hinzugefügt).
|RedHat | RHEL | 7-RAW-CI | Verkettete Werte der RHEL-Nebenversion und des Veröffentlichungsdatums (z. B. 7.6.2019072418) | Diese Images sind rohpartitioniert (es wurden beispielsweise keine logischen Volumes hinzugefügt). Für die Bereitstellung wird „cloud-init“ verwendet.
|RedHat | RHEL | 7-LVM | Verkettete Werte der RHEL-Nebenversion und des Veröffentlichungsdatums (z. B. 7.6.2019062414) | Dieses Images sind mit LVM partitioniert.
|RedHat | rhel-byos | rhel-{lvm,raw} | Verkettete Werte der RHEL-Nebenversion und des Veröffentlichungsdatums (z. B. 7.7.20190819) | Bei diesen Images handelt es sich um die RHEL 7-BYOS-Images. Sie sind an keine Repositorys angefügt, und für sie fällt nicht die RHEL-Premiumgebühr an. Wenn Sie an RHEL-BYOS-Images interessiert sind, können Sie [hier](https://aka.ms/rhel-byos) Zugriff anfordern. SKU-Werte enden auf die Nebenversion und geben an, ob ein Image roh- oder LVM-partitioniert wurde. Der SKU-Wert rhel-lvm77 bedeutet beispielsweise, dass es sich um ein LVM-partitioniertes RHEL 7.7-Image handelt.
|RedHat | RHEL | RHEL-SAP | Verkettete Werte der RHEL-Nebenversion und des Veröffentlichungsdatums (z. B. 7.6.2019071300) | Bei diesen Images handelt es sich um RHEL for SAP-Images. Sie haben Zugriff auf die SAP HANA- und Anwendungsrepositorys sowie auf RHEL E4S-Repositorys. Bei der Abrechnung werden die Premiumtarife von RHEL und SAP mit eingerechnet, zusätzlich zur Computegrundgebühr.
|RedHat | RHEL | RHEL-SAP-HA | Verkettete Werte der RHEL-Nebenversion und des Veröffentlichungsdatums (z. B. 7.6.2019062320) | Bei diesen Images handelt es sich um RHEL for SAP-Images mit Hochverfügbarkeit und Update-Diensten. Sie haben Zugriff auf die SAP HANA- und Anwendungsrepositorys sowie auf Repositorys mit Hochverfügbarkeit und RHEL E4S-Repositorys. Bei der Abrechnung werden die Premiumtarife für RHEL, SAP und Hochverfügbarkeit mit eingerechnet, zusätzlich zur Computegrundgebühr.
|RedHat | RHEL | RHEL-Hochverfügbarkeit | Verkettete Werte der RHEL-Nebenversion und des Veröffentlichungsdatums (z. B. 7.6.2019062019) | Bei diesen Images handelt es sich um RHEL-Images, die Zugriff auf das Hochverfügbarkeits-Add-On haben. Aufgrund des Premiumtarifs für das Hochverfügbarkeits-Add-On entstehen für diese Images zusätzlich zum RHEL-Tarif und den Computegrundgebühren geringe Zusatzkosten.
|RedHat | RHEL | RHEL-SAP-APPS | Verkettete Werte der RHEL-Nebenversion und des Veröffentlichungsdatums (z. B. 7.3.2017053118) | Diese Images sind mittlerweile veraltet, da die SAP-Anwendungs- und SAP HANA-Repositorys in SAP-Repositorys zusammengefasst wurden. Bei diesen Images handelt es sich um RHEL for SAP-Anwendungsimages. Sie sind berechtigt, auf SAP-Anwendungsrepositorys sowie auf RHEL-Basisrepositorys zuzugreifen.
|RedHat | RHEL | RHEL-SAP-HANA | Verkettete Werte der RHEL-Nebenversion und des Veröffentlichungsdatums (z. B. 7.3.2018051421) | Diese Images sind mittlerweile veraltet, da die SAP-Anwendungs- und SAP HANA-Repositorys in SAP-Repositorys zusammengefasst wurden. Bei diesen Images handelt es sich um RHEL for SAP HANA-Images. Sie sind berechtigt, auf SAP HANA-Anwendungsrepositorys sowie auf RHEL-Basisrepositorys zuzugreifen.

## <a name="rhel-8-image-types"></a>RHEL 8-Imagetypen

>[!NOTE]
> Red Hat empfiehlt die Verwendung von Grubby zum Konfigurieren der Kernel-Befehlszeilenparameter in RHEL 8+. Weitere Details finden Sie [hier](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel).

Weitere Informationen zu RHEL 8-Imagetypen finden Sie unten.

|Herausgeber | Angebot | SKU-Wert | Version | Details
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Verkettete Werte der RHEL-Nebenversion und des Veröffentlichungsdatums (z. B. 8.0.20191023) | Bei diesen Images handelt es sich um LVM-partitionierte RHEL 8-Images, die mit Standardrepositorys von Red Hat verbunden sind.
|RedHat | RHEL | 8-gen2 | Verkettete Werte der RHEL-Nebenversion und des Veröffentlichungsdatums (z. B. 8.0.20191024) | Bei diesen Images handelt es sich um LVM-partitionierte Hyper-V Generation 2-RHEL 8-Images, die mit Standardrepositorys von Red Hat verbunden sind. Weitere Informationen zu VMs der Generation 2 finden Sie unter [Unterstützung für VMs der Generation 2 in Azure](../../generation-2.md).

## <a name="rhel-longer-support-add-ons"></a>Längere RHEL-Unterstützung für Add-Ons

### <a name="extended-update-support"></a>Extended Update Support (EUS)

Seit April 2019 stehen RHEL-Images zur Verfügung, die standardmäßig an die EUS-Repositorys angefügt sind. Weitere Informationen zu RHEL EUS finden Sie in der [Red Hat-Dokumentation](https://access.redhat.com/articles/rhel-eus).

Der Wechsel zu EUS-Repositorys ist möglich und wird unterstützt. Anweisungen zum Wechsel zu EUS Ihres virtuellen Computers und weitere Informationen zum Ablauf des EUS-Supports finden Sie unter [RHEL EUS und RHEL-VMs mit Versionssperre](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS wird für RHEL Extras nicht unterstützt. Wenn Sie also ein Paket installieren, das in der Regel über den RHEL Extras-Kanal verfügbar ist, können Sie dies nicht installieren, solange Sie EUS nutzen. Weitere Informationen zum Produktlebenszyklus von Red Hat Extras finden Sie unter [Red Hat Enterprise Linux Extras-Lebenszyklus](https://access.redhat.com/support/policy/updates/extras/).

#### <a name="differentiate-between-regular-and-eus-images"></a>Unterscheidung zwischen regulären und EUS-Images

Kunden, die Images verwenden möchten, die EUS-Repositorys zugeordnet sind, sollten das RHEL-Image verwenden, das in der SKU eine RHEL-Nebenversionsnummer enthält.

Beispielsweise könnten die folgenden beiden RHEL 7.4-Images verfügbar sein.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

In diesem Fall ist `RedHat:RHEL:7.6:7.6.2019102813` standardmäßig mit EUS-Repositorys verknüpft. Der SKU-Wert ist 7.4. Und `RedHat:RHEL:7-LVM:7.6.2019062414` ist standardmäßig mit Nicht-EUS-Repositorys verknüpft. Der SKU-Wert ist 7-LVM.

Um reguläre (nicht EUS) Repositorys zu verwenden, verwenden Sie ein Image, das keine Nebenversionsnummer in der SKU enthält.

#### <a name="rhel-images-with-eus"></a>RHEL-Images mit EUS

Die Informationen in der folgenden Tabelle gelten für RHEL-Images, die mit EUS-Repositorys verbunden sind.

>[!NOTE]
> Zum Zeitpunkt des Verfassens verfügen nur RHEL 7.4 und spätere Nebenversionen über EUS-Unterstützung. EUS wird für RHEL-Versionen bis einschließlich 7.3 nicht mehr unterstützt.
>
> Weitere Informationen zur Verfügbarkeit von RHEL EUS finden Sie unter [Red Hat Enterprise Linux-Lebenszyklus](https://access.redhat.com/support/policy/updates/errata).

Nebenversion |EUS-Beispielimage              |EUS-Status                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | Images, die ab April 2019 veröffentlicht werden, nutzen EUS standardmäßig.|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Images, die ab Juni 2019 veröffentlicht werden, nutzen EUS standardmäßig. |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | Images, die ab Mai 2019 veröffentlicht werden, nutzen EUS standardmäßig. |
RHEL 8.0      |–                            | Kein EUS von Red Hat verfügbar.                               |

### <a name="update-services-for-sap"></a>Update Services für SAP

Die neuesten RHEL for SAP-Images sind mit den Update Services für SAP Solutions-Abonnements (E4S) verbunden. Weitere Informationen zu E4S finden Sie in der [Dokumentation](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions) zu Red Hat.

#### <a name="rhel-images-with-e4s"></a>RHEL-Images mit E4S

Images aus den folgenden Angeboten, die nach Dezember 2019 erstellt wurden, werden mit E4S-Repositorys verbunden:

* RHEL-SAP (RHEL for SAP)
* RHEL-SAP-HA (RHEL für SAP mit Hochverfügbarkeits- und Updatediensten)

## <a name="other-available-offers-and-skus"></a>Weitere verfügbare Angebote und SKUs

Die vollständige Liste der verfügbaren Angebote und SKUs enthält ggf. weitere Images, die nicht in der Tabelle oben aufgeführt werden. z. B. `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Diese Angebote können verwendet werden, um Unterstützung für bestimmte Marketplace-Lösungen bereitzustellen. Oder Sie können für Vorschau- und Testzwecke veröffentlicht werden. Sie können jederzeit ohne vorherige Warnung geändert oder entfernt werden. Verwenden Sie sie nicht, sofern ihr Vorhandensein nicht öffentlich von Microsoft oder Red Hat dokumentiert wurde.

## <a name="publishing-policy"></a>Veröffentlichungsrichtlinie

Microsoft und Red Hat aktualisieren Images, wenn neue Nebenversionen veröffentlicht werden, nach Bedarf für bestimmte allgemeine Sicherheitsrisiken und Schwachstellen (CVEs) oder gelegentlich bei Konfigurationsänderungen/-updates. Wir bemühen uns, die aktualisierten Images möglichst bald innerhalb von drei Werktagen nach der Veröffentlichung bzw. Verfügbarkeit eines CVE-Updates bereitzustellen.

Wir aktualisieren nur die aktuelle Nebenversion in einer bestimmten Imagefamilie. Wenn eine neuere Nebenversion veröffentlicht wird, beenden wir die Aktualisierung älterer Nebenversionen. Beispielsweise werden mit der Veröffentlichung von RHEL 7.6 keine RHEL 7.5-Images mehr aktualisiert.

>[!NOTE]
> Aktive Azure-VMs, die mit RHEL-Images mit nutzungsbasierter Bezahlung bereitgestellt wurden, sind mit der Azure-RHUI verbunden und erhalten Updates und Korrekturen, sobald sie von Red Hat veröffentlicht und in die Azure-RHUI repliziert wurden. Dies geschieht normalerweise in weniger als 24 Stunden nach der offiziellen Veröffentlichung durch Red Hat. Diese VMs benötigen kein neues veröffentlichtes Image zum Abrufen der Updates. Kunden besitzen vollständige Kontrolle darüber, wann das Update initiiert werden soll.

## <a name="image-retention-policy"></a>Aufbewahrungsrichtlinie für Images

Nach unserer aktuellen Richtlinie werden alle veröffentlichten Images beibehalten. Wir behalten uns vor, Images zu entfernen, die bekanntermaßen Probleme jeglicher Art verursachen. Beispielsweise können Images mit falschen Konfigurationen aufgrund späterer Plattform- oder Komponentenaktualisierungen entfernt werden. Falls ein Image entfernt wird, gilt die aktuelle Azure Marketplace-Richtlinie, nach der bis zu 30 Tage vor dem Entfernen des Images Benachrichtigungen bereitgestellt werden sollten.

## <a name="next-steps"></a>Nächste Schritte

* Die vollständige Liste der RHEL-Images in Azure finden Sie unter [Verfügbare RHEL-Images (Red Hat Enterprise Linux) in Azure](./redhat-imagelist.md).
* Weitere Informationen zur Azure Red Hat-Updateinfrastruktur finden Sie unter [Red Hat-Updateinfrastruktur für On-Demand-RHEL-VMs in Azure](./redhat-rhui.md).
* Weitere Informationen zum RHEL BYOS-Angebot finden Sie unter [Bring-Your-Own-Subscription-Gold-Images für Red Hat Enterprise Linux in Azure](./byos.md).
* Weitere Informationen zu Red Hat-Supportrichtlinien für alle RHEL-Versionen finden Sie unter [Red Hat Enterprise Linux Life Cycle (Red Hat Enterprise Linux-Lebenszyklus)](https://access.redhat.com/support/policy/updates/errata).
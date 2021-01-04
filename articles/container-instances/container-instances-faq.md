---
title: Häufig gestellte Fragen
description: Antworten auf häufig gestellte Fragen im Zusammenhang mit dem Azure Container Instances-Dienst
author: macolso
ms.author: macolso
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: e4760463a4d71e4c097b082aa6b93d8b339f94ca
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516969"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Häufig gestellte Fragen zu Azure Container Instances

Dieser Artikel behandelt häufig gestellte Fragen zu Azure Container Instances.

## <a name="deployment"></a>Bereitstellung

### <a name="how-large-can-my-container-image-be"></a>Wie groß darf mein Containerimage sein?

Die maximale Größe für ein bereitstellbares Containerimage in Azure Container Instances beträgt 15 GB. Möglicherweise können Sie abhängig von der genauen Verfügbarkeit im Moment der Bereitstellung größere Images bereitstellen, dies wird jedoch nicht garantiert.

Die Größe Ihres Containerimages wirkt sich auf die Dauer des Bereitstellungsvorgangs aus, daher sollten Sie Ihre Containerimages so klein wie möglich halten.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Wie kann ich die Bereitstellung meines Containers beschleunigen?

Da die Imagegröße einer der wichtigsten Faktoren für die Bereitstellungszeiten ist, suchen Sie nach Möglichkeiten zum Verringern der Größe. Entfernen Sie unnötige Ebenen, oder verringern Sie die Größe der Ebenen im Image (durch Auswahl eines leichteren Basisbetriebssystem-Images). Wenn Sie beispielsweise Linux-Container ausführen, erwägen Sie die Verwendung von Alpine als Basisimage anstelle eines vollständigen Ubuntu-Servers. Ebenso können Sie für Windows-Container nach Möglichkeit ein Nano Server-Basisimage einsetzen. 

Sie sollten auch die Liste der vorab zwischengespeicherten Images in Azure-Containerimages überprüfen, die über die API [List Cached Images](/rest/api/container-instances/location/listcachedimages) (Zwischengespeicherte Images auflisten) verfügbar ist. Für eines der vorab zwischengespeicherten Images können Sie möglicherweise eine Imageebene deaktivieren. 

Weitere Informationen finden Sie in der [ausführlichen Anleitung](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) zum Verringern der Dauer des Containerstartvorgangs.

### <a name="what-windows-base-os-images-are-supported"></a>Welche Windows-Basisbetriebssystem-Images werden unterstützt?

> [!NOTE]
> Aufgrund von Problemen mit der Abwärtskompatibilität nach den Windows-Updates im Jahr 2020 schließen die folgenden Imageversionen die Versionsnummer ein, die für Ihr Basisimage mindestens empfohlen wird. Aktuelle Bereitstellungen, in denen ältere Imageversionen verwendet werden, sind nicht betroffen, in neuen Bereitstellungen sollten jedoch die folgenden Basisimages verwendet werden. 

#### <a name="windows-server-2016-base-images"></a>Windows Server 2016-Basisimages

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `sac2016`, `10.0.14393.3506` oder neuer
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`, `10.0.14393.3506` oder neuer

> [!NOTE]
> Windows-Images, die auf dem halbjährlichen Kanalrelease 1709 oder 1803 basieren, werden nicht unterstützt.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 und Clientbasisimages (Vorschau)

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809`, `10.0.17763.1040` oder neuer
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019`, `1809`, `10.0.17763.1040` oder neuer
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809`, `10.0.17763.1040` oder neuer

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Welche .NET oder .NET Core-Imageebene sollte ich in meinem Container verwenden? 

Verwenden Sie das kleinste Image, das Ihre Anforderungen erfüllt. Unter Linux können Sie ein *runtime-alpine* -.NET Core-Image verwenden, das seit der Veröffentlichung von .NET Core 2.1 unterstützt wird. Für Windows müssen Sie bei Verwendung des vollständigen .NET Framework ein Windows Server Core-Image verwenden (reines Laufzeitimage, z.B. *4.7.2-windowsservercore-ltsc2016* ). Reine Laufzeitimages sind kleiner, unterstützen jedoch keine Workloads, für die das .NET SDK erforderlich ist.

## <a name="availability-and-quotas"></a>Verfügbarkeit und Kontingente

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Wie viele Kerne und Arbeitsspeicher sollte ich für meine Container oder die Containergruppe zuweisen?

Das hängt ganz von Ihrer Workload ab. Fangen Sie klein an, und testen Sie die Leistung, um herauszufinden, ob sie für Ihre Container ausreicht. [Überwachen Sie die Auslastung der CPU- und Arbeitsspeicherressourcen](container-instances-monitor.md), und fügen Sie Kerne oder Arbeitsspeicher je nach Art der Prozesse hinzu, die Sie im Container bereitstellen.

Stellen Sie außerdem sicher, dass Sie die [Ressourcenverfügbarkeit](container-instances-region-availability.md) für die Region prüfen, in der die Bereitstellung erfolgt, um die Obergrenzen für CPU-Kerne und Arbeitsspeicher pro Containergruppe zu ermitteln. 

> [!NOTE]
> Ein kleiner Teil der Ressourcen einer Containergruppe wird von der zugrunde liegenden Infrastruktur des Diensts verwendet. Ihre Container können auf die meisten, aber nicht alle Ressourcen zugreifen, die der Gruppe zugeordnet sind. Planen Sie aus diesem Grund einen kleinen Ressourcenpuffer ein, wenn Sie Ressourcen für Container in der Gruppe anfordern.

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>In welcher zugrunde liegenden Infrastruktur wird ACI ausgeführt?

Azure Container Instances wurde als serverloser Dienst zur bedarfsgerechten Containerbereitstellung konzipiert. Unser Ziel ist, dass Sie sich auf die Entwicklung Ihrer Container konzentrieren können, ohne sich Gedanken über die Infrastruktur machen zu müssen. Für diejenigen, die neugierig sind oder Leistungsvergleiche anstellen möchten: ACI wird auf Gruppen von Azure-VMs verschiedener SKUs ausgeführt, in erster Linie aus der F- und der D-Serie. Dies wird sich jedoch in Zukunft ändern, während wir den Dienst weiterentwickeln und optimieren. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Ich möchte Tausende von Kernen in ACI bereitstellen. Kann ich mein Kontingent erhöhen lassen?
 
Ja (manchmal). Aktuelle Kontingente und die Grenzwerte, die auf Anforderung erhöht werden können, finden Sie im Artikel [Kontingente und Grenzwerte](container-instances-quotas.md).

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Ist eine Bereitstellung mit mehr als 4 Kernen und 16 GB Arbeitsspeicher möglich?

Bisher nicht. Zurzeit sind dies die Höchstwerte für eine Containergruppe. Wenden Sie sich mit speziellen Anforderungen an den Azure-Support. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Wann wird ACI in einer bestimmten Region verfügbar?

Die aktuelle regionale Verfügbarkeit wird [hier](container-instances-region-availability.md) veröffentlicht. Wenn Sie eine Anforderung für eine bestimmte Region haben, wenden Sie sich an den Azure-Support.

## <a name="features-and-scenarios"></a>Features und Szenarien

### <a name="how-do-i-scale-a-container-group"></a>Wie skaliere ich eine Containergruppe?

Zurzeit ist die Skalierung für Container und Containergruppen nicht verfügbar. Wenn Sie weitere Instanzen ausführen müssen, verwenden Sie unsere API zum Automatisieren und Erstellen weiterer Anforderungen zur Containergruppenerstellung für den Dienst. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Welche Features sind für Instanzen verfügbar, die in einem benutzerdefinierten VNET ausgeführt werden?

Sie können [Containergruppen in einem virtuellen Azure-Netzwerk Ihrer Wahl bereitstellen](container-instances-vnet.md) und private IP-Adressen an die Containergruppen delegieren, um Datenverkehr innerhalb des VNET Azure-Ressourcen-übergreifend weiterzuleiten. Informationen zu Netzwerkszenarien und -einschränkungen mit Azure Container Instances finden Sie unter [Virtuelle Netzwerkszenarien und -ressourcen](container-instances-virtual-network-concepts.md).

## <a name="pricing"></a>Preise

### <a name="when-does-the-meter-start-running"></a>Ab wann werden Verbrauchseinheiten gezählt?

Die Containergruppendauer wird ab dem Zeitpunkt berechnet, zu dem wir beginnen, das Image Ihres ersten Containers (für eine neue Bereitstellung) abzurufen, oder zu dem Ihre Containergruppe neu gestartet wird (bei einer bestehenden Bereitstellung). Die Berechnung erfolgt, bis die Containergruppe beendet wird. Details hierzu finden Sie unter [Container Instances – Preise](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Wird die Gebührenberechnung angehalten, wenn meine Container beendet werden?

Verbrauchseinheiten werden nicht mehr gezählt, sobald Ihre gesamte Containergruppe beendet wurde. Solange ein Container in Ihrer Containergruppe ausgeführt wird, bleiben die Ressourcen bestehen für den Fall, dass Sie die Container erneut starten möchten. 

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen](container-instances-overview.md) zu Azure Container Instances.
* [Beheben von häufigen Problemen in Azure Container Instances](container-instances-troubleshooting.md)

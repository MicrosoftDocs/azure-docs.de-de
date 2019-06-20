---
title: Manuelles Beenden oder Starten von Containern in Azure Container Instances
description: Hier erfahren Sie, wie Sie eine Containergruppe in Azure Container Instances manuell beenden oder starten.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 8e62d106a42dfbec897e5e14cf68fd3d7fd823c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65070814"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Manuelles Beenden oder Starten von Containern in Azure Container Instances

Die Einstellung [Neustartrichtlinie](container-instances-restart-policy.md) einer Containergruppe bestimmt, wie Containerinstanzen standardmäßig gestartet oder beendet werden. Sie können die Standardeinstellung außer Kraft setzen, indem Sie eine Containergruppe manuell beenden oder starten.

## <a name="stop"></a>Beenden

Sie können eine ausgeführte Containergruppe manuell beenden, indem Sie z. B. den Befehl [az container stop][az-container-stop] oder das Azure-Portal verwenden. Für bestimmte Containerworkloads möchten Sie möglicherweise eine zeitintensive Containergruppe nach einem definierten Zeitraum beenden, um Kosten zu sparen. 

*Wenn eine Containergruppe in den Zustand „Beendet“ wechselt, wird sie beendet und verwendet alle Container in der Gruppe wieder. Der Containerzustand bleibt nicht erhalten.*

Wenn die Container wiederverwendet werden, wird die Zuordnung der [Ressourcen](container-instances-container-groups.md#resource-allocation) aufgehoben und die Abrechnung für die Containergruppe beendet.

Die Aktion „Beenden“ hat keine Auswirkungen, wenn die Containergruppe bereits beendet wurde (also im Zustand „Erfolgreich“ oder „Fehler“ ist). Angenommen, eine Containergruppe mit einmalig auszuführenden Containertasks, die erfolgreich ausgeführt wurden, wird im Zustand „Erfolgreich“ beendet. Versuche, die Gruppe in diesem Zustand zu beenden, ändern den Zustand nicht. 

## <a name="start"></a>Start

Wenn eine Containergruppe beendet ist (weil die Container einzeln beendet wurden oder weil Sie die Gruppe manuell beendet haben), können Sie die Container starten. Verwenden Sie beispielsweise den Befehl [az container stop][az-container-start] oder das Azure-Portal, um die Container in der Gruppe manuell zu starten. Wenn das Containerimage für einen Container aktualisiert wird, wird ein neues Image gepullt. 

Durch das Starten einer Containergruppe beginnt eine neue Bereitstellung mit der gleichen Containerkonfiguration. Diese Aktion kann Ihnen helfen, eine bekannte Containergruppenkonfiguration, die wie erwartet funktioniert, schnell wiederzuverwenden. Sie müssen keine neue Containergruppe erstellen, um die gleiche Workload auszuführen.

Alle Container in einer Containergruppe werden durch diese Aktion gestartet. Einen bestimmten Container in der Gruppe können Sie nicht starten.

Nachdem Sie eine Containergruppe manuell gestartet oder neu gestartet haben, wird die Containergruppe gemäß der konfigurierten Neustartrichtlinie ausgeführt.
  
## <a name="restart"></a>Neu starten

Sie können eine Containergruppe neu starten, während sie ausgeführt wird, indem Sie z. B. den Befehl [az container restart][az-container-restart] verwenden. Diese Aktion startet alle Container in der Containergruppe neu. Wenn das Containerimage für einen Container aktualisiert wird, wird ein neues Image gepullt. 

Der Neustart einer Containergruppe ist hilfreich, wenn Sie ein Bereitstellungsproblem beheben möchten. Wenn beispielsweise eine temporäre Ressourcenbeschränkung die erfolgreiche Ausführung Ihrer Container verhindert, kann ein Neustart der Gruppe das Problem beheben.

Alle Container in einer Containergruppe werden durch diese Aktion neu gestartet. Einen bestimmten Container in der Gruppe können Sie nicht neu starten.

Nachdem Sie eine Containergruppe manuell neu gestartet haben, wird die Containergruppe gemäß der konfigurierten Neustartrichtlinie ausgeführt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu [Neustartrichtlinieneinstellungen](container-instances-restart-policy.md) in Azure Container Instances.

Zusätzlich zum manuellen Beenden und Starten einer Containergruppe mit der vorhandenen Konfiguration können Sie [die Einstellungen einer ausgeführten Containergruppe aktualisieren](container-instances-update.md).

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop

---
title: Problembehandlung für Azure Red Hat OpenShift | Microsoft-Dokumentation
description: Behandlung und Lösung häufiger Probleme mit Azure Red Hat OpenShift
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 7f2bdf643f12671bec3d0c087d8775844099fe9a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66306244"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Problembehandlung für Azure Red Hat OpenShift

Dieser Artikel enthält Informationen zu einigen häufigen Problemen, die beim Erstellen oder Verwalten von Microsoft Azure Red Hat OpenShift-Clustern auftreten können.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Durchführen eines erneuten Erstellungsversuchs für einen Cluster nach einem Fehler

Wenn die Erstellung eines Azure Red Hat OpenShift-Clusters mit dem CLI-Befehl `az` nicht erfolgreich ist, tritt auch bei einem erneuten Erstellungsversuch ein Fehler auf.
Verwenden Sie `az openshift delete`, um den fehlerhaften Cluster zu löschen, und erstellen Sie anschließend einen neuen Cluster.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Ausgeblendete Azure Red Hat OpenShift-Clusterressourcengruppe

Derzeit ist die Ressource `Microsoft.ContainerService/openShiftManagedClusters`, die von der Azure CLI automatisch erstellt wird (Befehl `az openshift create`) im Azure-Portal ausgeblendet. Aktivieren Sie in der Ansicht **Ressourcengruppe** die Option **Ausgeblendete Typen anzeigen**, um die Ressourcengruppe anzuzeigen.

![Screenshot: Kontrollkästchen „Ausgeblendete Typen anzeigen“ im Portal](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Erstellung eines Clusters führt zu einem Fehler mit dem Hinweis, dass kein registrierter Ressourcenanbieter gefunden wurde

Wenn die Erstellung eines Clusters zu einem Fehler der Art `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview` führt, haben Sie die Vorschauversion genutzt. Sie müssen nun [reservierte Azure-VM-Instanzen erwerben](https://aka.ms/openshift/buy), um das allgemein verfügbare Produkt zu verwenden. Eine Reservierung senkt Ihre Ausgaben, da Sie im Voraus für vollständig verwaltete Azure-Datendienste zahlen. Unter [*Was sind Azure-Reservierungen*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) erhalten Sie weitere Informationen zu Reservierungen und erfahren, wie Sie damit Geld sparen können.

## <a name="next-steps"></a>Nächste Schritte

- Im [Hilfecenter zu Red Hat OpenShift](https://help.openshift.com/) finden Sie weitere Informationen zur Problembehandlung für OpenShift.

- Hier erhalten Sie Antworten auf [häufig gestellte Fragen zu Azure Red Hat OpenShift](openshift-faq.md).

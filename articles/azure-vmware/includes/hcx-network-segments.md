---
title: VMware HCX-Netzwerksegmente
description: Für VMware HCx sind vier Netzwerke erforderlich.
ms.topic: include
ms.date: 11/23/2020
ms.openlocfilehash: 48894c532c97b70cde1473fb8b81f406ded70343
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999626"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Für VMware HCX werden vier Netzwerke benötigt:

- **Verwaltungsnetzwerk:** In der Regel ist dieses Netzwerk mit dem Verwaltungsnetzwerk identisch, das im vSphere-Cluster verwendet wird. Identifizieren Sie in diesem Netzwerksegment mindestens zwei IP-Adressen für VMware HCX. Je nach Bereitstellung sind ggf. höhere Zahlen erforderlich.

   > [!NOTE]
   > Wir empfehlen die Erstellung eines Netzwerks vom Typ „/26“. In einem Netzwerk vom Typ „/26“ können bis zu zehn Dienstnetze und 60 Netzwerkextender (-1 pro Dienstnetz) verwendet werden. Mit privaten Azure VMware Solution-Clouds können pro Netzwerkextender acht Netzwerke gestreckt werden.
   >
   
- **vMotion-Netzwerk:** In der Regel ist dieses Netzwerk mit dem Netzwerk identisch, das für vMotion im vSphere-Cluster verwendet wird.  Identifizieren Sie in diesem Netzwerksegment mindestens zwei IP-Adressen für VMware HCX. Je nach Bereitstellung sind ggf. höhere Zahlen erforderlich.  

   Das vMotion-Netzwerk muss über einen verteilten virtuellen Switch oder vSwitch0 verfügbar gemacht werden. Ist dies nicht der Fall, ändern Sie die Umgebung.

   > [!NOTE]
   > Dieses Netzwerk kann privat (ohne Routing) sein.

- **Uplink-Netzwerk:** Sie möchten ein neues Netzwerk für VMware HCX Uplink erstellen und es über eine Portgruppe auf Ihren vSphere-Cluster erweitern. Identifizieren Sie in diesem Netzwerksegment mindestens zwei IP-Adressen für VMware HCX. Je nach Bereitstellung sind ggf. höhere Zahlen erforderlich.  

   > [!NOTE]
   > Wir empfehlen die Erstellung eines Netzwerks vom Typ „/26“. In einem Netzwerk vom Typ „/26“ können bis zu zehn Dienstnetze und 60 Netzwerkextender (-1 pro Dienstnetz) verwendet werden. Mit privaten Azure VMware Solution-Clouds können pro Netzwerkextender acht Netzwerke gestreckt werden.
   >
   
- **Replikationsnetzwerk:** Diese Eingabe ist optional. Sie möchten ein neues Netzwerk für VMware HCX Replication erstellen und dieses Netzwerk über eine Portgruppe auf Ihren vSphere-Cluster erweitern. Identifizieren Sie in diesem Netzwerksegment mindestens zwei IP-Adressen für VMware HCX. Je nach Bereitstellung sind ggf. höhere Zahlen erforderlich.

   > [!NOTE]
   > Diese Konfiguration ist nur möglich, wenn von den lokalen Clusterhosts ein dediziertes Replication-VMkernel-Netzwerk verwendet wird.  Wenn für Ihren lokalen Cluster kein dediziertes Replication-VMkernel-Netzwerk definiert ist, muss dieses Netzwerk nicht erstellt werden.

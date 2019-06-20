---
title: Übersicht zu OpenShift in Azure | Microsoft-Dokumentation
description: Eine Übersicht zu OpenShift in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: d9e3aa3dae81166ef91f57ea6a95087a952001ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65550986"
---
# <a name="openshift-in-azure"></a>OpenShift in Azure

OpenShift ist eine offene und erweiterbare Containeranwendungsplattform, die Docker und Kubernetes in das Unternehmen einführt.  

OpenShift enthält Kubernetes für die Containerorchestrierung und -verwaltung. Es fügt entwickler- und vorgangsorientierte Tools hinzu, die Folgendes ermöglichen:

- Schnelle Anwendungsentwicklung
- Einfache Bereitstellung und Skalierung
- Langfristige Lebenszykluswartung für Teams und Anwendungen

Von OpenShift sind mehrere Versionen verfügbar.  Von diesen Versionen können derzeit nur zwei von Kunden in Azure bereitgestellt werden: OpenShift Container Platform und OKD (früher OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift ist ein vollständig verwaltetes OpenShift-Angebot, das in Azure ausgeführt wird. Dieser Dienst wird gemeinsam von Microsoft und Red Hat verwaltet und unterstützt. Weitere Informationen finden Sie in der Dokumentation zu [Azure Red Hat OpenShift Service](https://docs.microsoft.com/azure/openshift/).

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform ist eine [kommerzielle Version](https://www.openshift.com) für unternehmen von und unterstützt von Red Hat. Mit dieser Version erwirbt der Kunde die erforderlichen Berechtigungen für OpenShift Container Platform und ist für die Installation und Verwaltung der gesamten Infrastruktur zuständig.

Da der Kunde der Besitzer der gesamten Plattform ist, kann die Installation in seinem lokalen Rechenzentrum oder in einer öffentlichen Cloud (z.B. Azure) erfolgen.

## <a name="okd"></a>OKD

OKD ist ein [Open Source](https://www.okd.io/) Upstream-Projekt von OpenShift, das von der Community unterstützt wird. OKD kann auf CentOS oder Red Hat Enterprise Linux (RHEL) installiert werden.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren allgemeiner Voraussetzungen für OpenShift in Azure](./openshift-prerequisites.md)
- [Bereitstellen von OpenShift Container Platform in Azure](./openshift-container-platform.md)
- [Bereitstellen eines selbstverwalteten OpenShift Container Platform-Marketplace-Angebots](./openshift-marketplace-self-managed.md)
- [Bereitstellen von OpenShift in Azure Stack](./openshift-azure-stack.md)
- [Aufgaben nach der Bereitstellung](./openshift-post-deployment.md)
- [Beheben von Problemen bei der Bereitstellung von OpenShift](./openshift-troubleshooting.md)

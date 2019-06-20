---
title: Einführung in die auf Data Science Virtual Machine basierenden Teamumgebungen – Azure | Microsoft-Dokumentation
description: Muster für die Bereitstellung von Data Science VM als Umgebung für Unternehmensteams.
keywords: Deep Learning, KI, Data Science-Tools, virtueller Data Science-Computer, räumliche Analysen, Data Science-Teamprozess
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 2e17ab5cfe51f3772148cc730c982671d602a79a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60502146"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Auf Data Science Virtual Machine basierende Teamanalyse und KI-Umgebung 
[Data Science Virtual Machine](overview.md) (DSVM) bietet eine umfangreiche Umgebung in der Azure-Plattform mit vorgefertigter Software für künstliche Intelligenz (KI) und Datenanalyse. 

Bisher wurde DSVM als einzelne Desktop-Analyseumgebung verwendet. Durch das gemeinsame Konzept können die jeweiligen Data Scientists mit der vorgefertigten Analyseumgebung nun Ihre Produktivität steigern. Da große Analyseteams ihre Analyseumgebungen für ihre Data Scientists und KI-Entwickler planen, ist eines der wiederkehrenden Themen eine gemeinsame Analyseinfrastruktur für Entwicklung und Experimente. Diese Infrastruktur wird in Übereinstimmung mit den IT-Richtlinien des Unternehmens verwaltet, die auch die Zusammenarbeit und Konsistenz zwischen den Data Science- und Analyseteams erleichtern. 

Eine gemeinsam genutzte Infrastruktur ermöglicht der IT-Abteilung außerdem eine bessere Nutzung der Analyseumgebung. Einige Organisationen bezeichnen die teambasierte Data Science- bzw. Analyseinfrastruktur als „Analyse-Sandbox“. Sie bietet Data Scientists die Möglichkeit, auf verschiedene Datenressourcen zuzugreifen, um Daten schnell zu verstehen, Experimente durchzuführen, Hypothesen zu überprüfen und Vorhersagemodelle zu erstellen, ohne die Produktionsumgebung zu beeinträchtigen. 

Da DSVM auf der Ebene der Azure-Infrastruktur arbeitet, können IT-Administratoren die DSVM-Instanz so konfigurieren, dass sie die IT-Richtlinien des Unternehmens erfüllt. Bei der Implementierung verschiedener Architekturen für die gemeinsame Nutzung bietet DSVM umfassende Flexibilität mit kontrolliertem Zugriff auf Unternehmensdaten. 

In diesem Abschnitt werden einige Muster und Richtlinien behandelt, die Sie zum Bereitstellen der DSVM-Instanz als teambasierte Data Science-Infrastruktur verwenden können. Die Bausteine für diese Muster stammen von Azure-Infrastructure-as-a-Service (IaaS), sie gelten also für alle Azure-VMs. Der Schwerpunkt dieser Artikelreihe liegt auf der Anwendung dieser standardmäßigen Azure-Infrastrukturfunktionen auf Data Science VM. 

Zu den wichtigsten Bausteinen einer Teamanalyseumgebung für Unternehmen gehören:

* [Automatisch skalierter Pool von Data Science Virtual Machine-Instanzen](dsvm-pools.md)
* [Gemeinsame Identität und Zugriff auf einen Arbeitsbereich über jede DSVM-Instanz im Pool](dsvm-common-identity.md)
* [Sicherer Zugriff auf Datenquellen](dsvm-secure-access-keys.md)


In dieser Artikelreihe werden Anleitungen und Hinweise zu jedem der vorangegangenen Aspekte bereitgestellt. Sie deckt nicht alle Überlegungen und Anforderungen beim Bereitstellen von DSVM in großen Unternehmenskonfigurationen ab. Weitere Informationen finden Sie auch in der folgenden Azure-Dokumentation, die Sie bei der Implementierung von DSVM-Instanzen in Ihrem Unternehmen verwenden können: 

* [Netzwerksicherheit](https://docs.microsoft.com/azure/security/azure-network-security)
* [Überwachung](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) und [Verwaltung](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Protokollierung und Überwachung](https://docs.microsoft.com/azure/security/azure-log-audit)
* [Rollenbasierte Zugriffssteuerung](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Richtlinieneinstellung und -erzwingung](../../governance/policy/overview.md)
* [Antischadsoftware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [Verschlüsselung](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Datenermittlung und Governance](https://docs.microsoft.com/azure/data-catalog/)

Das [Azure-Architekturzentrum](https://docs.microsoft.com/azure/architecture/) bietet detaillierte End-to-End-Architekturen und Muster für den Aufbau und die Verwaltung Ihrer cloudbasierten Analyseinfrastruktur. 

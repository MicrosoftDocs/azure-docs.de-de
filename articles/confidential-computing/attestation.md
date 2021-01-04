---
title: Nachweisen von Enklaven in Azure
description: Hier erfahren Sie, wie Sie mithilfe eines Nachweises überprüfen können, ob Ihre vertrauenswürdige Confidential Computing-Umgebung sicher ist.
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/22/2020
ms.author: JenCook
ms.openlocfilehash: 86856d1f66b5b7d723c907c17d7179ffcd2b82ab
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565669"
---
# <a name="attesting-sgx-enclaves"></a>Nachweisen von SGX-Enklaven

Confidential Computing in Azure bietet Intel SGX-basierte virtuelle Computer, die einen Teil Ihres Codes oder Ihrer Daten isolieren können. Beim Arbeiten mit diesen [Enklaven](confidential-computing-enclaves.md) möchten Sie überprüfen und verifizieren können, ob Ihre vertrauenswürdige Umgebung sicher ist. Diese Verifizierung wird als Nachweisvorgang bezeichnet. 

## <a name="overview"></a>Übersicht 

Mit der Erbringung des Nachweises kann das Vertrauen der vertrauenden Seite gestärkt werden, dass die eigene Software (1) in einer Enklave ausgeführt wird und (2) dass die Enklave aktuell und geschützt ist. Bei einer Enklave wird die zugrunde liegende Hardware aufgefordert, Anmeldeinformationen als Nachweis zu generieren, dass die Enklave auf der Plattform vorhanden ist. Der Bericht kann dann an eine zweite Enklave weitergegeben werden, die bestätigt, dass der Bericht auf derselben Plattform generiert wurde.

![Nachweisen von Code in der Enklave](media/attestation/attestation.png)



Der Nachweisvorgang muss über einen sicheren Nachweisdienst implementiert werden, der mit der Systemsoftware und der Hardware kompatibel ist. Zwei Beispiele für Dienste, die Sie nutzen können:

- [Microsoft Azure Attestation (Vorschau)](../attestation/overview.md) oder
- [Nachweis- und Bereitstellungsdienste von Intel](https://software.intel.com/sgx/attestation-services)


Beide sind mit der Intel SGX-Infrastruktur von Azure Confidential Computing kompatibel. 

## <a name="next-steps"></a>Nächste Schritte
Probieren Sie die [Microsoft Azure Attestation-Beispiele für enklavefähige Apps](/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/)aus.
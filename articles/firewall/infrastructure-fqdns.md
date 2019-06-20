---
title: Infrastruktur-FQDN für Azure Firewall
description: Erfahren Sie mehr über die Infrastruktur-FQDNs in Azure Firewall
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 34201a0eb4139de64261f77f285096a2aa2dd3aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61066321"
---
# <a name="infrastructure-fqdns"></a>Infrastruktur-FQDNs

Azure Firewall enthält eine integrierte Regelsammlung für Infrastruktur-FQDNs, die standardmäßig zulässig sind. Diese FQDNs sind plattformspezifisch und können nicht für andere Zwecke verwendet werden. 

Die folgenden Dienste sind in der integrierten Regelsammlung enthalten:

- Computezugriff auf das Speicher-PIR (Platform Image Repository, PIR)
- Speicherzugriff auf den Status verwalteter Datenträger
- Azure-Diagnose und -Protokollierung (MDS)
- Azure Active Directory

## <a name="overriding"></a>Überschreiben 

Diese integrierte Infrastrukturregelsammlung kann außer Kraft gesetzt werden. Erstellen Sie hierzu eine Anwendungsregelsammlung vom Typ „Alle ablehnen“, die ganz zum Schluss verarbeitet wird. Sie wird immer vor der Infrastrukturregelsammlung verarbeitet. Alles, was sich nicht in der Infrastrukturregelsammlung befindet, wird standardmäßig abgelehnt.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie eine [Azure Firewall bereitstellen und konfigurieren](tutorial-firewall-deploy-portal.md).
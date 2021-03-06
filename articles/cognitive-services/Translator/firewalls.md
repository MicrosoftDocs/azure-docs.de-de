---
title: 'Übersetzen hinter Firewalls: Translator'
titleSuffix: Azure Cognitive Services
description: Translator von Azure Cognitive Services kann hinter Firewalls entweder mithilfe der Domänennamen- oder der IP-Filterung Übersetzungen durchführen.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: f5dd72328180574809c812d670f8165ad84963ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98897746"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>Übersetzung hinter IP-Firewalls mithilfe von Translator

Translator kann hinter Firewalls entweder mithilfe der Domänennamen- oder der IP-Filterung Übersetzungen durchführen. Die Domänennamenfilterung ist hierbei die bevorzugte Methode. Es wird **nicht empfohlen**, Microsoft Translator hinter einer IP-gefilterten Firewall auszuführen. Die Unterstützung für dieses Setup wird in Zukunft möglicherweise ohne Vorankündigung eingestellt.

## <a name="translator-ip-addresses"></a>IP-Adressen für Translator
Die IP-Adressen für Translator (api.cognitive.microsofttranslator.com) lauten ab dem 21. August 2019 wie folgt:

* **Asien-Pazifik:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **Nordamerika:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Übersetzung hinter IP-Firewalls in Translator](reference/v3-0-translate.md)

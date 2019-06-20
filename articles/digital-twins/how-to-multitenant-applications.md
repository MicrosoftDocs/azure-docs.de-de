---
title: Aktivieren von mehrinstanzenfähigen Anwendungen mit Azure Digital Twins | Microsoft-Dokumentation
description: Konfigurieren von mehrinstanzenfähigen Anwendungen für Azure Digital Twins.
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: mavoge
ms.openlocfilehash: 03b5693fe016cfb11d6f685f9088fe6e94f03b90
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66688602"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Aktivieren von mehrinstanzenfähigen Anwendungen mit Azure Digital Twins

Entwickler, die ihre Lösungen auf Azure Digital Twins erstellen, möchten möglicherweise mehrere Kunden mit einem einzigen Dienst oder einer einzelnen Lösung unterstützen. *Mehrinstanzenfähige* Anwendungen gehören zu den häufigsten Azure Digital Twins-Konfigurationen.

In diesem Dokument ist beschrieben, wie eine Azure Digital Twins-App konfiguriert wird, in der mehrere AD-Mandanten (Azure Active Directory) und Kunden unterstützt werden.

## <a name="multitenancy"></a>Mehrinstanzenfähigkeit

Eine *mehrinstanzenfähige* Ressource ist eine einzelne bereitgestellte Instanz, die mehrere Kunden unterstützt. Jeder Kunde verfügt über eigene unabhängige Daten und Berechtigungen. Die Erfahrungen jedes Kunden sind voneinander isoliert, sodass jeweils eine andere „Ansicht“ angezeigt wird.

Weitere Informationen zur Mehrinstanzenfähigkeit finden unter [Mehrinstanzenfähige Anwendungen in Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications).

## <a name="problem-scenario"></a>Problemszenario

In diesem Szenario erstellt ein Entwickler eine Azure Digital Twins-Lösung (**ENTWICKLER**) und ein Kunde verwendet die Lösung (**KUNDE**):

- Der **ENTWICKLER** hat ein Azure-Abonnement mit einem Azure Active Directory-Mandanten.
- Der **ENTWICKLER** hat eine Azure Digital Twins-Instanz in seinem Azure-Abonnement bereitgestellt. Azure Active Directory hat automatisch ein Dienstprinzipal im Azure Active Directory-Mandanten des **ENTWICKLERS** erstellt.
- Benutzer innerhalb des Azure Active Directory-Mandanten des **ENTWICKLERS** können dann [OAuth 2.0-Token](./security-authenticating-apis.md) vom Azure Digital Twins-Dienst abrufen.
- Der **ENTWICKLER** erstellt jetzt eine mobile App, die direkt in die Verwaltungs-API von Azure Digital Twins integriert ist.
- Der **ENTWICKLER** gestattet dem **KUNDEN** die Nutzung der mobilen App.
- Der **KUNDE** muss autorisiert werden, um die Verwaltungs-API von Azure Digital Twins in der Anwendung des **ENTWICKLERS** verwenden zu können.

Die Problematik:

- Wenn sich der **KUNDE** bei der Anwendung des **ENTWICKLERS** anmeldet, kann die App keine Token für die Benutzer des **KUNDEN** abrufen, um sich mit den Azure Digital Twins-Verwaltungs-APIs zu authentifizieren.
- In Azure Active Directory wird eine Ausnahme ausgegeben, die anzeigt, dass Azure Digital Twins nicht im Verzeichnis des **KUNDEN** erkannt wird.

## <a name="problem-solution"></a>Problemlösung

Um das vorgenannte Szenario zu lösen, müssen die folgenden Aktionen ausgeführt werden, um einen Azure Digital Twins-Dienstprinzipal im Azure Active Directory-Mandanten des **KUNDEN** zu erstellen:

- Wenn der **KUNDE** noch kein Azure-Abonnement für den Azure Active Directory-Mandanten hat:

  - Der Azure Active Directory-Mandantenadministrator des **KUNDEN** muss ein [Azure-Abonnement mit nutzungsbasierter Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/) erwerben.
  - Der Azure Active Directory-Mandantenadministrator des **KUNDEN** muss dann den [Mandanten mit dem neuen Abonnement verknüpfen](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity).

- Im [Azure-Portal](https://portal.azure.com) führt der Azure Active Directory-Mandantenadministrator des **KUNDEN** folgende Schritte aus:

  1. Er öffnet **Abonnements**.
  1. Er wählt das Abonnement aus, das den Azure Active Directory-Mandanten umfasst, der in der App des **ENTWICKLERS** verwendet werden soll.

     ![Azure Active Directory-Abonnements][1]

  1. Er wählt **Ressourcenanbieter** aus.
  1. Er sucht nach **Microsoft.IoTSpaces**.
  1. Wählen Sie **Registrieren**.

     ![Azure Active Directory-Ressourcenanbieter][2]
  
## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Verwendung von benutzerdefinierten Funktionen mit Azure Digital Twins finden Sie unter [Erstellen von benutzerdefinierten Funktionen in Azure Digital Twins](./how-to-user-defined-functions.md).

- Informationen zur Verwendung der rollenbasierten Zugriffssteuerung zum zusätzlichen Schutz von Anwendung mit Rollenzuweisungen finden Sie unter [Erstellen und Verwalten von rollenbasierter Zugriffssteuerung mit Azure Digital Twins](./security-create-manage-role-assignments.md).

<!-- Images -->
[1]: media/multitenant/ad-subscriptions.png
[2]: media/multitenant/ad-resource-providers.png

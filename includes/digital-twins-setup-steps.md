---
author: baanders
description: Includedatei für die Schrittübersicht beim Setup von Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: c1cfab8c9e68d9e6b0c3b84e8848645a50c24710
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560775"
---
Das vollständige Setup für eine neue Azure Digital Twins-Instanz umfasst zwei Teile:
1. **Erstellen der Instanz**
2. **Einrichten von Benutzerzugriffsberechtigungen:** Azure-Benutzer müssen über die Rolle *Azure Digital Twins Data Owner* (Azure Digital Twins-Datenbesitzer) auf der Azure Digital Twins-Instanz verfügen, um sie und ihre Daten verwalten zu können. In diesem Schritt weisen Sie als Besitzer/Administrator des Azure-Abonnements diese Rolle der Person zu, die Ihre Azure Digital Twins-Instanz verwalten soll. Dies können Sie selbst sein oder eine andere Person in Ihrer Organisation.
 
>[!NOTE]
>Diese Vorgänge sollen von einem Benutzer durchgeführt werden, der die Berechtigung hat, beide Ressourcen und den Benutzerzugriff auf das Azure-Abonnement zu verwalten. Obwohl einige Schritte mit niedrigeren Berechtigungen ausgeführt werden können, ist die Zusammenarbeit mit einem Benutzer mit diesen Berechtigungen erforderlich, um eine verwendbare Instanz vollständig einzurichten. Weitere Informationen hierzu finden Sie im Abschnitt [*Voraussetzungen: Erforderliche Berechtigungen*](#prerequisites-permission-requirements) weiter unten.
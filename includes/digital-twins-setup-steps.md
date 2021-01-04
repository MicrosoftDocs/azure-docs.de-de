---
author: baanders
description: Includedatei für die Schrittübersicht beim Setup von Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: bf442da43fd7945bf69fbb889ef0c517b8832809
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478841"
---
>[!NOTE]
>Diese Vorgänge sollen von einem Benutzer durchgeführt werden, der die Berechtigung hat, beide Ressourcen und den Benutzerzugriff auf das Azure-Abonnement zu verwalten. Obwohl einige Schritte mit niedrigeren Berechtigungen ausgeführt werden können, ist die Zusammenarbeit mit einem Benutzer mit diesen Berechtigungen erforderlich, um eine verwendbare Instanz vollständig einzurichten. Weitere Informationen hierzu finden Sie im Abschnitt [*Voraussetzungen: Erforderliche Berechtigungen*](#prerequisites-permission-requirements) weiter unten.

Das vollständige Setup für eine neue Azure Digital Twins-Instanz umfasst zwei Teile:
1. **Erstellen der Instanz**
2. **Einrichten von Benutzerzugriffsberechtigungen:** Azure-Benutzer müssen über die Rolle *Azure Digital Twins Data Owner* (Azure Digital Twins-Datenbesitzer) auf der Azure Digital Twins-Instanz verfügen, um sie und ihre Daten verwalten zu können. In diesem Schritt weisen Sie als Besitzer/Administrator des Azure-Abonnements diese Rolle der Person zu, die Ihre Azure Digital Twins-Instanz verwalten soll. Dies können Sie selbst sein oder eine andere Person in Ihrer Organisation.

[!INCLUDE [digital-twins-role-rename-note.md](digital-twins-role-rename-note.md)]
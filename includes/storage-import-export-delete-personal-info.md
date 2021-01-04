---
title: include file
description: include file
services: azure-policy
author: craigshoemaker
ms.service: azure-policy
ms.topic: include
ms.date: 05/18/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 137aca7c6c857ee6e833c359b710e1c1848d15ed
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558666"
---
## <a name="deleting-personal-information"></a>Löschen personenbezogener Informationen

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Personenbezogene Informationen sind für den Import/Export-Dienst (über das Portal und die API) während Import- und Exportvorgängen wichtig. Dabei werden u.a. die folgenden Daten verwendet:

- Kontaktname
- Telefonnummer
- Email
- Anschrift
- City
- Postleitzahl
- State
- Land/Provinz/Region
- Laufwerk-ID
- Kontonummer des Versanddienstleisters
- Nachverfolgungsnummer für den Versand

Wenn ein Import/Export-Auftrag erstellt wird, geben die Benutzer Kontaktinformationen und eine Lieferanschrift an. Personenbezogene Informationen werden an bis zu zwei verschiedenen Speicherorten gespeichert: im Auftrag und optional in den Portaleinstellungen. Personenbezogene Informationen werden nur in den Portaleinstellungen gespeichert, wenn Sie das Kontrollkästchen **Speichert den Spediteur und die Adresse für Rücksendungen als Standardwert** im Abschnitt *Informationen für Rücksendung* während des Exportprozesses aktivieren.

Personenbezogene Kontaktinformationen können auf folgende Weise gelöscht werden:

- Daten, die mit dem Auftrag gespeichert wurden, werden mit dem Auftrag gelöscht. Benutzer können Aufträge manuell löschen, und abgeschlossene Aufträge werden nach 90 Tagen automatisch gelöscht. Sie können die Aufträge manuell über die REST-API oder das Azure-Portal löschen. Um einen Auftrag im Azure-Portal zu löschen, wechseln Sie zu Ihrem Import/Export-Auftrag, und klicken Sie auf der Befehlsleiste auf *Löschen*. Weitere Informationen zum Löschen eines Import/Export-Auftrags per REST-API finden Sie unter [Löschen eines Import/Export-Auftrags](/previous-versions/azure/storage/common/storage-import-export-cancelling-and-deleting-jobs).

- Kontaktinformationen, die in den Portaleinstellungen gespeichert wurden, können durch Löschen der Portaleinstellungen entfernt werden. Sie können Portaleinstellungen löschen, indem Sie folgende Schritte ausführen:
  - Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
  - Klicken Sie unter *Einstellungen* auf das Symbol ![Azure-Einstellungen](media/storage-import-export-delete-personal-info/azure-settings-icon.png).
  - Klicken Sie auf *Alle Einstellungen exportieren* (um die aktuellen Einstellungen in einer `.json`-Datei zu speichern).
  - Klicken Sie auf *Alle Einstellungen und private Dashboards löschen*, um alle Einstellungen einschließlich der gespeicherten Kontaktinformationen zu löschen.

Weitere Informationen finden Sie im [Trust Center in der Microsoft-Datenschutzrichtlinie](https://www.microsoft.com/trustcenter).
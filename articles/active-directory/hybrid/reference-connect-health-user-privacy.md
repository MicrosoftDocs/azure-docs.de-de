---
title: Azure AD Connect Health und Datenschutz | Microsoft-Dokumentation
description: Dieses Dokument beschreibt den Datenschutz mit Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27e5ae48a8194c05a19e5164ee2cc6be68967a62
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60386251"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Datenschutz und Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Dieser Artikel befasst sich mit Azure AD Connect Health und dem Datenschutz.  Informationen zu Azure AD Connect Health und zum Datenschutz finden Sie in [diesem Artikel](reference-connect-user-privacy.md).

## <a name="user-privacy-classification"></a>Datenschutzklassifizierung
Azure AD Connect Health fällt in die Kategorie **Datenverarbeitung** der DSGVO-Klassifizierung. Als eine Datenverarbeitungs-Pipeline stellt der Dienst Datenverarbeitungsdienste für wichtige Partner und Endbenutzer bereit. Azure AD Connect Health generiert keine Benutzerdaten und kann nicht unabhängig steuern, welche personenbezogenen Daten erfasst und wie diese verwendet werden. Datenabruf, Aggregation, Analyse und Berichterstellung in Azure AD Connect Health basieren auf vorhandenen lokalen Daten. 

## <a name="data-retention-policy"></a>Datenaufbewahrungsrichtlinie
Azure AD Connect Health erstellt keine Berichte, führt keine Analysen aus und bietet keine Einblicke über 30 Tage hinaus. Aus diesem Grund erfolgt in Azure AD Connect Health keine Speicherung, Verarbeitung oder Beibehaltung von Daten über 30 Tage hinaus. Dieser Entwurf ist mit den DSGVO-Vorschriften, den Microsoft-Richtlinien für Datenschutz und Compliance und den Azure AD-Richtlinien zur Datenaufbewahrung konform. 

Server mit aktiven **Fehlerbenachrichtigungen** des Typs **Die Daten des Integritätsdiensts sind nicht aktuell** für mehr als 30 aufeinander folgende Tage deuten darauf hin, dass während dieser Zeitspanne keine Daten Connect Health erreicht haben. Diese Server werden deaktiviert und nicht im Connect Health-Portal angezeigt. Um die Server erneut zu aktivieren, müssen Sie den Integritäts-Agent deinstallieren und dann [erneut installieren](how-to-connect-health-agent-install.md). Beachten Sie, dass dies nicht für **Warnungen** mit derselben Warnungstyp gilt. Warnungen geben an, dass ein Teil der Daten auf dem Server nicht vorhanden ist, für den Sie benachrichtigt werden. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Deaktivieren der Datensammlung und Überwachung in Azure AD Connect Health
Mithilfe von Azure AD Connect Health können Sie die Datensammlung für jeden überwachten Server einzeln oder für eine Instanz eines überwachten Diensts beenden. Sie können beispielsweise die Datensammlung für einzelne AD FS-Server (Active Directory Federation Services) beenden, die mit Azure AD Connect Health überwacht werden. Sie können auch die Datensammlung für die gesamte AD FS-Instanz beenden, die unter Verwendung von Azure AD Connect Health überwacht wird. In diesem Fall werden die entsprechenden Server nach dem Beenden der Datensammlung aus dem Azure AD Connect Health-Portal gelöscht. 

>[!IMPORTANT]
> Sie benötigen globale Administratorberechtigungen für Azure AD oder die Rolle „Mitwirkender“ in RBAC zum Löschen überwachter Server aus Azure AD Connect Health.
>
> Das Entfernen eines Servers oder einer Dienstinstanz aus Azure AD Connect Health kann nicht rückgängig gemacht werden. 

### <a name="what-to-expect"></a>Was können Sie erwarten?
Wenn Sie die Datensammlung und die Überwachung für einen einzelnen überwachten Server oder eine Instanz eines überwachten Diensts beenden möchten, beachten Sie Folgendes:

- Wenn Sie eine Instanz eines überwachten Diensts löschen, wird die Instanz aus der Liste des Azure AD Connect Health-Überwachungsdiensts im Portal entfernt. 
- Wenn Sie einen überwachten Server oder eine Instanz eines überwachten Diensts löschen, wird der Health-Agent NICHT deinstalliert oder von Ihren Servern entfernt. Der Health-Agent ist nicht für das Senden von Daten an Azure AD Connect Health konfiguriert. Sie müssen den Health-Agent manuell von zuvor überwachten Servern deinstallieren.
- Wenn Sie vor Ausführung dieses Schritts den Connect Health-Agent nicht deinstalliert haben, werden auf dem Server bzw. den Servern möglicherweise Fehlerereignisse in Zusammenhang mit dem Connect Health-Agent angezeigt.
- Alle Daten der Instanz des überwachten Diensts werden gemäß der Microsoft Azure-Datenaufbewahrungsrichtlinie gelöscht.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Deaktivieren der Datensammlung und Überwachung für eine Instanz eines überwachten Diensts
Weitere Informationen finden Sie unter [Entfernen einer Dienstinstanz aus Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Deaktivieren der Datensammlung und Überwachung für einen überwachten Server
Weitere Informationen finden Sie unter [Entfernen eines Servers aus Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Deaktivieren der Datensammlung und Überwachung für alle überwachten Dienste in Azure AD Connect Health
Azure AD Connect Health bietet auch die Option zum Beenden der Datensammlung für **alle** im Mandanten registrierten Dienste. Vor einer derartigen Aktion wird eine sorgfältige Abwägung und vollumfängliche Bestätigung aller globalen Administratoren empfohlen. Nach Einleitung des Vorgangs beendet der Connect Health-Dienst den Empfang und die Verarbeitung aller Daten sowie die Berichterstellung für Ihre gesamten Dienste. Vorhandene Daten werden im Connect Health-Dienst nicht mehr als 30 Tage lang aufbewahrt.
Wenn Sie die Datensammlung für bestimmte Server beenden möchten, führen Sie die unter Löschen von bestimmten Servern beschriebenen Schritte aus. Um die mandantenweise Datensammlung zu beenden, führen Sie die folgenden Schritte aus, um die Datensammlung zu beenden und alle Dienste des Mandanten zu löschen.

1. Klicken Sie im Hauptblatt unter „Konfiguration“ auf **Allgemeine Einstellungen**. 
2. Klicken Sie oben auf dem Blatt auf die Schaltfläche **Datensammlung beenden**. Die anderen Optionen unter den Einstellungen zur Mandantenkonfiguration werden deaktiviert, sobald der Prozess gestartet wird.  
 
   ![Beenden der Datensammlung](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. Vergewissern Sie sich anhand der Liste über die eingebundenen Dienste, die durch das Beenden von Datensammlungen betroffen sind. 
4. Geben Sie den exakten Mandantennamen ein, um die Aktionsschaltfläche **Löschen** zu aktivieren.
5. Klicken Sie auf **Löschen**, um das Löschen aller Dienste auszulösen. Connect Health beendet den Empfang und die Verarbeitung aller Daten sowie die Berichterstellung von Ihren eingebundenen Diensten. Der gesamte Vorgang kann bis zu 24 Stunden dauern. Beachten Sie, dass dieser Schritt nicht rückgängig gemacht werden kann. 
6. Nachdem der Vorgang abgeschlossen ist, werden keine registrierten Dienste mehr in Connect Health angezeigt. 

   ![Nach dem Beenden der Datensammlung](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Erneutes Aktivieren der Datensammlung und Überwachung in Azure AD Connect Health
Um die Überwachung in Azure AD Connect Health für einen zuvor gelöschten überwachten Dienst erneut zu aktivieren, müssen Sie den Health-Agent auf allen Servern deinstallieren und [erneut installieren](how-to-connect-health-agent-install.md).

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Erneutes Aktivieren der Datensammlung und Überwachung für alle überwachten Dienste

Die mandantenweise Datensammlung kann in Azure AD Connect Health fortgesetzt werden. Vor einer derartigen Aktion wird eine sorgfältige Abwägung und vollumfängliche Bestätigung aller globalen Administratoren empfohlen.

>[!IMPORTANT]
> 24 Stunden nach der Deaktivierungsaktion stehen die folgenden Schritte zur Verfügung.
> Nach dem erneuten Aktivieren der Datensammlung enthalten die Erkenntnisse und Überwachungsdaten in Connect Health keine älteren Daten, die zuvor gesammelt wurden. 

1. Klicken Sie im Hauptblatt unter „Konfiguration“ auf **Allgemeine Einstellungen**. 
2. Klicken Sie oben auf dem Blatt auf die Schaltfläche **Datensammlung aktivieren**. 
 
   ![Aktivieren der Datensammlung](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. Geben Sie den exakten Mandantennamen ein, um die Schaltfläche **Aktivieren** zu aktivieren.
4. Klicken Sie auf die Schaltfläche **Aktivieren**, um die Berechtigung für die Datensammlung im Connect Health-Dienst zu gewähren. Die Änderung wird kurzfristig übernommen. 
5. Führen Sie den [Installationsvorgang](how-to-connect-health-agent-install.md) aus, um den Agent in den zu überwachenden Servern neu zu installieren, und die Dienste werden im Portal angezeigt.  


## <a name="next-steps"></a>Nächste Schritte
* [Die Microsoft-Datenschutzrichtlinie im Trust Center](https://www.microsoft.com/trustcenter)
* [Azure AD Connect und Datenschutz](reference-connect-user-privacy.md)


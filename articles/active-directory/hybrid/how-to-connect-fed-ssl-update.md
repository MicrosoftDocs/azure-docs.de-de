---
title: 'Azure AD Connect: Aktualisieren des SSL-Zertifikats für eine AD FS-Farm | Microsoft-Dokumentation'
description: Dieses Dokument erläutert die Schritte zum Aktualisieren des SSL-Zertifikats einer AD FS-Farm mithilfe von Azure AD Connect.
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory  
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39ac0e9cf11a0c6c212c4beadb6635ad2b6b056d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60244741"
---
# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Aktualisieren des SSL-Zertifikats für eine Active Directory-Verbunddienste-Farm (AD FS)

## <a name="overview"></a>Übersicht
In diesem Artikel wird beschrieben, wie Sie Azure AD Connect verwenden können, um das SSL-Zertifikat für eine AD FS-Farm (Active Directory Federation Services, Active Directory-Verbunddienste) zu aktualisieren. Sie können mit dem Tool Azure AD Connect das SSL-Zertifikat für die AD FS-Farm ganz einfach aktualisieren – auch dann, wenn die Anmeldemethode des Benutzers nicht auf AD FS festgelegt ist.

Sie können den gesamten Vorgang der Aktualisierung des SSL-Zertifikats für die AD FS-Farm über alle Verbundserver und Webanwendungsproxy-Server (WAP) hinweg in drei einfachen Schritten ausführen:

![Drei Schritte](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Weitere Informationen zu von AD FS verwendeten Zertifikaten finden Sie im Artikel [Grundlegendes zu den von AD FS verwendeten Zertifikaten](https://technet.microsoft.com/library/cc730660.aspx).

## <a name="prerequisites"></a>Voraussetzungen

* **AD FS-Farm**: Stellen Sie sicher, dass die AD FS-Farm unter Windows Server 2012 R2 oder höher ausgeführt wird.
* **Azure AD Connect**: Stellen Sie sicher, dass die Version von Azure AD Connect 1.1.553.0 oder höher lautet. Sie verwenden den Vorgang **AD FS-SSL-Zertifikat aktualisieren**.

![Vorgang „SSL aktualisieren“](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Schritt 1: Bereitstellen von Informationen zur AD FS-Farm

Azure AD Connect versucht, die Informationen zur AD FS-Farm über folgende Schritte automatisch abzurufen:
1. Abfragen der Farminformationen von AD FS (Windows Server 2016 oder höher)
2. Verweisen auf die Informationen aus vorherigen Ausführungen, die mit Azure AD Connect lokal gespeichert werden

Sie können die angezeigte Liste mit Servern bearbeiten, indem Sie Server hinzufügen oder entfernen, um die derzeitige Konfiguration der AD FS-Farm abzubilden. Sobald die Serverinformationen bereitgestellt wurden, zeigt Azure AD Connect die Konnektivität und den aktuellen Status des SSL-Zertifikats an.

![AD FS-Serverinformationen](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Wenn die Liste einen Server enthält, der nicht zur AD FS-Farm gehört, klicken Sie auf **Entfernen**, um den Server aus der Liste der Server in der AD FS-Farm zu löschen.

![Offlineserver in der Liste](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Das Entfernen eines Servers aus der Liste der Server für eine AD FS-Farm in Azure AD Connect ist ein lokaler Vorgang, durch den die Informationen für die AD FS-Farm aktualisiert werden, die Azure AD Connect lokal verwaltet. Azure AD Connect ändert nicht die AD FS-Konfiguration, um die Änderung wiederzugeben.    

## <a name="step-2-provide-a-new-ssl-certificate"></a>Schritt 2: Bereitstellen eines neuen SSL-Zertifikats

Nachdem Sie die Informationen zu den AD FS-Farmservern bestätigt haben, fragt Azure AD Connect nach dem neuen SSL-Zertifikat. Stellen Sie ein kennwortgeschütztes PFX-Zertifikat bereit, um die Installation fortzuführen.

![SSL-Zertifikat](./media/how-to-connect-fed-ssl-update/certificate.png)

Nachdem Sie das Zertifikat bereitgestellt haben, geht Azure AD eine Reihe von Voraussetzungen durch. Überprüfen Sie das Zertifikat, um sicherzustellen, dass es für die AD FS-Farm passend ist:

-   Der Antragstellername/Alternative Antragstellername für das Zertifikat ist entweder der Name des Verbunddiensts oder ein Platzhalterzertifikat.
-   Das Zertifikat ist mehr als 30 Tage gültig.
-   Die Vertrauenskette des Zertifikats ist gültig.
-   Das Zertifikat ist kennwortgeschützt.

## <a name="step-3-select-servers-for-the-update"></a>Schritt 3: Auswählen von Servern für das Update

Wählen Sie im nächsten Schritt die Server aus, deren SSL-Zertifikat aktualisiert werden muss. Server, die offline sind, können nicht für das Update ausgewählt werden.

![Auswählen von zu aktualisierenden Servern](./media/how-to-connect-fed-ssl-update/selectservers.png)

Nachdem die Konfiguration abgeschlossen ist, zeigt Azure AD Connect die Meldung mit dem Status des Updates an und stellt eine Option zum Überprüfen der AD FS-Anmeldung bereit.

![Konfiguration abgeschlossen](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Häufig gestellte Fragen

* **Was sollte der Antragstellername des Zertifikats für das neue AD FS-SSL-Zertifikats sein?**

    Azure AD Connect überprüft, ob der Antragstellername bzw. alternative Antragstellername des Zertifikats den Namen des Verbunddiensts enthält. Wenn der Name Ihres Verbunddiensts beispielsweise „fs.contoso.com“ lautet, muss der Antragstellername bzw. alternative Antragstellername „fs.contoso.com“ lauten.  Platzhalterzertifikate werden ebenfalls akzeptiert.

* **Warum werde ich auf der Seite „WAP-Server“ erneut nach Anmeldeinformationen gefragt?**

    Wenn die Anmeldeinformationen für die Verbindung mit AD FS-Servern nicht auch über die Berechtigung zum Verwalten der WAP-Server verfügen, fragt Azure AD Connect nach Anmeldeinformationen, die die Verwaltungsberechtigung für WAP-Server haben.

* **Der Server wird als offline angezeigt. Wie soll ich vorgehen?**

    Azure AD Connect kann keinen Vorgang durchführen, solange der Server offline ist. Wenn der Server Teil einer AD FS-Farm ist, überprüfen Sie die Verbindung mit dem Server. Nachdem Sie das Problem behoben haben, klicken Sie im Assistenten zum Aktualisieren des Status auf das Aktualisierungssymbol. Wenn der Server zuvor Teil der Farm war, jedoch nicht mehr vorhanden ist, klicken Sie auf **Entfernen**, um ihn aus der Liste mit Servern zu löschen, die Azure AD Connect verwaltet. Durch das Entfernen des Servers aus der Liste in Azure AD Connect wird die AD FS-Konfiguration selbst nicht geändert. Wenn Sie AD FS unter Windows Server 2016 oder höher nutzen, verbleibt der Server in den Konfigurationseinstellungen und wird bei der nächsten Ausführung des Vorgangs erneut angezeigt.

* **Kann ich einen Teil meiner Farm-Server mit dem neuen SSL-Zertifikat aktualisieren?**

    Ja. Sie können den Vorgang **SSL-Zertifikat aktualisieren** jederzeit erneut ausführen, um die verbleibenden Server zu aktualisieren. Auf der Seite **Server für SSL-Zertifikataktualisierung auswählen** können Sie die Liste der Server nach **SSL-Ablaufdatum** sortieren, um einfach auf die Server zuzugreifen, die noch nicht aktualisiert wurden.

* **Ich habe den Server beim vorherigen Ausführen entfernt. Er wird jedoch immer noch als offline angezeigt und auf der AD FS-Server-Seite aufgelistet. Warum wird der Offlineserver selbst nach seinem Entfernen noch angezeigt?**

    Durch das Entfernen des Servers aus der Liste in Azure AD Connect wird dieser nicht aus der AD FS-Konfiguration entfernt. Azure AD Connect bezieht sich auf AD FS (Windows Server 2016 oder höher), um beliebige Informationen zur Farm abzurufen. Wenn der Server noch in der AD FS-Konfiguration vorhanden ist, wird er wieder in der Liste angezeigt werden.  

## <a name="next-steps"></a>Nächste Schritte

- [Azure AD Connect und Verbund](how-to-connect-fed-whatis.md)
- [Verwaltung der Active Directory-Verbunddienste und Anpassung mit Azure AD Connect](how-to-connect-fed-management.md)


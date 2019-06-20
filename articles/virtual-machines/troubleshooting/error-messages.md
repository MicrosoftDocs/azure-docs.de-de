---
title: Gängige VM-Fehlercodes in Azure | Microsoft-Dokumentation
description: Lernen Sie einige der gängigen Fehlercodes kennen, die beim Bereitstellen und Verwalten virtueller Computer in Azure auftreten können.
services: virtual-machines
documentationcenter: ''
author: xujing-ms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 5/22/2017
ms.author: xujing
ms.openlocfilehash: 5945be210812a6cbc24c9a3bb12414be5212be17
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60711202"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Grundlegendes zu allgemeinen Fehlermeldungen bei der Verwaltung virtueller Computer in Azure

Dieser Artikel beschreibt einige der häufigsten Fehlercodes und -meldungen, die beim Erstellen oder Verwalten virtueller Computer (VMs) in Azure auftreten können.

>[!NOTE]
> Sie können Kommentare auf dieser Seite als Feedback bzw. über [Azure-Feedback](https://feedback.azure.com/forums/216843-virtual-machines) unter dem Tag „#azerrormessage“ hinterlassen.

## <a name="error-response-format"></a>Format von Fehlerantworten 
Auf Azure-VMs wird für Fehlerantworten das folgende JSON-Format verwendet:

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner evel error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

Eine Fehlerantwort enthält immer einen Statuscode und ein Fehlerobjekt. Jedes Fehlerobjekt enthält immer einen Fehlercode und eine Fehlermeldung. Wenn der virtuelle Computer mit einer Vorlage erstellt wird, enthält das Fehlerobjekt auch einen Abschnitt mit Details zur inneren Ebene mit Fehlercodes und -meldungen. Normalerweise ist die innerste Ebene von Fehlermeldungen die Grundursache.


## <a name="common-virtual-machine-management-errors"></a>Allgemeine Verwaltungsfehler bei virtuellen Computern

In diesem Abschnitt sind die allgemeinen Fehlermeldungen aufgeführt, die bei der Verwaltung Ihrer virtuellen Computer unter Umständen angezeigt werden:

|  Fehlercode  |  Fehlermeldung  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Fehler beim Abrufen der Lease während der Erstellung des Datenträgers „{0}“ unter Verwendung des Blobs mit URI {1}. Blob wird bereits verwendet.  |  
|  AllocationFailed  |  Fehler bei der Zuordnung. Reduzieren Sie die Größe oder die Anzahl der virtuellen Computer, und versuchen Sie es später erneut, oder führen Sie die Bereitstellung in einer anderen Verfügbarkeitsgruppe oder an einem anderen Azure-Standort durch.  |  
|  AllocationFailed  |  Interner Fehler bei der VM-Zuordnung. Versuchen Sie es später erneut, oder stellen Sie an einem anderen Speicherplatz bereit.  |
|  ArtifactNotFound  |  Die VM-Erweiterung mit Herausgeber „{0}“ und Typ „{1}“ konnte am Speicherort „{2}“ nicht gefunden werden.  |
|  ArtifactNotFound  |  Erweiterung mit Publisher „{0}“, Typ „{1}“ und Typhandlerversion „{2}“ wurde im Erweiterungsrepository nicht gefunden.  |
|  ArtifactVersionNotFound  |  Im Artefaktrepository wurde keine Version gefunden, die der angeforderten Version „{0}“ entspricht.  |
|  ArtifactVersionNotFound  |  Im Artefaktrepository wurde keine Version gefunden, die der angeforderten Version „{0}“ für die VM-Erweiterung mit Herausgeber „{1}“ und Typ „{2}“ entspricht.  |
|  AttachDiskWhileBeingDetached  |  Datenträger „{0}“ kann nicht an virtuellen Computer „{1}“ angefügt werden, da der Datenträger aktuell getrennt wird. Warten Sie, bis der Datenträger vollständig getrennt wurde, und versuchen Sie es erneut.  |
|  BadRequest  |  Verfügbarkeitsgruppen vom Typ „Angepasst“ werden in dieser Region noch nicht unterstützt.  |
|  BadRequest  |  Das Hinzufügen eines virtuellen Computers mit verwalteten Datenträgern zu einer nicht verwalteten Verfügbarkeitsgruppe oder das Hinzufügen eines virtuellen Computers mit blobbasierten Datenträgern zu einer verwalteten Verfügbarkeitsgruppe wird nicht unterstützt. Erstellen Sie eine Verfügbarkeitsgruppe, für die die Eigenschaft „managed“ festgelegt wurde, um dieser einen virtuellen Computer mit verwalteten Datenträgern hinzuzufügen.  |
|  BadRequest  |  Managed Disks wird in dieser Region nicht unterstützt.  |
|  BadRequest  |  Mehrere VMExtensions-Werte pro Handler werden für den Betriebssystemtyp „{0}“ nicht unterstützt. VMExtension-Wert „{1}“ mit Handler „{2}“ wurde in der Eingabe bereits hinzugefügt oder angegeben.  |
|  BadRequest  |  Der Vorgang „{0}“ wird für die Ressource „{1}“ mit verwalteten Datenträgern nicht unterstützt.  |
|  CertificateImproperlyFormatted  |  Die JSON-Darstellung des Schlüssels, die von „{0}“ abgerufen wurde, verfügt über ein Datenfeld, das keiner ordnungsgemäß formatierten PFX-Datei entspricht, oder das angegebene Kennwort decodiert die PFX-Datei nicht ordnungsgemäß.  |
|  CertificateImproperlyFormatted  |  Die von „{0}“ abgerufenen Daten können nicht in JSON deserialisiert werden.  |
|  Konflikt:  |  Eine Größenänderung für den Datenträger ist nur zulässig, wenn ein virtueller Computer erstellt wird oder die Zuweisung des virtuellen Computers aufgehoben wurde.  |
|  ConflictingUserInput  |  Der Datenträger „{0}“ kann nicht angefügt werden, weil der Datenträger sich bereits im Besitz der VM „{1}“ befindet.  |
|  ConflictingUserInput  |  Quell- und Zielressourcengruppe sind gleich.  |
|  ConflictingUserInput  |  Quell- und Zielspeicherkonten für Datenträger „{0}“ sind unterschiedlich.  |
|  ContainerAlreadyOnLease  |  Es gibt bereits eine Lease für den Speichercontainer mit dem Blob mit URI {0}.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Die Anforderung zum Verschieben von Ressourcen enthält KeyVault-Ressourcen, auf die durch mindestens einen {0} in der Anforderung verwiesen wird. Dies wird für abonnementübergreifende Verschiebungen derzeit nicht unterstützt. Überprüfen Sie die Fehlerdetails für die KeyVault-Ressourcen-IDs.  |
|  DiagnosticsOperationInternalError  |  Interner Fehler beim Verarbeiten des Diagnoseprofils des virtuellen Computers „{0}“.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  Blob „{0}“ wird bereits von einem anderen Datenträger verwendet, der zum virtuellen Computer „{1}“ gehört. Sie können die Blobmetadaten für die Datenträgerreferenzinformationen überprüfen.  |
|  DiskBlobNotFound  |  VHD-Blob mit URI {0} für Datenträger „{1}“ konnte nicht gefunden werden.  |
|  DiskBlobNotFound  |  Das VHD-Blob mit dem URI „{0}“ wurde nicht gefunden.  |
|  DiskEncryptionKeySecretMissingTags  |  Der geheime Schlüssel „{0}“ weist keine „{1}“-Tags auf. Aktualisieren Sie die Version des geheimen Schlüssels, fügen Sie die erforderlichen Tags hinzu, und versuchen Sie es erneut.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Fehler beim Extrahieren des Werts für den geheimen Schlüssel „{0}“ mithilfe von Schlüssel „{1}“.  |
|  DiskImageNotReady  |  Datenträgerimage „{0}“ befindet sich im Zustand „{1}“. Versuchen Sie es erneut, wenn das Image bereit ist.  |
|  DiskPreparationError  |  Beim Vorbereiten von VM-Datenträgern ist mindestens ein Fehler aufgetreten. Weitere Informationen finden Sie in der Datenträgerinstanzansicht.  |
|  DiskProcessingError  |  Die Datenträgerverarbeitung wurde angehalten, weil die VM weitere Datenträger unter „Fehlerhafte Datenträger“ aufweist.  |
|  ImageBlobNotFound  |  VHD-Blob mit URI {0} für Datenträger „{1}“ konnte nicht gefunden werden.  |
|  ImageBlobNotFound  |  Das VHD-Blob mit dem URI „{0}“ wurde nicht gefunden.  |
|  IncorrectDiskBlobType  |  Datenträgerblobs können nur vom Typ „Seitenblob“ sein. Blob „{0}“ für Datenträger „{1}“ ist vom Typ „Blockblob“.  |
|  IncorrectDiskBlobType  |  Datenträgerblobs können nur vom Typ „Seitenblob“ sein. Das Blob „{0}“ weist den Typ „{1}“ auf.  |
|  IncorrectImageBlobType  |  Datenträgerblobs können nur vom Typ „Seitenblob“ sein. Blob „{0}“ für Datenträger „{1}“ ist vom Typ „Blockblob“.  |
|  IncorrectImageBlobType  |  Datenträgerblobs können nur vom Typ „Seitenblob“ sein. Das Blob „{0}“ weist den Typ „{1}“ auf.  |
|  InternalOperationError  |  Das Speicherkonto „{0}“ konnte nicht aufgelöst werden. Stellen Sie sicher, dass es durch den Speicherressourcenanbieter am gleichen Speicherort wie die Serverressource erstellt wurde.  |
|  InternalOperationError  |  Fehler bei {0} Zielsucheaufgaben.  |
|  InternalOperationError  |  Beim Überprüfen des Netzwerkprofils des virtuellen Computers „{0}“ ist ein Fehler aufgetreten.  |
|  InvalidAccountType  |  AccountType „{0}“ ist ungültig.  |
|  InvalidParameter  |  Der Wert von Parameter „{0}“ ist ungültig.  |
|  InvalidParameter  |  Das angegebene Administratorkennwort ist unzulässig.  |
|  InvalidParameter  |  Das angegebene Kennwort muss zwischen {0} und {1} Zeichen lang sein und mindestens {2} der folgenden Kennwortkomplexitätsanforderungen erfüllen: <ol><li> Enthält einen Großbuchstaben</li><li>Enthält einen Kleinbuchstaben</li><li>Enthält eine Ziffer</li><li>Enthält ein Sonderzeichen</li></ol>  |
|  InvalidParameter  |  Der angegebene Administratorbenutzername ist unzulässig.  |
|  InvalidParameter  |  Ein vorhandener Betriebssystemdatenträger kann nicht angefügt werden, wenn der virtuelle Computer aus einem Plattform- oder Benutzerimage erstellt wird.  |
|  InvalidParameter  |  Containername „{0}“ ist ungültig. Containernamen müssen zwischen 3 und 63 Zeichen lang sein und dürfen nur kleine alphanumerische Zeichen und Bindestriche enthalten. Vor und nach einem Bindestrich muss ein alphanumerisches Zeichen stehen.  |
|  InvalidParameter  |  Containername „{0}“ in URL „{1}“ ist ungültig. Containernamen müssen zwischen 3 und 63 Zeichen lang sein und dürfen nur kleine alphanumerische Zeichen und Bindestriche enthalten. Vor und nach einem Bindestrich muss ein alphanumerisches Zeichen stehen.  |
|  InvalidParameter  |  Der Blobname in der URL „{0}“ enthält einen Schrägstrich. Dieses Zeichen wird für Datenträger zurzeit nicht unterstützt.  |
|  InvalidParameter  |  Der URI „{0}“ scheint kein korrekter Blob-URI zu sein.  |
|  InvalidParameter  |  Ein Datenträger namens „{0}“ verwendet bereits dasselbe LUN: {1}.  |
|  InvalidParameter  |  Ein Datenträger namens „{0}“ ist bereits vorhanden.  |
|  InvalidParameter  |  Benutzerimage-Außerkraftsetzungen für einen Datenträger, der bereits im angegebenen Imageverweis definiert wurde, können nicht angegeben werden.  |
|  InvalidParameter  |  Ein Datenträger namens „{0}“ verwendet bereits dieselbe VHD-URL „{1}“.  |
|  InvalidParameter  |  Die angegebene Fehlerdomänenanzahl {0} muss im Bereich {1} bis {2} liegen.  |
|  InvalidParameter  |  Der Lizenztyp „{0}“ ist ungültig. Gültige Lizenztypen: „Windows_Client“ oder „Windows_Server“. Die Groß-/Kleinschreibung muss beachtet werden.  |
|  InvalidParameter  |  Der Linux-Hostname darf nicht mehr als {0} Zeichen umfassen und darf die folgenden Zeichen nicht enthalten: {1}.  |
|  InvalidParameter  |  Zielpfad für öffentliche SSH-Schlüssel ist aktuell aufgrund eines bekannten Problems im Linux-Bereitstellungs-Agent auf den Standardwert {0} beschränkt.  |
|  InvalidParameter  |  Ein Datenträger für LUN „{0}“ ist bereits vorhanden.  |
|  InvalidParameter  |  Abonnement „{0}“ der Anforderung muss mit Abonnement „{1}“ übereinstimmen, das in der ID des verwalteten Datenträgers enthalten ist.  |
|  InvalidParameter  |  Benutzerdefinierte Daten in „OSProfile“ müssen mit Base64 codiert sein und eine maximale Länge von {0} Zeichen haben.  |
|  InvalidParameter  |  Der Blobname in der URL „{0}“ muss auf die Erweiterung „{1}“ enden.  |
|  InvalidParameter  |  „{0}“ ist kein gültiges erfasstes VHD-Blobnamenpräfix. Ein gültiges Präfix entspricht Regex „{1}“.  |
|  InvalidParameter  |  Dem virtuellen Computer können keine Zertifikate hinzugefügt werden, wenn der VM-Agent nicht bereitgestellt wurde.  |
|  InvalidParameter  |  Ein Datenträger für LUN „{0}“ ist bereits vorhanden.  |
|  InvalidParameter  |  Die VM kann nicht geändert werden, weil die angeforderte Größe ({0}) in dem Cluster nicht verfügbar ist, dem die Verfügbarkeitsgruppe zurzeit zugeordnet ist. Folgende Größen sind verfügbar: {1}. Weitere Informationen zur VM-Größenstrategie finden Sie unter https://aka.ms/azure-resizevm.  |
|  InvalidParameter  |  Die angeforderte VM-Größe {0} ist in der aktuellen Region nicht verfügbar. Folgende Größen stehen in der aktuellen Region zur Verfügung: {1}. Weitere Informationen zu den in den einzelnen Regionen verfügbaren VM-Größen finden Sie unter https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Die angeforderte VM-Größe {0} ist in der aktuellen Region nicht verfügbar. Weitere Informationen zu den in den einzelnen Regionen verfügbaren VM-Größen finden Sie unter https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Windows-Administratorbenutzername darf nicht länger als {0} Zeichen sein, mit einem Punkt (.) enden oder die folgenden Zeichen enthalten: {1}.  |
|  InvalidParameter  |  Windows-Computername darf nicht länger als {0} Zeichen sein, nur aus Ziffern bestehen oder die folgenden Zeichen enthalten: {1}.  |
|  MissingMoveDependentResources  |  Die Anforderung zum Verschieben von Ressourcen enthält nicht alle abhängigen Ressourcen. Prüfen Sie die Fehlerdetails, um fehlende Ressourcen-IDs zu ermitteln.  |
|  MoveResourcesHaveInvalidState  |  Die Anforderung zur Ressourcenverschiebung enthält VMs, die ungültigen Speicherkonten zugeordnet sind. Überprüfen Sie die Details für diese Ressourcen-IDs und referenzierten Speicherkontonamen.  |
|  MoveResourcesHavePendingOperations  |  Die Anforderung zum Verschieben von Ressourcen enthält Ressourcen mit ausstehenden Vorgängen. Prüfen Sie die Details für diese Ressourcen-IDs. Versuchen Sie den Vorgang erneut, sobald die ausstehenden Vorgänge abgeschlossen sind.  |
|  MoveResourcesNotFound  |  Die Anforderung zum Verschieben von Ressourcen enthält nicht gefundene Ressourcen. Prüfen Sie die Details für diese Ressourcen-IDs.  |
|  NetworkingInternalOperationError  |  Unbekannter Netzwerkzuordnungsfehler.  |
|  NetworkingInternalOperationError  |  Unbekannter Netzwerkzuordnungsfehler  |
|  NetworkingInternalOperationError  |  Beim Verarbeiten des Netzwerkprofils des virtuellen Computers ist ein interner Fehler aufgetreten.  |
|  NotFound  |  Die Verfügbarkeitsgruppe „{0}“ kann nicht gefunden werden.  |
|  NotFound  |  Die in der Anforderung angegebene Quell-VM „{0}“ ist nicht an diesem Azure-Standort vorhanden.  |
|  NotFound  |  Mandant mit ID {0} nicht gefunden.  |
|  NotFound  |  Das Image „{0}“ wurde nicht gefunden.  |
|  NotSupported  |  Der Lizenztyp lautet „{1}“, aber das Imageblob „{0}“ ist nicht aus der lokalen Umgebung.  |
|  OperationNotAllowed  |  Verfügbarkeitsgruppe „{0}“ kann nicht gelöscht werden. Stellen Sie vor dem Löschen einer Verfügbarkeitsgruppe sicher, dass sie keine virtuellen Computer enthält.  |
|  OperationNotAllowed  |  Das Ändern der Verfügbarkeitsgruppen-SKU von „Angepasst“ in „Klassisch“ ist unzulässig.  |
|  OperationNotAllowed  |  Erweiterungen im virtuellen Computer können nur geändert werden, wenn der virtuelle Computer ausgeführt wird.  |
|  OperationNotAllowed  |  Die Aktion zum Erfassen wird nur auf einem virtuellen Computer mit blobbasierten Datenträgern unterstützt. Verwenden Sie die Image-Ressourcen-APIs, um ein Image aus einem verwalteten virtuellen Computer zu erstellen.  |
|  OperationNotAllowed  |  Die Ressource „{0}“ kann erst aus dem Image „{1}“ erstellt werden, wenn das Image erfolgreich erstellt wurde.  |
|  OperationNotAllowed  |  Aktualisierungen von „encryptionSettings“ sind nicht zulässig, wenn der virtuelle Computer zugewiesen ist. Versuchen Sie es nach Freigabe des virtuellen Computers erneut.  |
|  OperationNotAllowed  |  Das Hinzufügen eines verwalteten Datenträgers zu einem virtuellen Computer mit blobbasierten Datenträgern wird nicht unterstützt.  |
|  OperationNotAllowed  |  An eine VM dieser Größe können maximal {0} Datenträger angefügt werden.  |
|  OperationNotAllowed  |  Das Hinzufügen eines blobbasierten Datenträgers zu einem virtuellen Computer mit verwalteten Datenträgern wird nicht unterstützt.  |
|  OperationNotAllowed  |  Der Vorgang „{0}“ ist für das Image „{1}“ unzulässig, weil das Image zum Löschen markiert ist. Sie können nur versuchen, den Löschvorgang zu wiederholen (oder warten Sie auf den Abschluss eines laufenden Vorgangs).  |
|  OperationNotAllowed  |  Der Vorgang „{0}“ ist für den virtuellen Computer „{1}“ nicht zulässig, da der virtuelle Computer generalisiert ist.  |
|  OperationNotAllowed  |  Der Vorgang „{0}“ ist nicht erlaubt, weil die Wiederherstellungspunktsammlung „{1}“ zum Löschen markiert ist.  |
|  OperationNotAllowed  |  Der Vorgang „{0}“ ist für die VM-Erweiterung „{1}“ nicht zulässig, da sie zum Löschen markiert ist. Sie können nur versuchen, den Löschvorgang zu wiederholen (oder warten Sie auf den Abschluss eines laufenden Vorgangs).  |
|  OperationNotAllowed  |  Der Vorgang „{0}“ ist nicht zulässig, weil die virtuellen Computer „{1}“ über das Image „{2}“ bereitgestellt werden.  |
|  OperationNotAllowed  |  Der Vorgang „{0}“ ist nicht zulässig, weil die VM-Skalierungsgruppe „{1}“ zurzeit das Image „{2}“ verwendet.  |
|  OperationNotAllowed  |  Der Vorgang „{0}“ ist für den virtuellen Computer „{1}“ nicht zulässig, da der virtuelle Computer zum Löschen markiert ist. Sie können nur versuchen, den Löschvorgang zu wiederholen (oder warten Sie auf den Abschluss eines laufenden Vorgangs).  |
|  OperationNotAllowed  |  Der Vorgang „{0}“ ist für den virtuellen Computer „{1}“ nicht zulässig, da der virtuelle Computer entweder freigegeben oder zum Freigeben markiert wurde.  |
|  OperationNotAllowed  |  Der Vorgang „{0}“ ist für den virtuellen Computer „{1}“ nicht zulässig, da der virtuelle Computer ausgeführt wird. Falls Sie den virtuellen Computer aus dem Gastbetriebssystem heruntergefahren haben, schalten Sie ihn explizit aus.  |
|  OperationNotAllowed  |  Der Vorgang „{0}“ ist nicht für die VM „{1}“ erlaubt, weil die Zuordnung für die VM nicht aufgehoben wurde.  |
|  OperationNotAllowed  |  Der Vorgang „{0}“ ist für die VM „{1}“ unzulässig, weil die VM über eine Erweiterung „{2}“ in einem fehlerhaften Zustand verfügt.  |
|  OperationNotAllowed  |  Vorgang „{0}“ ist für den virtuellen Computer „{1}“ nicht zulässig, da ein anderer Vorgang ausgeführt wird.  |
|  OperationNotAllowed  |  Der Vorgang „{0}“ erfordert, dass der virtuelle Computer „{1}“ generalisiert wird.  |
|  OperationNotAllowed  |  Für den Vorgang ist es erforderlich, dass der virtuelle Computer ausgeführt wird (oder zur Ausführung festgelegt ist).  |
|  OperationNotAllowed  |  Ein Datenträger mit einer Größe von {0} GB, der kleiner ist als der entsprechende Datenträger im Image ({1} GB), ist unzulässig.  |
|  OperationNotAllowed  |  VM-Skalierungsgruppenerweiterungen von Handler „{0}“ können nur beim Erstellen der VM-Skalierungsgruppe hinzugefügt werden.  |
|  OperationNotAllowed  |  VM-Skalierungsgruppenerweiterungen von Handler „{0}“ können nur beim Löschen der VM-Skalierungsgruppe gelöscht werden.  |
|  OperationNotAllowed  |  Die VM „{0}“ verwendet bereits verwaltete Datenträger.  |
|  OperationNotAllowed  |  Die VM „{0}“ gehört zur „klassischen“ Verfügbarkeitsgruppe „{1}“. Please update the availability set to use 'Aligned' SKU and then retry the Conversion. (Aktualisieren Sie die Verfügbarkeitsgruppe für die Verwendung der SKU „Aligned“, und versuchen Sie dann erneut, die Konvertierung durchzuführen.)  |
|  OperationNotAllowed  |  Der aus dem Image erstellte virtuelle Computer darf keine blobbasierten Datenträger umfassen. Bei allen Datenträgern muss es sich um verwaltete Datenträger handeln.  |
|  OperationNotAllowed  |  Erfassungsvorgang kann nicht abgeschlossen werden, da der virtuelle Computer nicht generalisiert ist.  |
|  OperationNotAllowed  |  Verwaltungsvorgänge für die VM „{0}“ sind nicht erlaubt, weil momentan VM-Datenträger in verwaltete Datenträger konvertiert werden.  |
|  OperationNotAllowed  |  Ein laufender Vorgang ändert den Betriebszustand des virtuellen Computers „{0}“ in „{1}“. Führen Sie den Vorgang „{2}“ später aus.  |
|  OperationNotAllowed  |  Die VM kann nicht hinzugefügt oder aktualisiert werden. Die angeforderte VM-Größe {0} steht in der vorhandenen Zuordnungseinheit möglicherweise nicht zur Verfügung. Weitere Informationen zur VM-Größenstrategie finden Sie unter https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Die Größe der VM kann nicht geändert werden, weil die angeforderte Größe {0} in dem Cluster nicht verfügbar ist, dem die Verfügbarkeitsgruppe zurzeit zugeordnet ist. Folgende Größen sind verfügbar: {1}. Weitere Informationen zur VM-Größenstrategie finden Sie unter https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Die Größe der VM kann nicht geändert werden, weil die angeforderte Größe {0} in dem Cluster nicht verfügbar ist, dem die VM zurzeit zugeordnet ist. Um die Größe Ihrer VM in {1} zu ändern, heben Sie die Zuordnung auf (mit dem Vorgang „Beenden“ im Azure-Portal), und wiederholen Sie den Vorgang zur Größenänderung. Weitere Informationen zur VM-Größenstrategie finden Sie unter https://aka.ms/azure-resizevm.  |
|  OSProvisioningClientError  |  Fehler bei der Betriebssystembereitstellung für den virtuellen Computer „{0}“, weil das Gastbetriebssystem zurzeit bereitgestellt wird.  |
|  OSProvisioningClientError  |  Fehler bei der Betriebssystembereitstellung für den virtuellen Computer „{0}“. Fehlerdetails: {1} Stellen Sie sicher, dass das Image ordnungsgemäß vorbereitet (generalisiert) wurde. <ul><li>Anleitungen für Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Fehler beim Generieren des SSH-Hostschlüssels. Fehlerdetails: {0}. Überprüfen Sie, ob der Linux-Agent ordnungsgemäß eingerichtet ist, um dieses Problem zu beheben. <ul><li>Lesen Sie die Anleitungen unter: https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  Der für die VM angegebene Benutzername ist für diese Linux-Distribution ungültig. Fehlerdetails: {0}.  |
|  OSProvisioningInternalError  |  Interner Fehler bei der Betriebssystembereitstellung für den virtuellen Computer „{0}“.  |
|  OSProvisioningTimedOut  |  Betriebssystembereitstellung für virtuellen Computer „{0}“ wurde nicht in der zugewiesenen Zeit abgeschlossen. Der virtuelle Computer kann dennoch erfolgreich bereitgestellt werden. Überprüfen Sie den Bereitstellungszustand später.  |
|  OSProvisioningTimedOut  |  Betriebssystembereitstellung für virtuellen Computer „{0}“ wurde nicht in der zugewiesenen Zeit abgeschlossen. Der virtuelle Computer kann dennoch erfolgreich bereitgestellt werden. Überprüfen Sie den Bereitstellungszustand später. Stellen Sie außerdem sicher, dass das Image ordnungsgemäß vorbereitet (generalisiert) wurde.   <ul><li>Anleitungen für Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Anleitungen für Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  Betriebssystembereitstellung für virtuellen Computer „{0}“ wurde nicht in der zugewiesenen Zeit abgeschlossen. Es wurde jedoch erkannt, dass der VM-Gast-Agent ausgeführt wird. Dies deutet darauf hin, dass das Gastbetriebssystem nicht ordnungsgemäß für die Verwendung als VM-Image vorbereitet wurde (mit CreateOption=FromImage). Um dieses Problem zu lösen, verwenden Sie die VHD unverändert mit CreateOption=Attach, oder bereiten Sie sie ordnungsgemäß für die Verwendung als Image vor:   <ul><li>Anleitungen für Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Anleitungen für Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  Die erforderliche VM-Größe ist aktuell am ausgewählten Speicherort nicht verfügbar.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Eine Ressourcenaktualisierung ist zurzeit nicht möglich, weil ein Plattformupdate ausgeführt wird. Versuchen Sie es später noch mal.  |
|  StorageAccountLimitation  |  Speicherkonto „{0}“ unterstützt keine Seitenblobs, die zum Erstellen von Datenträgern erforderlich sind.  |
|  StorageAccountLimitation  |  Das Speicherkonto „{0}“ hat das zugewiesene Kontingent überschritten.  |
|  StorageAccountLocationMismatch  |  Das Speicherkonto „{0}“ konnte nicht aufgelöst werden. Stellen Sie sicher, dass es durch den Speicherressourcenanbieter am gleichen Speicherort wie die Serverressource erstellt wurde.  |
|  StorageAccountNotFound  |  Das Speicherkonto „{0}“ wurde nicht gefunden. Stellen Sie sicher, dass das Speicherkonto nicht gelöscht wurde und zum gleichen Azure-Standort wie der virtuelle Computer gehört.  |
|  StorageAccountNotRecognized  |  Verwenden Sie ein Speicherkonto, das vom Speicherressourcenanbieter verwaltet wird. {0} wird nicht unterstützt.  |
|  StorageAccountOperationInternalError  |  Interner Fehler beim Zugriff auf das Speicherkonto „{0}“.  |
|  StorageAccountSubscriptionMismatch  |  Speicherkonto „{0}“ gehört nicht zum Abonnement „{1}“.  |
|  StorageAccountTooBusy  |  Speicherkonto „{0}“ ist derzeit beschäftigt. Verwenden Sie ein anderes Konto.  |
|  StorageAccountTypeNotSupported  |  Der Datenträger „{0}“ verwendet „{1}“, hierbei handelt es sich um ein Blobspeicherkonto. Wiederholen Sie den Vorgang mit einem allgemeinen Speicherkonto.  |
|  StorageAccountTypeNotSupported  |  Speicherkonto „{0}“ gehört zum Typ „{1}“. Die Startdiagnose unterstützt Speicherkonten vom Typ „{2}“.  <ul><li>Dieser Fehler tritt auf, wenn Sie das Premium-Speicherkonto für die Startdiagnose verwenden. Weitere Informationen finden Sie unter [Verwenden der Startdiagnose](boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  Das Abonnement ist nicht autorisiert.  |
|  TargetDiskBlobAlreadyExists  |  Blob „{0}“ ist bereits vorhanden. Geben Sie einen anderen Blob-URI an, um einen neuen leeren Datenträger „{1}“ zu erstellen.  |
|  TargetDiskBlobAlreadyExists  |  Erfassungsvorgang kann nicht fortgesetzt werden, da Zielimageblob „{0}“ bereits vorhanden ist und das Flag zum Überschreiben von VHD-Blobs nicht festgelegt wurde. Löschen Sie den Blob, oder legen Sie das Flag für das Überschreiben von VHD-Blobs fest, und versuchen Sie es erneut.  |
|  TargetDiskBlobAlreadyExists  |  Erfassungsvorgang kann nicht fortgesetzt werden, da das Zielimageblob „{0}“ über eine aktive Lease verfügt.   |
|  TargetDiskBlobAlreadyExists  |  Blob „{0}“ ist bereits vorhanden. Geben Sie einen anderen Blob-URI als Ziel für Datenträger „{1}“ an.  |
|  TooManyVMRedeploymentRequests  |  Für den virtuellen Computer „{0}“ oder für die VMs in der Verfügbarkeitsgruppe für diesen virtuellen Computer wurden zu viele Anforderungen zur erneuten Bereitstellung empfangen. Versuchen Sie es später noch mal.  |
|  VHDSizeInvalid  |  Der angegebene Wert für die Datenträgergröße {0} für Datenträger „{1}“ mit Blob {2} ist ungültig. Datenträgergröße muss zwischen {3} und {4} liegen.  |
|  VMAgentStatusCommunicationError  |  Der virtuelle Computer „{0}“ weist keinen gemeldeten Status für den VM-Agent oder VM-Erweiterungen auf. Überprüfen Sie, ob der virtuelle Computer über einen ausgeführten VM-Agent verfügt und ausgehende Verbindungen mit dem Azure-Speicher herstellen kann.  |
|  VMArtifactRepositoryInternalError  |  Bei der Kommunikation mit dem Artefaktrepository zum Abrufen der VM-Artefaktdetails ist ein Fehler aufgetreten.  |
|  VMArtifactRepositoryInternalError  |  Interner Fehler beim Abrufen der VM-Artefaktdaten aus dem Artefaktrepository.  |
|  VMExtensionHandlerNonTransientError  |  Handler „{0}“ hat einen Fehler für VM-Erweiterung „{1}“ mit Terminalfehlercode „{2}“ gemeldet. Fehlermeldung: „{3}“  |
|  VMExtensionManagementInternalError  |  Beim Verarbeiten der VM-Erweiterung „{0}“ ist ein interner Fehler aufgetreten.  |
|  VMExtensionManagementInternalError  |  Beim Vorbereiten der VM-Erweiterungen sind mehrere Fehler aufgetreten. Weitere Informationen finden Sie in der Instanzansicht der VM-Erweiterungen.  |
|  VMExtensionProvisioningError  |  Der virtuelle Computer hat beim Verarbeiten der Erweiterung „{0}“ einen Fehler gemeldet. Fehlermeldung: „{1}“.  |
|  VMExtensionProvisioningError  |  Mehrere VM-Erweiterungen konnten nicht auf dem virtuellen Computer bereitgestellt werden. Weitere Informationen finden Sie in der VM-Erweiterungsinstanz.  |
|  VMExtensionProvisioningTimeout  |  Zeitüberschreitung bei Bereitstellung der VM-Erweiterung „{0}“. Möglicherweise dauert die Erweiterungsinstallation zu lange, oder der Erweiterungsstatus konnte nicht abgerufen werden.  |
|  VMMarketplaceInvalidInput  |  Das Erstellen eines virtuellen Computers aus einem Marketplace-fremden Image erfordert keine Planinformationen. Entfernen Sie die Planinformationen in der Anforderung. Betriebssystem-Datenträgername lautet „{0}“.  |
|  VMMarketplaceInvalidInput  |  Die Kaufinformationen stimmen nicht überein. Bereitstellung aus dem Marketplaceimage nicht möglich. Betriebssystem-Datenträgername lautet „{0}“.  |
|  VMMarketplaceInvalidInput  |  Das Erstellen eines virtuellen Computers aus einem Marketplaceimage erfordert Planinformationen in der Anforderung. Betriebssystem-Datenträgername lautet „{0}“.  |
|  VMNotFound  |  Die VM „{0}“ wurde nicht gefunden.  |
|  VMRedeploymentFailed  |  Interner Fehler bei der erneuten Bereitstellung des virtuellen Computers „{0}“. Versuchen Sie es später noch mal.  |
|  VMRedeploymentTimedOut  |  Die erneute Bereitstellung des virtuellen Computers „{0}“ wurde nicht innerhalb der vorgesehenen Zeit abgeschlossen. Möglicherweise wird der Vorgang in Kürze erfolgreich abgeschlossen. Andernfalls starten Sie den Vorgang neu.  |
|  VMStartTimedOut  |  Der virtuelle Computer „{0}“ wurde nicht in der zugewiesenen Zeit gestartet. Der virtuelle Computer kann dennoch erfolgreich gestartet werden. Überprüfen Sie den Betriebszustand später.  |


## <a name="next-steps"></a>Nächste Schritte
Wenn Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie **Support erhalten**aus.
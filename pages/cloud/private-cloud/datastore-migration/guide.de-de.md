---
title: Datastore zwischen zwei Dedicated Cloud kopieren
slug: datastore-copy
excerpt: Hier erfahren Sie, wie Sie über die OVHcloud APIs von einem anderen Dedicated Cloud aus auf die Kopie eines Datastores eines Dedicated Cloud zugreifen.
section: OVHcloud Funktionen
hidden: true
---

> [!primary]
> Diese Übersetzung wurde durch unseren Partner SYSTRAN automatisch erstellt. In manchen Fällen können ungenaue Formulierungen verwendet worden sein, z.B. bei der Beschriftung von Schaltflächen oder technischen Details. Bitte ziehen Sie beim geringsten Zweifel die englische oder französische Fassung der Anleitung zu Rate. Möchten Sie mithelfen, diese Übersetzung zu verbessern? Dann nutzen Sie dazu bitte den Button «Mitmachen» auf dieser Seite.
>

**Stand 29.03.2021**

## Ziel

Im Anschluss an die Störung im Datacenter SBG können Sie die Datastores eines von der Störung betroffenen Dedicated Cloud auf ein Ziel-PCC migrieren.

**Hier erfahren Sie, wie Sie über die OVHcloud APIs einen Datastore eines Dedicated Cloud von einem anderen Dedicated Cloud aus kopieren.**

## Voraussetzungen

- Sie sind mit den [OVHcloud APIs verbunden](https://api.ovh.com/)
- Sie sind in Ihrem [vSphere Interface](../den_vsphere_client_installieren/) angemeldet.

> [!warning]
>
> Wenn Ihre Quell-PCC über ein [HDS](https://www.ovhcloud.com/de/enterprise/certification-conformity/hds/) oder [PCI-DSS](https://www.ovhcloud.com/de/enterprise/certification-conformity/pci-dss/) Zertifikat verfügt, muss Ihre Ziel-PCC über das gleiche aktive **Zertifikat verfügen,** um den Datastore abzurufen.
>
> Weitere Informationen finden Sie in unserer [Anleitung Aktivierung der Compliance Funktion Healthcare (HDS) oder Payment Services (PCI DSS)](../pci-dss-option-aktivieren/).
>

## In der praktischen Anwendung

Wenn Sie sich nicht an die Funktionsweise der OVHcloud APIs gewöhnt haben, lesen Sie unsere Anleitung [Erste Schritte mit den OVHcloud APIs](https://docs.ovh.com/gb/en/api/first-steps-with-ovh-api/).

### Schritt 1: Die FilerID des Datastores abrufen

Sie müssen zuerst die zu kopierende FilerId ins Visier nehmen.

Loggen Sie sich auf [https://api.ovh.com/](https://api.ovh.com/) ein und verwenden Sie folgenden Anruf:

> [!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/filer

Geben Sie die Variablen ein:

- serviceName: Name der ursprünglichen Dedicated Cloud in SBG (z. B.: pcc-192-0-2-15).
- datacenterId: die ID des Quellrechenzentrums (z. B.: 1337).

### Schritt 2: Datastore-Kopie starten

> [!warning]
>
> Das Ziel-Dedicated Cloud muss sich in einem anderen Bereich als sbg1a befinden.
>

Sobald Sie die FilerId identifiziert haben, verwenden Sie folgenden Aufruf, um den Datastore auf das Ziel-PCC zu kopieren:

> [!api]
>
> @api {POST} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/copyFiler

Geben Sie die Variablen ein:

- serviceName: Name der Ziel-KPC (z. B.: pcc-192-0-2-50).
- datacenterId: die ID des Zielrechenzentrums (z. B.: 1515).
- filerId: die im vorherigen Schritt abgerufene FilerId (z. B.: 001234).

Die Replikation der Daten kann mehrere Stunden dauern. Sobald die Kopie abgeschlossen ist, erhalten Sie eine E-Mail, in der der Erfolg der Kopie bestätigt wird.

### Schritt 3: Zugriff auf die Kopie über vSphere

Gehen Sie [in Ihrem vSphere](../den_vsphere_client_installieren/) Interface in die Ansicht `Storage`{.action}.

![ds_restore](images/ds-restore.png){.thumbnail}

Die Kopie wird über einen Datastore auf allen Hosts des Zieldatacenters unter dem Namen `restore-XXXXXX` (XXXXXX bezeichnet die Nummer des Quelldatastores) angezeigt.

> [!warning]
>
> Der wiedergewonnene Datastore ist nur leserlich.
>

Bei virtuellen Maschinen müssen diese [in der vSphereInventur gespeichert werden](../vsphere-register-vm-vmx/) und anschließend [zu einem](../eine-vm-klonen/) der Datastores geklont werden, bevor sie gestartet werden können.

## Weiterführende Informationen

Für den Austausch mit unserer User Community gehen Sie auf <https://community.ovh.com/en/>.

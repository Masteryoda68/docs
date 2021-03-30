---
title: Copia un datastore tra due PCC
slug: datastore-copy
excerpt: Scopri come accedere alla copia di un datastore di un PCC da un altro PCC tramite le API OVHcloud
section: Funzionalità OVHcloud
hidden: true
---

> [!primary]
> Questa traduzione è stata generata automaticamente dal nostro partner SYSTRAN. I contenuti potrebbero presentare imprecisioni, ad esempio la nomenclatura dei pulsanti o alcuni dettagli tecnici. In caso di dubbi consigliamo di fare riferimento alla versione inglese o francese della guida. Per aiutarci a migliorare questa traduzione, utilizza il pulsante "Modifica" di questa pagina.
>

**Ultimo aggiornamento: 29/03/2021**

## Obiettivo

In seguito all'incidente verificatosi nel datacenter SBG, è possibile migrare i datastore di un PCC interessato dall'incidente verso un PCC di destinazione.

**Come accedere alla copia di un datastore di un PCC da un altro PCC tramite le API OVHcloud**

## Prerequisiti

- Essere connesso alle [API OVHcloud](https://api.ovh.com/)
- Avere accesso all’[interfaccia vSphere](../connessione-interfaccia-vsphere/).

> [!warning]
>
> Se il tuo Dedicated Cloud sorgente dispone di una certificazione [HDS](https://www.ovhcloud.com/fr/enterprise/certification-conformity/hds/) o [PCI-DSS](https://www.ovhcloud.com/it/enterprise/certification-conformity/pci-dss/), il tuo Dedicated Cloud di destinazione dovrà disporre della stessa certificazione **attiva** per recuperare il datastore.
>
> Per maggiori informazioni, consulta la guida [Attivare il processo di conformità Healthcare (HDS) o Payment Services (PCI DSS)](../attivare-certificazione-pci-dss-private-cloud-ovh/).
>

## Procedura

Se non sei abituato al funzionamento delle API OVHcloud, consulta la nostra guida Iniziare [a utilizzare le API OVHcloud](https://docs.ovh.com/gb/en/api/first-steps-with-ovh-api/).

### Step 1: recuperare il filerID del datastore

Per prima cosa, devi indirizzare il filerId da copiare.

Accedi alla pagina [https://api.ovh.com/](https://api.ovh.com/) e utilizza questa chiamata:

> [!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/filer

Inserisci le variabili:

- serviceName: il nome del PCC originario di SBG (ad esempio: pcc-192-0-2-15).
- datacenterId: ID del datacenter sorgente (ad esempio: 1337).

### Step 2: avviare la copia del datastore

> [!warning]
>
> Il PCC di destinazione deve essere situato in una zona diversa da sbg1a.
>

Una volta identificato il filerId, utilizza questa chiamata per copiare il datastore sul PCC di destinazione:

> [!api]
>
> @api {POST} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/copyFiler

Inserisci le variabili:

- serviceName: il nome del PCC di destinazione (ad esempio: pcc-192-0-2-50).
- datacenterId: ID del datacenter di destinazione (ad esempio: 1515).
- filerId: filerId recuperato allo step precedente (es.: 001234).

La replica dei dati può richiedere diverse ore. Una volta completata l'operazione, riceverai un'email di conferma del completamento dell'operazione.

### Step 3: accedere alla copia da vSphere

Nella tua [interfaccia vSphere](../connessione-interfaccia-vsphere/), posizionati nella vista `Storage`{.action}.

![ds_restore](images/ds-restore.png){.thumbnail}

La copia viene presentata attraverso un datastore su tutti gli host del datacenter di destinazione, con il nome `restore-XXXXXX` (XXXXXX indica il numero del datastore sorgente).

> [!warning]
>
> Il datastore recuperato è in sola lettura.
>

Per le macchine virtuali, è necessario [registrarle nell'inventario vSphere](../vsphere-register-vm-vmx/) e poi [clonarle](../clonare-una-vm/) verso uno dei datastore prima di poterle avviare.

## Per saperne di più

Contatta la nostra Community di utenti all’indirizzo <https://community.ovh.com/en/>.

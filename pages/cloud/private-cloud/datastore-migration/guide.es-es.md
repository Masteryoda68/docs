---
title: Copiar un datastore entre dos PCC
slug: datastore-copy
excerpt: Cómo acceder a la copia de un datastore de un Private Cloud desde otro Private Cloud a través de las API de OVHcloud
section: Funcionalidades de OVHcloud
hidden: true
---

> [!primary]
> Esta traducción ha sido generada de forma automática por nuestro partner SYSTRAN. En algunos casos puede contener términos imprecisos, como en las etiquetas de los botones o los detalles técnicos. En caso de duda, le recomendamos que consulte la versión inglesa o francesa de la guía. Si quiere ayudarnos a mejorar esta traducción, por favor, utilice el botón «Contribuir» de esta página.
> 

**Última actualización: 29/03/2021**

## Objetivo

Tras la incidencia en el datacenter SBG, puede migrar los datastores de un PCC afectado por la incidencia a un PCC de destino.

**Cómo acceder a la copia de un datastore de un Private Cloud desde otro Private Cloud a través de las API de OVHcloud**

## Requisitos

- Estar conectado a la [API de OVHcloud.](https://api.ovh.com/)
- Estar conectado a la [interfaz vSphere](../instalar_el_vsphere_client/).

> [!warning]
>
> Si su Private Cloud de origen dispone de una certificación [HDS](https://www.ovhcloud.com/es-es/enterprise/certification-conformity/hds/) o [PCI-DSS](https://www.ovhcloud.com/es-es/enterprise/certification-conformity/pci-dss/), su Dedicated Cloud de destino deberá disponer de la misma certificación **activa** para recuperar el datastore.
>
> Para más información, consulte nuestra guía [Activación del procedimiento de conformidad Healthcare (HDS) o Payment Services (PCI DSS)](../activar-certificacion-pci-dss-en-private-cloud-ovh/).
>

## Procedimiento

Si no está familiarizado con el funcionamiento de las API de OVHcloud, consulte nuestra guía [Primeros pasos con las API de OVHcloud](https://docs.ovh.com/gb/en/api/first-steps-with-ovh-api/).

### 1. descargar el filerId del datastore

Primero debe apuntar al filerId que quiere copiar.

Conéctese a [https://api.ovh.com/](https://api.ovh.com/) y utilice la siguiente llamada:

> [!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/filer

Introduzca las variables:

- serviceName: el nombre del PCC original situado en SBG (p. ej.: pcc-192-0-2-15).
- datacenterId: ID del datacenter de origen (p. ej.: 1337).

### 2. ejecutar la copia del datastore

> [!warning]
>
> El PCC de destino debe estar situado en una de las siguientes zonas: RBX (Roubaix), LIM (Frankfurt) o ERI (Londres).
>

Una vez que haya identificado el filerId, utilice la siguiente llamada para copiar el datastore al Private Cloud de destino:

> [!api]
>
> @api {POST} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/copyFiler

Introduzca las variables:

- serviceName: el nombre del PCC de destino (p. ej.: pcc-192-0-2-50).
- datacenterId: ID del datacenter de destino (p. ej.: 1515).
- filerId : el filerId recuperado en el paso anterior (p. ej.: 001234).

La replicación de los datos puede tardar varias horas. Una vez completada, recibirá un mensaje de correo electrónico confirmando la correcta copia de la copia.

### 3. conocer el progreso de la copia

Para consultar el progreso de la copia de los datastores, realice la siguiente llamada:

> [!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/copyFilerStatus

Introduzca las variables:

- serviceName: el nombre del PCC de destino (p. ej.: pcc-192-0-2-50).
- datacenterId: ID del datacenter de destino (p. ej.: 1515).

Si se ha solicitado una copia, la API le devolverá todas las operaciones de copia, pendientes, en curso o terminadas (porcentaje de progresión, tamaño de los datos transferidos, estado de la tarea, etc.).

### 4. acceder a la copia desde vSphere

En su [interfaz vSphere](../instalar_el_vsphere_client/), sitúese en la vista `Almacenamiento`{.action}.

![ds_restore](images/ds-restore.png){.thumbnail}

La copia se presenta a través de un datastore en todos los hosts del datacenter de destino, con el nombre `restore-XXXXXX` (XXXX designando el número del datastore de origen).

> [!warning]
>
> El datastore recuperado es de solo lectura.
>

Para las máquinas virtuales, es necesario [registrarlas en el inventario vSphere](../vsphere-register-vm-vmx/) y [clonar](../clonar-una-mv/) estas máquinas hacia uno de los datastores antes de poder iniciarlas.

## Más información

Interactúe con nuestra comunidad de usuarios en <https://community.ovh.com/en/>.

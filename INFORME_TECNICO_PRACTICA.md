# Informe técnico de la práctica

## 1. Objetivo

Diseñar y demostrar en GNS3 una red de laboratorio con un router Cisco 7200, un switch Cisco vIOS L2, un FortiGate 7.0.9 y una máquina Kali Linux. Desde Kali se comprueba la conectividad y se ejecuta una auditoría automatizada sobre un subconjunto de recomendaciones CIS seleccionadas. La práctica muestra auditoría PRE, sugerencias, Apply interactivo y comprobación POST.

El auditor no certifica la conformidad CIS integral. El alcance y los controles se documentan en [`../controles/alcance_cis.md`](../controles/alcance_cis.md).

## 2. Arquitectura del laboratorio

La red de administración es `10.20.98.0/24`: Kali usa `10.20.98.10/24`, FortiGate port3 usa `10.20.98.1/24` y SW1 VLAN 1 usa `10.20.98.2/24`. R1 está conectado por la red de tránsito `10.20.79.0/30`: R1 `10.20.79.1/30` y FortiGate port2 `10.20.79.2/30`. FortiGate port1 conserva el enlace DHCP externo.

El diagrama limpio está en [`../diagramas/topologia_tecnica.svg`](../diagramas/topologia_tecnica.svg); las capturas están en [`../imagenes/`](../imagenes/). El direccionamiento técnico se detalla en [`TOPOLOGIA_TECNICA.md`](TOPOLOGIA_TECNICA.md).

## 3. Preparación y pruebas

1. Se confirmó la dirección de Kali y la ruta hacia la LAN desde `ip -br addr` e `ip route`.
2. Se probaron los destinos `10.20.98.1`, `10.20.98.2` y `10.20.79.1` con cuatro solicitudes ICMP. La captura muestra cuatro respuestas por destino.
3. Se habilitó/validó administración SSH con las cuentas existentes en los dispositivos. El auditor solicita las credenciales en cada ejecución; no las almacena en el código.
4. Se ejecutaron comandos de solo lectura para reconocer versión/estado y recoger evidencia. Las claves host se validan mediante `known_hosts`.

La evidencia visual está indexada en [`EVIDENCIAS.md`](EVIDENCIAS.md). No se incluyen direcciones DHCP externas ni respaldos completos.

## 4. Flujo del auditor

### Audit Only

`--mode audit --phase PRE` conecta por SSH, ejecuta comandos de lectura, evalúa los controles y crea JSON, CSV, HTML y evidencia.

### Plan

`--mode plan --phase PRE` repite una auditoría actual y genera sugerencias para los controles fallidos. No envía configuración.

### Apply

`--mode apply` comienza con un nuevo PRE, prepara cambios únicamente para reglas con acción implementada, crea respaldos privados, pide confirmación `APPLY`, configura el running-config y realiza POST. El programa no ejecuta `write memory` y no modifica FortiGate port1. Si un control modificado no pasa POST, intenta su reversión.

El alcance es deliberadamente parcial. Por ejemplo, DNS/NTP requieren datos de red, los servicios de administración pueden cortar acceso y sustituir `ALL` requiere analizar el flujo de cada política; quedan para revisión manual.

## 5. Resultados observados

- En una ejecución inicial de auditoría se registraron `PASS=17`, `FAIL=8`, `NOT_EVALUATED=1`, `ERROR=0`.
- La captura posterior muestra un plan para dos controles Cisco por dispositivo (enable secret y MOTD), además del pre-login banner y hostname del FortiGate. La ejecución reportó `PASS=23`, `FAIL=3`, `NOT_EVALUATED=0`, `ERROR=0`.
- El informe visible de SW1 todavía mostraba el servidor HTTP habilitado como `FAIL` (`1.2.2.5`).
- El conteo es puntual de una corrida. Para conocer qué otros controles quedaron pendientes, se debe consultar el `report.html`/`report.json` local de esa misma ejecución.

La captura Apply incluida es un recorte de la salida compartida. Los informes completos y respaldos no se incluyeron porque no se recibieron como archivos y pueden contener detalles de configuración sensibles.

## 6. Referencias y límites de versión

- Para IOS 15.2 y vIOS L2 se usó el CIS Cisco IOS Benchmark v2.2 como referencia cercana; es una edición archivada y genérica, no específica de las imágenes usadas.
- Para FortiOS 7.0.9 se usó FortiGate 7.0.x v1.2.0 como la edición cercana; Fortinet indica que fue probada con 7.0.10.
- Se evalúa únicamente la lista de controles de [`../controles/alcance_cis.md`](../controles/alcance_cis.md). Un resultado `PASS` indica que pasó la condición local implementada, no que el equipo esté certificado.

## 7. Reproducción de la práctica

Desde la raíz del repositorio en Kali:

```bash
./venv/bin/python scripts/auditor.py --mode audit --phase PRE
./venv/bin/python scripts/auditor.py --mode plan --phase PRE
./venv/bin/python scripts/auditor.py --mode apply
```

Las tres ejecuciones producen directorios separados bajo `reports/`. En Apply, revisa el plan antes de escribir `APPLY`. Para abrir los resultados, usa la ruta impresa por la terminal:

```bash
xdg-open reports/<FECHA_HORA>/report.html
```

Para configuración, versiones, direcciones y rutas, consulta [`TOPOLOGIA_TECNICA.md`](TOPOLOGIA_TECNICA.md). Para los comandos completos y el manejo de credenciales, consulta [`USO_EN_KALI.md`](USO_EN_KALI.md).

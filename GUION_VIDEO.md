# Guion para video (máximo 10 minutos)

## Preparación

- Inicia la grabación mostrando fecha y hora, rostro y voz, si lo solicita la práctica.
- Ten abiertos GNS3, una terminal de Kali y el README.
- No muestres contraseñas, archivos de credenciales, respaldos ni listados de archivos personales.

## Secuencia recomendada

| Tiempo | Qué explicar/mostrar |
|---|---|
| 0:00–0:40 | Preséntate, indica fecha/hora y explica que el objetivo es auditar controles seleccionados, revisar sugerencias y mostrar PRE/POST. |
| 0:40–2:00 | Muestra la topología GNS3 y nombra R1, SW1, FW1 y Kali. Señala port1 externo preservado, port2 a R1 y port3 a la LAN de gestión. |
| 2:00–3:00 | En Kali muestra `ip -br addr` e `ip route`; corre los tres `ping -c 4` descritos en la documentación técnica. |
| 3:00–4:15 | Ejecuta `./venv/bin/python scripts/auditor.py --mode audit --phase PRE`; explica conectividad y conteo de controles. |
| 4:15–5:15 | Ejecuta `./venv/bin/python scripts/auditor.py --mode plan --phase PRE`; muestra sugerencias y aclara que no cambia configuración. |
| 5:15–7:30 | Si la demostración requiere Apply, ejecuta `./venv/bin/python scripts/auditor.py --mode apply`, revisa el plan y confirma `APPLY` solo si aceptas los cambios. |
| 7:30–9:30 | Abre el HTML impreso por la terminal. Muestra PRE, POST, los controles que pasaron y los que siguen pendientes/manuales. |
| 9:30–10:00 | Resume limitaciones: controles seleccionados, referencia Cisco archivada y FortiOS 7.0.9 cercano a la prueba 7.0.10. |

Si no quieres modificar la topología durante el video, muestra Audit y Plan; no confirmes Apply. Evita afirmar cumplimiento CIS completo: el proyecto mide solo el subconjunto documentado en [controles/alcance_cis.md](../controles/alcance_cis.md).

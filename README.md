# multirotor-sw

**On-board and ground software for UCA&Air's multirotor UAV (Student AirRace, Munich).**

Software del multirrotor de UCA&Air, el club aeroespacial de la Universidad de Cádiz,
para la Student AirRace que organiza EUROAVIA München. Aquí está todo lo que se ejecuta
en el aire y en el puesto de tierra; la controladora de vuelo usa firmware externo y no
se desarrolla en este repositorio.

## Qué resuelve

La prueba de esta edición, el Zero Delta Challenge, pide que el multirrotor
**aterrice solo sobre un marcador impreso en el suelo**, con tres condiciones que marcan
todo el diseño del software:

- Todo el procesamiento ocurre **a bordo**: está prohibido enviar el vídeo a tierra para calcularlo allí.
- **Sin GNSS en el lazo de guiado** durante el aterrizaje, ni siquiera dentro de los filtros.
- La cámara mira hacia abajo y hay que **entregar el fotograma del momento del contacto** como prueba.

## Estructura

```
onboard/   Lo que corre en el ordenador de a bordo: captura, detección del marcador,
           estimación de posición y envío de consignas a la controladora.
ground/    Puesto de tierra: telemetría, vigilancia de límites, registro de la misión
           y pantalla del operador.
tools/     Utilidades: calibración de cámara, generación de marcadores, análisis de
           vuelos y bancos de prueba.
docs/      Notas técnicas que acompañan al código y decisiones de diseño.
```

## Hardware de referencia

Según la lista de componentes vigente del proyecto:

| Elemento | Qué hace |
|---|---|
| Ordenador de a bordo | Detección del marcador y cálculo de la consigna de aterrizaje |
| Cámara de obturador global, mirando abajo | Imagen del marcador sin deformación por movimiento |
| Telémetro láser | Altura real sobre el suelo |
| Sensor de flujo óptico | Desplazamiento horizontal sin satélites |
| Controladora de vuelo | Estabilización y ejecución del vuelo (firmware externo) |

La controladora concreta está **por definir**: hace falta una con al menos seis puertos
serie libres. Hasta que se cierre, el código de `onboard/` no debe asumir un modelo
concreto más allá del protocolo de enlace.

## Dos sistemas con autoridad distinta

Es la decisión de diseño más importante del proyecto y conviene no perderla de vista
al escribir código:

- El **cálculo geométrico** localiza el marcador y obtiene distancia y ángulo por
  trigonometría. Es el único que genera consignas de vuelo.
- La **red neuronal** amplía el alcance cuando el marcador está lejos, torcido o con
  reflejo, pero su salida es una propuesta con un valor de confianza, y **no tiene
  autoridad sobre el vuelo**.

Si la red falla o tarda, el sistema sigue con la detección geométrica sola: se pierde
alcance de detección, no capacidad de aterrizar.

## Cómo empezar

```bash
git clone git@github.com:UCA-Air/multirotor-sw.git
cd multirotor-sw
```

Las instrucciones de entorno y dependencias se añadirán aquí en cuanto esté cerrada la
primera versión de `onboard/`. Mientras tanto, cualquier dependencia nueva se anota en
la pull request que la introduce.

## Cómo trabajamos

Ramas, commits, revisiones y documentación: **[CONTRIBUTING.md](CONTRIBUTING.md)**.

En corto: `main` está protegida, todo entra por pull request, y el encargado del
proyecto revisa.

## Equipo

- Responsables: Fran e Ismael
- Apoyo y coordinación del área: Javi

## Licencia

MIT — ver [LICENSE](LICENSE). Úsalo, modifícalo y compártelo citando la autoría.

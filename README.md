# Análisis de estados financieros con Python

Soy ingeniero financiero y hice este notebook para dejar de armar a mano en Excel los estados financieros y los indicadores cada vez que quiero mirar una empresa. Ahora le doy un ticker y en un par de minutos tengo los tres estados ordenados como un informe NIIF, los indicadores, una lectura de cómo está la empresa y un dashboard.

Los datos salen de Yahoo Finance con `yfinance`. Lo he usado sobre todo con acciones de la BVC, como Grupo Cibest y Nu.

![Dashboard](dashboard_NUCO_cl.png)

## Cómo correrlo

```bash
pip install -r requirements.txt
```

Abre `analisis_estados_financieros.ipynb`, cambia el ticker en la primera celda y corre todo:

```python
ticker = "CIBEST.cl"   # BVC con .cl; para EE. UU. solo el símbolo: "AAPL", "NU"
año_inicio = 2022
año_fin = 2025
```

El dashboard queda guardado como `dashboard_<ticker>.png`.

## Qué hace

Primero baja el estado de resultados, el balance y el flujo de caja, y llena las cuentas que Yahoo deja vacías usando identidades contables (activo = pasivo + patrimonio, utilidad bruta = ingresos − costo, etc.). Siempre deja registro de qué datos calculó yo y cuáles venían de Yahoo.

Después calcula lo que normalmente miraría en un análisis:

- análisis horizontal y vertical;
- liquidez y endeudamiento;
- márgenes, ROE y ROA sobre saldos promedio, y DuPont;
- ciclo de efectivo y flujo de caja libre;
- múltiplos de mercado.

Con eso arma una interpretación por reglas, con alertas y lo que yo revisaría en cada caso. Los umbrales que usé son referencias generales, no verdades: siempre hay que comparar con empresas del mismo sector.

## Lo que aprendí haciéndolo

**Yahoo no siempre cuadra.** Al correrlo con Grupo Cibest me salían avisos de que nada cuadraba, y me puse a revisar uno por uno. Ninguno era un error del código:

- La utilidad del estado de resultados es la de la controladora y la del flujo de caja incluye a los minoritarios.
- El banco define el efectivo distinto en el balance y en el flujo.
- En 2025 la utilidad cayó por la venta de Banistmo, que quedó como operación discontinuada.

Por eso el notebook ahora separa los **problemas** (diferencias sin explicación, que hay que ir a buscar al informe oficial) de las **notas** (diferencias que ya tienen explicación). Los cuadres se hacen siempre con los datos originales de Yahoo, no con los que completé, para que un dato calculado no "cuadre" por construcción.

**Un banco no tiene ventas.** Con Nu me di cuenta de que analizar un banco como si fuera una empresa industrial no tiene sentido: el margen bruto, el EBITDA o la razón corriente no dicen nada. Si el notebook detecta una financiera, cambia el estado de resultados a este formato:

```
  Ingresos por intereses
- Gastos por intereses
= Margen neto de intereses
+ Comisiones y otros ingresos
= Ingresos operacionales netos     (lo que en un banco hace de "ventas")
- Provisiones de cartera
- Gastos operacionales
= Utilidad antes de impuestos
```

En ese caso calcula el NIM, la eficiencia (gastos / ingresos) y cuánto de los ingresos se va en provisiones.

**Ojo con los dobles listados.** Nu cotiza en la BVC en pesos pero reporta en dólares, y Yahoo me daba un P/B de más de 13.000 veces porque dividía pesos entre dólares. Ahora el notebook convierte la capitalización con el tipo de cambio. Igual, el precio de `NUCO.cl` se mueve poco, así que para los múltiplos prefiero usar `NU`, el ticker de Nueva York.

## Limitaciones

El NIM lo calculo sobre el activo total y la solidez de un banco la aproximo con patrimonio / activo, así que no reemplazan los indicadores regulatorios. Si hubo un efecto extraordinario, como una venta o un deterioro, el notebook lo señala pero no lo ajusta.

Es un proyecto de estudio: nada de lo que imprime es una recomendación de inversión.

## Autor

Juan — Ingeniero Financiero, Universidad de Medellín.

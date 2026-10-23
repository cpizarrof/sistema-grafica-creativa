# Sistema de ventas y gestión de Gráfica Creativa

Gráfica Creativa SpA es una imprenta familiar de Santiago. Más de ocho de cada diez de sus ventas llegan por
Compra Ágil, en Mercado Público, donde cada día hábil se publican más de veinte mil compras. Desde septiembre de
2026 armé el sistema con que la empresa trabaja hoy: un radar que encuentra las compras de imprenta, un motor que
prepara las ofertas y llena los anexos, y una página donde la familia lleva los trabajos, las facturas y los números.

El código y los datos son de la empresa, así que no están aquí. Este repositorio muestra qué hace el sistema, cómo
está armado y algunos problemas que hubo que resolver. Las compras del Radar son reales y públicas; las cifras de
la página de trabajos, el tablero y el cotizador son de ejemplo.

![El Radar en el computador y en el celular](imagenes/01_portada.webp)

| En números | |
|---|---|
| Revisión de Mercado Público | 4 pasadas al día, con un tope de 6.000 consultas diarias a la API |
| Oportunidades abiertas en el Radar | unas 550 en un día normal, entre Compras Ágiles y licitaciones |
| Pruebas del llenado de anexos | 126 compras reales y 638 comprobaciones, más un banco de 2.600 Compras Ágiles |
| Personas que lo usan a diario | el equipo de ventas (3) y la familia |
| Código | casi 70 mil líneas de Python y JavaScript, y 24 mil de pruebas |

## El Radar

La API de Mercado Público no filtra por rubro, así que el Radar lee todo lo que se publica y se queda con lo que es
de imprenta: primero por el nombre de la compra y, cuando el nombre no basta, revisando sus productos uno por uno.
A cada compra le pone una nota A, B o C según qué tan probable es ganarla. La nota sale de una regresión logística
entrenada con las cotizaciones que la empresa ya hizo (más de mil desde 2025), con el tipo de producto, el
comprador, el presupuesto y la época del año. Probada en meses que el modelo no vio, las compras con nota A se
ganaron cerca de cuatro veces más seguido que las con C.

Cada vendedor ve las compras, las toma para que nadie trabaje dos veces en la misma y pide la oferta desde ahí.
Funciona igual en el computador y en el celular, con modo claro y oscuro.

![El Radar en modo oscuro](imagenes/radar_oscuro.webp)

![Mis ofertas: lo tomado y lo preparado por cada vendedor](imagenes/radar_mis_ofertas.webp)

![El Radar en el celular: la lista y el detalle de una compra](imagenes/anim_celular.webp)

## Preparar una oferta

El vendedor escribe los precios (o usa el recomendado) y aprieta «Preparar oferta». El computador de la oficina
toma el pedido en menos de dos minutos y hace el resto:

1. Baja los documentos de la compra.
2. Claude lee las bases y dice qué piden, con la página donde está escrito cada requisito. Los requisitos
   obligatorios se confirman con un botón antes de subir.
3. Llena cada anexo del comprador: Word, Excel o PDF, con formatos que cambian en cada institución. Los datos salen
   siempre de la planilla de la empresa; la IA lee y revisa, pero no escribe datos.
4. Pone la firma autorizada solo donde corresponde y deja en blanco lo que firma otra persona.
5. Arma la cotización, la propuesta con fotos y la experiencia con las órdenes de compra que la respaldan.
6. Deja una lista corta de lo que hay que mirar antes de subir, con un semáforo.

![Preparar oferta: los precios recomendados se llenan y el total se actualiza](imagenes/anim_preparar.webp)

![Lo que pide la compra, leído de las bases](imagenes/radar_que_piden.webp)

![La lista para revisar antes de subir, marcándose](imagenes/anim_revisar.webp)

Lo que sale, listo para subir (cuenta bancaria, RUT personal y timbre tapados):

![Propuesta, experiencia, cotización y una declaración llenada](imagenes/documentos.webp)

## Precios y resultados

«Precio sugerido» combina un modelo ridge entrenado con unas 148 mil líneas de órdenes de compra de imprenta de todo
el mercado con las ventas parecidas de la propia empresa. A los vendedores les muestra solo lo que sirve para
cotizar: el precio recomendado y lo que la empresa cobró antes por algo parecido.

![Precio sugerido para carpetas, con las 27 ventas anteriores](imagenes/radar_precio_sugerido.webp)

«Resultados» cruza cada oferta con los datos abiertos de ChileCompra: si se ganó y, si no, a quién se adjudicó y por
cuánto.

![Resultados de las ofertas](imagenes/radar_resultados.webp)

## La página de trabajos

Cada trabajo se lleva en una orden de trabajo que replica la planilla Excel que la empresa ya usaba, con las mismas
fórmulas: venta, costos, factura, factoring y el reparto de la utilidad. Se guarda sola, guarda el historial de cada
cambio y se puede bajar como Excel. Las facturas, las cesiones al factoring y los impuestos llegan solos desde el
SII dos veces al día, así que la lista de facturas y lo que está en producción quedan al día sin que nadie los
anote.

*Cifras de ejemplo.*

![La lista de trabajos](imagenes/trabajos_lista.webp)

![Una orden de trabajo](imagenes/trabajos_orden.webp)

![Facturas: cobradas, vendidas al factoring y por cobrar](imagenes/trabajos_facturas.webp)

![Lo que está en producción y todavía no se factura](imagenes/trabajos_produccion.webp)

## El tablero

Lee los mismos datos y muestra la empresa completa: venta, margen, caja, cobranza, impuestos, banco y dónde se
vende. Una misma venta puede aparecer en la planilla, en el registro del SII, en la cartola del banco y en un Excel
bajado del propio tablero; cada fuente se cruza con las demás para no contar nada dos veces.

*Cifras de ejemplo.*

![Resumen del negocio](imagenes/tablero_resumen.webp)

![Caja: cuánto se puede gastar sin quedar corto](imagenes/tablero_caja.webp)

![Algunos gráficos del tablero](imagenes/tablero_graficos.webp)

![Venta por región](imagenes/tablero_mapa.webp)

## El cotizador

Calcula cuántas piezas caben en el pliego, cuántas planchas y tiros hacen falta según la máquina, las terminaciones
y el despacho, y arma el precio línea por línea. Cada línea se puede corregir a mano, y el cotizador avisa cuando
algo depende del trabajo y hay que confirmarlo.

*Tarifas de ejemplo.*

![Ficha de un díptico](imagenes/cotizador_ficha.webp)

![El precio detalle por detalle y la medida](imagenes/cotizador_detalle.webp)

## Cómo está armado

![Diagrama de la arquitectura](imagenes/arquitectura.webp)

- **Computador de la oficina:** Python con tareas programadas de Windows, de 8:50 a 23:00. Los anexos se llenan con
  Word, Excel y PowerPoint reales (pywin32), y también con python-docx, pypdf, pdfplumber y reportlab. LibreOffice
  convierte los `.doc` antiguos que Word no abre.
- **Datos:** SQLite con las compras, las ofertas y los precios de Mercado Público desde 2025, y los datos abiertos
  de ChileCompra (un archivo por mes con todas las ofertas de cada Compra Ágil).
- **Páginas:** HTML y JavaScript sin frameworks. Cloudflare Pages y Functions, con D1 para las ofertas y las
  planillas y KV para lo transitorio. Solo entran los correos autorizados (Cloudflare Access).
- **IA:** la API de Claude, con Haiku para lo corto y Sonnet para las bases largas, con un tope de gasto mensual y
  una caché para no pagar dos veces por el mismo documento.
- **Claves:** la del certificado del SII, la de la empresa y los tokens viven en el Administrador de credenciales de
  Windows; ninguna está en el código.

## Algunos problemas que hubo que resolver

**La hora que no era.** La API de Compra Ágil lee las fechas de búsqueda como hora de Chile aunque traigan zona
horaria. El Radar las mandaba en UTC, así que cada búsqueda quedaba corrida tres horas y se perdían las compras
publicadas justo después de cada pasada. Apareció al comparar lo que traía la API con la ficha pública de cada
compra; desde entonces las fechas van sin zona.

**Un filtro que la API no tiene.** El portal filtra por los rubros de la empresa, pero la API no. Buscando solo por
el nombre, el Radar veía el 21 % de las compras del rubro. Ahora las dudosas se revisan producto por producto con
una regla estricta, y así ve el 99,5 % del rubro, incluidas todas las que la empresa había cotizado en el período
medido, sin pasar de la cuota diaria de consultas.

**La firma.** Word deformaba la imagen de la firma en documentos antiguos convertidos, y a veces una marca interna
quedaba impresa en el PDF. La firma ahora se pone sobre el PDF final, en un lugar marcado con anclas invisibles en
el Word, y es todo o nada: si algo falla, el PDF queda sin firma y la lista de revisión dice dónde firmar a mano.
Corriendo los dos métodos en paralelo con compras reales, el nuevo no puso ninguna firma fuera de lugar; con el
anterior, seis de once firmas quedaban mal.

**Gratis no es ilimitado.** El plan gratuito de Cloudflare permite mil listados al día en su almacén clave-valor, y
la página los gastaba consultando cada pocos segundos. Ahora solo el computador de la oficina escribe un índice en
D1 cuando algo cambia, y la página lee ese índice en vez de listar.

**Un formulario distinto en cada compra.** Cada institución arma sus anexos a su manera: tablas, rayas, casillas,
cuadros de texto, Excel con fórmulas, PDF sin campos. Cada error encontrado se vuelve un caso de prueba con el
formulario original, y antes de instalar cualquier cambio tienen que pasar todos los anteriores.

## Cómo se prueba

- **Banco de pruebas:** 126 compras reales con sus formularios originales y 638 comprobaciones sobre lo que debe
  quedar escrito en cada uno.
- **Aislamiento:** las pruebas corren el mismo procedimiento que producción, pero con rutas desviadas, una firma de
  prueba y sin red. Un *audit hook* de Python detiene la prueba si intenta escribir fuera de su carpeta.
- **Revisión con IA a escala:** un modelo revisa por lotes los anexos llenados de un banco de 2.600 Compras Ágiles,
  una segunda pasada separa los errores reales de las falsas alarmas, y cada error real pasa al banco de pruebas.
- **Páginas:** pruebas con Playwright para cada función (tomar una compra, preparar una oferta, el modo oscuro, el
  celular), y pruebas de la API con una base SQLite en memoria.

---

Carlos Pizarro Fuentes · Ingeniería Estadística, Universidad de Santiago de Chile

[Currículum](https://cpizarrof.github.io/cv/) · [Portafolio](https://cpizarrof.github.io/Portafolio/) · [LinkedIn](https://www.linkedin.com/in/carlos-pizarro-fuentes/)

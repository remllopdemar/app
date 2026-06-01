Tecnologías utilizadas
HTML, CSS y JavaScript vanilla.
Supabase como almacenamiento compartido.
LocalStorage como caché local.
Open-Meteo para previsión meteorológica.
Open-Meteo Marine API para previsión de mar y oleaje.
GitHub Pages para el despliegue.
Roles de usuario

La app contempla tres tipos principales de usuario:

Patró

Puede gestionar la app de forma global:

aprobar o rechazar nuevos socios;
resetear claves de acceso;
marcar socios como cap de grup;
crear, editar o eliminar avisos;
crear eventos del calendario;
gestionar sortides;
abrir manualmente sortides cuando proceda;
añadir socios manualmente a una sortida.
Cap de grup

Tiene permisos sobre su propio grupo:

puede ver y gestionar las sortides de su grupo;
puede abrir manualmente una sortida de su grupo si hay plazas y se respeta la llista d’espera;
puede ver registros relacionados con su grupo.
Soci

Puede:

apuntarse a sortides;
desapuntarse;
entrar en llista d’espera;
participar en enquestes;
consultar avisos;
consultar la previsión meteo y el calendario del club.
Seguridad básica

La app es 100% client-side. No sustituye una autenticación completa de servidor, pero evita guardar claves en texto plano.

Las claves personales de socios se guardan con hash SHA-256.
La clave del patró también se valida mediante hash.
La sesión se guarda localmente en localStorage.
La app usa una anon key de Supabase, válida para uso cliente.
Nunca debe incluirse una service_role key en el HTML.
Datos en Supabase

Los datos se guardan en una tabla app_data, usando claves con prefijo ldm:*.

Claves principales:

ldm:membres
ldm:groups
ldm:avisos
ldm:outings
ldm:sessions
ldm:results
ldm:polls
ldm:events

La app mantiene una copia local en localStorage para mejorar la velocidad y sincroniza los cambios con Supabase.

Funcionamiento de las sortides
Ciclo semanal

La app funciona con un ciclo semanal.

Las sortides de la semana siguiente se generan a partir del:

sábado a las 08:00

En ese momento se crean las sortides de:

lunes;
miércoles;
viernes.

Si nadie abre la app el sábado a las 08:00, las sortides se crearán cuando alguien abra la app después, pero siguiendo la lógica de la semana activa.

La generación automática evita duplicados: si una sortida ya existe, no se vuelve a crear.

Lunes y miércoles: sortides por grupo

Las sortides de lunes y miércoles son salidas de grupo.

Cada grupo tiene su horario habitual:

Grup F · 08:00
Grup E Matins · 09:15
Grup D · 16:30
Grup C · 17:45
Grup B · 19:00
Grup A · 20:15

Desde que se generan, quedan reservadas al grupo correspondiente.

Ejemplo:

Sortida Grup B · dilluns 19:00

Desde el sábado a las 08:00 pueden apuntarse los socios del Grup B.

Apertura al resto de socios

Las sortides de lunes y miércoles se abren al resto de socios siguiendo la hora natural de la salida.

Tipo de sortida	Apertura general
Mañana, antes de las 10:00	Día anterior a las 20:00
Tarde / noche	El mismo día a las 07:30

Ejemplos:

Sortida	Apertura general
Grup F · 08:00	Día anterior a las 20:00
Grup E · 09:15	Día anterior a las 20:00
Grup D · 16:30	Mismo día a las 07:30
Grup C · 17:45	Mismo día a las 07:30
Grup B · 19:00	Mismo día a las 07:30
Grup A · 20:15	Mismo día a las 07:30

Pero una sortida no se abre automáticamente solo porque llegue la hora.

Para abrirse a todos deben cumplirse tres condiciones:

ha llegado la hora de apertura;
hay plazas libres;
se ha respetado la llista d’espera del grupo.
Regla de plazas

Una sortida solo se abre al general si quedan plazas libres.

Ejemplo con una sortida de 8 plazas:

Apuntados	Estado
6	Puede abrirse a todos si ya toca
7	Puede abrirse a todos si ya toca
8	No se abre a todos
8 + espera	Sigue llena, con llista d’espera

Si alguien se da de baja después de la hora de apertura, la app recalcula automáticamente el estado.

Llista d’espera en lunes y miércoles

La llista d’espera funciona antes y después de la apertura general.

Antes de abrir al general

Solo pueden apuntarse socios del grupo propietario.

Si la sortida se llena, los siguientes socios de ese grupo entran en llista d’espera.

Ejemplo:

Sortida Grup B, 8 plazas.
Hay 8 socios del Grup B apuntados.
Otro socio del Grup B intenta apuntarse.
Entra en llista d’espera.

Esa espera tiene prioridad antes de abrir la sortida al resto de socios.

En el momento de abrir al general

Antes de abrir la sortida a todos, la app revisa la llista d’espera del grupo.

Si hay plaza libre y hay alguien del grupo esperando, primero sube esa persona.

Solo si después sigue habiendo plazas libres, la sortida se abre a todos los socios.

Después de abrir al general

Si la sortida ya está abierta a todos y se llena, cualquier socio puede entrar en llista d’espera.

Si alguien se da de baja:

primero se promociona a quien tenga prioridad del grupo;
después se promociona al resto por orden de entrada;
si no hay nadie en espera y queda plaza, la sortida queda abierta a todos.
Botón manual “Obrir”

El botón manual para abrir una sortida se mantiene.

Lo pueden usar:

el patró;
el cap de grup de esa sortida.

Pero respeta las mismas reglas:

no puede abrir si no hay plazas;
no puede saltarse la llista d’espera del grupo;
si hay alguien del grupo esperando, primero debe subir esa persona;
si después sigue habiendo plaza, entonces puede abrirse a todos.

El botón manual sirve para adelantar una apertura cuando tiene sentido, no para saltarse las reglas de plazas o espera.

Viernes: sortides generales

Los viernes son distintos.

No son sortides por grupo. Son sortides generales abiertas a todos los grupos.

Cada viernes se generan dos salidas:

Hora	Tipo	Vogadors	Timonels
17:30	General	12	hasta 4
18:45	General	12	hasta 4

Las sortides de viernes se generan dentro del mismo ciclo semanal, a partir del sábado a las 08:00.

Quedan programadas y se abren el:

lunes a las 07:30

Desde ese momento, cualquier socio puede apuntarse.

Llista d’espera en viernes

Como las sortides de viernes no pertenecen a ningún grupo, no hay prioridad de grupo.

Funcionan por orden normal:

se llenan las 12 plazas de vogadors;
los siguientes socios entran en llista d’espera;
si alguien se borra, sube el primero de la lista;
si no hay lista de espera y queda plaza, la sortida vuelve a estar abierta.

Los timonels van aparte, hasta un máximo de 4.

Cuándo una sortida pasa a ser “passada”

Una sortida se considera pasada 15 minutos después de su hora de inicio.

Ejemplos:

Sortida	Hora inicio	Pasa a “passada”
Grup F	08:00	08:15
Grup E	09:15	09:30
Divendres	17:30	17:45
Divendres	18:45	19:00
Grup A	20:15	20:30

A partir de ese momento:

ya no se puede apuntar nadie;
ya no se puede entrar en llista d’espera;
ya no se puede abrir a todos;
ya no se promociona gente de la llista d’espera;
se muestra como sortida passada;
pasa a formar parte del historial.
Pantalla Inici

La pantalla de inicio está organizada así:

Avisos
Previsió meteo
Sortides
Enquestes
Calendari del club
Avisos

Los avisos pueden ser:

generales;
de social;
de competició;
personales;
registros automáticos de actividad.

Los socios ven los avisos que les corresponden según sus secciones.

Previsió meteo

La previsión meteo de Inici es compacta.

Muestra solo los días relevantes de salida:

Dl · Dc · Dv

Al clicar un día, se muestra la previsión diaria por horas, de forma resumida.

Franjas horarias orientativas:

08h
11h
14h
17h
20h

Cada línea incluye:

temperatura;
viento en nudos;
dirección del viento;
lluvia si la hay;
altura de ola;
dirección de ola;
periodo de ola.

Ejemplo:

17h · 16° · Vent E 8 kn · Ona 0,4 m E · 5 s

Los datos de mar son previsión de modelo, no lectura directa de una boya.

Sortides en Inici

La pantalla inicial muestra las próximas sortides relevantes.

Los estados visibles deben ayudar a entender la situación:

Estado	Significado
Reservada al grup	Solo puede apuntarse el grupo propietario
S’obre a tots avui a les 07:30	Aún no está abierta al resto
Programada · s’obre dilluns a les 07:30	Sortida de viernes pendiente de apertura
Oberta a tots	Cualquier socio puede apuntarse
Completa	No quedan plazas
Completa · llista d’espera oberta	Se puede entrar en espera
Sortida passada	Ya no admite cambios
Enquestes

La app permite crear encuestas internas.

Los resultados muestran:

votos;
porcentajes;
barras visuales;
votantes ocultos por defecto.

Los nombres de votantes solo aparecen si se pulsa:

Veure votants
Calendari del club

El calendario del club no incluye las sortides regulares.

Solo debe mostrar:

regatas de competició;
regatas sociales;
otros eventos del club.

Los días sin evento no son clicables.

Al clicar un día con evento, se despliegan las fichas correspondientes debajo del calendario.

Los eventos son editables solo por el patró.

Registro de socios

Los nuevos socios pueden solicitar acceso desde la pantalla inicial.

El registro pide:

nombre y apellidos;
grupo;
sección o secciones;
clave personal.

Las secciones disponibles son:

social
competició

Norma del club:

Un socio de competició también pertenece a social.

Las solicitudes quedan pendientes hasta que el patró las aprueba.

Secciones

La app distingue entre:

social;
competició.

Esto afecta a:

avisos;
encuestas;
visibilidad de algunos contenidos;
organización interna de socios.
Historial

Las sortides pasadas pueden aparecer en el historial.

Una sortida se considera pasada 15 minutos después de su inicio, no simplemente al cambiar de día.

El historial ayuda a revisar actividad anterior sin mezclarla con las sortides activas.

Consideraciones técnicas
Aplicación cliente

La app funciona enteramente en el navegador.

No hay backend propio ni cron de servidor.

Por eso, algunas tareas automáticas, como la generación semanal de sortides, se ejecutan cuando alguien abre la app.

Supabase

Supabase se usa como base de datos compartida.

La app lee y escribe datos en la tabla app_data.

LocalStorage

LocalStorage se usa como caché local para que la app cargue más rápido.

GitHub Pages

La app está pensada para subirse directamente a GitHub Pages.

Para publicar una nueva versión:

sustituir index.html;
mantener la carpeta Assets;
conservar Assets/logo-llop-de-mar.png;
comprobar que la ruta del logo respeta mayúsculas y minúsculas.



Yo probaría como mínimo esto:

1. Generación semanal

Comprobar que:

Caso	Resultado esperado
Abro la app antes del sábado 08:00	No genera la semana siguiente todavía
Abro sábado después de las 08:00	Genera lunes, miércoles y viernes
Vuelvo a abrir la app	No duplica sortides
Ya hay sortides con gente apuntada	No las borra
Viernes antiguos vacíos por grupo	Se limpian si procede
2. Lunes y miércoles

Probar una sortida de cada tipo:

Grupo	Hora	Apertura esperada
Grup F	08:00	Día anterior 20:00
Grup E	09:15	Día anterior 20:00
Grup D/C/B/A	tarde	Mismo día 07:30

Casos clave:

6 o 7 apuntados → se abre a todos cuando toca.
8 apuntados → no se abre.
8 apuntados + baja después de la apertura → se reabre si no hay espera.
Hay espera del grupo → primero sube la espera, no se abre directamente a otros.
3. Llista d’espera

Probar:

Situación	Resultado esperado
Grupo lleno antes de apertura general	Solo espera del grupo
Baja antes de apertura general	Sube alguien del grupo
Sortida abierta a todos y llena	Cualquier socio puede entrar en espera
Baja con espera mixta	Prioridad según la regla acordada
Viernes lleno	Espera por orden de llegada, sin prioridad de grupo
4. Viernes

Comprobar:

Caso	Resultado esperado
Viernes antes del lunes 07:30	Programada, no abierta
Lunes 07:30	Abierta a todos
12 vogadors apuntados	Completa
13.º vogador	Entra en llista d’espera
Hasta 4 timoneles	Se aceptan
5.º timonel	No debería entrar como timonel
5. Sortida passada

Probar con horas reales:

Hora salida	Debe pasar a “passada”
08:00	08:15
17:30	17:45
18:45	19:00
20:15	20:30

Antes de esos 15 minutos debería seguir activa. Después ya no debería dejar apuntarse, desapuntarse ni abrir.

6. Meteo

Comprobar:

aparece en Inici;
solo muestra Dl / Dc / Dv;
al clicar día cambia la previsión;
muestra viento, temperatura y ola de forma compacta;
si falla la API, no rompe la app.

Lo ideal sería hacer una fase de pruebas con datos ficticios: 8 o 10 socios repartidos entre grupos, simular apuntarse/desapuntarse y revisar Supabase después. También podemos crear una pequeña checklist de test para que la siga el patrón antes de usarla con todos.

A ver el checklist? 
Pensó durante un par de segundos

Aquí tienes una checklist de testeo lista para copiar en un TESTING.md o en un issue de GitHub.

# Checklist de pruebas · Llop de Mar App

Esta checklist sirve para validar el funcionamiento principal de la app antes de usarla con todos los socios.

---

## 0. Preparación previa

- [ ] Subir la última versión de `index.html` a GitHub Pages.
- [ ] Comprobar que existe la carpeta:

```text
Assets/
└── logo-llop-de-mar.png
 Abrir la app en navegador móvil.
 Abrir también en navegador de escritorio para revisar errores en consola.
 Confirmar que la app carga sin pantalla en blanco.
 Confirmar que el logo aparece en la pantalla de login.
 Confirmar que Supabase carga datos correctamente.
 Confirmar que se puede entrar como patró.
 Confirmar que se puede entrar como soci.
 Confirmar que se puede entrar como cap de grup.
1. Login y usuarios
Patró
 El patró puede entrar con su clave.
 Si la clave es incorrecta, aparece mensaje de error.
 El patró puede cerrar sesión.
 Al cerrar sesión, vuelve a la pantalla inicial.
Soci
 Un socio existente puede seleccionar su nombre.
 Un socio con clave creada puede entrar.
 Una clave incorrecta muestra error.
 Un socio puede cerrar sesión.
Primer acceso de socio
 Un socio sin clave puede crear una clave nueva.
 La clave debe tener mínimo 6 caracteres.
 Si las claves no coinciden, aparece error.
 Tras crear clave, el socio entra correctamente.
 Al volver a entrar, se solicita la clave creada.
Registro nuevo
 Un usuario puede solicitar crear cuenta nueva.
 El formulario exige nombre.
 El formulario exige clave mínima de 6 caracteres.
 El formulario exige repetir la clave correctamente.
 El usuario queda como pendiente.
 El patró puede aprobar la solicitud.
 El patró puede rechazar la solicitud.
 Un socio de competició queda también como social.
2. Generación semanal de sortides

La app debe trabajar por ciclo semanal.

Las sortides de la semana se generan desde:

sábado a las 08:00
Casos a comprobar
 Antes del sábado a las 08:00 no se generan aún las sortides de la semana siguiente.
 Después del sábado a las 08:00 se generan las sortides de lunes, miércoles y viernes.
 Si nadie abre la app el sábado, se generan cuando alguien entra después.
 Al volver a abrir la app no se duplican las sortides.
 Si ya existen sortides con gente apuntada, no se borran.
 Si existen viernes antiguos vacíos por grupo, se limpian de forma conservadora.
 Si existen viernes antiguos con gente apuntada, no se eliminan.
3. Sortides de lunes y miércoles

Las sortides de lunes y miércoles son por grupo.

Horarios habituales:

Grup F · 08:00
Grup E Matins · 09:15
Grup D · 16:30
Grup C · 17:45
Grup B · 19:00
Grup A · 20:15
Creación
 Se crea una sortida por grupo para el lunes.
 Se crea una sortida por grupo para el miércoles.
 Cada sortida tiene su hora correcta.
 Cada sortida queda inicialmente reservada a su grupo.
 Los socios del grupo pueden apuntarse desde que la sortida existe.
 Los socios de otros grupos no pueden apuntarse antes de la apertura general.
4. Apertura general de lunes y miércoles
Regla de apertura natural
Tipo de sortida	Apertura general
Mañana, antes de las 10:00	Día anterior a las 20:00
Tarde / noche	Mismo día a las 07:30
Casos por grupo
 Grup F · 08:00 se abre al general el día anterior a las 20:00.
 Grup E · 09:15 se abre al general el día anterior a las 20:00.
 Grup D · 16:30 se abre al general el mismo día a las 07:30.
 Grup C · 17:45 se abre al general el mismo día a las 07:30.
 Grup B · 19:00 se abre al general el mismo día a las 07:30.
 Grup A · 20:15 se abre al general el mismo día a las 07:30.
Regla de plazas

Con una sortida de 8 plazas:

 Con 6 apuntados, se abre al general cuando toca.
 Con 7 apuntados, se abre al general cuando toca.
 Con 8 apuntados, no se abre al general.
 Si está llena, debe mostrarse como Completa.
 Si está llena y permite espera, debe mostrarse como Completa · llista d’espera oberta.
 Si alguien se da de baja después de la hora de apertura y queda plaza, la sortida se recalcula.
 Si no hay espera pendiente y queda plaza, se abre a todos automáticamente.
5. Llista d’espera en lunes y miércoles
Antes de la apertura general
 Si la sortida está reservada al grupo y hay plazas, un socio del grupo se apunta normal.
 Si la sortida está reservada al grupo y está llena, un socio del grupo entra en llista d’espera.
 Un socio de otro grupo no puede entrar en llista d’espera antes de la apertura general.
 La llista d’espera muestra la posición del socio.
Prioridad del grupo
 Si hay alguien del grupo en espera y se libera una plaza, sube primero esa persona.
 La sortida no se abre a otros grupos mientras haya alguien del grupo esperando y pueda subir.
 Si después de subir alguien del grupo sigue quedando plaza, la sortida puede abrirse a todos.
Después de la apertura general
 Si la sortida está abierta a todos y hay plazas, un socio de otro grupo puede apuntarse.
 Si la sortida está abierta a todos y está llena, un socio de otro grupo entra en llista d’espera.
 Si alguien se da de baja, sube automáticamente la persona que corresponda.
 La prioridad del grupo solo se aplica a quien ya tenía derecho preferente.
 El resto de la llista d’espera funciona por orden de entrada.
6. Botón manual “Obrir”

El botón manual lo pueden usar:

patró;
cap de grup de esa sortida.
Casos
 El patró ve el botón Obrir cuando tiene sentido abrir.
 El cap de grup ve el botón Obrir solo en sus sortides.
 Un socio normal no ve el botón Obrir.
 El botón no aparece si la sortida está completa.
 El botón no abre la sortida si no hay plazas.
 El botón no se salta la llista d’espera del grupo.
 Si hay alguien del grupo esperando, primero debe promocionarse.
 Si después queda plaza, la sortida puede abrirse a todos.
 Si se abre manualmente, los socios de otros grupos pueden apuntarse.
7. Viernes: sortides generales

Los viernes son sortides generales, no por grupo.

Cada viernes deben generarse dos sortides:

Hora	Vogadors	Timonels
17:30	12	hasta 4
18:45	12	hasta 4
Creación
 Se genera una sortida de viernes a las 17:30.
 Se genera una sortida de viernes a las 18:45.
 No se generan viernes por grupo.
 Las sortides de viernes tienen 12 plazas de vogadors.
 Las sortides de viernes permiten hasta 4 timonels.
 Las sortides de viernes no tienen grupo propietario.
Apertura

Las sortides de viernes se abren:

lunes a las 07:30
 Antes del lunes a las 07:30 aparecen como programadas.
 El texto visible debería indicar algo como Programada · s’obre dilluns a les 07:30.
 Desde el lunes a las 07:30 aparecen como Oberta a tots.
 Cualquier socio puede apuntarse desde la apertura.
 Si hay 12 vogadors apuntados, aparece como completa.
 Si se libera una plaza después de estar completa, se recalcula y vuelve a permitir apuntarse.
8. Llista d’espera en viernes

En viernes no hay prioridad de grupo.

 Se pueden apuntar hasta 12 vogadors.
 El vogador número 13 entra en llista d’espera.
 La llista d’espera funciona por orden de entrada.
 Si un vogador se da de baja, sube el primero de la llista d’espera.
 Si no hay nadie en espera y queda plaza, la sortida vuelve a estar abierta.
 Se pueden apuntar hasta 4 timonels.
 El quinto timonel no debería poder entrar como timonel.
9. Desapuntarse
Antes de la salida
 Un socio apuntado puede desapuntarse.
 Al desapuntarse, desaparece de la lista de vogadors.
 Si había llista d’espera, se promociona automáticamente a quien corresponda.
 Si no había espera y queda plaza, la sortida se recalcula.
 Si la sortida ya debía estar abierta a todos, vuelve a abrirse.
Desde llista d’espera
 Un socio en espera puede salir de la llista d’espera.
 Al salir, se actualiza su posición.
 No se altera la lista de apuntados.
10. Sortida passada

Una sortida se considera pasada 15 minutos después de su hora de inicio.

Hora de sortida	Pasa a passada
08:00	08:15
09:15	09:30
17:30	17:45
18:45	19:00
20:15	20:30
Casos
 Antes de la hora de inicio, la sortida está activa.
 Durante los 15 minutos posteriores al inicio, la sortida sigue activa.
 A partir de los 15 minutos, aparece como Sortida passada.
 Una sortida passada no permite apuntarse.
 Una sortida passada no permite entrar en llista d’espera.
 Una sortida passada no permite desapuntarse.
 Una sortida passada no permite abrir al general.
 Una sortida passada no promociona gente de la llista d’espera.
 Una sortida passada pasa al historial.
11. Vista según rol
Patró
 Ve todas las sortides.
 Puede gestionar miembros.
 Puede abrir sortides manualmente si procede.
 Puede añadir socios manualmente a una sortida.
 Puede eliminar sortides si corresponde.
 Puede ver avisos y registros.
Cap de grup
 Ve las sortides de su grupo.
 Ve sortides abiertas a todos.
 Puede abrir manualmente sortides de su grupo si procede.
 No puede abrir sortides de otros grupos.
 No puede gestionar miembros globalmente.
Soci
 Ve sus sortides de grupo.
 Ve sortides abiertas a todos.
 Ve las sortides de viernes cuando estén abiertas.
 No ve botones de gestión.
 Puede apuntarse, desapuntarse o entrar en espera según reglas.
12. Avisos
 El patró puede crear avisos.
 Los avisos generales los ven todos.
 Los avisos de social los ven socios de social.
 Los avisos de competició los ven socios de competició.
 Los avisos personales los ve solo el destinatario.
 Los avisos personales no leídos aparecen destacados.
 Se pueden marcar como leídos.
 Los logs automáticos se generan correctamente al abrir, apuntarse o desapuntarse.
13. Enquestes
 El menú inferior muestra Enquestes.
 El patró puede crear una encuesta.
 Los socios pueden votar.
 Un socio no puede votar dos veces la misma opción si no procede.
 Los resultados muestran votos.
 Los resultados muestran porcentajes.
 Los resultados muestran barras.
 Los votantes no se ven por defecto.
 Al clicar Veure votants, aparecen los nombres.
 Los nombres aparecen como xips compactos.
 No aparecen avatares ni iniciales.
14. Meteo en Inici

La previsión debe ser compacta.

 La meteo aparece en la pantalla de Inici.
 Está situada después de Avisos y antes de Sortides.
 Solo muestra días de salida: Dl, Dc, Dv.
 Al clicar Dl, muestra la previsión del lunes.
 Al clicar Dc, muestra la previsión del miércoles.
 Al clicar Dv, muestra la previsión del viernes.
 Muestra previsión por horas de forma condensada.
 Muestra temperatura.
 Muestra viento en nudos.
 Muestra dirección del viento.
 Muestra lluvia si existe.
 Muestra altura de ola.
 Muestra dirección de ola.
 Muestra periodo de ola.
 Si falla la API meteorológica, la app no se rompe.
 Si falla la API marina, la app no se rompe.
15. Calendari del club

El calendario no debe incluir sortides regulares.

 El calendario aparece en Inici después de Enquestes.
 Solo muestra regatas y eventos del club.
 Los días sin evento no son clicables.
 Los días con evento muestran un indicador visual.
 Al clicar un día con evento, se muestran las fichas debajo.
 El patró puede crear eventos.
 El patró puede editar eventos.
 El patró puede eliminar eventos.
 Los socios no pueden editar eventos.
16. Historial
 Las sortides pasadas aparecen en historial.
 El historial no se mezcla con las sortides activas.
 El historial está paginado.
 Se muestran 5 ítems por página.
 Las sortides pasadas conservan la lista de participantes.
17. Persistencia y sincronización
Supabase
 Al crear una sortida, aparece guardada en Supabase.
 Al apuntarse un socio, se actualiza Supabase.
 Al desapuntarse, se actualiza Supabase.
 Al crear aviso, se actualiza Supabase.
 Al votar encuesta, se actualiza Supabase.
 Al crear evento, se actualiza Supabase.
LocalStorage
 La app carga rápido con caché local.
 Si se borra LocalStorage, la app vuelve a cargar datos desde Supabase.
 La sesión se conserva si corresponde.
 Al cerrar sesión, se limpia la sesión local.
18. Pruebas multiusuario

Estas pruebas son importantes porque la app la usarán varios socios.

 Abrir la app en dos móviles distintos.
 Entrar con dos socios diferentes.
 Apuntar a un socio desde un móvil.
 Comprobar que aparece en el otro móvil al recargar.
 Desapuntar desde un móvil.
 Comprobar que se actualiza en el otro.
 Crear una llista d’espera desde varios usuarios.
 Comprobar que el orden se mantiene.
 Evitar duplicados del mismo socio en una sortida.
 Evitar que un socio esté a la vez en apuntados y en espera.
19. Pruebas visuales en móvil
 Login se ve bien en móvil.
 Botones son suficientemente grandes.
 Las tarjetas de sortides son legibles.
 Los xips de participantes no rompen el diseño.
 La meteo compacta no ocupa demasiado.
 Las enquestes son cómodas de usar.
 El calendario es legible.
 El modal de logout se ve correctamente.
 No hay scroll horizontal accidental.
20. Casos límite
 Socio sin grupo asignado.
 Socio sin secciones.
 Socio de competició sin social: debe normalizarse también como social.
 Sortida sin hora.
 Sortida sin capacidad.
 Sortida antigua sin campos nuevos de timonel.
 Sortida antigua sin waitlist.
 Datos de Supabase vacíos.
 API meteo no responde.
 API marina no responde.
 Usuario abre la app justo durante una apertura automática.
 Usuario abre la app justo cuando una sortida pasa a passada.
 Usuario intenta apuntarse dos veces.
 Patró elimina una sortida vacía.
 Patró intenta eliminar una sortida con gente apuntada.
21. Prueba final de aceptación

La app se puede considerar lista para uso del club cuando:

 Se generan correctamente las sortides semanales.
 No hay duplicados.
 Lunes y miércoles funcionan por grupo.
 Viernes funciona como sortida general.
 Las aperturas respetan horarios y plazas.
 La llista d’espera promociona correctamente.
 Las sortides pasan a historial 15 minutos después del inicio.
 La meteo se ve compacta y útil.
 Avisos, enquestes y calendario funcionan.
 Supabase guarda los cambios.
 La app funciona correctamente en móvil.

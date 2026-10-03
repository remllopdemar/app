# Registro de cambios Â· App Llop de Mar

## 3 de octubre de 2026 Â· Puesta en marcha de temporada

**QuÃ© pasaba:** la app abrÃ­a, pero salÃ­a vacÃ­a: 0 socios y ninguna salida programada.

**Por quÃ©:** el proyecto de Supabase (plan gratuito) se habÃ­a pausado solo por llevar meses sin uso desde junio. Sin base de datos, la app funcionaba en modo sin conexiÃ³n y tampoco generaba las salidas de la semana, porque solo las crea cuando consigue cargar los datos.

**QuÃ© se hizo:**
- Se restaurÃ³ el proyecto de Supabase desde su panel. Los datos de junio estaban intactos: socios, avisos, encuestas e historial.
- Al recargar, la app generÃ³ sola las salidas de la semana del 5 de octubre (lunes, miÃ©rcoles y viernes).
- Se cambiÃ³ la clave del patrÃ³, porque no se recordaba la anterior. Solo se modificÃ³ el hash en `index.html`; la clave no estÃ¡ escrita en ningÃºn sitio.
- Se aÃ±adiÃ³ `.github/workflows/supabase-keepalive.yml`, una tarea automÃ¡tica de GitHub que hace una consulta a Supabase cada 3 dÃ­as para que no se vuelva a pausar. No toca la app ni los datos.

**QuÃ© no cambia:** el funcionamiento de la app es el mismo que en junio y los socios entran con sus claves de siempre.

**Si vuelve a salir vacÃ­a:** mirar en supabase.com/dashboard si el proyecto estÃ¡ pausado y comprobar en la pestaÃ±a Actions que el keepalive sale en verde.

## 1 a 4 de junio de 2026 Â· Ajustes antes del verano

- Nueva sincronizaciÃ³n con Supabase: cambios en tiempo real y refresco cada 60 segundos.
- PrevisiÃ³n meteorolÃ³gica y estado del mar integrados en la app.
- Logo del club.
- Calendario: cambios visuales.
- EstadÃ­sticas y encuestas mejoradas.
- Limpieza de cÃ³digo.
- Seguridad: los nombres de los socios se escapan al mostrarlos.
- README ampliado con las reglas de las salidas y una lista de pruebas.

## 24 y 25 de mayo de 2026 Â· Primera versiÃ³n

- App inicial en un solo `index.html`: login de patrÃ³, cap de grup y soci; salidas con plazas, timoneles y lista de espera; avisos y encuestas.

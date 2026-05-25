Estat del projecte — App Llop de Mar
Infraestructura

Un sol fitxer index.html (vanilla JS) · GitHub Pages · Supabase com a BD compartida
Càrrega optimitzada: render immediat des de localStorage, Supabase en segon pla
Refresc automàtic cada 60 segons

Autenticació

Patró (PIN hardcoded, hash SHA-256)
Cap de grup (assignat pel patró)
Soci (clau pròpia, primer accés crea clau)
Sessió persistent · Autoregistre amb aprovació del patró

Sortides socials (recentment implementat)

Generació automàtica els dissabtes (Dl + Dc, un per grup)
Capacitats per grup: A-D,F=8+1R · E=16+1R
Timonels per grup i dia (fora del recompte de vogadors)
Obertura automàtica per hora (tarda 7:30h, matins dia anterior 20h)
Llista d'espera amb prioritat de grup
El patró pot afegir/treure socis directament
Botó de forçar generació per proves (panell patró)

Altres funcionalitats

Avisos del club (patró, amb fixar/eliminar)
Votacions/enquestes
Calendari d'esdeveniments
Estadístiques d'entrenaments (competició)
Resultats de regates
Gestió de membres (afegir, editar, caps, reset clau)
150 socis ficticis de prova carregats a Supabase

Pendent / idees

Banner "tens sortida demà"
Exportar llista apuntats per WhatsApp
Historial personal del soci
Substituir socis ficticis pels reals

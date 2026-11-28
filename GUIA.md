# Timer + Meta de subs BLOODSITAX: instalación paso a paso

## Qué hace cada archivo

| Archivo | Qué es |
|---|---|
| `index.html` | Panel del **timer** y su overlay. **Es el que cuenta todo**: subs, bits y raids. Cada sub suma tiempo (con multiplicador) y +1 a la meta (sin multiplicador). |
| `meta.html` | **Solo el overlay** de la meta de subs (los contenedores). Muestra lo que cuenta el timer. Si lo abres sin `?display`, te manda al panel. |
| `database.rules.json` | Reglas de seguridad de Firebase. |

Todo se controla desde **un solo panel** (`index.html`): el timer y, más abajo, la sección **Meta de subs**.

---

## Paso 1. Crear el proyecto de Firebase

1. Entra a https://console.firebase.google.com > **Agregar proyecto**. Nombre: por ejemplo `timer-bloodsitax`. Google Analytics: no hace falta.
2. Menú izquierdo > **Compilación > Realtime Database** > **Crear base de datos** > elige la ubicación > **Comenzar en modo bloqueado**.
3. Engrane (arriba a la izquierda) > **Configuración del proyecto** > **General** > abajo, en "Tus apps", el ícono **`</>`** (Web). Ponle un nombre, **no** marques Hosting, dale Registrar.
4. Te muestra un bloque `firebaseConfig`. Déjalo abierto: lo usas en el paso 3.

## Paso 2. Activar el inicio de sesión

1. Menú izquierdo > **Compilación > Authentication** > **Comenzar**.
2. Pestaña **Método de acceso**:
   - **Google** > Habilitar > elige tu correo de soporte > Guardar.
   - **Correo electrónico/contraseña** > Habilitar (solo la primera opción) > Guardar.
3. Pestaña **Configuración** > **Dominios autorizados** > **Agregar dominio** > `bloodsitaxvods.github.io` (el dominio de tu GitHub Pages, sin `https://` y sin carpeta).

## Paso 3. Pegar la configuración en los dos archivos

1. Abre `index.html` con un editor de texto y busca `CONFIG DE FIREBASE`.
2. Reemplaza cada `PEGA_AQUI` con los datos del `firebaseConfig` del paso 1 (apiKey, authDomain, databaseURL, projectId, storageBucket, messagingSenderId, appId).
   - Si tu `firebaseConfig` no trae `databaseURL`, cópiala de Realtime Database (arriba de los datos sale algo como `https://timer-bloodsitax-default-rtdb.firebaseio.com`).
3. Haz exactamente lo mismo en `meta.html`. **Los dos tienen que quedar con la misma config.**

## Paso 4. Crear la cuenta del overlay

1. Authentication > pestaña **Usuarios** > **Agregar usuario**.
2. Correo: `overlay@bloodsitax.app`.
3. Contraseña: una larga e inventada (por ejemplo 20 letras y números). **Esta es la clave del overlay.** Guárdala.
4. Copia el **UID** que aparece en la lista de usuarios para esa cuenta.

## Paso 5. Subir a GitHub

1. Crea un repo nuevo en la cuenta `bloodsitaxvods` (por ejemplo `timer-bloodsitax`).
2. Sube `index.html` y `meta.html` a la raíz del repo. (`database.rules.json` y esta guía pueden ir también; no tienen datos secretos.)
3. Repo > **Settings > Pages** > Source: **Deploy from a branch** > Branch: `main` / `(root)` > Save.
4. Espera 1 o 2 minutos. Tu panel queda en `https://bloodsitaxvods.github.io/extensible-bloody/`.

## Paso 6. Darte permisos (y a ella)

1. Abre el panel y entra con Google. Te va a decir **"no tiene permiso"** y te muestra un **código (UID)**. Cópialo.
2. Ella hace lo mismo con su cuenta y te pasa su código.
3. En Firebase > Realtime Database > pestaña **Datos**, pasa el mouse sobre la raíz y dale **`+`** para crear:

```
admins
   <tu código>: true
   <código de ella>: true
counters
   <UID de la cuenta del overlay>: true
```

- En "Valor" escribe `true` sin comillas (tiene que quedar como booleano).
- Para agregar otro mod después: entra al panel, te pasa su código y lo agregas en `admins`.

4. Recarga el panel: ya debe entrar.

## Paso 7. Publicar las reglas de seguridad

1. Realtime Database > pestaña **Reglas**.
2. Borra todo lo que hay, pega el contenido completo de `database.rules.json` y dale **Publicar**.

Con esto:
- Cualquiera puede **ver** el timer y la meta (OBS no inicia sesión con Google).
- Solo ustedes (`admins`) y el overlay con clave (`counters`) pueden **cambiar** cosas.
- Nadie puede darse permisos desde la página; solo desde la consola de Firebase.

## Paso 8. Configurar precios y tiempo

En el panel del timer:
1. **Precios**: revisa cuánto suma cada sub, bits, raid y donación. Dale **GUARDAR PRECIOS**.
2. **Pon el tiempo exacto**: el tiempo con el que arranca el extensible.
3. **Tiempo total**: cuando empiece el directo, **EMPEZÓ AHORA**.

En el mismo panel, sección **Meta de subs**:
1. **Cambiar cada**: cada cuántas subs pasa al siguiente contenedor (por ejemplo 15) > **GUARDAR**.
2. Orden de los contenedores: flechas para subir o bajar > **GUARDAR**.
3. Si ya tenías subs contadas, **Número fijo** con ese número.

## Paso 9. Poner los overlays en OBS

1. En el panel del timer, abre **Link para OBS** y escribe la **clave del overlay** (paso 4).
2. **COPIAR LINK TIMER CON CLAVE** > OBS > Fuentes > `+` > **Navegador** > pega en URL. Ancho 1920, alto 1080.
3. **COPIAR LINK META CON CLAVE** > otra fuente de **Navegador** > pega el link. Mismo tamaño.
4. En las dos fuentes, **desmarca** "Apagar fuente cuando no esté visible". Si no, el timer deja de contar cuando cambias de escena.
5. Para que salgan en varias escenas: clic derecho > Copiar > en la otra escena, clic derecho > **Pegar (referencia)**. No uses "Pegar (duplicado)".

Revisa arriba del panel las dos luces:
- **Overlay timer: verde "contando"**. Es la importante: es la que cuenta subs y tiempo.
- **Overlay meta: verde "en pantalla"**. Solo te dice que la calabaza está en OBS.

## Paso 10. Prueba antes del evento (5 minutos)

1. Panel del timer > botón **Sub Tier 1 / Prime** (sumar a mano). Debe sumar 5 min al timer **y** 1 a la meta.
2. Activa el multiplicador x2 con "Sub Tier 1" marcado y aprieta otra vez el botón: debe sumar 10 min al timer y **solo 1** a la meta.
3. Sección Meta de subs > **EFECTO 2 S** y **Probar cambio de contenedor**: deben verse en OBS.
4. Al terminar: meta > **Reiniciar a 0** (o Número fijo), timer > **Pon el tiempo exacto**, y si quieres borra los dos historiales.

## Paso 11. Durante el evento

- **OBS (PC de ella)**: los dos overlays con clave. Es el contador principal.
- **Tu PC**: panel del timer abierto. Es el respaldo con otra conexión a internet, y desde ahí haces las correcciones manuales.
- **Correcciones manuales solo una persona**: los botones manuales siempre suman, así que si dos personas corrigen lo mismo, se suma doble.
- Si una luz se pone roja, revisa OBS. Lo que pasó mientras estuvo roja lo ves en el historial.

### Meta de subs (contenedores)

- Cada contenedor dura "Cambiar cada" subs. Con 15: el 1.º de 0 a 15, el 2.º de 15 a 30, etc.
- Al llegar al final: se queda lleno mostrando el número nuevo (por ejemplo 22 · 15), brilla y salen murciélagos 4 segundos, y pasa al siguiente (22 · 30). Si un regalo salta varios, los hace uno por uno.
- Después del último se queda lleno y el número sigue subiendo.
- Si bajas el número o cambias "Cambiar cada", se acomoda directo, sin festejo.
- Un regalo de 5 o más subs de golpe lanza el efecto premium (por ahora brillo y murciélagos).
- **EFECTO 2 S**: lanza los murciélagos 2 segundos cuando quieras.
- **OCULTAR EN OBS**: esconde la meta un rato sin quitar la fuente.

- **Lista de contenedores:** en cada lugar eliges qué contenedor va (se puede repetir), lo mueves con las flechas, lo quitas con la X y agregas más con **+ AGREGAR CONTENEDOR** (hasta 20). Nada cambia en OBS hasta darle **GUARDAR**; **Deshacer cambios** vuelve a lo guardado.

### Bits acumulados

- Cada cheer del chat suma sus bits al total una sola vez, aunque haya multiplicador o el timer haya terminado.
- **+ SUMAR**: para Power-ups o combos (no llegan por el chat). Solo suma al total de bits; el tiempo de esos bits súmalo con los botones del timer.
- **NÚMERO FIJO** y **Reiniciar a 0** para corregir.
- Es la base para el contenedor de bits.

### Colores del historial del timer

| Color | Significa |
|---|---|
| Verde | Contado automáticamente desde el chat |
| Rojo | Sumado o quitado a mano |
| Lila | Contado con multiplicador activo |
| Naranja | No sumó tiempo (timer terminado o falla). Lee la nota: si dice **NO SE CONTÓ**, súmala a mano con el botón de sub |
| Azul **SIN META** | Un botón manual sumó el tiempo, pero no el +1 de la meta. Suma 1 a mano en el panel de la meta |

### Lluvias de bits

Los cheers que llegan casi al mismo tiempo se juntan en una sola escritura, así que no se pierden. Si Firebase no pudiera guardar después de muchos intentos, queda un aviso naranja **NO SE CONTÓ** en el historial para sumarlo a mano.

---

## Paso 12. Limpiar lo viejo

**Antes de borrar, pasa el conteo de la meta vieja:**
1. Abre el panel viejo de la meta (con su link de siempre) > **Respaldo** > **EXPORTAR RESPALDO (JSON)**.
2. En el panel nuevo de la meta: **Número fijo** con tu conteo y **Meta total** con tu meta.
3. El estilo de la calabaza vuelve al original; ajústalo de nuevo o pásame el JSON.

**Borrar los proyectos viejos** (`timer-kiza` y `contador-subs-calabazas-bloody`):
1. Confirma que Kiza ya no usa su timer: al borrarlo deja de funcionar para siempre.
2. https://www.twitch.tv/settings/connections > busca tu app de follows en "Otras conexiones" > **Desconectar**. El proyecto viejo tenía reglas abiertas y guardaba ese permiso.
3. En Firebase, abre cada proyecto viejo > engrane > Configuración del proyecto > General > abajo del todo > **Borrar proyecto**.
4. Archiva o borra los repos viejos (`kiza-timer` y el de la meta vieja).

## Sobre los correos de "vulnerable"

Eran avisos de que la base de datos vieja tenía **reglas abiertas**. Con el paso 7 queda resuelto.

Si también te llegó un aviso de **clave de API expuesta en GitHub**: en una app web de Firebase la `apiKey` es pública por diseño; lo que protege los datos son las reglas. Para cerrarla un poco más: Google Cloud Console > APIs y servicios > Credenciales > "Browser key" > Restricciones de aplicaciones > **Sitios web** > `https://bloodsitaxvods.github.io/*`. Con eso, los overlays **descargados** como archivo local dejan de poder iniciar sesión; en OBS usa los links de GitHub Pages.

## Follows (opcional)

En el panel del timer > **Conexión Twitch**: Client ID de tu app de dev.twitch.tv y **AUTORIZAR** con tu cuenta de mod. En la app de Twitch, la URL de redirección tiene que ser exactamente la del panel nuevo.

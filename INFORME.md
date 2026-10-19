# Informe de reparación – Grand Line: Duelo de Leyendas

## 1. Archivos recibidos (todos se abrieron)
- `index3.html` (juego original, 3 vs 3, 30 personajes, energía, pasivas, estados).
- `Fotos_juego.zip` con 5 JPG. **El orden real no coincide con el descrito**: IMG_5051, IMG_5054 2 e IMG_5058 = hojas de personajes (10 cada una); IMG_5052 = escenario; IMG_5061 = efectos. Se usaron según su contenido.
- Las hojas son JPG (sin canal alfa): el recorte se hizo con detección de fondo conectado al borde, conservando los blancos internos (ojos, ropas, dientes).

## 2. Errores corregidos en la interfaz
- Nombre del rival: ahora debajo de la barra de vida, abajo a la izquierda de su panel (HUD propio con barra, números, estados y nombre). Verificado por medición en escritorio, tablet y dos móviles con nombres largos.
- Sprites, HUD, ficha y desplegables en capas separadas; el sprite se limita para no tapar el HUD.
- Vida actual/máxima de ambos combatientes siempre visible, acotada a 0..máx. (comprobado en 22 combates aleatorios).
- Desplegables de habilidades y pasiva: se redibujan en cada turno con las habilidades del personaje activo.
- Ficha completa al pasar el cursor (también rivales) y bottom-sheet al tocar en móvil, con botón ✕ / toque fuera. El juego no tiene niveles, por lo que no se muestra «nivel».
- Daño real en puntos («Daño base: 163 PV · ≈ 99 contra Sanji») desde la misma función que usa el combate.
- Paleta azul cielo/amarillo en todas las pantallas.

## 3. Errores de lógica encontrados y corregidos
1. El rival podía quedar jugando mientras el jugador pulsaba (variable `playerTurn` puesta a true antes de tiempo): sustituido por un único bucle asíncrono de turnos con bloqueo.
2. Oden: «cae derrotado» se registraba y podía forzar un cambio de personaje antes de aplicar su pasiva.
3. Doflamingo: su pasiva era un no-op; ahora el aliado caído hace un último ataque básico gratuito.
4. Regeneración mal asignada: el código de «se regenera si hay aliados heridos» estaba en Marco (id 5) cuando pertenece a Sanji (id 1). Marco ahora cura 8 % al final de su turno (antes 10 % + 5 % solo en el jugador).
5. Akainu quemaba a quien era golpeado en lugar del atacante.
6. Yamato (Guardián de la Nieve) y Marco (Llamas del Renacimiento) tenían su efecto de apoyo inalcanzable; ahora son híbridas (daño + escudo / curación 15 %).
7. Sengoku: pasiva de Garp (ignorar defensa) aplicada por error a Sengoku; ahora su pasiva es +10 % crítico de equipo en la ronda 1. Su habilidad de potenciar ataque no tenía efecto real; ahora sí.
8. Fujitora reducía su propio daño al criticar; ahora reduce los críticos recibidos.
9. Yamato: la reducción de daño elemental no se aplicaba en el lado del jugador.
10. Kizaru: el +15 % por ataques de área estaba ligado a King; ahora es de Kizaru.
11. Kid: las cargas de metal no hacían nada; ahora +5 % DEF por carga y se ganan al recibir golpes.
12. Big Mom (festín), Rayleigh (críticos), Barbablanca (inmunidad/curación), Roger (tope 3 usos), Shanks (valía solo del lado rival), Katakuri, Kaido, Law (Estocada Perforante), Hancock (solo a objetivos masculinos): implementadas según el texto de su pasiva.
13. El rival ignoraba pasivas del atacante y críticos; ahora jugador e IA comparten `resolveSkill`/`calcDamage`.
14. Aturdimiento/congelación/petrificación solo funcionaban para el rival; ahora afectan a ambos bandos.

## 4. Cambios de equilibrio (documentados)
- Energía inicial 2 y +1 al inicio de cada turno propio (jugador e IA simétricos; antes el jugador no ganaba en su primer turno).
- Ataques de área: ×0,7 por objetivo (el texto original decía «daño reducido por objetivo» pero no se aplicaba).
- Nuevos efectos con temática existente: Aokiji (Cápsula del Tiempo) 35 % de congelar 1 turno; Akainu (Perro de Fuego Infernal) 40 % de quemar; Sabo (Puño de Fuego) 30 % de quemar.
- «Debilitado» ahora reduce ATQ y VEL (según el texto de Shanks) en lugar de DEF.
- Los nombres repetidos «Extinción Divina» (Roger/Shanks) y «Trueno de las Ocho Trigramas» (Yamato/Kaido) se mantienen: son canónicos y no interfieren.
- **Duración**: con jugador aleatorio, un combate dura ~57-63 rondas (daños 50-150 sobre 1000-1300 PV, como en el original). No lo he cambiado; `DAMAGE_SCALE` permite acortarlo.

## 5. Recursos
- 30 sprites identificados por vestuario/rostro y posición en la hoja (confianza alta en todos).
- Escenario: los dos círculos se eliminaron clonando adoquines cercanos con mezcla (OpenCV `seamlessClone`). Puede notarse una textura ligeramente repetida si se amplía mucho.
- Efectos (31 recortes; el juego usa 1 por personaje, Kuzan 2). **Asignación de confianza alta**: Zoro, Sanji, Mihawk, Marco, Law, Kid, Kuma, Barbanegra, Sengoku, Kizaru, Akainu, Fujitora, Aramaki, Beckman, Hancock, Doflamingo, King, Sabo, Oden. **Confianza media (inferida por tema/color; revisar)**: Rayleigh (rayo amarillo), Yamato (club con hielo), Roger (rayo negro), Barbablanca (ondas), Shanks (espada roja), Kaido (tornado y escamas), Big Mom (llamas rosas), Garp (puño azul), Kuzan (dos cristales de hielo), Katakuri (nubes de mochi), Lucci (garras grises). Para cambiar una asignación basta con editar `EFFECTS` en el script.

## 6. Pruebas ejecutadas (Chromium headless, Playwright)
- Sintaxis JS (`node --check`): correcta. 62 rutas de imagen existen; 30 sprites con alfa real; 30 efectos presentes.
- Arranque sin errores de consola/página en escritorio 1280×900 y móvil 390×844.
- 22 combates aleatorios completos (≈1 300 acciones, con dobles pulsaciones, raciones, tónicos y cambios): 0 violaciones de vida/escudo/energía, 0 combates colgados, 0 errores JS; terminan en victoria o derrota y se puede empezar otro.
- Daño anunciado vs. real (24 ataques, 6 parejas con distinta DEF): cociente 0,95–1,05 salvo críticos (1,48) y fallos por precisión.
- Animaciones reales de los 30 personajes: el turno siempre se desbloquea y no quedan efectos en pantalla.
- Pasivas/estados verificados: Sabo (quemadura), Akainu (quema al atacante), Oden (sobrevive a 1 HP), ración (cura), escudo de Marco, doble pulsación ignorada.
- Hover (ratón) abre/cierra la ficha; toque abre bottom-sheet y se cierra con ✕. Sin scroll horizontal en 4 resoluciones; nombre bajo la barra, a la izquierda, sin solapes con sprite.

## 7. No comprobado / limitaciones
- No probado en un iPhone/Safari real ni con pantalla táctil física (solo emulación táctil de Chromium).
- El sonido (pitidos simples) no se probó.
- Pasivas de Fujitora (ceguera) y Beckman (crítico a distancia) solo se implementan en la parte aplicable (no existen cegueras ni ataques «a distancia» en el sistema).
- Los efectos son imágenes estáticas animadas con CSS/WAAPI; no hay animación por fotogramas.

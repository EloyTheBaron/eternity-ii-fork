# Primeros Pasos - Guía Para Novatos

¡Bienvenido! Este documento te guiará paso a paso para empezar a usar el proyect.

---

## 🎯 ¿Qué es este Proyecto?

Es un juego/herramienta para **resolver puzzles tipo Eternity II**:
- 256 piezas cuadradas
- Cada pieza tiene 4 bordes con **colores/patrones**
- Los bordes que se tocan deben tener **el mismo color**
- Objetivo: encajar todas las piezas en una cuadrícula 16×16

**Lo especial:** Combina interfaz gráfica (juego), algoritmos de resolución automática y análisis.

---

## 💻 Instalación (5 minutos)

### Paso 1: Preparar Entorno

Abre terminal en la carpeta del proyecto y corre:

```bash
# Windows (PowerShell)
python -m venv venv
.\venv\Scripts\Activate.ps1

# Mac/Linux
python -m venv venv
source venv/bin/activate
```

👉 **Verás**: La terminal debería mostrar `(venv)` al inicio

### Paso 2: Instalar Dependencias

```bash
pip install -r requirements.txt
```

👉 **Espera**: Descargará ~50MB, puede tomar 1-2 minutos

### ✅ ¡Listo!

Si no hay errores en rojo, ¡estás listo para empezar!

---

## 🚀 Tu Primer Comando (2 minutos)

### Ver Todas las Piezas del Puzzle

```bash
python show_pieces.py -conf data/eternity2/eternity2_256.csv
```

👉 **Qué pasa**: Se abre una ventana mostrando las 256 piezas del puzzle original, colocadas secuencialmente.

**Controles:**
- **i**: mostrar/ocultar números en las piezas
- Click y arrastra: mover vista
- **ESC** o cerrar ventana: salir

---

## 🎮 Jugar Manualmente (10 minutos)

¿Quieres intentar resolver el puzzle tú mismo?

```bash
python play.py -conf data/eternity2/eternity2_256.csv \
               -hints data/eternity2/eternity2_256_hints.csv
```

**Controles:**

| Acción | Tecla/Botón |
|--------|------------|
| Seleccionar pieza para intercambiar | Click **izquierdo** |
| Realizar intercambio | Click izquierdo en otra pieza |
| Rotar pieza seleccionada | Click **derecho** |
| Ver números | Presionar **i** |
| Guardar intento | Presionar **s** |

**Tip:** Las piezas con **borde gris** van donde las pistas las ponen. ¡Empieza de ahí!

---

## 👀 Entender la Pantalla

### Visuales

```
Cada pieza cuadrada tiene 4 bordes:

        [Patrón N]
           ↑
  [Pat W] [PIEZA] [Pat E]
        ↓
      [Patrón S]

Los bordes deben coincidir entre piezas adyacentes
```

### Puntuación en Pantalla

```
S 1234/130560
  ↑     ↑
  |     └─→ Máximo posible (480 bordes coincidentes)
  └────────→ Puntuación actual (bordes que SÍ coinciden)
```

---

## 🤖 Dejar que la Máquina Resuelva (2 minutos)

¿Cansado de intentar manualmente? Deja que un algoritmo lo haga:

```bash
python replacing.py -conf data/eternity2/eternity2_256.csv \
                    -hints data/eternity2/eternity2_256_hints.csv
```

👉 **Qué pasa:**
- El programa corre en terminal (sin UI gráfica)
- Coloca piezas automáticamente cada iteración
- Imprime progreso en terminal
- Guarda soluciones parciales cada cierto tiempo

**Para ver en tiempo real**, en otra terminal:

```bash
python monitor.py -conf data/eternity2/eternity2_256.csv -dir ./
```

Esto abrirá una ventana que se actualiza automáticamente cuando hay progreso.

---

## 📊 Analizar el Puzzle

¿Quieres entender la estructura del puzzle?

```bash
python analyze.py -conf data/eternity2/eternity2_256.csv
```

**Output típico:**
```
color:1, count:128
color:2, count:256
color:3, count:512
...
edge_middle_colors: {6: 60, 7: 60, 8: 60, ...}
```

Es información estadística sobre los colores usados.

---

## ✔️ Validar Puzzle

¿El puzzle está bien formado?

```bash
python validate.py -conf data/eternity2/eternity2_256.csv
```

👉 **Si sale bien**: No imprime nada (silencio es éxito)
👉 **Si hay error**: Muestra `Exception: Validation failure...`

---

## 📁 Estructura Básica

```
edge_puzzle/
├── DOCUMENTATION.md     ← Documentación completa (lee si necesitas detalles)
├── ARCHITECTURE.md      ← Para desarrolladores avanzados
├── QUICKREF.md          ← Tiraditas rápidas y referencia
├── requirements.txt     ← (Ya instalamos)
│
├── show_pieces.py       ← Ver galería
├── play.py              ← Juego manual
├── replacing.py         ← Resolvedor automático
├── swapping.py          ← Otro resolvedor
├── monitor.py           ← Monitorear progreso
│
├── core/
│   ├── defs.py          ← Definiciones (no tocar)
│   └── board.py         ← Lógica tablero (no tocar)
│
├── data/
│   └── eternity2/
│       ├── eternity2_256.csv           ← Puzzle 256 piezas
│       └── eternity2_256_hints.csv     ← Pistas
│
└── ui/
    └── ui.py            ← Código gráfico (no tocar)
```

---

## 🎯 Ejercicios Progresivos

### Ejercicio 1: Conocer la Estructura (5 min)

```bash
# Paso 1: Ver piezas
python show_pieces.py -conf data/eternity2/eternity2_256.csv

# Paso 2: Ver solo pistas
python show_hints.py -conf data/eternity2/eternity2_256.csv \
                     -hints data/eternity2/eternity2_256_hints.csv

# Paso 3: Comparar qué diferencia hay
```

**Observación:** Las pistas son solo 4 piezas de las 256. ¡El resto debes colocarlas!

### Ejercicio 2: Jugar Manualmente 10 Minutos

```bash
python play.py -conf data/eternity2/eternity2_256.csv \
               -hints data/eternity2/eternity2_256_hints.csv
```

**Meta:** Encaja 5-10 piezas sin equivocarte.

**Tip:** Empieza por una esquina, luego sigue los bordes.

### Ejercicio 3: Resolver Automáticamente

```bash
# Terminal 1
python replacing.py -conf data/eternity2/eternity2_256.csv \
                    -hints data/eternity2/eternity2_256_hints.csv

# Terminal 2
python monitor.py -conf data/eternity2/eternity2_256.csv -dir ./
```

**Observación:** ¿Cuánto tiempo tarda en mejorar? ¿Dónde se queda atrapado?

### Ejercicio 4: Comparar Estrategias

```bash
# Comparar dos algoritmos
# Terminal 1
python replacing.py -conf data/eternity2/eternity2_256.csv -hints ... > log1.txt

# Terminal 2
python swapping.py -conf data/eternity2/eternity2_256.csv -hints ... > log2.txt

# Terminal 3
python monitor.py -conf data/eternity2/eternity2_256.csv -dir ./
```

**Pregunta:** ¿Cuál llega más lejos? ¿Más rápido?

---

## 🐛 ¿Algo Salió Mal?

### Error: "ModuleNotFoundError: No module named 'pygame'"

```bash
# Solución: reinstalar dependencias
pip install -r requirements.txt

# O específicamente
pip install pygame
```

### Error: "File not found: data/eternity2/eternity2_256.csv"

```bash
# Asegúrate de estar en la carpeta correcta
cd c:\Users\eloyb\Documents\REPO-ALBERTO-PIEZAS-PUZZLE\edge_puzzle

# Verifica que el archivo existe
dir data\eternity2\

# Si no aparece, el repo no se clonó bien
```

### Error: "Validation failure, color X not in even count"

El puzzle tiene un error (número impar de un color). No es un error tuyo, es del puzzle.

### UI se congela o no responde

Significa que el algoritmo está procesando en el thread principal. Esto es normal para operaciones cortas. Si tarda >10 segundos, usa `monitor.py` en otra terminal en su lugar.

---

## 💡 Conceptos Clave (En Pocas Palabras)

### Pieza
Cuadrado con ID número y 4 colores (bordes).

### Orientación / Rotación
La misma pieza girada 0°, 90°, 180° o 270°.

### Match / Coincidencia
Cuando dos piezas adyacentes tienen el MISMO color en el borde compartido.

### Score / Puntuación
Número total de matches. Máximo = 480 para 16×16.

### Hints / Pistas
4 piezas ya colocadas y rotadas correctamente (para facilitar la resolución).

### CSV
Archivos de texto con datos separados por comas. Usamos para guardar puzzle y soluciones.

---

## ❓ Preguntas Frecuentes

**P: ¿Cuánto tiempo toma resolver manualmente?**  
R: Eternity II original es casi imposible manualmente (solo 2 personas lo lograron, les tomó ~6 años combinados). Pero puzzles pequeños (6×6) ~2-3 horas para alguien dedicado.

**P: ¿Cuánto tarda el programa en resolver?**  
R: Depende del algoritmo. Para 256×256 con hints, `replacing.py` mejora bastante en primeros 1-2 minutos, luego desacelera. `swapping.py` puede quedar atrapado en mínimos locales.

**P: ¿Puedo crear mi propio puzzle?**  
R: Sí, pero necesitarías entender el formato CSV. Lee DOCUMENTATION.md para detalles. O usa `puzzle_generator.py`.

**P: ¿Cuál es la mejor estrategia?**  
R: No hay una única. `replacing.py` bueno al inicio (greedy). `swapping.py` bueno localmente (hill climbing). Lo óptimo: combinar ambas.

**P: ¿Puedo modificar el código?**  
R: ¡Sí! Siéntete libre de cambiar `replacing.py` y `swapping.py`. Evita modificar `core/` a menos que sepas qué haces.

---

## 🔗 Próximos Pasos

### Si Eres Usuario Casual
- Sigue jugando con `play.py`
- Experimenta con `replacing.py` vs `swapping.py`
- Lee QUICKREF.md para atajos

### Si Eres Desarrollador
- Lee DOCUMENTATION.md completamente
- Revisa ARCHITECTURE.md para entender diseño
- Intenta modificar `replacing.py` para experimentar
- Agrega tu propio solver

### Si Eres Investigador
- Analiza el problema NP-completo
- Diseña heurísticas nuevas
- Combina algoritmos genéticos con constraint satisfaction
- ¡Publica tus resultados!

---

## 📞 Tips Pro

1. **Guarda checkpoints.** Si encuentras una buena solución parcial y quieres mejorarla, guárdala con `s` en play.py. Luego cárgala con `-load archivo.csv`.

2. **Usa dos terminales.** Una para resolver, otra para monitorear. Los resolvedores no tienen UI (consola solo), pero `monitor.py` sí.

3. **Analiza primero.** Antes de resolver, corre `analyze.py` para entender la estructura del puzzle.

4. **Ve el patrón.** Algunos colores/patrones aparecen más en bordes que interior. El puzzle no es aleatorio.

5. **Experimenta.** Los puzzles pequeños (4×4, 6×6) resuelven rápido. Usa para testear algoritmos nuevos.

---

## 🎓 Recursos Incluidos

- **Puzzle original**: `data/eternity2/eternity2_256.csv`
- **Variantes Eternity-like**: `data/eternity2_like/` (6×6 a 16×16)
- **Puzzles generados**: `data/generated_*/` (pequeñas instancias para testing)

---

## 📈 Progreso Típico

```
Iteración 1:    Score ~10K / 130K    (primeras piezas colocadas)
Minuto 1:       Score ~20K / 130K    (bordes empezando a coincidir)
Minuto 5:       Score ~40K / 130K    (interior parcialmente completo)
Minutos 10+:    Score ~50K / 130K    (desacelera, mínimos locales)
Horas ~1000:    Score ~70K?          (con mucha suerte y buena estrategia)
```

**Conclusión:** Es muy difícil. El programa mejora rápido al inicio, luego muy lentamente.

---

## 🚀 ¡Listo para Empezar!

Ejecuta esto ahora:

```bash
python show_pieces.py -conf data/eternity2/eternity2_256.csv
```

¡Disfruta explorando el puzzle!

---

**¿Necesitas más ayuda?**  
Revisa:
- `DOCUMENTATION.md` para guía completa
- `QUICKREF.md` para referencia rápida
- `ARCHITECTURE.md` para detalles técnicos

**Última actualización**: Abril 2026

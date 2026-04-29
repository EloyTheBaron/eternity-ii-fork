# Documentación del Proyecto de Puzzles de Borde (Eternity II)

## 🎯 Resumen Ejecutivo

Este proyecto es un **playground interactivo para resolver y analizar puzzles de borde tipo Eternity II**. Combina:
- **Python** para algoritmos de resolución y análisis
- **C++** para optimizaciones de performance
- **Pygame** para visualización interactiva

El objetivo es resolver (o ayudar a resolver) el Eternity II puzzle de 256 piezas, o crear/resolver variantes análogas.

---

## 📚 Conceptos Fundamentales

### 1. Estructura de una Pieza

Cada pieza tiene **4 colores** (patrones), uno en cada dirección cardinal, en orden: **E, S, W, N** (Este, Sur, Oeste, Norte, en sentido horario).

```
        N
        |
    W --+-- E
        |
        S
```

Ejemplo: Pieza 139 = `[14, 8, 8, 9]` → E=14, S=8, W=8, N=9

### 2. Tipos de Piezas

Por número de colores "grises" (frontera = 0):

| Tipo | Grises | Ubicación | Cantidad (16×16) | Descrición |
|------|--------|-----------|-------------------|------------|
| **Corner** | 2 | Esquinas | 4 | Dos bordes exteriores |
| **Edge** | 1 | Bordes | 60 | Un borde exterior |
| **Inner** | 0 | Interior | 236 | Sin borde exterior |

### 3. Orientación (Rotación)

Las piezas pueden rotarse 0°, 90°, 180° o 270°:
- **Dirección 0 (E)**: colores en posición original
- **Dirección 1 (S)**: girada 90° sentido horario
- **Dirección 2 (W)**: girada 180°
- **Dirección 3 (N)**: girada 270°

Cálculo: `color_efectivo = get_color((posición - rotación) % 4)`

### 4. Puntuación (Score)

- **Match**: dos piezas adyacentes con colores iguales en el borde común
- **Puntuación máxima**: `width × (height - 1) + height × (width - 1)` = matches posibles
  - Para 16×16: 16×15 + 16×15 = **480 matches**

---

## 📁 Estructura del Proyecto

```
edge_puzzle/
├── DOCUMENTATION.md                    # Este archivo
├── README.md                           # Guía rápida
├── requirements.txt                    # Dependencias Python
│
├── 🔧 MÓDULOS CORE (lógica del puzzle)
├── core/
│   ├── defs.py                        # Definiciones base: PieceDef, PieceRef, PuzzleDefinition
│   └── board.py                       # Tablero de juego: Board, operaciones principales
│
├── 🎮 SCRIPTS DE RESOLUCIÓN
├── backtracking.py                    # Fuerza bruta + backtracking (educativo)
├── backtracker.py                     # Optimización mejorada de backtracking
├── backtracking_branching.py          # Variante con ramificación
├── backtracking_restarting.py         # Variante con reinicio
├── divide_backtracking.py             # Divide & conquer (divide en border + interior)
├── replacing.py                       # Estrategia constructiva (reemplazo greedy)
├── swapping.py                        # Estrategia local (intercambios)
├── genetic_algorithm.py               # Algoritmo genético (DEAP)
│
├── 🛠️ HERRAMIENTAS DE ANÁLISIS
├── analyze.py                         # Análisis estadístico del puzzle
├── validate.py                        # Validación de integridad
├── solution_to_rotation.py            # Convierte soluciones → rotaciones
│
├── 🎨 INTERFAZ INTERACTIVA
├── play.py                            # Juego manual: click para swaps, right-click para rotar
├── monitor.py                         # Monitor en tiempo real de archivos generados
├── remove_nonmatching.py              # Editor: remueve piezas mal colocadas
├── show_hints.py                      # Visualiza solo las piezas hint
├── show_pieces.py                     # Visualiza todas las piezas en orden
├── puzzle_generator.py                # Genera puzzles aleatorios
├── ui/
│   └── ui.py                          # Motor gráfico pygame
│
├── 📊 DATOS
├── data/
│   ├── eternity2/                     # Original Eternity II puzzle
│   ├── eternity2_like/                # Variantes generadas
│   ├── generated_*/                   # Puzzles de distintos tamaños
│   └── patterns/                      # Imágenes de patrones
│
├── 🔨 C/C++ (performance backend)
└── cpp/
    ├── backtracker/                   # Versión C++ del backtracking
    ├── swapper/                       # Versión C++ del swappings
    └── core/                          # Core C++
```

---

## 🔑 Módulos Core

### `core/defs.py` - Definiciones Base

**Clases principales:**

```python
class PieceDef:
    id: int                    # Identificador único
    colors: list[int]         # [E, S, W, N]
    type: int                 # TYPE_CORNER, TYPE_EDGE, TYPE_INNER
    
    def get_color(idx)        # Obtiene color en dirección idx

class PieceRef:
    piece_def: PieceDef       # Referencia a la pieza
    dir: int                  # Rotación (0-3)
    i, j: int                 # Posición en tablero
    
    def get_color(pos)        # Obtiene color efectivo considerando rotación

class PuzzleDefinition:
    height, width: int        # Dimensiones del tablero
    corners, edges, inner: list  # Piezas clasificadas
    all: dict                 # {id → PieceDef}
    hints: list               # [(i, j, id_pieza, rotación), ...]
    
    def load(filename, hints_file)  # Lee CSV
```

**Constantes de dirección:**
```python
E = 0  # East (Este)
S = 1  # South (Sur)
W = 2  # West (Oeste)
N = 3  # North (Norte)
```

---

### `core/board.py` - Tablero

**Clase Board:**

```python
class Board:
    puzzle_def: PuzzleDefinition
    board: list[list[PieceRef]]       # Tablero actual (2D)
    board_by_id: dict                 # {pieza_id → PieceRef}
    marks: list[list[int]]            # Anotaciones/números de piezas
    hints: list[list[PieceDef]]       # Posiciones fijas (no intercambiables)
    
    # Métodos principales
    def put_piece(i, j, piece_def, dir)  # Coloca pieza con rotación
    def load(filename)                    # Carga solución parcial desde CSV
    def save(filename)                    # Guarda estado actual
    def evaluate()                        # Calcula score (matches totales)
    def randomize()                       # Llena board con piezas aleatorias
    
    # Enumeradores
    def enumerate_corners()               # Itera (0,0), (0,w-1), (h-1,0), (h-1,w-1)
    def enumerate_edges()                 # Itera todos los bordes
    def enumerate_inner()                 # Itera interior
    def enumerate_neigbours(i,j)         # Itera vecinos de (i,j)
```

**Archivos CSV:**

**Formato de definición (puzzle definition):**
```
altura,ancho,colores_borde,colores_interior
id,E,S,W,N
1,0,0,6,0
2,0,2,7,0
...
```

**Formato de hints:**
```
fila,columna,id_pieza,rotación
0,0,139,0
1,1,245,2
...
```

**Formato de solución (board state):**
```
fila,columna,id_pieza,rotación
0,0,139,0
0,1,245,2
...
```

---

## 🎯 Scripts Principales

### 📊 Herramientas de Análisis

#### `analyze.py`
Analiza estadísticas del puzzle:
- Cuenta ocurrencias de cada color
- Detecta piezas duplicadas
- Analiza distribución de colores

```bash
python analyze.py -conf data/eternity2/eternity2_256.csv
```

#### `validate.py`
Valida integridad del puzzle:
- Verifica cada color aparece número par de veces
- Comprueba que colores de borde coincidan
- Lanza excepciones si hay problemas

```bash
python validate.py -conf data/eternity2/eternity2_256.csv
```

---

### 🎮 Interfaz Interactiva

#### `play.py` - Juego Manual
Resuelve el puzzle manualmente con interfaz gráfica.

```bash
python play.py -conf data/eternity2/eternity2_256.csv \
               -hints data/eternity2/eternity2_256_hints.csv
```

**Controles:**
- **Click izquierdo**: seleccionar pieza origen/destino para intercambio
- **Click derecho**: rotar pieza seleccionada
- **i**: mostrar/ocultar números de piezas

#### `show_pieces.py` - Galería de Piezas
Visualiza todas las piezas colocadas secuencialmente (ID 1, 2, 3...).

```bash
python show_pieces.py -conf data/eternity2/eternity2_256.csv
```

#### `show_hints.py` - Visor de Pistas
Muestra solo las piezas con hints colocadas y calcula score inicial.

```bash
python show_hints.py -conf data/eternity2/eternity2_256.csv \
                     -hints data/eternity2/eternity2_256_hints.csv
```

#### `monitor.py` - Monitor en Tiempo Real
Supervisa carpeta en busca de soluciones `.csv` nuevas y las carga automáticamente si mejoran el score.

```bash
python monitor.py -conf data/eternity2/eternity2_256.csv -dir ./solutions/
```

Útil para monitorear algoritmos de resolución que generan checkpoints periódicamente.

#### `remove_nonmatching.py` - Editor de Piezas Defectuosas
Carga una solución parcial y remueve piezas que no coinciden con sus vecinas (colores no iguales).

```bash
python remove_nonmatching.py -conf data/eternity2/eternity2_256.csv \
                             -load partial_solution.csv
```

---

### 🤖 Algoritmos de Resolución

#### `replacing.py` - Estrategia Constructiva (Greedy)
Coloca piezas una por una escogiendo la mejor orientación según puntuación.

**Flujo:**
1. Coloca una pieza disponible
2. Evalúa cada orientación
3. Calcula matches (recompensa) vs mismatches (penalización de vecinos afectados)
4. Repite hasta llenar tablero

#### `swapping.py` - Estrategia Local (Hill Climbing)
Intercambia piezas para mejorar score local iterativamente.

**Modos:**
- `QUICK_SWAPPING`: intercambios rápidos de dos piezas
- `RANDOM_SHUFFLING`: mezcla aleatoria de 5-10 piezas (mutación)
- `RANDOM_RECOVERING`: recuperación aleatoria

#### `backtracking.py` - Fuerza Bruta (Educativo)
Intenta todas las combinaciones con backtracking. **Muy lento** para 16×16 (y NP-completo).

```bash
python backtracking.py -conf data/eternity2/eternity2_256.csv \
                       -hints data/eternity2/eternity2_256_hints.csv
```

#### `genetic_algorithm.py` - Evolución Genética
Usa librería DEAP para evolucionar población de soluciones.

**Características:**
- Crossover y mutación genética
- Selección por fitness (score)
- Elitismo

---

### 🔧 Herramientas de Conversión

#### `solution_to_rotation.py`
Convierte una solución CSV en archivo de rotaciones.

```bash
python solution_to_rotation.py -conf data/eternity2/eternity2_256.csv \
                               -load solution.csv \
                               -save rotations.csv
```

Output: `(id_pieza, rotación)` para cada celda

---

## 🚀 Quickstart

### 1. Instalación

```bash
# Clonar repo
git clone <repo>
cd edge_puzzle

# Crear entorno virtual
python -m venv venv
source venv/Scripts/activate  # Windows: .\venv\Scripts\Activate.ps1

# Instalar dependencias
pip install -r requirements.txt
```

### 2. Primeros Pasos

```bash
# Ver galería de piezas
python show_pieces.py -conf data/eternity2/eternity2_256.csv

# Ver pistas colocadas
python show_hints.py -conf data/eternity2/eternity2_256.csv \
                     -hints data/eternity2/eternity2_256_hints.csv

# Jugar manualmente
python play.py -conf data/eternity2/eternity2_256.csv \
               -hints data/eternity2/eternity2_256_hints.csv

# Analizar puzzle
python analyze.py -conf data/eternity2/eternity2_256.csv
python validate.py -conf data/eternity2/eternity2_256.csv
```

### 3. Resolver Automáticamente

```bash
# En terminal 1: correr resolvedor
python replacing.py -conf data/eternity2/eternity2_256.csv ...

# En terminal 2: monitorear progreso
python monitor.py -conf data/eternity2/eternity2_256.csv -dir ./
```

---

## 📊 Flujos de Trabajo Típicos

### Flujo 1: Verificar e Investigar Puzzle Nuevo

```bash
# 1. Validar integridad
python validate.py -conf data/nuevo_puzzle.csv -hints hints.csv

# 2. Ver estadísticas
python analyze.py -conf data/nuevo_puzzle.csv

# 3. Visualizar
python show_pieces.py -conf data/nuevo_puzzle.csv
python show_hints.py -conf data/nuevo_puzzle.csv -hints hints.csv
```

### Flujo 2: Limpiar Solución Parcial

```bash
# 1. Cargar y visualizar solución parcial
python show_pieces.py -conf puzzle.csv -load partial.csv

# 2. Remover piezas mal colocadas
python remove_nonmatching.py -conf puzzle.csv -load partial.csv
# → Guarda como "manual_save_<uuid>_<score>.csv" en UI

# 3. Usar versión limpia como base para resolver
python replacing.py -conf puzzle.csv -load manual_save_*.csv
```

### Flujo 3: Resolver Interactivamente

```bash
# 1. Jugar manualmente
python play.py -conf puzzle.csv -hints hints.csv
# → Guardar con 's' key

# 2. Cargar solución parcial  
python replacing.py -conf puzzle.csv -load mi_solucion.csv

# 3. Monitorear mejoras
python monitor.py -conf puzzle.csv -dir ./solutions/
```

### Flujo 4: Comparar Algoritmos

```bash
# En terminal 1: Algoritmo A
python replacing.py -conf puzzle.csv ... > replacing_log.txt

# En terminal 2: Algoritmo B
python swapping.py -conf puzzle.csv ... > swapping_log.txt

# En terminal 3: Monitorear ambos
python monitor.py -conf puzzle.csv -dir ./
```

---

## 🏗️ Extender el Proyecto

### Agregar Nuevo Algoritmo de Resolución

1. **Crear `nuevo_algoritmo.py`:**
```python
from core import board
from core.defs import PuzzleDefinition

class MiResolvedor:
    def __init__(self, puzzle_def, hints=None):
        self.board = board.Board(puzzle_def)
        if hints:
            self.board.load(hints)
    
    def solve(self):
        # Tu lógica aquí
        pass
    
    def step(self):
        # Mejora incremental
        pass

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument('-conf', type=str, required=True)
    parser.add_argument('-hints', type=str, required=False, default=None)
    args = parser.parse_args()
    
    puzzle_def = PuzzleDefinition()
    puzzle_def.load(args.conf, args.hints)
    
    resolvedor = MiResolvedor(puzzle_def, args.hints)
    resolvedor.solve()
    resolvedor.board.save(f"solution_{uuid.uuid4()}.csv")
```

2. **Integrar monitoreo:**
```python
# Guardar checkpoints periódicamente
for step in range(iterations):
    resolvedor.step()
    if step % 100 == 0:
        score = resolvedor.board.evaluate()
        resolvedor.board.save(f"checkpoint_{step}_{score}.csv")
```

### Crear Variantes de Puzzle

```bash
python puzzle_generator.py \
  --height 16 \
  --width 16 \
  --edge-colors 5 \
  --inner-colors 17 \
  --output data/generated_16x16/16_16.csv
```

---

## 📈 Optimizaciones

### Performance Bottlenecks Conocidos

1. **Cálculo deScore**: Se evalúa en O(n) para todo el tablero
   - **Solución**: Evaluar solo deltas locales
   
2. **Búsqueda de Piezas**: Búsqueda lineal en dictionaries
   - **Solución**: Indexar por color/tipo

3. **Interfaz Gráfica**: Redibuja completo cada frame
   - **Solución**: Usar dirty-rect updates

### Backend C/C++

Para operaciones críticas, existe versión C++:

```bash
cd cpp/
cmake .
make
./backtracker -conf ../data/eternity2/eternity2_256.csv
```

---

## 🐛 Debugging

### Logs y Salida

```python
# Muestra info detallada
import logging
logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)
logger.debug(f"Pieza colocada: {i},{j} id={id} dir={dir}")
```

### Testear Piezas

```python
from core.defs import PieceDef, PieceRef

piece = PieceDef(139, 14, 8, 8, 9)
ref = PieceRef(piece, 1, 0, 0)  # Rotación 1

print(f"E color: {ref.get_color(0)}")  # Debería ser 14 rotado
```

### Validar Estado del Tablero

```python
def check_board_validity(board):
    score = board.evaluate()
    used_ids = set(board.board_by_id.keys())
    all_ids = set(board.puzzle_def.all.keys())
    
    # Verificaciones
    assert len(used_ids) <= len(all_ids), "IDs duplicados!"
    assert score <= board.max_score(), "Score imposible!"
    return score
```

---

## 📚 Referencias

- **Eternity II**: https://en.wikipedia.org/wiki/Eternity_II_puzzle
- **Pygame**: https://www.pygame.org/docs/
- **DEAP (Genético)**: https://deap.readthedocs.io/

---

## 🤝 Contribuciones

### Antes de Commit

1. Ejecutar validaciones:
```bash
python validate.py -conf data/eternity2/eternity2_256.csv
python analyze.py -conf data/eternity2/eternity2_256.csv
```

2. Testear interactivamente:
```bash
python play.py -conf data/eternity2/eternity2_256.csv \
               -hints data/eternity2/eternity2_256_hints.csv
```

3. Documentar cambios en el código

---

## 📝 FAQ

**P: ¿Por qué el backtracking es tan lento?**
R: Eternity II (16×16 con 256 piezas) es NP-completo. Hay 256! × 4^256 combinaciones posibles. Backtracking puro solo funciona para puzzles pequeños (<8x8).

**P: ¿Cuál algoritmo funciona mejor?**
R: Depende del puzzle. Para hints con estrategia de reemplazo (replacing) + hill climbing (swapping) da buenos resultados. Para optimización pura, genetic algorithm puede superar a otros.

**P: ¿Cómo exportar la solución final?**
R: El board guarda automáticamente con `board.save("solution.csv")`. Convertir a rotaciones con `solution_to_rotation.py`.

**P: ¿Puedo crear puzzles propios?**
R: Sí, usa `puzzle_generator.py` o crea CSV manualmente respetando el formato documentado.

**P: ¿Hay versión mobile/web?**
R: No en este momento. El proyecto es desktop (Pygame).

---

**Actualizado**: Abril 2026  
**Autor**: Alberto Piezas  
**Licencia**: [Especificar como sea necesario]

# Guía de Referencia Rápida

## ⚡ Comandos Esenciales

### Setup Inicial
```bash
# Crear entorno virtual
python -m venv venv
source venv/Scripts/activate  # Win: .\venv\Scripts\Activate.ps1

# Instalar dependencias
pip install -r requirements.txt
```

### Visualizar
```bash
# Ver todas las piezas
python show_pieces.py -conf data/eternity2/eternity2_256.csv

# Ver pistas colocadas
python show_hints.py -conf data/eternity2/eternity2_256.csv \
                     -hints data/eternity2/eternity2_256_hints.csv

# Jugar manualmente
python play.py -conf data/eternity2/eternity2_256.csv \
               -hints data/eternity2/eternity2_256_hints.csv
```

### Resolver Automáticamente
```bash
# Estrategia greedy (reemplazo)
python replacing.py -conf data/eternity2/eternity2_256.csv \
                    -hints data/eternity2/eternity2_256_hints.csv

# Estrategia local (hill climbing via swaps)
python swapping.py -conf data/eternity2/eternity2_256.csv \
                   -hints data/eternity2/eternity2_256_hints.csv

# Monitorear en tiempo real
python monitor.py -conf data/eternity2/eternity2_256.csv -dir ./
```

### Analizar
```bash
# Estadísticas del puzzle
python analyze.py -conf data/eternity2/eternity2_256.csv

# Validar integridad
python validate.py -conf data/eternity2/eternity2_256.csv
```

### Procesar Soluciones
```bash
# Limpiar solución parcial (remover piezas mal colocadas)
python remove_nonmatching.py -conf data/eternity2/eternity2_256.csv \
                             -load partial_solution.csv

# Convertir solución a rotaciones
python solution_to_rotation.py -conf data/eternity2/eternity2_256.csv \
                               -load solution.csv \
                               -save rotations.csv
```

---

## 🎮 Controles UI (play.py)

| Tecla | Función |
|-------|---------|
| Click izq | Seleccionar pieza origen/destino (swap) |
| Click der | Rotar pieza seleccionada |
| **i** | Mostrar/ocultar números de piezas |
| **s** | Guardar solución manual |

---

## 📊 Estructura CSV

### Definición de Puzzle
```csv
altura,ancho,colores_borde,colores_interior
1,0,0,6,0
2,0,2,7,0
3,0,1,8,0
...
```

**Encabezado:** `altura,ancho,num_colores_borde,num_colores_interior`  
**Líneas:** `id,E,S,W,N`
- `id`: 1 a (width × height)
- `E,S,W,N`: colores en derecha, abajo, izquierda, arriba
- `0`: gris (borde exterior)

### Hints (Pistas)
```csv
fila,columna,id_pieza,rotación
0,0,139,0
1,1,245,2
```

**Formato:** `fila,columna,id_pieza,rotación`
- `fila, columna`: posición (0-indexed)
- `id_pieza`: ID en rango [1, width×height]
- `rotación`: 0-3 (número de giros 90° CW)

### Solución (Board State)
```csv
fila,columna,id_pieza,rotación
0,0,139,0
0,1,245,2
1,0,67,1
```

**Idéntico a hints, pero puede tener múltiples piezas**

---

## 🔢 Direcciones y Rotaciones

### Dirección Cardinal (Constants)
```python
E = 0  # East (Este)
S = 1  # South (Sur)
W = 2  # West (Oeste)
N = 3  # North (Norte)
```

### Cálculo de Color con Rotación
```
Piece colors original: [E_orig, S_orig, W_orig, N_orig]
dir = 1 (rotación 90° CW)

get_color(E):  idx = (E - dir) % 4 = (0 - 1) % 4 = 3 → N_orig
get_color(S):  idx = (S - dir) % 4 = (1 - 1) % 4 = 0 → E_orig
get_color(W):  idx = (W - dir) % 4 = (2 - 1) % 4 = 1 → S_orig
get_color(N):  idx = (N - dir) % 4 = (3 - 1) % 4 = 2 → W_orig
```

### Tipos de Pieza
```python
TYPE_CORNER = 0  # 2 grises (colores 0)
TYPE_EDGE = 1    # 1 gris
TYPE_INNER = 2   # 0 grises
```

---

## 📁 Rutas de Datos

### Puzzles Predefinidos
```
data/
  eternity2/
    eternity2_256.csv              ← Puzzle original (16×16, 256 piezas)
    eternity2_256_hints.csv        ← Pistas del original
```

### Puzzles Generados
```
data/
  generated_NxN/
    N_N_edge*_inner*.csv
    N_N_edge*_inner*_solution.csv
    N_N_edge*_inner*_hint_corner.csv
```

### Variantes Eternity-like
```
data/
  eternity2_like/
    6by6/
    7by7/
    ...
    16by16/
```

---

## 🐛 Debugging Rápido

### Test de Pieza
```python
from core.defs import PieceDef, PieceRef

piece = PieceDef(1, 14, 8, 8, 9)
ref = PieceRef(piece, 1, 0, 0)  # id=1, E=14, S=8, W=8, N=9, dir=1 (rotado)

print(f"Color en E: {ref.get_color(0)}")  # Debería ser 9 (N original)
print(f"Color en S: {ref.get_color(1)}")  # Debería ser 14 (E original)
```

### Test de Tablero
```python
from core.defs import PuzzleDefinition
from core.board import Board

puzzle_def = PuzzleDefinition()
puzzle_def.load("data/eternity2/eternity2_256.csv", "data/eternity2/eternity2_256_hints.csv")

board = Board(puzzle_def)
board.randomize()

print(f"Score: {board.evaluate()}")
print(f"Max score: {board.max_score()}")
```

### Verificar Estado
```python
# Verificar integridad de board
assert len(board.board_by_id) <= len(board.puzzle_def.all)
assert board.evaluate() <= board.max_score()

# Voltear entre dos IDs en board_by_id
ref1 = board.board_by_id[139]
print(f"Pieza 139 en ({ref1.i}, {ref1.j}) con rotación {ref1.dir}")
```

---

## 📊 Métricas Clave

### Score (Puntuación)
```
Definición: Número de matches (bordes coincidentes) entre piezas adyacentes
Máximo: width × (height - 1) + height × (width - 1)
Para 16×16: 480 matches

Fórmula puzzle 16×16:
  max_score = 16 × 15 + 16 × 15 = 240 + 240 = 480
```

### Tipos de Pieza (Eternity II 16×16)
```
Corners: 4
Edges: 2 × (16 - 2) + 2 × (16 - 2) = 2 × 14 + 2 × 14 = 56
Inner: (16 - 2) × (16 - 2) = 14 × 14 = 196
Total: 4 + 56 + 196 = 256 ✓
```

---

## 🚀 Flujos de Trabajo Típicos

### Workflow A: Resolver Desde Cero
```bash
# 1. Validar
python validate.py -conf puzzle.csv

# 2. Resolver (elegir estrategia)
python replacing.py -conf puzzle.csv -hints hints.csv
# o
python swapping.py -conf puzzle.csv -hints hints.csv

# 3. Monitorear en otra terminal
python monitor.py -conf puzzle.csv -dir ./
```

### Workflow B: Mejorar Solución Existente
```bash
# 1. Cargar solución parcial
python show_pieces.py -conf puzzle.csv -load partial.csv

# 2. Limpiar piezas defectuosas (si es necesario)
python remove_nonmatching.py -conf puzzle.csv -load partial.csv

# 3. Usar como base para resolver
python replacing.py -conf puzzle.csv -load cleaned.csv -hints hints.csv
```

### Workflow C: Análisis Exploratorio
```bash
# 1. Ver estructura
python show_pieces.py -conf puzzle.csv
python analyze.py -conf puzzle.csv

# 2. Jugar manualmente para entender
python play.py -conf puzzle.csv -hints hints.csv

# 3. Guardar intento (key 's')
# Luego usar esa solución como base para algoritmos
```

---

## 🔍 Búsqueda Rápida de Problemas

| Problema | Causa Probable | Solución |
|----------|-----------------|----------|
| "ID already on board!" | Pieza duplicada | Verificar puzzle.csv, remover duplicados |
| Score no mejora | Mínimo local | Activar `RANDOM_SHUFFLING` en swapper |
| CSV no carga | Formato incorrecto | Verificar: `i,j,id,dir` separado por comas |
| UI congela | Solver en main thread | Usar `monitor.py` en terminal separada |
| Color mismatch | Rotación incorrecta | Verificar dirección en hints CSV |
| Validación falla | Puzzle inválido | Correr `analyze.py` para diagnosticar |

---

## 🎓 Ejemplos Mínimos

### Crear Tablero y Colocar Pieza
```python
from core.defs import PuzzleDefinition, PieceRef, E, S, W, N
from core.board import Board

# Cargar
puzzle_def = PuzzleDefinition()
puzzle_def.load("data/eternity2/eternity2_256.csv")
board = Board(puzzle_def)

# Colocar esquina
corner_piece = puzzle_def.corners[0]
board.put_piece(0, 0, corner_piece, E)

# Colocar borde
edge_piece = puzzle_def.edges[0]
board.put_piece(0, 1, edge_piece, E)

# Colocar interior
inner_piece = puzzle_def.inner[0]
board.put_piece(1, 1, inner_piece, 0)

# Evaluar
score = board.evaluate()
print(f"Score: {score}")
```

### Iterar Posiciones por Tipo
```python
board = Board(puzzle_def)

# Esquinas
for i, j in board.enumerate_corners():
    print(f"Corner at ({i}, {j})")

# Bordes
for i, j in board.enumerate_edges():
    print(f"Edge at ({i}, {j})")

# Interior
for i, j in board.enumerate_inner():
    print(f"Inner at ({i}, {j})")
```

### Guardar y Cargar
```python
# Guardar
board.save("mi_solucion.csv")

# Cargar en nuevo tablero
board2 = Board(puzzle_def)
board2.load("mi_solucion.csv")
```

---

## 🔗 Relaciones de Módulos

```
CLI User
  ↓
play.py (UI Input)
  ├→ ui.py (Rendering)
  ├→ board.py (Logic)
  └→ core.defs (Data)

Resolver (replacing.py, swapping.py, ...)
  ├→ board.py (State management)
  ├→ core.defs (Read puzzle definition)
  └→ [saves CSV] → monitor.py (can reload)

Analyzer (analyze.py, validate.py)
  └→ core.defs (Read only)
```

---

## 📈 Escalabilidad Observada

| Tamaño | Piezas | Aproximación | Tiempo (replacing) |
|--------|--------|--------------|-------------------|
| 3×3 | 9 | Trivial | <1s |
| 4×4 | 16 | Fácil | <1s |
| 6×6 | 36 | Posible | ~10s |
| 8×8 | 64 | Difícil | ~60s |
| 10×10 | 100 | Muy difícil | ~5min |
| 16×16 | 256 | Intractable (sin hints) | → ∞ |

*Con hints, 16×16 (Eternity II) es resoluble con buena estrategia*

---

## 💾 Archivos Importantes

| Archivo | Propósito | ¿Editar? |
|---------|-----------|----------|
| `core/defs.py` | Definiciones core | ⚠️ Solo si cambio formato CSV |
| `core/board.py` | Lógica tablero | ⚠️ Crítico, cambios → verificar |
| `replacing.py` | Resolvedor | ✅ Extensible |
| `swapping.py` | Resolvedor local | ✅ Extensible |
| `ui/ui.py` | Rendering | ℹ️ para cambios visuales |
| Data CSV | Puzzles | ✅ Crear nuevos puzzles |

---

## 🔐 Invariantes del Sistema

```
1. len(board_by_id) <= len(puzzle_def.all)
   (No hay IDs duplicadas)

2. board.evaluate() <= board.max_score()
   (Score nunca excede máximo teórico)

3. Para cada hint en puzzle_def.hints:
   - board.hints[i][j] contiene la pieza indicada
   - No puede intercambiarse
   
4. Cada PieceRef.dir ∈ {0, 1, 2, 3}
   (Válid rotation)

5. get_color debe respetar: idx = (pos - dir) % 4
   (Rotación consistente)
```

---

## 📚 Recursos Externos

- **Eternity II Wikipedia**: https://en.wikipedia.org/wiki/Eternity_II_puzzle
- **Pygame Docs**: https://www.pygame.org/docs/
- **DEAP Genetic Algorithms**: https://deap.readthedocs.io/
- **NP-Completeness**: https://en.wikipedia.org/wiki/NP-completeness

---

**Última actualización**: Abril 2026  
**Versión**: 1.0

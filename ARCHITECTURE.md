# Guía de Arquitectura y Desarrollo

## 🏗️ Arquitectura General

```
┌─────────────────────────────────────────────────────────────┐
│                        UI LAYER (Pygame)                     │
│  play.py, show_*, monitor.py, remove_nonmatching.py         │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────┴──────────────────────────────────────┐
│                   SOLVER LAYER                              │
│  replacing.py, swapping.py, backtracking.py, genetic_*.py   │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────┴──────────────────────────────────────┐
│                    CORE LAYER                               │
│  Board (board.py) ↔ PuzzleDefinition (defs.py)             │
│  - Gestión de estado del tablero                           │
│  - Operaciones de piezas y posiciones                      │
│  - Cálculo de score                                        │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────┴──────────────────────────────────────┐
│                    DATA LAYER                               │
│  CSV files, hints, puzzles, solutions                      │
└─────────────────────────────────────────────────────────────┘
```

---

## 🔍 Detalle de Módulos

### core/defs.py - Estado del Puzzle (Inmutable)

**Responsabilidades:**
- Representar definición estática del puzzle
- Clasificar piezas por tipo
- No modificar estado (solo lectura)

**Clases:**

```python
class PieceDef:
    """Definición de una pieza (propiedades invariables)"""
    - id: int                           # ID único (1 a width×height)
    - colors: list[int]                 # [E, S, W, N] en rotación 0
    - type: int                         # TYPE_CORNER, TYPE_EDGE, TYPE_INNER
    
    @property
    def get_color(idx: int) → int      # idx ∈ {E, S, W, N}

class PieceRef:
    """Referencia posicionada de una pieza (estado útil para tablero)"""
    - piece_def: PieceDef              # Referencia a definición base
    - dir: int                          # Rotación actual (0-3)
    - i, j: int                         # Posición en tablero
    
    def get_color(pos: int) → int      # Considera rotación
        → idx = (pos - dir) % 4
        → return piece_def.colors[idx]

class PuzzleDefinition:
    """Definición completa del puzzle (inmutable)"""
    - all: dict[int, PieceDef]         # {id → pieza}
    - corners: list[PieceDef]          # Las 4 esquinas
    - edges: list[PieceDef]            # N-2 × 2 + 2 bordes
    - inner: list[PieceDef]            # (H-2) × (W-2) interiores
    - hints: list[(i, j, id, dir)]     # Posiciones fijas
    - height, width: int
    
    def load(filename: str, hints: str = None)  # CSV → estructura
```

**Invariantes:**
- `len(corners) == 4`
- `len(edges) == 2×(height-2) + 2×(width-2)`
- `len(inner) == (height-2) × (width-2)`
- `sum(colores_sin_gris) % 2 == 0` (validación)

---

### core/board.py - Estado Mutable del Tablero

**Responsabilidades:**
- Mantener estado actual (pieza en cada posición)
- Calcular score (matches)
- Soportar operaciones: put, load, save, rotate, swap
- Enumerar posiciones por tipo

**Clases:**

```python
class Board:
    """Tablero mutable - estado actual de la solución"""
    
    # Estado
    - puzzle_def: PuzzleDefinition     # Definición (inmutable)
    - board: list[list[PieceRef]]      # Grid [i][j] con piezas
    - board_by_id: dict[int, PieceRef] # Índice rápido por ID
    - marks: list[list[int]]           # Anotaciones (números visibles)
    - hints: list[list[PieceDef]]      # Posiciones fijas (no intercambiables)
    
    # Operaciones
    put_piece(i, j, piece_def: PieceDef, dir: int)
        → Coloca pieza, actualiza board y board_by_id
        → Lanza excepción si ID ya existe
    
    load(filename: str)
        → Lee CSV de solución parcial
        → Formato: i,j,id,dir
    
    save(filename: str)
        → Escribe CSV de estado actual
        → Omite posiciones vacías
    
    evaluate() → int
        → Cuenta matches totales
        → Para cada par adyacente: colors igual → +1
        → O(height × width)
    
    max_score() → int
        → Máximo matches posible
        → = height × (width-1) + width × (height-1)
    
    # Enumeradores (generadores)
    enumerate_corners() → [(i,j), ...]  # Las 4 esquinas
    enumerate_edges() → [(i,j), ...]    # Todos los bordes
    enumerate_inner() → [(i,j), ...]    # Todo el interior
    enumerate_neigbours(i,j) → [PieceRef]  # Vecinos directos
```

**Operación Crítica: Cálculo de Score**

```python
def evaluate(self):
    score = 0
    for i in range(height):
        for j in range(width):
            if board[i][j] is None:
                continue
            
            # Vecino a la derecha
            if j+1 < width and board[i][j+1]:
                if board[i][j].get_color(E) == board[i][j+1].get_color(W):
                    score += 1
            
            # Vecino abajo
            if i+1 < height and board[i+1][j]:
                if board[i][j].get_color(S) == board[i+1][j].get_color(N):
                    score += 1
    
    return score
```

**⚠️ Oportunidad de Optimización:** 
- Está en O(n × 4 direcciones)
- Para incrementos, solo evaluar deltas de piezas afectadas
- Caché parciales de score por región

---

## 🤖 Patrón de Solvers

Todos los solvers heredan este patrón:

```python
class MiResolver:
    def __init__(self, puzzle_def: PuzzleDefinition, hints: str = None):
        self.puzzle_def = puzzle_def
        self.board = Board(puzzle_def)
        if hints:
            self.board.load(hints)
        self.state = {}  # Estado específico del algoritmo

    def initialize(self):
        """Configuración inicial, análisis previo, etc."""
        pass

    def step(self):
        """Una iteración pequeña del algoritmo"""
        # Modificar board, mejorar score
        pass

    def solve(self):
        """Loop principal - puede ser sync o async"""
        while not converged():
            self.step()
            if improved():
                self.checkpoint()

    def checkpoint(self) -> float:
        """Guardar progreso, retornar nuevo score"""
        score = self.board.evaluate()
        self.board.save(f"solution_{uuid.uuid4()}_{score}.csv")
        return score
```

### Replacing.py - Estudio de Caso

**Estrategia:** Construcción incremental (Forward Checking)

```python
class Replacer:
    def __init__(self, board):
        self.board = board
        self.unplaced = deque(all_pieces)  # Cola de piezas disponibles
        random.shuffle(self.unplaced)
        
        # Orientaciones permitidas por posición
        self.orientations[corner] = [single_dir]    # Esquina: 1 orientación
        self.orientations[edge] = [single_dir]       # Borde: 1 orientación
        self.orientations[inner] = [0,1,2,3]        # Interior: 4 orientaciones

    def step(self):
        if not unplaced: return  # Done
        
        # Buscar mejor lugar + orientación para próxima pieza
        for piece in unplaced:
            for i,j in all_positions:
                if occupied[i][j]: continue
                
                best_match = self._score_piece(piece, i, j)
                if best_match best_so_far:
                    best_so_far = best_match
                    best_pos = (i, j)
        
        # Colocar mejor opción
        self.board.put_piece(*best_pos, best_piece, best_dir)
        self.unplaced.remove(best_piece)
    
    def _score_piece(self, piece, i, j):
        """Utilidad de colocar pieza en (i,j)"""
        for dir in allowed_orientations[i][j]:
            for neighbor_pos in neighbors(i, j):
                neighbor = board[neighbor_pos]
                if neighbor:
                    if colors_match(piece, dir, neighbor):
                        score += 1
                    else:
                        penalty += cascade_effect(neighbor_pos)
        
        return score - penalty
```

**Complejidad:**
- **Tiempo**: O(n × m × p × d) donde n,m=dimensiones, p=piezas, d=direcciones
- **Espacio**: O(n × m) para board
- **Converge**: Sí, termina cuando todas las piezas colocadas

---

### Swapping.py - Estudio de Caso

**Estrategia:** Local Search / Hill Climbing

```python
class Swapper:
    MODES = [QUICK_SWAPPING, RANDOM_SHUFFLING, RANDOM_RECOVERING]
    
    def __init__(self, board):
        self.board = board
        self.max_score = board.evaluate()
        self.state = QUICK_SWAPPING

    def step(self):
        if self.state == QUICK_SWAPPING:
            # Intenta swaps locales inteligentes
            self._quick_swap()
        elif self.state == RANDOM_SHUFFLING:
            # Mezcla aleatoria (escape local mínima)
            self._shuffle()
        else:  # RANDOM_RECOVERING
            # Recuperación de solución anterior
            self._recover()

    def _quick_swap(self):
        """Busca par de piezas cuyo swap mejora score"""
        for (i1,j1), (i2,j2) in combinations(all_positions, 2):
            if hints_locked(i1,j1) or hints_locked(i2,j2):
                continue
            
            # Swap virtual
            score_delta = calculate_delta_swap(...)
            
            if score_delta > 0:
                # Efectuar swap
                board[i1][j1], board[i2][j2] = board[i2][j2], board[i1][j1]
                new_score = board.evaluate()
                
                if new_score > self.max_score:
                    self.max_score = new_score
                    return  # Encontrado, volver a intentar
                else:
                    # Revertir si no fue beneficioso finalmente
                    board[i1][j1], board[i2][j2] = board[i2][j2], board[i1][j1]

    def _shuffle(self):
        """Inyectar entropía para escapar mínimas locales"""
        random_inner_pieces = random.sample(inner_positions, k=5)
        refs = [board[i][j] for i,j in random_inner_pieces]
        random.shuffle(refs)
        # Redistribuir refs aleatoriamente
```

**Complejidad:**
- **Tiempo por step**: O(n²) en worst case (todos pares)
- **Espacio**: O(1) extra
- **Convergencia**: No garantizada, puede oscilar

**Ventajas:**
- Escape de mínimas locales via shuffling
- Rápido para mejoras locales

---

## 📊 Flujos de Datos

### Flujo 1: Cargar y Visualizar

```
CSV file (.../eternity2_256.csv)
    ↓
PuzzleDefinition.load()
    ├→ parse header (height, width)
    ├→ parse pieces (id, E, S, W, N)
    ├→ classify by type (corner, edge, inner)
    └→ PuzzleDefinition populated

Board.init(puzzle_def)
    └→ 2D grid initialized

UI.render()
    ├→ iterate board[i][j]
    ├→ fetch PieceRef at position
    ├→ get_color(dir) considering rotation
    └→ draw pattern image
```

### Flujo 2: Colocar Pieza

```
Resolver.step()
    └→ Select piece P, position (i,j), orientation d

Board.put_piece(i, j, P, d)
    ├→ Create PieceRef(P, d, i, j)
    ├→ board[i][j] = PieceRef
    ├→ board_by_id[P.id] = PieceRef
    └→ (Exception if P.id duplicate)

Resolver.checkpoint()
    └→ Board.evaluate()
        ├→ iterate all board[i][j]
        ├→ for each placed piece, check neighbors
        │   ├→ get_color(E) == neighbor.get_color(W) → +1
        │   └─→ get_color(S) == neighbor.get_color(N) → +1
        └→ return total matches

Board.save(filename)
    ├→ iterate board[i][j]
    ├→ write i,j,PieceRef.piece_def.id,PieceRef.dir
    └→ save CSV
```

### Flujo 3: Intercambiar Piezas

```
Swapper.step() → _quick_swap()
    ├→ Select pieces at (i1,j1) and (i2,j2)
    ├→ Calculate score delta for swap
    │   ├→ Remove current pieces from scoring
    │   ├→ Place swapped
    │   └→ Recalculate affected neighbors only
    └→ If delta > 0: execute swap, update board
```

---

## 🔧 Extensibilidad

### Agregar Nuevo Solver

1. **Herencia de patrón:**
```python
class MiNuevoSolver:
    def __init__(self, puzzle_def, hints=None):
        self.board = Board(puzzle_def)
        if hints: self.board.load(hints)
        self.history = []

    def initialize(self):
        """Análisis previo, índices, etc."""
        self._build_color_index()
        self._precompute_constraints()

    def step(self):
        """Una iteración"""
        candidate = self._select_next_move()
        if candidate.score_delta > 0:
            self._apply_move(candidate)
            self.history.append(candidate)

    def solve(self, max_iterations=None):
        for _ in range(max_iterations or float('inf')):
            self.step()
            if self.converged():
                break

if __name__ == '__main__':
    puzzle_def = PuzzleDefinition()
    puzzle_def.load(...)
    solver = MiNuevoSolver(puzzle_def, hints=...)
    solver.initialize()
    solver.solve()
    solver.board.save(f"solution_{uuid.uuid4()}.csv")
```

2. **Integración con UI (opcional):**
```python
# En play.py o monitor.py:
from mi_nuevo_solver import MiNuevoSolver

solver = MiNuevoSolver(board.puzzle_def)
while True:
    solver.step()
    ui.board = solver.board
    ui.update()
```

### Agregar Métrica de Análisis

```python
# nuevo_metric.py
from core import board
from core.defs import PuzzleDefinition

def analyze_color_distribution(puzzle_def):
    """Calcula entropía de distribución de colores"""
    color_counts = Counter()
    for piece in puzzle_def.all.values():
        for color in piece.colors:
            if color != 0:  # No contar grises
                color_counts[color] += 1
    
    total = sum(color_counts.values())
    entropy = -sum((c/total) * log2(c/total) for c in color_counts.values())
    return entropy

if __name__ == '__main__':
    puzzle_def = PuzzleDefinition()
    puzzle_def.load(...)
    print(f"Color entropy: {analyze_color_distribution(puzzle_def)}")
```

---

## ⚡ Optimizaciones Implementables

### 1. Incremental Score Calculation

**Sitio:** `Board.evaluate()`  
**Problema:** O(n²) recalcula todo siempre  
**Solución:**

```python
class Board:
    def __init__(self, puzzle_def):
        # ... existente ...
        self.score_cache = 0
        self.dirty_regions = set()

    def put_piece(self, i, j, piece, dir):
        # ... existente ...
        
        # Marcar región como sucia
        self.dirty_regions.update([
            (i, j), (i-1, j), (i+1, j), (i, j-1), (i, j+1)
        ])

    def evaluate_delta(self):
        """Solo recalcular regiones sucias"""
        delta = 0
        for (i, j) in self.dirty_regions:
            if 0 <= i < height and 0 <= j < width:
                delta += self._score_position(i, j)
        
        self.score_cache += delta
        self.dirty_regions.clear()
        return self.score_cache
```

### 2. Color Index

**Sitio:** Búsqueda de píezas compatibles  
**Problema:** Para cada posición, itera todos los colores  
**Solución:**

```python
class ColorIndex:
    def __init__(self, puzzle_def):
        # {color → [piece_with_color]}
        self.by_color = defaultdict(list)
        for piece in puzzle_def.all.values():
            for color in piece.colors:
                if color != 0:
                    self.by_color[color].append(piece)

    def find_matching(self, required_color, direction):
        """Retorna piezas que pueden tener color en direction"""
        return self.by_color[required_color]
```

### 3. Constraint Propagation

**Sitio:** Reducir búsqueda en backtrackers  
**Problema:** Explosión combinatoria  
**Solución:**

```python
def propagate_constraints(board, i, j):
    """Reduce domains de vecinos basado en colocación en (i,j)"""
    placed = board[i][j]
    
    for neighbor_pos in neighbors(i, j):
        if board[neighbor_pos]:
            continue  # Ya tiene pieza
        
        # Filtrar piezas no compatibles
        required_color = get_required_color(placed, neighbor_pos)
        feasible = [p for p in unplaced if has_color(p, required_color)]
        
        constraints[neighbor_pos] = feasible
```

---

## 🧪 Testing

### Test Básico de Componentes

```python
# test_core.py
from core.defs import PieceDef, PieceRef, PuzzleDefinition
from core.board import Board

def test_piece_rotation():
    piece = PieceDef(1, E=1, S=2, W=3, N=4)
    ref = PieceRef(piece, dir=1, i=0, j=0)
    
    assert ref.get_color(0) == 4  # E rotado → N original
    assert ref.get_color(1) == 1  # S rotado → E original
    print("✓ Piece rotation works")

def test_board_score():
    puzzle_def = PuzzleDefinition()
    puzzle_def.load("data/eternity2/eternity2_256.csv")
    
    board = Board(puzzle_def)
    board.randomize()
    
    score = board.evaluate()
    assert 0 <= score <= board.max_score()
    print(f"✓ Board score valid: {score}/{board.max_score()}")

def test_save_load():
    board1.save("test.csv")
    board2 = Board(puzzle_def)
    board2.load("test.csv")
    
    assert board1.evaluate() == board2.evaluate()
    print("✓ Save/load roundtrip works")
```

### Benchmarking

```python
# benchmark.py
import time
from replacing import Replacer
from swapping import Swapper

puzzle_def = PuzzleDefinition()
puzzle_def.load("data/eternity2/eternity2_256.csv", hints=...)

solvers = [
    ("Replacing", Replacer),
    ("Swapping", Swapper),
]

for name, SolverClass in solvers:
    solver = SolverClass(puzzle_def)
    
    start = time.time()
    solver.solve(max_iterations=10000)
    elapsed = time.time() - start
    
    score = solver.board.evaluate()
    print(f"{name}: {score} score in {elapsed:.2f}s")
```

---

## 📋 Checklist para Contribuidores

- [ ] Código respeta convención Python (PEP 8)
- [ ] Clases heredan el patrón Resolver si es solver
- [ ] Operaciones son O(n) o mejor si afectan score
- [ ] Board.save() genera CSV compatible
- [ ] Prueba manualmente con `python play.py`
- [ ] Prueba con `python validate.py`
- [ ] Actualiza DOCUMENTATION.md
- [ ] Agrega tests si es lógica crítica

---

## 📞 Contacto & Issues Comunes

**P: Mi solver es lento**
- Verificar que no recalcule score innecesariamente
- Usar profiler: `python -m cProfile -s cumtime my_solver.py ...`

**P: "ID already on board" error**
- Una pieza fue colocada 2 veces simultáneamente
- Verificar lógica de deduplicación

**P: UI se congela**
- El solver está en main thread
- Mover solver a thread separado (ver monitor.py)

**P: CSV no carga**
- Verificar formato: `i,j,piece_id,rotation`
- Verificar IDs coinciden con puzzle_def.all

---

**Última actualización**: Abril 2026

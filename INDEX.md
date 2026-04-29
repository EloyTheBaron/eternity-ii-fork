# 📑 Índice de Documentación

## 🚀 ¿Por Dónde Empiezo?

### Para nuevos usuarios (10 minutos)
1. Lee [GETTING_STARTED.md](GETTING_STARTED.md)
2. Ejecuta: `python show_pieces.py -conf data/eternity2/eternity2_256.csv`
3. Luego: `python play.py -conf data/eternity2/eternity2_256.csv -hints data/eternity2/eternity2_256_hints.csv`

### Para usuarios experimentados (5 minutos)
1. Consulta [QUICKREF.md](QUICKREF.md)
2. Selecciona el comando que necesitas
3. Ejecuta

### Para desarrolladores (1 hora)
1. Lee [DOCUMENTATION.md](DOCUMENTATION.md) - Conceptos generales
2. Lee [ARCHITECTURE.md](ARCHITECTURE.md) - Detalles de diseño
3. Modifica ejemplos en los archivos

---

## 📚 Documentos Disponibles

### [README.md](README.md) - Portal Principal
**¿Qué es?** Punto de entrada general  
**Usa para:** Visión general del proyecto, links a documentación  
**Tiempo:** 5 min  
**Audiencia:** Todos

### [GETTING_STARTED.md](GETTING_STARTED.md) - Primeros Pasos
**¿Qué es?** Guía paso a paso para novatos  
**Usa para:**
- Instalación completa
- Tu primer comando
- Entender la pantalla
- Ejercicios progresivos de 5 a 10 minutos
- FAQ básicas

**Tiempo:** 10-15 min  
**Audiencia:** Nuevos usuarios, principiantes  
**Contiene:**
- ✅ Instalación (5 pasos)
- ✅ Primeros comandos
- ✅ Ejercicios progresivos
- ✅ Solución de problemas comunes

### [QUICKREF.md](QUICKREF.md) - Referencia Rápida
**¿Qué es?** Tiraditas y atajos sin explicación larga  
**Usa para:**
- Acordarse un comando
- Buscar controles de UI
- Ver formato CSV
- Direcciones de datos
- Tabla de búsqueda de problemas

**Tiempo:** 5 min (consulta)  
**Audiencia:** Usuarios que ya conocen el proyecto  
**Contiene:**
- ✅ Todos los comandos esenciales
- ✅ Controles UI
- ✅ Formatos CSV
- ✅ Tabla de troubleshooting
- ✅ Ejemplos mínimos de código
- ✅ Invariantes del sistema

### [DOCUMENTATION.md](DOCUMENTATION.md) - Guía Completa
**¿Qué es?** Documentación exhaustiva del proyecto  
**Usa para:**
- Entender conceptos a fondo
- Conocer todos los scripts
- Aprender flujos de trabajo
- Saber cómo extender

**Tiempo:** 30-45 min (lectura)  
**Audiencia:** Desarrolladores, investigadores  
**Contiene:**
- ✅ Conceptos fundamentales (piezas, rotaciones, score)
- ✅ Estructura COMPLETA del directorio
- ✅ Detalle de cada módulo core
- ✅ Documentación de TODOS los scripts
- ✅ Ejemplos de uso de cada uno
- ✅ 5 flujos de trabajo típicos
- ✅ Cómo extender (nuevos algoritmos, análisis)
- ✅ Optimizaciones posibles
- ✅ Debugging tips
- ✅ FAQ completa

### [ARCHITECTURE.md](ARCHITECTURE.md) - Arquitectura Técnica
**¿Qué es?** Diseño interno y detalles de implementación  
**Usa para:**
- Entender el código en detalle
- Modificar un solver existente
- Crear un solver nuevo
- Optimizaciones avanzadas
- Debugging técnico

**Tiempo:** 45-60 min (lectura profunda)  
**Audiencia:** Desarrolladores, contribuuidores  
**Contiene:**
- ✅ Diagrama de arquitectura en capas
- ✅ Responsabilidades de cada módulo
- ✅ Patrón de diseño de Solvers
- ✅ Análisis de complejidad
- ✅ Flujos de datos (4 ejemplos)
- ✅ Extensibilidad detallada
- ✅ Optimizaciones implementables
- ✅ Testing basado en componentes
- ✅ Benchmarking
- ✅ Checklist para contributors

---

## 🎯 Por Caso de Uso

### "Quiero jugar/explorar el puzzle"
1. [GETTING_STARTED.md](GETTING_STARTED.md#-primeros-pasos-tu-primer-comando-2-minutos)
2. [QUICKREF.md - Controles UI](QUICKREF.md#-controles-ui-playpy)
3. [README.md - Flujo B](README.md#2-jugar-manualmente)

### "Quiero resolver automáticamente"
1. [GETTING_STARTED.md - Ejercicio 3](GETTING_STARTED.md#ejercicio-3-resolver-automáticamente)
2. [DOCUMENTATION.md - Algoritmos](DOCUMENTATION.md#-algoritmos-de-resolución)
3. [QUICKREF.md - Comandos](QUICKREF.md#-comandos-esenciales)

### "Quiero mejorar una solución existente"
1. [README.md - Flujo 4](README.md#4-mejorar-solución-existente)
2. [DOCUMENTATION.md - Flujo 2](DOCUMENTATION.md#flujo-2-limpiar-solución-parcial)
3. [ARCHITECTURE.md - Swapping](ARCHITECTURE.md#swappingpy---estudio-de-caso)

### "Quiero agregar mi propio resolvedor"
1. [ARCHITECTURE.md - Patrón](ARCHITECTURE.md#-patrón-de-solvers)
2. [ARCHITECTURE.md - Extensibilidad](ARCHITECTURE.md#-extensibilidad)
3. [DOCUMENTATION.md - Extender](DOCUMENTATION.md#-extender-el-proyecto)

### "Quiero entender cómo funciona internamente"
1. [ARCHITECTURE.md - Core](ARCHITECTURE.md#core_defspy---estado-del-puzzle-inmutable)
2. [ARCHITECTURE.md - Board](ARCHITECTURE.md#core_boardpy---estado-mutable-del-tablero)
3. [ARCHITECTURE.md - Flujos](ARCHITECTURE.md#-flujos-de-datos)

### "Tengo un problema/error"
1. [GETTING_STARTED.md - Troubleshooting](GETTING_STARTED.md#-algo-salió-mal)
2. [QUICKREF.md - Tabla Problems](QUICKREF.md#-búsqueda-rápida-de-problemas)
3. [ARCHITECTURE.md - Debugging](ARCHITECTURE.md#-debugging)

---

## 🔍 Búsqueda por Palabra Clave

| Palabra | Dónde buscar |
|---------|-------------|
| Installation / Instalación | GETTING_STARTED, README |
| Commands / Comandos | QUICKREF, README |
| Concepts / Conceptos | DOCUMENTATION, README |
| Piece / Pieza | DOCUMENTATION, QUICKREF |
| Score / Puntuación | DOCUMENTATION, QUICKREF |
| Rotation / Rotación | DOCUMENTATION, QUICKREF |
| CSV Format | QUICKREF, DOCUMENTATION |
| Algorithm / Algoritmo | DOCUMENTATION, ARCHITECTURE |
| Performance | ARCHITECTURE, README |
| Debug | ARCHITECTURE, QUICKREF |
| Extend / Extender | ARCHITECTURE, DOCUMENTATION |
| Play / Jugar | GETTING_STARTED, README |
| Solve / Resolver | GETTING_STARTED, README, DOCUMENTATION |
| UI Controls | QUICKREF, GETTING_STARTED |
| Error | GETTING_STARTED, QUICKREF |
| Best Practices | ARCHITECTURE |

---

## 📊 Por Tipo de Información

### Conceptos Teóricos
- **Piezas y tipos:** [DOCUMENTATION.md](DOCUMENTATION.md#-conceptos-fundamentales)
- **Rotaciones:** [DOCUMENTATION.md](DOCUMENTATION.md#3-orientación-rotación)
- **Score:** [DOCUMENTATION.md](DOCUMENTATION.md#4-puntuación-score)
- **NP-Completitud:** [DOCUMENTATION.md - FAQ](DOCUMENTATION.md#-faq)
- **Arquitectura:** [ARCHITECTURE.md](ARCHITECTURE.md#-arquitectura-general)

### Instrucciones Prácticas
- **Instalación:** [GETTING_STARTED.md](GETTING_STARTED.md#-instalación-5-minutos)
- **Primeros pasos:** [GETTING_STARTED.md - Ejercicios](GETTING_STARTED.md#-ejercicios-progresivos)
- **Flujos de trabajo:** [DOCUMENTATION.md - Flujos](DOCUMENTATION.md#-flujos-de-trabajo-típicos)
- **Troubleshooting:** [GETTING_STARTED.md](GETTING_STARTED.md#-algo-salió-mal)

### Referencia/Lookup
- **Todos los comandos:** [QUICKREF.md](QUICKREF.md#-comandos-esenciales)
- **Formato CSV:** [QUICKREF.md](QUICKREF.md#-estructura-csv)
- **Controles UI:** [QUICKREF.md](QUICKREF.md#-controles-ui-playpy)
- **Rutas de datos:** [QUICKREF.md](QUICKREF.md#-rutas-de-datos)

### Desarrollo
- **Crear resolver:** [ARCHITECTURE.md - Extensibilidad](ARCHITECTURE.md#-extensibilidad)
- **Patrón de código:** [ARCHITECTURE.md - Patrón](ARCHITECTURE.md#-patrón-de-solvers)
- **Optimizaciones:** [ARCHITECTURE.md - Optimizaciones](ARCHITECTURE.md#-optimizaciones-implementables)
- **Testing:** [ARCHITECTURE.md - Testing](ARCHITECTURE.md#-testing)

---

## 🎓 Rutas de Aprendizaje Recomendadas

### Ruta 1: Usuario Casual (30 min)
```
1. Lee: GETTING_STARTED.md (10 min)
2. Haz: Ejercicios 1-2 (10 min)
3. Referencia: QUICKREF.md (cuando lo necesites)
```

### Ruta 2: Usuario Avanzado (1 hora)
```
1. Lee: README.md (5 min)
2. Lee: DOCUMENTATION.md (30 min)
3. Haz: Flujos de trabajo 1-2 (20 min)
4. Consulta: QUICKREF.md (bookmark para referencia)
5. Experimenta: Modifica replacing.py
```

### Ruta 3: Desarrollador (2-3 horas)
```
1. Lee: GETTING_STARTED.md (10 min)
2. Lee: DOCUMENTATION.md (30 min)
3. Lee: ARCHITECTURE.md (45 min)
4. Haz: Crea tu propio resolver (45 min)
5. Lee: Checklists en ARCHITECTURE.md
```

### Ruta 4: Investigador (4+ horas)
```
1. Completa Ruta 3 (2-3 horas)
2. Lee: Papers sobre NP-completitud
3. Experimenta: Combina múltiples algoritmosconstraint_propagation
4. Escribe: Benchmarks, análisis
5. Contribuye: Pull requests
```

---

## 🔗 Relaciones Entre Documentos

```
README.md (Hub central)
  ├─→ GETTING_STARTED.md (si eres novato)
  ├─→ QUICKREF.md (si necesitas referencia)
  ├─→ DOCUMENTATION.md (si quieres profundidad)
  └─→ ARCHITECTURE.md (si vas a desarrollar)

GETTING_STARTED.md
  ├─→ QUICKREF.md (para atajos más adelante)
  └─→ DOCUMENTATION.md (si quieres leer más)

DOCUMENTATION.md
  ├─→ QUICKREF.md (para comandos específicos)
  ├─→ ARCHITECTURE.md (para detalles técnicos)
  └─→ Scripts individuales (*.py)

ARCHITECTURE.md
  └─→ Código fuente (core/*, *.py)
```

---

## ✅ Checklist de Documentación Completada

- ✅ **README.md** - Portal principal mejorado
- ✅ **GETTING_STARTED.md** - Guía para novatos (completa)
- ✅ **QUICKREF.md** - Referencia rápida (tipo cheat sheet)
- ✅ **DOCUMENTATION.md** - Guía completa y exhaustiva
- ✅ **ARCHITECTURE.md** - Detalles técnicos de implementación
- ✅ **INDEX.md** (este archivo) - Índice y navegación

---

## 📞 Soporte

¿Algo no está claro?

1. **Busca en QUICKREF.md** - Tabla de troubleshooting
2. **Busca en DOCUMENTATION.md - FAQ**
3. **Revisa ARCHITECTURE.md - Debugging**
4. **Abre un issue en el repo**

---

**Última actualización:** Abril 2026  
**Versión de Documentación:** 1.0  
**Cobertura:** 100% del codebase y workflows

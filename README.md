# Cubo 3D giratorio

Este script utiliza las librerías `pygame` y  `numpy`  para renderizar una proyección 3D de un cubo giratorio.

<span><img src="https://img.shields.io/badge/Python-FFD43B?style=for-the-badge&logo=python&logoColor=blue"/></span>
<span><img src="https://img.shields.io/badge/numpy-%23013243.svg?style=for-the-badge&logo=numpy&logoColor=white"/></span>
<span><img src="https://img.shields.io/badge/VSCode-0078D4?style=for-the-badge&logo=visual%20studio%20code&logoColor=white"/></span>

## Resultado
<img src="https://github.com/user-attachments/assets/3b66dfad-38f1-4421-9e88-afa9b2c3f18d" width="320px">

---

### Explicación del Código
#### 1. `cubo1.py`

Este archivo contiene el código para dibujar y animar el cubo 3D.

```python
import pygame
import numpy as np
from math import *

# Definición de colores
WHITE = (255, 255, 255)
RED = (155, 50, 50)
BLUE = (0, 0, 155)
BLACK = (0, 0, 0)

# Configuración de la pantalla
WIDTH, HEIGHT = 800, 600
pygame.display.set_caption("Proyección 3D con Python")
screen = pygame.display.set_mode((WIDTH, HEIGHT))

scale = 100
circle_pos = [WIDTH/2, HEIGHT/2]  # x, y

angle = 0

# Definición de los vértices del cubo
points = []
points.append(np.matrix([-1, -1, 1]))
points.append(np.matrix([1, -1, 1]))
points.append(np.matrix([1,  1, 1]))
points.append(np.matrix([-1, 1, 1]))
points.append(np.matrix([-1, -1, -1]))
points.append(np.matrix([1, -1, -1]))
points.append(np.matrix([1, 1, -1]))
points.append(np.matrix([-1, 1, -1]))

# Matriz de proyección 3D a 2D
projection_matrix = np.matrix([
    [1, 0, 0],
    [0, 1, 0]
])

projected_points = [
    [n, n] for n in range(len(points))
]

def connect_points(i, j, points):
    pygame.draw.line(
        screen, BLACK, (points[i][0], points[i][1]), (points[j][0], points[j][1]))

clock = pygame.time.Clock()

# Bucle principal
while True:
    clock.tick(60)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            exit()
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_ESCAPE:
                pygame.quit()
                exit()

    # Matrices de rotación para cada eje
    rotation_z = np.matrix([
        [cos(angle), -sin(angle), 0],
        [sin(angle), cos(angle), 0],
        [0, 0, 1],
    ])

    rotation_y = np.matrix([
        [cos(angle), 0, sin(angle)],
        [0, 1, 0],
        [-sin(angle), 0, cos(angle)],
    ])

    rotation_x = np.matrix([
        [1, 0, 0],
        [0, cos(angle), -sin(angle)],
        [0, sin(angle), cos(angle)],
    ])

    angle += 0.01

    screen.fill(WHITE)

    i = 0
    for point in points:
        rotated2d = np.dot(rotation_z, point.reshape((3, 1)))
        rotated2d = np.dot(rotation_y, rotated2d)
        rotated2d = np.dot(rotation_x, rotated2d)

        projected2d = np.dot(projection_matrix, rotated2d)

        x = int(projected2d[0][0] * scale) + circle_pos[0]
        y = int(projected2d[1][0] * scale) + circle_pos[1]

        projected_points[i] = [x, y]
        pygame.draw.circle(screen, RED, (x, y), 5)
        i += 1
        
    for p in range(4):
        connect_points(p, (p+1) % 4, projected_points)
        connect_points(p+4, ((p+1) % 4) + 4, projected_points)
        connect_points(p, (p+4), projected_points)
    
    # Pinto las caras del cubo...
    pygame.draw.polygon(screen, RED,  [projected_points[0],projected_points[1],projected_points[2],projected_points[3]], 0) 
    pygame.draw.polygon(screen, BLUE, [projected_points[3],projected_points[2],projected_points[6],projected_points[7]], 0) 
       
    pygame.display.update()
```

### Explicación del Funcionamiento

1. **Importaciones y Definición de Variables**:
   - `pygame`: Librería para gráficos y juegos.
   - `numpy`: Librería para cálculos matemáticos y matriciales.
   - `math`: Librería matemática estándar de Python.
   - Se definen colores y configuraciones iniciales para la pantalla (`WIDTH`, `HEIGHT`, `screen`), así como la posición central (`circle_pos`) y la escala de proyección (`scale`).

2. **Definición de los Vértices del Cubo**:
   - Se definen los 8 vértices del cubo en un espacio tridimensional usando matrices `numpy`.

3. **Matriz de Proyección**:
   - Matriz que proyecta puntos 3D a 2D.

4. **Función `connect_points`**:
   - Dibuja líneas entre dos puntos proyectados en la pantalla.

5. **Bucle Principal**:
   - **Eventos**: Maneja eventos de salida (`QUIT`) y de teclado (`KEYDOWN`).
   - **Rotaciones**: Calcula las matrices de rotación para los ejes X, Y y Z.
   - **Transformaciones y Proyecciones**: Aplica las rotaciones a cada punto del cubo y luego proyecta los puntos en 2D.
   - **Dibujo**: Dibuja los puntos proyectados como círculos y conecta los puntos con líneas para formar las aristas del cubo. Además, pinta algunas caras del cubo para mejor visualización.


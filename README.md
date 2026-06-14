# Lab5
## 1. Control de fuerza proporcional
### Fundamento teórico
En esta práctica se realizará un control de fuerza con un lazo interno de posición de un manipulador RR. El objetivo es regular la fuerza de interacción entre el efector final del robot y un entorno elástico.

La dinámica del robot se linealiza mediante una ley de control por dinámica inversa que compensa los efectos dinámicos del manipulador, incluyendo las fuerzas externas:

$$
\boldsymbol{\tau} = \boldsymbol{M(q)}\boldsymbol{\ddot{q}}_d + \boldsymbol{C(q,\dot{q})\dot{q}} + 
\boldsymbol{F}_b\boldsymbol{\dot{q}} + \boldsymbol{g(q)} + \boldsymbol{J}^T\boldsymbol{(q)f}_e
$$

donde:

- $\boldsymbol{M(q)}$ es la matriz de inercia.

- $\boldsymbol{C(q,\dot{q})}$ representa los términos centrífugos y de Coriolis.

- $\boldsymbol{F}_b$ modela el rozamiento viscoso.

- $\boldsymbol{g(q)}$ contiene los efectos gravitatorios.

- $\boldsymbol{J(q)}$ es el jacobiano del manipulador.

- $\boldsymbol{f}_e$ es la fuerza de interacción con el entorno.

Gracias a esta compensación, el comportamiento dinámico del robot queda desacoplado y puede describirse mediante una relación puramente cinemática:

$$
\boldsymbol{\ddot{q}=J^{-1}(q)\left(\ddot{x}-\dot{J}(q,\dot{q})\dot{q}\right)}
$$

Se considera que el robot interactúa con una superficie elástica modelada mediante una ley lineal de tipo resorte:

$$
\boldsymbol{f}_e = \boldsymbol{K}(\boldsymbol{x}_e - \boldsymbol{x}_r)
$$

donde:

- $\boldsymbol{K}$ es la matriz de rigidez del entorno.
- $\boldsymbol{x}_e$ es la posición actual del efector final.
- $\boldsymbol{x}_r$ es la posición de equilibrio de la superficie.

Este modelo establece que la fuerza de contacto es proporcional a la deformación producida sobre el entorno.

El error de fuerza se transforma en una referencia de posición virtual $x_F$ mediante la relación:

$$
\boldsymbol{x}_F = \boldsymbol{C}_F(\boldsymbol{f}_d - \boldsymbol{f}_e)
$$

donde:

- $\boldsymbol{C}_F$ es una constante de complianza.

- $\boldsymbol{f}_d$ es la fuerza deseada.

A continuación se muestra la ecuación del controlador junto con su correspondiente esquema de bloques (figura 5.1.1).

$$
\boldsymbol{M}_d \boldsymbol{\ddot{x}}_e + \boldsymbol{K}_D \boldsymbol{\dot{x}}_e + 
\boldsymbol{K}_P (\boldsymbol{I}_3 + \boldsymbol{C}_F \boldsymbol{K})\boldsymbol{x}_e =
\boldsymbol{K}_P \boldsymbol{C}_F (\boldsymbol{Kx}_r + \boldsymbol{f}_d)
$$

<p align="center">
    <img src="/images/controlador_proporcional.png">
    <br>
    <em>Figura 5.1.1: Esquema del controlador de fuerza proporcional.</em>
</p>

### Aplicacion práctica

Para simular el la respuesta del sistema se van a utilizar los siguientes valores:

- Posición de referencia del contacto:

$$
x_r =
\begin{bmatrix}
1.2, \
0.7
\end{bmatrix}
\text{ m}
$$

- Fuerza deseada:

$$
f_d =
\begin{bmatrix}
10, \
0
\end{bmatrix}
\text{ N}
$$

- Posición inicial del efector final:

$$
x_{e0} =
\begin{bmatrix}
1.3, \
0.7
\end{bmatrix}
\text{ m}
$$

- Rigidez del entorno:

$$
K = 
\begin{bmatrix}
1000 & 0 \\
0 & 0
\end{bmatrix}
\text{N/m}
$$

- Matriz de complianza:

$$
C_F = 
\begin{bmatrix}
0.05 & 0 \\
0 & 0
\end{bmatrix}
$$

- Inercia deseada:

$$
M_d =
\begin{bmatrix}
1000 & 0 \\
0 & 1000
\end{bmatrix}
\ \text{N/m}
$$

- Amortiguamiento deseado:
  
$$
K_D =
\begin{bmatrix}
5000 & 0 \\
0 & 5000
\end{bmatrix}
\ \text{N/m}
$$

- Rigidez deseada:
  
$$
K_P =
\begin{bmatrix}
5000 & 0 \\
0 & 5000
\end{bmatrix}
\ \text{N/m}
$$

Tras introducir estas matrices en Simulink, se obtiene el esquema de la figura 5.1.2.

<p align="center">
    <img src="/images/controlador_P_implementado.png">
    <br>
    <em>Figura 5.1.2: Esquema del controlador de fuerza proporcional implementado.</em>
</p>

### Resultados

Tras la simulación, se obtienen las fuerzas de la figura 5.1.3.

<p align="center">
    <img src="/images/fuerzas_control_P.png">
    <br>
    <em>Figura 5.1.3: Gráfica de las fuerzas con controlador P.</em>
</p>

En el eje $y$ la fuerza aplicada es nula, al igual que la fuerza deseada. En el eje $x$ la fuerza aplicada es de $-14\,\text{N}$, lo que produce un error de $24\,\text{N}$ respecto a la referencia. Por tanto, el controlador no realiza su función correctamente.

Tal y como se muestra en la figura 5.1.4, la posición en el eje $y$ no se mantiene, porque en esa dirección no existe una fuerza de referencia ni una restricción efectiva impuesta por el entorno o el controlador. De esta forma, la gravedad hace que manipulador caiga.

<p align="center">
    <img src="/images/posicion_P_implementado.png">
    <br>
    <em>Figura 5.1.4: Gráfica de la posición con controlador P.</em>
</p>

Para corregir el error de posición, hay que imponer una restricción de posición en el eje $y$ de la forma en la que se muestra en la figura 5.1.5.

<p align="center">
    <img src="/images/controlador_P_implementado_alternativa.png">
    <br>
    <em>Figura 5.1.5: Esquema del controlador P modificado.</em>
</p>

En la figura 5.1.6 se muestar que efectivamente se ha resuelto el problema de la posición.

<p align="center">
    <img src="/images/posicion_P_implementado_alternativa.png">
    <br>
    <em>Figura 5.1.6: Gráfica de la posición con controlador P modificado.</em>
</p>

## 2. Control de fuerza proporcional-integral
### Fundamento teórico

Para corregir el error de fuerza, se va a implementar un controlador PI. Para ello se hará un ligero cambio al controlador anterior:

$$
\boldsymbol{C}_F = \boldsymbol{K}_F + \boldsymbol{K}_I \int_{0}^{t} (\cdot)\, d\varsigma
$$

Donde $\boldsymbol{K}_I$ es la matriz de ganancias integral y $\boldsymbol{K}_F$ es la matriz de ganancias proporcional.

### Aplicacion práctica

Para simular la respuesta del sistema se van a utilizar los siguientes valores:

- Ganancias proporcionales:
  
$$
\boldsymbol{K}_F =
\begin{bmatrix}
0.03 & 0 \\
0 & 0.03
\end{bmatrix}
$$

- Ganancias integrales:
  
$$
\boldsymbol{K}_I =
\begin{bmatrix}
0.03 & 0 \\
0 & 0.03
\end{bmatrix}
$$

El esquema del nuevo controlador se muestra en la figura 5.2.1.

<p align="center">
    <img src="/images/controlador_PI_implementado.png">
    <br>
    <em>Figura 5.2.1: Esquema del controlador PI implementado.</em>
</p>

Tal y como muestra la figura 5.2.2, con el controlador PI sí se alcanza la fuerza deseada.

<p align="center">
    <img src="/images/fuerzas_control_PI.png">
    <br>
    <em>Figura 5.2.2: Gráfica de las fuerzas con controlador PI.</em>
</p>

Con el objetivo de mejorar la respuesta del controlador, se analizaron los efectos de modificar las ganancias $\boldsymbol{K}_F$ y $\boldsymbol{K}_I$. Se observó que al aumentar $\boldsymbol{K}_F$, la frecuencia de las oscilaciones se incrementa y la fuerza de referencia se alcanza ligeramente más rápido. De manera similar, al aumentar $\boldsymbol{K}_I$, el sistema alcanza la consigna en un menor tiempo, aunque a costa de un aumento en la amplitud de las oscilaciones.

Por otro lado, la reducción de cualquiera de estas dos ganancias disminuye las oscilaciones del sistema, pero provoca un incremento significativo del tiempo de establecimiento.

Por este motivo, se optó por modificar la ganancia $\boldsymbol{K}_D$. Al aumentar su valor, se incrementa el amortiguamiento del sistema, lo que permite reducir la amplitud de las oscilaciones sin penalizar de forma apreciable el tiempo de establecimiento. De esta manera, se obtiene una respuesta más estable manteniendo una respuesta rápida. 

Las respuestas temporales de la fuerza y de la posición obtenidas tras el ajuste del controlador se muestran en las figuras 5.2.3 y 5.2.4, respectivamente. Para ello, se empleó la siguiente $\boldsymbol{K}_D$:

$$
\boldsymbol{K}_D =
\begin{bmatrix}
8000 & 0 \\
0 & 8000
\end{bmatrix}
$$

<p align="center">
    <img src="/images/fuerzas_PI_modificado.png">
    <br>
    <em>Figura 5.2.3: Gráfica de las fuerzas con controlador PI modificado.</em>
</p>

<p align="center">
    <img src="/images/posicion_PI_modificado.png">
    <br>
    <em>Figura 5.2.4: Gráfica de las posiciones con controlador PI modificado.</em>
</p>

# Teoria lineal de errores

- Error absoluto: $e_a = x - \hat{x}$

> [!NOTE]
> El valor que medimos nosotros es $\hat{x}$ mientras que x es el valor real.

- Cota de error: $|e_a| \leq \Delta x$

- Error relativo: $e_r = \frac{x - \hat{x}}{x}$

> [!NOTE]
> El error relativo significa "cuanto me pase" del error absoluto.

Como a veces no se puede usar la definición de Error relativo se lo puede aproximar como:

$$e_r = \frac{\Delta x}{\hat{x}}$$

> [!NOTE]
> Para obtener el error relativo porcentual simplemente se multiplica por 100.

## Notación

Para dar una respuesta la notación es:

$$x = \hat{x} \pm \Delta x $$

$$\Delta x = 0\text{,d}_{1} \times 10^{\text{-t}}$$

### Ejemplo

$$\hat{x} = 123.45678$$

$$\Delta x = 0.0033$$

- Primero debemos mayorar la cota de error $\rightarrow$ $\Delta x < 0.004$.

- Ahora redondeamos $\hat{x}$ teniendo en cuenta el digito siguiente al último del error, si este es mayor o igual a 5 mayoramos, sino dejamos igual.

Finalmente tenemos el resultado:

$$x = 123.457 \pm 0.004$$

## Propagación de errores.

Tenemos una función $z = f(x, y, t, \cdots)$ y queremos el resultado de la cota del error de z ($\Delta z$).

$$\Delta z \leq |\frac{\delta z}{\delta x}|_{\hat{x}, \hat{y}, \cdots} \cdot \Delta x + |\frac{\delta z}{\delta y}|_{\hat{x}, \hat{y}, \cdots} \cdot \Delta y + \cdots$$

> [!NOTE]
> No es más que el polinomio de Taylor de grado 1 $\rightarrow$ Propagación lineal de errores.

Entonces, la forma general es:

$$\Delta z = \sum_{i = 0}^{\infty} |\frac{\delta f}{\delta x_{i}}|_{\hat{x}i} \cdot \Delta x{i}$$

> [!WARNING]
> Siempre debe ocurrir qué $\Delta z \neq 0$.

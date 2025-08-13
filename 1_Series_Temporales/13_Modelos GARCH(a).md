## Ejemplo

Analicemos la serie de la variación diaria del precio del euro frente al dólar (eur_vs_usd).

```python
eur = pd.read_csv("../data/eur_vs_usd.csv").dropna()

eur = pd.Series(eur["US dollar/Euro (EXR.D.USD.EUR.SP00.A)"].values,index=eur["DATE"])

eur.plot();
```
![[Pasted image 20250605183137.png]]

Miremos la correlación y vemos que no es estacionaria

```python
plot_acf(eur, bartlett_confint=False);
```
![[Pasted image 20250605183202.png]]

Probemos ahora tomar diferencias
```python
y = eur.diff().dropna()

y.plot();
plt.title(f"Media de las diferencias {np.mean(y)} +- {2*np.std(y)/np.sqrt(y.size)}");
```
![[Pasted image 20250605183219.png]]

Miremos ahora la autocorrelación de la serie diferenciada, se asemeja a ruido blanco:
```python
plot_acf(y);
```
![[Pasted image 20250605183247.png]]

Sin embargo, la serie **no es estacionaria**. Presenta momentos de mayor y menor varianza (volatilidad). En particular la distribución se aleja de una Gaussiana.
```python
qqplot(y,line='s');
```
![[Pasted image 20250605183328.png]]

**Interpretación:**
- A corto plazo, la serie se comporta como un paseo al azar sin deriva (la media de las diferencias da $0$).
- Pero a largo plazo, la serie presenta **heterocedasticidad**, varianza variable.

Esto significa que al intentar ajustar un modelo [[10_Modelos ARIMA|ARIMA]] no va a dar los mejores resultados.

## Modelos GARCH

Esta familia de modelos permiten modelar series cuya **varianza** no es estacionaria. Como vimos para EUR vs USD.

```python
y = eur.diff().dropna()

y.plot();

plt.title(f"Media de las diferencias {np.mean(y)} +- {2*np.std(y)/np.sqrt(y.size)}");
```
![[Pasted image 20250605183539.png]]

### Idea
Modelar la **varianza** de la serie como un proceso autorregresivo. Si la serie está centrada, esto es equivalente a ajustar un modelo ARMA a los valores de $x_t^2$. Este modelo se llama $GARCH$: Generalized Autorregressive Conditionally Heteroskedsatic.

```python
y2 = y**2
y2.plot();
```
![[Pasted image 20250605183726.png]]
```python
plot_acf(y2,bartlett_confint=False);
```
![[Pasted image 20250605183739.png]]
```python
plot_pacf(y2);
```
![[Pasted image 20250605183754.png]]

La biblioteca `statsmodels` no incluye modelos de tipo GARCH por lo que requerimos la biblioteca `arch`

```python
from arch import arch_model

fit = arch_model(y, mean='Zero', vol='GARCH', p=1,q=1, rescale=True).fit();

fit.summary()
```

```
Iteration:      1,   Func. Count:      5,   Neg. LLF: 3542121476.1780243
Iteration:      2,   Func. Count:     12,   Neg. LLF: 6432.502659518372
Iteration:      3,   Func. Count:     16,   Neg. LLF: 13054.967420877505
Iteration:      4,   Func. Count:     21,   Neg. LLF: 8158.432872015319
Iteration:      5,   Func. Count:     28,   Neg. LLF: 9559.09420278957
Iteration:      6,   Func. Count:     33,   Neg. LLF: 8652.224376988284
Iteration:      7,   Func. Count:     39,   Neg. LLF: 7699.221113600659
Iteration:      8,   Func. Count:     45,   Neg. LLF: 6407.88196659977
Iteration:      9,   Func. Count:     49,   Neg. LLF: 6407.647697238508
Iteration:     10,   Func. Count:     53,   Neg. LLF: 6407.620967274368
Iteration:     11,   Func. Count:     57,   Neg. LLF: 6407.619060270876
Iteration:     12,   Func. Count:     61,   Neg. LLF: 6407.6189735249945
Iteration:     13,   Func. Count:     64,   Neg. LLF: 6407.6189735213
Optimization terminated successfully    (Exit mode 0)
            Current function value: 6407.6189735249945
            Iterations: 13
            Function evaluations: 64
            Gradient evaluations: 13
```
<details>
<table class="simpletable">
<caption>Zero Mean - GARCH Model Results</caption>
<tr>
  <th>Dep. Variable:</th>        <td>None</td>        <th>  R-squared:         </th>  <td>   0.000</td> 
</tr>
<tr>
  <th>Mean Model:</th>         <td>Zero Mean</td>     <th>  Adj. R-squared:    </th>  <td>   0.000</td> 
</tr>
<tr>
  <th>Vol Model:</th>            <td>GARCH</td>       <th>  Log-Likelihood:    </th> <td>  -6407.62</td>
</tr>
<tr>
  <th>Distribution:</th>        <td>Normal</td>       <th>  AIC:               </th> <td>   12821.2</td>
</tr>
<tr>
  <th>Method:</th>        <td>Maximum Likelihood</td> <th>  BIC:               </th> <td>   12841.6</td>
</tr>
<tr>
  <th></th>                        <td></td>          <th>  No. Observations:  </th>    <td>6499</td>   
</tr>
<tr>
  <th>Date:</th>           <td>Mon, May 19 2025</td>  <th>  Df Residuals:      </th>    <td>6499</td>   
</tr>
<tr>
  <th>Time:</th>               <td>16:26:23</td>      <th>  Df Model:          </th>      <td>0</td>    
</tr>
</table>
<table class="simpletable">
<caption>Volatility Model</caption>
<tr>
      <td></td>        <th>coef</th>     <th>std err</th>      <th>t</th>       <th>P>|t|</th>     <th>95.0% Conf. Int.</th>   
</tr>
<tr>
  <th>omega</th>    <td>1.2267e-03</td> <td>5.081e-04</td> <td>    2.414</td> <td>1.577e-02</td> <td>[2.309e-04,2.223e-03]</td>
</tr>
<tr>
  <th>alpha[1]</th> <td>    0.0282</td> <td>3.649e-03</td> <td>    7.720</td> <td>1.168e-14</td> <td>[2.102e-02,3.532e-02]</td>
</tr>
<tr>
  <th>beta[1]</th>  <td>    0.9694</td> <td>3.914e-03</td> <td>  247.714</td>   <td>0.000</td>     <td>[  0.962,  0.977]</td>  
</tr>
</table><br><br>Covariance estimator: robust
</details>

```python
#Obtenemos los valores de varianza estimada por el modelo ajustado

#Horizon=1 es que predice el valor siguiente, start=0 es que arranca al comienzo de la serie)
predicciones = fit.forecast(horizon=1,start=0).variance
predicciones = pd.Series(predicciones["h.1"])
desvio = np.sqrt(predicciones)*np.std(y)

desvio.plot();
plt.title("Desvío estándar estimado");
```
![[Pasted image 20250605184019.png]]

```python
y.plot(alpha=0.3)
(2*desvio).plot()
(-2*desvio).plot();
```
![[Pasted image 20250605184033.png]]
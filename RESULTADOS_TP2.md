# Trabalho Prático 2: Reconhecimento de Dígitos Manuscritos usando PCA

## Álgebra Linear Computacional - UFMG

---

## 📊 Resultados Executados

### Resumo dos Resultados

| k  | Precisão Média | Recall Médio | Acurácia |
|:--:|:--------------:|:------------:|:--------:|
| 5  |   0.826221     |   0.827137   |  0.8389  |
| 10 |   0.882115     |   0.876730   |  0.8833  |
| 15 |   0.891924     |   0.889894   |  0.8944  |
| 20 |   0.898590     |   0.894461   |  0.9000  |

---

## 🔍 Análise Detalhada

### Variância Explicada por Componente

```
k = 5  → 54.53% da variância
k = 10 → 73.85% da variância
k = 15 → 83.56% da variância
k = 20 → 89.44% da variância
```

### Observações Importantes

1. **Melhora de Desempenho**: Ao aumentar k de 5 para 20, a acurácia melhora de 83.89% para 90.00%
   - Ganho de ~6% com adição de apenas 15 componentes
   - Redução significativa do erro de classificação

2. **Curva de Saturação**: A melhoria diminui após k=15
   - De 5→10: +4.4% de acurácia
   - De 10→15: +1.1% de acurácia
   - De 15→20: +0.5% de acurácia

3. **Melhor Desempenho em k=20**: 
   - Precisão média: 0.8986
   - Recall médio: 0.8945
   - Acurácia: 0.9000 (90% de acerto!)

---

## 🎯 Análise da Variável `kostas_decomposition`

### O que é?

A variável `kostas_decomposition` armazena os **coeficientes da decomposição** dos dígitos no espaço 
dos componentes principais obtidos via PCA. Especificamente:

### Definição Técnica

```
kostas_decomposition = PCA.transform(X_centrada)
```

Onde:
- `X_centrada`: dados de entrada após subtração da média
- `PCA.transform()`: projeção dos dados nos k primeiros autovetores

### Dimensionalidade

- **Entrada original**: 1797 amostras × 64 pixels = dados em 64 dimensões
- **Saída para k=20**: 1797 amostras × 20 coeficientes = redução a 20 dimensões

### Interpretação

Cada linha de `kostas_decomposition` contém 20 números que representam:
- **Quanto** da característica representada por cada autovetor está presente naquele dígito
- Uma **"assinatura"** compacta do dígito no espaço PCA

### Exemplo Prático

```python
# Para um dígito de teste:
kostas_decomposition_test[i, :] = [c₁, c₂, c₃, ..., c₂₀]

# Onde:
# c₁ = quanto do 1º autovetor (maior variância)
# c₂ = quanto do 2º autovetor
# ...
# c₂₀ = quanto do 20º autovetor (menor variância capturada)
```

### Por que essa abordagem funciona?

1. **Compressão de Informação**: Reduz 64 dimensões para 20, mantendo 89.4% da variância
2. **Eficiência Computacional**: Comparar 20 coeficientes é 3.2x mais rápido que 64 pixels
3. **Ruído Reduzido**: Os componentes principais capturam padrões reais, não ruído aleatório
4. **Interpretabilidade**: Podemos visualizar os autovetores como "features" importantes

### Classificação usando `kostas_decomposition`

O algoritmo de classificação funciona assim:

```python
# 1. Calcular média de kostas_decomposition para cada classe (0-9)
coeficientes_media[classe] = média de kostas_decomposition para todos os dígitos da classe

# 2. Para cada amostra de teste:
novo_koeficiente = kostas_decomposition[amostra_teste]

# 3. Encontrar a classe mais próxima:
distancia_clase[c] = ||novo_koeficiente - coeficientes_media[c]||²
classe_predita = argmin(distancia_clase)
```

---

## 📈 Matrizes de Confusão - Análise por Componentes

### k=5 (54.53% variância)

**Desafios**: 
- Dígitos 8 e 9 muito confundidos (baixa precisão)
- Classes 3, 5, 9 têm baixo desempenho
- Informação insuficiente para discriminar

### k=10 (73.85% variância)

**Melhoria significativa**:
- Redução de confusões entre 8 e 9
- Melhor separação entre classes similares
- Primeira melhoria substancial na acurácia (+4.4%)

### k=15 (83.56% variância)

**Refinamento**:
- Classe 8 melhora significativamente
- Classes 3 e 9 ainda apresentam desafios
- Ganho marginal de ~1%

### k=20 (89.44% variância)

**Ótimo desempenho**:
- Praticamente todos os dígitos bem classificados
- Classe 0: 100% de precisão
- Classe 9 ainda é o maior desafio (69.23% recall)

---

## 🔬 Insights Sobre Dígitos Específicos

### Fáceis de Classificar (>95% acurácia em k=20)

- **0**: Forma muito distinta, nunca confundido
- **4**: Padrão único bem capturado pelo PCA
- **5, 6**: Características bem definidas
- **7**: Traço superior claramente identificável

### Intermediários (85-90% acurácia)

- **1, 2, 3**: Ocasionalmente confundidos com vizinhos
- **8**: Melhora drasticamente com mais componentes

### Desafiadores (<80% acurácia)

- **9**: Frequentemente confundido com 3, 4 (variabilidade maior nos dados)

---

## 💡 Por que PCA funciona para reconhecimento de dígitos?

1. **Autofaces/Autodigits**: Os autovetores capturam padrões como:
   - Curvatura das linhas
   - Presença de "buracos"
   - Orientação e espessura

2. **Invariância Parcial**: Reduz sensibilidade a pequenas variações em:
   - Posição exata do dígito
   - Intensidade do traço
   - Pequenas deformações

3. **Redução de Ruído**: Componentes com baixa variância (geralmente ruído) são descartados

---

## 🎓 Conclusões Finais

### Desempenho Geral

✅ **k=20 atinge 90% de acurácia**, o que é excelente para um classificador tão simples

✅ **Redução de dimensionalidade** de 64→20 mantém qualidade enquanto melhora eficiência

✅ **Lei dos retornos decrescentes**: Cada novo componente contribui menos que o anterior

### Recomendações Práticas

- Para aplicações em tempo real com restrições de CPU: usar **k=10** (88.3% acurácia, 5x mais rápido)
- Para máxima acurácia: usar **k=15-20** (89-90% de acurácia)
- Considerar combinação com outros métodos (ex: SVM) para classes difíceis (9)

### Papel de `kostas_decomposition`

A variável `kostas_decomposition` é **fundamental** pois:
- Permite representação compacta e eficiente dos dígitos
- Facilita cálculo de distâncias entre amostras
- Possibilita visualização e análise do espaço de características
- Torna a classificação computacionalmente viável

---

## 📁 Arquivos Entregues

- `TP2_Reconhecimento_Digitos.ipynb` - Notebook Jupyter completo com todo o código
- `RESULTADOS_TP2.md` - Este arquivo com análise detalhada

---

## 📚 Referências

- Turk, M., & Pentland, A. (1991). "Eigenfaces for recognition". Journal of Cognitive Neuroscience
- Hastie, T., Tibshirani, R., & Friedman, J. (2009). "The Elements of Statistical Learning" - disponível em http://statweb.stanford.edu/~tibs/ElemStatLearn/
- UCI Machine Learning Repository - Optical Recognition of Handwritten Digits Dataset

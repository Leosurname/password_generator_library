# password_generator_library

Biblioteca Python leve para gerar senhas e frases-senha seguras, e estimar a força de uma senha. Sem dependências externas: usa apenas a biblioteca padrão (`secrets`, `math`).

> **Status:** em desenvolvimento. Este README descreve a API planejada.

## Recursos

- `password()`: senhas aleatórias configuráveis.
- `passphrase()`: frases-senha com palavras sorteadas (lista em português embutida).
- `strength()`: estimativa de entropia e nota de força.

## Instalação

Sem pacote por enquanto: clone o repositório e use o arquivo `password_generator_library.py` diretamente.

```bash
git clone https://github.com/Leosurname/password_generator_library.git
```

## Uso

```python
from password_generator_library import password, passphrase, strength

password(20)                    # 'vR7#kPq2!mXz9Lc$4TwB'
passphrase(5, separator=" ")    # 'rio tigela cavalo nuvem faca'
strength("123456")              # {'entropy_bits': 19.9, 'score': 'fraca'}
```

### `password(length=16, symbols=True, digits=True, uppercase=True, exclude_ambiguous=False)`

Gera uma senha aleatória.

| Parâmetro           | Descrição                                                        |
|---------------------|------------------------------------------------------------------|
| `length`            | Tamanho da senha.                                                |
| `symbols`           | Inclui símbolos (`!#$%...`).                                     |
| `digits`            | Inclui dígitos.                                                  |
| `uppercase`         | Inclui letras maiúsculas.                                        |
| `exclude_ambiguous` | Remove caracteres ambíguos (`0 O o`, `1 l I`).                   |

**Como funciona:** monta um "alfabeto" juntando os tipos de caractere habilitados (letras minúsculas sempre; maiúsculas, dígitos e símbolos conforme os parâmetros). Primeiro sorteia um caractere de cada tipo habilitado, para garantir que a senha tenha pelo menos um de cada. Depois completa o restante do tamanho sorteando do alfabeto inteiro e, no fim, embaralha tudo para que os caracteres garantidos não fiquem sempre no começo. Com `exclude_ambiguous=True`, os caracteres que se confundem visualmente (`0 O o 1 l I`) saem do alfabeto antes do sorteio. Tudo é sorteado com `secrets`. Se `length` for menor que o número de tipos habilitados, levanta `ValueError`.

**Quando usar:** para contas e sistemas que aceitam senhas longas e aleatórias, guardadas em um gerenciador de senhas. Use `exclude_ambiguous=True` se alguém for digitar a senha à mão.

### `passphrase(words=4, separator="-", capitalize=False)`

Gera uma frase-senha, por exemplo `cavalo-nuvem-tigela-rio`.

**Como funciona:** sorteia `words` palavras de uma lista em português que fica dentro do arquivo e junta com o `separator`. Com `capitalize=True`, cada palavra começa com maiúscula. Cada palavra é sorteada de forma independente com `secrets.choice`. A segurança vem da quantidade de palavras e do tamanho da lista: com 200 palavras, cada uma vale cerca de 7,6 bits (`log2(200)`), então 4 palavras dão ~30 bits e 6 palavras dão ~46 bits. Uma lista maior ou mais palavras aumentam a entropia.

**Quando usar:** quando você precisa lembrar ou digitar a senha (senha mestra do gerenciador, disco criptografado, Wi-Fi). É mais fácil de memorizar que uma sequência de caracteres aleatórios.

### `strength(senha)`

Devolve `{"entropy_bits": float, "score": str}`. A entropia é estimada por `comprimento × log2(tamanho do alfabeto)`, com penalidade para sequências (`1234`, `abcd`) e repetições (`aaaa`).

**Como funciona:** identifica quais tipos de caractere a senha usa (minúsculas = 26, maiúsculas = 26, dígitos = 10, símbolos = ~32) e soma para obter o tamanho do alfabeto. A entropia em bits é `comprimento × log2(tamanho do alfabeto)`: quanto maior, mais tentativas um atacante precisaria. Depois aplica penalidades para padrões previsíveis, como sequências (`1234`, `abcd`) e repetições (`aaaa`), que reduzem a entropia efetiva. Por fim converte o resultado numa nota: por exemplo, menos de 40 bits é `fraca`, de 40 a 70 é `média` e acima de 70 é `forte` (as faixas exatas ficam a seu critério).

**Limitação:** é uma estimativa simples. Ela não detecta senhas comuns como `senha123` ou `Brasil2024!`, que têm entropia alta no cálculo mas aparecem em listas de ataque. Para isso seria preciso comparar com uma lista de senhas vazadas.

## Segurança

A geração usa o módulo [`secrets`](https://docs.python.org/3/library/secrets.html), adequado para criptografia, e nunca `random`.

## Desenvolvimento

```bash
pytest
```

## Licença

A definir.

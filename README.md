# password_generator_library

Biblioteca Python leve para gerar senhas e frases-senha seguras, e estimar a força de uma senha. Sem dependências externas: usa apenas a biblioteca padrão (`secrets`, `math`).

> **Status:** em desenvolvimento. Este README descreve a API planejada.

## Recursos

- `password()`: senhas aleatórias configuráveis.
- `passphrase()`: frases-senha com palavras sorteadas (lista em português embutida).
- `strength()`: estimativa de entropia e nota de força.
- CLI mínima: `pwgen`.

## Instalação

```bash
pip install password_generator_library
```

Ou, para desenvolvimento:

```bash
git clone https://github.com/Leosurname/password_generator_library.git
cd password_generator_library
pip install -e .
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

### `passphrase(words=4, separator="-", capitalize=False)`

Gera uma frase-senha, por exemplo `cavalo-nuvem-tigela-rio`.

### `strength(senha)`

Devolve `{"entropy_bits": float, "score": str}`. A entropia é estimada por `comprimento × log2(tamanho do alfabeto)`, com penalidade para sequências (`1234`, `abcd`) e repetições (`aaaa`).

## CLI

```bash
pwgen 20            # senha de 20 caracteres
pwgen --phrase 5    # frase-senha de 5 palavras
```

## Segurança

A geração usa o módulo [`secrets`](https://docs.python.org/3/library/secrets.html), adequado para criptografia, e nunca `random`.

## Desenvolvimento

```bash
pip install -e ".[dev]"
pytest
```

## Licença

A definir.

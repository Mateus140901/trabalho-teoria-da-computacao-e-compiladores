# HomeScript

Uma linguagem de programação simples, em português, para controlar dispositivos de uma casa inteligente.

Projeto desenvolvido para a disciplina de **Teoria da Computação e Compiladores** — Engenharia da Computação — FHO Araras.

---

## O que é?

A HomeScript é uma DSL (Linguagem de Domínio Específico) que permite escrever rotinas de automação residencial de forma simples, sem precisar saber programar.

Em vez de escrever código Python complexo, você escreve:

```
ligar sala.luz
ajustar quarto.ar_condicionado para 22
se sala.temperatura > 28 entao
    ligar sala.ventilador
senao
    desligar sala.ventilador
fim
cena "dormir"
```

O compilador lê esse arquivo e gera um código Python executável automaticamente.

---

## Como funciona

O compilador passa por 4 etapas:

```
seu_programa.hs
      |
      v
[1. Léxico]      → quebra o código em tokens (palavras, símbolos)
      |
      v
[2. Sintático]   → verifica se a estrutura está correta (gramática BNF)
      |
      v
[3. Semântico]   → verifica se o sentido está correto (tipos, valores)
      |
      v
[4. Compilação]  → gera um arquivo .py pronto para rodar
```

---

## Instalação

Você precisa ter Python 3 instalado. Depois instale a única dependência:

```bash
pip install ply
```

---

## Como usar

**Rodar no modo interpretador (direto):**
```bash
python main.py rotina_diaria.hs
```

**Rodar no modo compilador (gera um .py):**
```bash
python main.py rotina_diaria.hs --compilar
python rotina_diaria.py
```

**Ver apenas os tokens gerados:**
```bash
python main.py rotina_diaria.hs --lexico
```

**Ver a árvore sintática (AST):**
```bash
python main.py rotina_diaria.hs --ast
```

---

## Sintaxe da linguagem

### Ligar e desligar dispositivos
```
ligar sala.luz
desligar quarto.luz
```

### Ajustar valor de um dispositivo
```
ajustar quarto.ar_condicionado para 22
```

### Esperar um tempo
```
aguardar 30
```

### Condicional
```
se sala.temperatura > 28 entao
    ligar sala.ventilador
senao
    desligar sala.ventilador
fim
```

### Repetição
```
repetir 3 vezes
    ligar banheiro.luz
    aguardar 1
    desligar banheiro.luz
fim
```

### Cenas predefinidas
```
cena "cinema"
cena "jantar"
cena "dormir"
cena "acordar"
```

### Condição por horário
```
se hora > 22:00 entao
    cena "dormir"
fim
```

### Operadores lógicos
```
se sala.temperatura > 28 e sala.umidade > 70 entao
    ligar sala.ar_condicionado
fim
```

---

## Dispositivos disponíveis

| Dispositivo | Tipo | Comando |
|---|---|---|
| sala.luz | booleano | ligar / desligar |
| sala.ventilador | booleano | ligar / desligar |
| sala.ar_condicionado | numérico | ajustar para N (16–30°C) |
| sala.temperatura | sensor | usar em condições |
| sala.umidade | sensor | usar em condições |
| quarto.luz | booleano | ligar / desligar |
| quarto.ar_condicionado | numérico | ajustar para N (16–30°C) |
| cozinha.luz | booleano | ligar / desligar |
| cozinha.cafeteira | booleano | ligar / desligar |
| banheiro.luz | booleano | ligar / desligar |
| garagem.luz | booleano | ligar / desligar |
| sala.som_ambiente | booleano | ligar / desligar |

---

## Exemplo completo — Rotina diária

```
# Manhã
cena "acordar"
ligar sala.luz
ligar cozinha.luz
ligar cozinha.cafeteira
ligar sala.som_ambiente
ligar banheiro.luz

se quarto.temperatura < 18 entao
    ajustar quarto.ar_condicionado para 22
senao
    desligar quarto.ar_condicionado
fim

# Tarde
desligar cozinha.luz
desligar quarto.luz
desligar sala.luz
desligar sala.som_ambiente
desligar cozinha.cafeteira

se sala.temperatura > 30 entao
    ajustar sala.ar_condicionado para 24
senao
    desligar sala.ar_condicionado
fim

aguardar 60
ligar sala.luz
ligar cozinha.luz
ajustar sala.ar_condicionado para 22

# Noite
se hora > 18:00 entao
    cena "jantar"
    aguardar 120
fim

ajustar quarto.ar_condicionado para 20
desligar cozinha.luz
aguardar 5
desligar sala.luz
cena "dormir"
```

---

## Estrutura dos arquivos

```
HomeScript/
├── main.py                       # ponto de entrada, orquestra tudo
├── lexer.py                      # análise léxica (tokens)
├── hs_parser.py                  # análise sintática (gramática + AST)
├── semantico.py                  # análise semântica (verificações)
├── compilador.py                 # geração de código Python
├── interpretador.py              # execução direta da AST
├── rotina_diaria.hs              # exemplo de programa HomeScript
└── extras/
    └── extra_interpretador_gpio.py  # versão para Raspberry Pi
```

---

## Rodando no Raspberry Pi

O arquivo `extras/extra_interpretador_gpio.py` é uma versão do interpretador adaptada para controlar pinos GPIO reais.

Instale a biblioteca:
```bash
pip install RPi.GPIO
```

Substitua o interpretador:
```bash
cp extras/extra_interpretador_gpio.py interpretador.py
python main.py rotina_diaria.hs
```

O arquivo detecta automaticamente se está rodando no Raspberry Pi. Se não estiver, roda em modo simulado normalmente.

**Mapeamento de pinos:**

| Dispositivo | Pino GPIO |
|---|---|
| sala.luz | 17 |
| sala.ventilador | 27 |
| sala.ar_condicionado | 22 |
| quarto.luz | 23 |
| cozinha.luz | 25 |
| cozinha.cafeteira | 5 |
| banheiro.luz | 6 |
| sala.som_ambiente | 19 |

---

## Autor

**Mateus Hypolito Silva**
Fundação Herminio Ometto — FHO Araras
Engenharia da Computação

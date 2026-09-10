# Prática 1 — Pipeline de Pré-processamento com Filtros Lineares

**Disciplina:** Visão Computacional · CESAR School
**Professor:** Eronides F. da Silva Neto
**Integrante:** Phelipe Calado de Souza Costa

Pipeline de pré-processamento de imagens de placas veiculares, restrito a **filtros lineares**, com métrica
própria de avaliação. Conjunto de 100 imagens no padrão CCPD.

---

## 1. Pré-requisitos

| Item | Versão | Observação |
|---|---|---|
| Python | 3.10 ou superior | `python --version` para conferir |
| VS Code | qualquer recente | |
| Extensão **Python** | Microsoft | ID `ms-python.python` |
| Extensão **Jupyter** | Microsoft | ID `ms-toolsai.jupyter` |

Instale as extensões pela barra lateral do VS Code (`Ctrl+Shift+X`, ou `Cmd+Shift+X` no macOS),
pesquisando pelos IDs acima.

---

## 2. Estrutura do projeto

Monte exatamente esta árvore. Os caminhos no notebook são **relativos à pasta `notebooks/`**, então a
estrutura precisa ser respeitada para que a execução funcione sem ajustes.

```
pratica1_vc/
├── data/
│   ├── raw/                                  # as 100 imagens .jpg de entrada
├── ├── interim/                              # intermediário, resultado de transformação parcial
│   └── processed/                            # saídas geradas pelo notebook
├── notebooks/
│   └── vc_pratica1_Phelipe_C_S_Costa.ipynb
├── requirements.txt
├── .gitignore
└── README.md
```

Duas convenções por trás disso:

- **`data/raw/` é imutável.** Nenhuma célula do notebook escreve ali. Uma execução malfeita não corrompe os
  dados de entrada.
- **`data/processed/` é descartável.** Pode ser apagada por inteiro e reconstruída com um *Run All*. É essa
  propriedade que torna a reprodutibilidade verificável, e não apenas afirmada.

Ambas ficam fora do controle de versão (ver `.gitignore`): a entrada por não ser nossa, a saída por ser
derivada. O repositório versiona código.

---

## 3. Preparação do ambiente

### 3.1 Criar a pasta e abrir no VS Code

```bash
mkdir pratica1_vc && cd pratica1_vc
code .
```

Crie as subpastas `data/raw`, `data/processed` e `notebooks`, e coloque nelas os arquivos do projeto.

### 3.2 Criar o ambiente virtual

Abra o terminal integrado do VS Code (``Ctrl+` ``) **na raiz do projeto** e execute:

**Windows (PowerShell)**
```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

> Se o PowerShell bloquear o script de ativação, execute uma única vez:
> `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned`

**macOS / Linux**
```bash
python3 -m venv .venv
source .venv/bin/activate
```

O prompt deve passar a exibir `(.venv)`. O ambiente virtual isola as dependências deste projeto das demais
instalações da máquina — o que é o que permite ao `requirements.txt` significar alguma coisa.

### 3.3 Instalar as dependências

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

### 3.4 Selecionar o kernel no VS Code

1. Abra `notebooks/vc_pratica1_Phelipe_C_S_Costa.ipynb`
2. Clique em **Select Kernel**, no canto superior direito
3. Escolha **Python Environments** → o interpretador dentro de `.venv`

Se o `.venv` não aparecer na lista, use `Ctrl+Shift+P` → *Python: Select Interpreter* → *Enter interpreter
path* e aponte para `.venv/Scripts/python.exe` (Windows) ou `.venv/bin/python` (macOS/Linux).

---

## 4. Preparar os dados

Descompacte o arquivo fornecido pelo professor e copie as **100 imagens `.jpg` diretamente** para
`data/raw/` — sem subpastas intermediárias.

Confira a contagem:

```bash
# Windows (PowerShell)
(Get-ChildItem data\raw\*.jpg).Count

# macOS / Linux
ls data/raw/*.jpg | wc -l
```

O resultado esperado é `100`. O notebook valida isso na ingestão e interrompe com mensagem clara caso
encontre arquivos fora do padrão.

---

## 5. Executar

Com o kernel selecionado, use **Run All** na barra superior do notebook, ou `Ctrl+Shift+P` →
*Notebook: Run All Cells*.

O notebook é projetado para executar **de cima para baixo, sem dependência de ordem manual**. Toda a
parametrização vive em um único bloco `CONFIG` na primeira célula de código — não há constantes espalhadas
pelas demais células.

**Tempo aproximado:** poucos minutos, dominados pela leitura e retificação das 100 imagens.

---

## 6. O que a execução produz

| Saída | Onde |
|---|---|
| Tabelas de perfilamento e correlação | inline, no notebook |
| Figuras de diagnóstico | inline, no notebook |
| 100 imagens filtradas (220×70, cinza, `.jpg` q100) | `data/processed/` |
| `.zip` do entregável | `data/processed/` |

---

## 7. Problemas comuns

**`FileNotFoundError: Nenhum .jpg encontrado`**
As imagens não estão em `data/raw/`, ou ficaram dentro de uma subpasta criada pela descompactação. Mova os
`.jpg` para o nível de `data/raw/` diretamente.

**`ModuleNotFoundError: No module named 'cv2'`**
O kernel selecionado não é o do `.venv`. Refaça o passo 3.4. É o erro mais frequente — o VS Code costuma
oferecer o Python global por padrão.

**Kernel não aparece na lista**
Reinicie o VS Code após criar o `.venv`. Se persistir, confirme que a extensão Jupyter está instalada e
ative o ambiente no terminal antes de abrir o notebook.

**Acentuação estranha nos gráficos**
Sem impacto no resultado. Ocorre quando o Matplotlib não encontra uma fonte com os glifos necessários.

---

## 8. Decisões de projeto registradas

Estas escolhas estão justificadas em detalhe no próprio notebook, na Parte 1:

- **Saída 220×70 px** — razão 3,14:1, a mesma da placa física (440×140 mm). Tamanho fixo privilegia
  comparabilidade entre imagens, ao custo de interpolar para cima as placas menores.
- **Tons de cinza** — o pipeline reduz a escalar e é isso que um reconhecedor consome. O sinal cromático,
  além disso, colapsa nas imagens escuras.
- **`.jpg` qualidade 100** — espelha o formato de entrada. Erro medido de 0,29 níveis de cinza,
  desprezível para o uso pretendido.
- **Todo o código no notebook, sem `src/`** — o entregável exige um notebook autocontido cujas saídas
  coincidam com o `.zip`. Um módulo ausente na máquina do avaliador seria falha de reprodução com custo
  alto e benefício nulo.

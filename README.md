# Análise de Eficiência de Aletas Retangulares Retas

## Este projeto foi desenvolvido para a disciplina de Fenômenos de Transporte 2, Universidade Federal de Sergipe.

### A sua implementação como site faz ser uma aplicação web para análise da eficiência de aletas retangulares retas, utilizando Vue.js 3, Plotly.js para a plotagem de gráficos e npm para gerenciamento de dependências. O objetivo é permitir que o usuário insira parâmetros físicos (coeficiente de convecção, condutividade térmica, espessura e comprimento da aleta) e visualize tanto o valor calculado de eficiência quanto um gráfico interativo que mostra o comportamento da eficiência em função do comprimento.

## Tecnologias utilizadas:
```
Vue.js 3: Framework JavaScript para construção da interface do usuário.
Plotly.js: Biblioteca para geração de gráficos interativos.
Node.js / npm: Ambiente de execução do JavaScript e gerenciador de pacotes.
HTML5 / CSS3: Estrutura e estilização da página.
```
## Estrutura do projeto:
```
├── nodemodules
├── public/ 
│   └── index.html 
├── src/ 
│   └── components/ 
│       └── AletasCalc.vue 
│   └── App.vue 
│   └── main.js 
├── babel.config.js 
├── jsconfig.json 
├── package-lock.json 
├── package.json 
├── vue.config.js 
└── README.md 
```
#### App.vue: carrega o componente AletasCalc.
#### AletasCalc.vue: contém o template (inputs, resultados, gráfico), o script (script setup) com validações e cálculos, e o estilo específico do componente.

## Validações e Parâmetros
### No arquivo AletasCalc.vue, definimos um computed chamado isValid que verifica se todos os valores de entrada são numéricos e se estão dentro dos intervalos pré-estabelecidos:
```
Coeficiente de Convecção (h): 0.1 ≤ h ≤ 10.000 (W/m²K)
Condutividade Térmica (k): 0.02 ≤ k ≤ 500 (W/mK)
Espessura (t): 0.001 ≤ t ≤ 0.1 (m)
Comprimento (L): 0.01 ≤ L ≤ 10 (m)
Esses limites foram definidos para cobrir uma ampla gama de cenários, respeitando a realidade física de aplicações.
```
## Como Executar Localmente:

### 1. Instalar Node.js e npm
### Faça o download do Node.js (versão LTS recomendada) e instale no seu computador.
### Para verificar a instalação, abra um terminal e execute:
```
node -v
npm -v
```

### 2.  Clonar o Repositório
### No terminal, rode:
```
git clone https://github.com/leafarfernandes/transcal-aletas.git
cd transcal-aletas
```

### 3. Instalar Dependências:
### Dentro da pasta do projeto, execute:
```
npm install
```

### 4. Executar a Aplicação em Ambiente de Desenvolvimento
### Após instalar as dependências, rode:
```
npm run serve
```
### O terminal exibirá um link do tipo http://localhost:3000/ ou http://localhost:8080/. Abra esse link no seu navegador.
```
1. Caso não consiga ainda assim, são necessários:
2. Instalar algum editor de código (Visual Studio Code, Atom, Sublime Text, Vim etc..)
3. Criar pasta
4. Instalar o Node.js
5. Instalar o Vue.js 3
6. Implementar o algoritmo da mesma forma da estrutura do atual projeto
7. Executar "npm run serve" na pasta local
```

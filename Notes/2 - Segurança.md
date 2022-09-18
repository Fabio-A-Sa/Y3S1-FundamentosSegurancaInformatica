# 2 - Segurança

Os sistemas devem ser correctos, tanto a nível de inputs como de formatação da informação. Deve prever potenciais ataques e não comprometer dados sensíveis dos utilizadores. <br>
No entanto isto não é o suficiente. A segurança é um processo contínuo, onde se cria novas defesas a partir da invenção de novos ataques, ambicionando um equilíbrio ao longo do tempo. Há três eixos essenciais:

1. **Confidencialidade** - segredo, privacidade;
2. **Integridade** - não alteração de dados, dados fidedignos e autênticos;
3. **Disponibilidade** - existência, constância;

## 2.1 - Modelos de Segurança

### 2.1.1 - Binário

Característico das criptografias. Há caracterização formal do atacante e dos objectivos da segurança do sistema, de forma a garantir que naquele modelo nenhum ataque poderá ocorrer.

#### Desvantagens

- Demasiado tempo usado numa fase inicial;
- Implementação da segurança sem ter em conta o custo monetário;
- Não útil para sistemas complexos;
- É abstrato e não aplicável em sistemas concretos / reais;
- Ignora problemas reais (por exemplo side-channels);

### 2.1.2 - Gestão de Risco

Característico da engenharia de software e segurança no mundo real. Minimiza os riscos de ataques em função das ameaças mais prováveis. Equilibra o possível custo com as potenciais perdas. É necessário ter resiliência, mitigar os problemas e ter sempre em conta o risco.

#### Desvantagens

- Pode haver incorreção na análise do risco;
- Mesmo com este modelo, não há segurança total;

#### Matriz de análise de risco

Existem dois eixos: ativos (#TODO) e ameaças.  
#TODO

## 2.2 - Vulnerabilidade

Falha que está acessível a um adversário que pode ter a capacidade de a explorar. +Exemplos

## 2.3 - Ataques

Ocorre quando alguém tenta explorar uma vulnerabilidade. Existem ataques ativos (), passivos () e DDos. <br>
Estrutura de um ataque:

1. Motivo
2. Oportunidade
3. Método

## 2.4 - Ameaça

Causa 

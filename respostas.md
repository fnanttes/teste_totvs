# 📝 Teste Técnico - Desenvolvedor ADVPL  

Este documento contém minhas respostas para as questões do teste técnico para vaga de Desenvolvedor ADVPL na TOTVS.  

---

## 1️⃣ Nome das tabelas do ERP Protheus  

| Sigla           | Nome da Tabela         |
|-----------------|------------------------|
| **SX2**         | Dicionário de dados    |
| **SX3**         | Estrutura de campos    |
| **SIX**         | Índices das Tabelas    |
| **SX6**         | Parâmetros do Sistema  |
| **SX7**         | Gatilhos do Sistema    |
| **SYS_USR**     | Usuários do Sistema    |
| **SYS_COMPANY** | Empresas Cadastradas   |
| **XB3**         | Informações do release |
| **XB5**         | Layout de relatórios   |
| **SX5**         | Tabelas genéricas      |

---

## 2️⃣ Problema na alteração de parâmetro do sistema  

A criação do **parâmetro** só foi realizada para as empresas **01 e 02**. A solução para funcionar para todas as emrpesas é criá-lo para as demais empresas. Visto que não é citado que há unidades de negócio, então existe um dicionário por empresa.  

---

## 3️⃣ Melhor prática para exibir descrições em MBROWSE  

Para exibir uma **descrição de outro cadastro** dentro de um **MBROWSE**, a melhor prática é utilizar um **lookup dinâmico**, evitando armazenar a descrição duplicada.  

📌 **Opções recomendadas:**  
1. **DbSeek()** para buscar a descrição dinamicamente na tabela de referência.  
2. **JOIN em SQL** para carregar a descrição diretamente da base.  

---

## 4️⃣ Vantagens e Desvantagens de SOAP e REST  

Referente a SOAP e REST, segue baixo estão suas principais vantagens e desvantagens na minha opinião:

### 🔹 **SOAP**  
✅ **Vantagens:**  
- Mais seguro.
- Suporte a **transações complexas**.  
- Padrão estruturado (**XML/WSDL**).  
- Independente de protocolo (funciona com **HTTP, SMTP, TCP**).  

❌ **Desvantagens:**  
- Mais **lento e pesado**.  
- Difícil de implementar (exige **WSDL**).  
- Menos flexível para mudanças.  

### 🔹 **REST**  
✅ **Vantagens:**  
- Mais **leve e rápido**.  
- Simples de implementar.  
- Flexível e compatível com **Web e Mobile**.  
- Suporte a **cache** para otimizar requisições.  

❌ **Desvantagens:**  
- Segurança precisa ser **configurada manualmente**.  
- Sem suporte nativo a **transações complexas**.  

---

## 5️⃣ Segurança em chamadas de Webservice  

Para garantir a **segurança** em Webservices no **Protheus**, algumas boas práticas devem ser seguidas:

### 🔒 **1. Autenticação e Autorização**  
- Utilizar **Auth 2.0**.  

### 🔒 **2. Criptografia de Dados**  
- Habilitar **SSL/TLS** para evitar interceptação de dados.  
- Configurar o **AppServer do Protheus** para utilizar SSL.  

---

## 6️⃣ MER 

---

### 📌 **Entidades e Atributos Principais**  

1️⃣ **Clientes** (`Clientes`)  
   - `ID_Cliente` (PK)  
   - `Nome`  
   - `CPF/CNPJ`  
   - `Endereço`  
   - `Telefone`  

2️⃣ **Produtos** (`Produtos`)  
   - `ID_Produto` (PK)  
   - `Nome_Produto`  
   - `Descrição`  
   - `Preço`  
   - `Estoque`  

3️⃣ **Vendas** (`Vendas`)  
   - `ID_Venda` (PK)  
   - `ID_Cliente` (FK → Clientes)  
   - `Data_Venda`  
   - `Valor_Total`  

4️⃣ **Itens da Venda** (`Itens_Venda`)  
   - `ID_Item` (PK)  
   - `ID_Venda` (FK → Vendas)  
   - `ID_Produto` (FK → Produtos)  
   - `Quantidade`  
   - `Preço_Unitário`  
   - `Subtotal` (`Quantidade * Preço_Unitário`)  

---

### 🔗 **Relacionamentos**  

- Cada **cliente** pode ter **várias vendas**.  
- Cada **venda** pode ter **vários itens**.  
- Cada **item da venda** refere-se a um **produto**.  

---

### 📌 **Representação Visual do MER**  

```
[Clientes] 1 ─── ∞ [Vendas] 1 ─── ∞ [Itens_Venda] ∞ ─── 1 [Produtos]
```

---

## 7️⃣ Query SQL - Listagem de Notas Fiscais Ordenadas  

```sql
SELECT 
    SF2.F2_DOC AS Numero_Nota_Fiscal,
    SF2.F2_EMISSAO AS Data_Emissao,
    SF2.F2_CLIENTE AS Codigo_Cliente,
    SD2.D2_COD AS Codigo_Produto,
    SD2.D2_QUANT AS Quantidade,
    SD2.D2_PRCVEN AS Preco_Unitario,
    SD2.D2_TOTAL AS Valor_Total
FROM SF2
JOIN SD2 ON SF2.F2_DOC = SD2.D2_DOC
ORDER BY SD2.D2_TOTAL DESC
```
---

## 8️⃣ Query SQL - Total de Notas por Cliente e Data  

```sql
SELECT 
    SF2.F2_CLIENTE AS Codigo_Cliente,
    SF2.F2_EMISSAO AS Data_Emissao,
    SUM(SF2.F2_VALBRUT) AS Valor_Total
FROM SF2
WHERE SF2.F2_EMISSAO BETWEEN '20240101' AND '20240131' -- Ajuste a data conforme necessário
GROUP BY SF2.F2_CLIENTE, SF2.F2_EMISSAO
ORDER BY SF2.F2_EMISSAO, SF2.F2_CLIENTE
```

---

## 9️⃣ Otimização de Query Lenta no Protheus  

1️⃣ **Analisar o Plano de Execução (`EXPLAIN`)** → Identificar índices ineficientes e tabelas que sofrem **Full Scan**.  
2️⃣ **Criar e Ajustar Índices** → Garantir que os campos em **JOINs, WHERE e ORDER BY** possuam **índices apropriados**.  
3️⃣ **Evitar `SELECT *`** → Buscar **apenas as colunas necessárias** para reduzir carga no banco.  
4️⃣ **Otimizar `JOINs` e Subqueries** → Usar **JOINs indexados** e evitar subconsultas pesadas.  
5️⃣ **Implementar Paginação (`LIMIT e OFFSET`)** → Melhorar performance em consultas com **grande volume de dados**.  
6️⃣ **Evitar Funções em Filtros** → Evitar expressões como `WHERE UPPER(campo) = 'X'`, pois **impedem o uso de índices**.  
7️⃣ **Monitorar Locks e Deadlocks** → Consultas lentas podem gerar **bloqueios no banco**, afetando todo o ERP.  

---

## 🔟 Query SQL - Quantidade de Clientes por Estado  

```sql
SELECT 
    SA1.A1_COD AS Codigo_Cliente,
    SA1.A1_NOME AS Nome_Cliente,
    SA1.A1_EST AS Estado,
    COUNT(SA1.A1_COD) OVER (PARTITION BY SA1.A1_EST) AS Qtd_Por_Estado
FROM SA1
ORDER BY SA1.A1_EST;
```

---

## 1️⃣1️⃣ Correção do Algoritmo de Cálculo do Peso Ideal  

Os erros que identifiquei foram: **falta de leitura da altura antes do cálculo, ausência de validação de entrada e um erro de formatação matemática**. 

### 📌 **Correção em portugol**
```plaintext
INICIO
    ESCREVER "Digite seu nome: "
    LER nome
    
    ESCREVER "Digite sua altura (m): "
    LER altura

    ESCREVER "Digite seu sexo (M/F): "
    LER sexo

    SE sexo = "M" ENTAO
        peso_ideal ← (72.7 * altura) - 58
    SENAO SE sexo = "F" ENTAO
        peso_ideal ← (62.1 * altura) - 44.7
    SENAO
        ESCREVER "Erro: Sexo inválido! Digite M ou F."
        SAIR
    FIM_SE

    ESCREVER "Peso ideal: ", peso_ideal

FIM
```

---

## 1️⃣2️⃣ Algoritmo para Ordenação de Nomes

### 📌 **Lista Original de Alunos**  
- André Souza  
- Paulo Silva  
- Rodrigo Rodrigues  
- André de Souza  
- Rafael de Paula  
- Paula Fernandes  
- Roberta Antunes  
- Roberto Ferreira  
- Bento Bernardes  
- Fátima Lopes  
- Maria Paula  
- Mel Lisboa  
- Peter Parker  

### 📌 **Código JavaScript para Ordenação**

```javascript
// Lista de nomes dos alunos
const alunos = [
    "André Souza", "Paulo Silva", "Rodrigo Rodrigues", "André de Souza",
    "Rafael de Paula", "Paula Fernandes", "Roberta Antunes", "Roberto Ferreira",
    "Bento Bernardes", "Fátima Lopes", "Maria Paula", "Mel Lisboa", "Peter Parker"
];

// Ordenação alfabética
alunos.sort();

// Exibir lista ordenada
console.log("Lista de alunos ordenada:");
alunos.forEach(aluno => console.log(aluno));
```

---

## 1️⃣3️⃣ Como Liberar Memória em ADVPL  

---

### 📌 **Como liberar memória corretamente?**  

1️⃣ **Para Objetos** → Definir como `Nil`  
```advpl
oWebService := Nil // Remove o objeto da memória
```

2️⃣ **Para Vetores** → Esvaziar com `{}`
```advpl
aDados := {} // Esvazia o vetor, liberando memória
```

3️⃣ **Para Arrays** → Redefinir tamanho com `ASize()`
```advpl
ASize(aDados, 0) // Remove todos os elementos do array
```

---

## 1️⃣4️⃣ Calculadora - Tabuada do 1 ao 10

Por mais simples que pareça, eu nunca fiz isso em ADVPL. Mas acho que é isso...

---

```advpl
User Function Tabuada()
    Local nMult, nFator
    
    // Percorre os números de 1 a 10
    For nMult := 1 To 10
        FWLogMsg("Tabuada do " + CValToChar(nMult) + ":")
        
        // Multiplica de 1 a 10
        For nFator := 1 To 10
            FWLogMsg(nMult + " x " + nFator + " = " + (nMult * nFator))
        Next
        
        FWLogMsg("") // Linha em branco para separar as tabuadas
    Next
Return
```

---

## 1️⃣5️⃣ Cálculo do Fatorial

---

```javascript
// Função para calcular o fatorial de um número
function calFator(numero) {
    if (numero < 0) {
        return "Número inválido! O fatorial não é definido para números negativos.";
    }

    let fatorial = 1;
    for (let i = 1; i <= numero; i++) {
        fatorial *= i;
    }

    return fatorial;
}

// Entrada do usuário via prompt
const numero = parseInt(prompt("Digite um número para calcular o fatorial:"));
const resultado = calFator(numero);

// Exibir resultado no console
console.log(`O fatorial de ${numero} é ${resultado}`);
```

---

## 1️⃣6️⃣ Explicação e Aplicação da Função ADVPL  

Esta função busca um código em um vetor e, se ele existir, **soma um valor** ao total acumulado. Se o código **não existir**, ele é **adicionado ao vetor**.  

---

```advpl
User Function Teste(aVet, cExp, nVal)
    Local nPos := 0
    
    // Procura a posição do elemento no vetor
    nPos := aScan(aVet, {|x| x[1] == cExp})

    // Se não encontrou, adiciona um novo item ao vetor
    If nPos == 0
        aAdd(aVet, { cExp, 0 })
        nPos := Len(aVet)
    EndIf

    // Soma o valor na posição encontrada
    aVet[nPos][2] += nVal

Return aVet
```

---

## 1️⃣7️⃣ Cálculo de Salário com Comissão

---

```advpl
User Function CalcSal(nSalFix, nVendas)
    Local nComis := 0

    // Calcula a comissão sobre as vendas
    If nVendas <= 1800
        nComis := nVendas * 0.03
    Else
        nComis := (1800 * 0.03) + ((nVendas - 1800) * 0.07)
    EndIf

// Calcula o salário total
Return nSalFix + nComis
```

---

## 1️⃣8️⃣ Troca de Valores sem Variável Auxiliar em ADVPL  

Se eu entendi corretamente, é isso...

---

```advpl
User Function TrocaAB(nA, nB)
    nA := nA + nB
    nB := nA - nB
    nA := nA - nB

Return {nA, nB}
```

---

## 1️⃣9️⃣ Métodos Ágeis para Sustentação e Projetos  

Sendo breve, para sustentação, Kanban é a melhor opção, pois permite fluxo contínuo, priorização dinâmica e resposta rápida a incidentes.
Para projetos e inovação, Scrum organiza o trabalho em sprints curtas e entregas incrementais.
Lean e Design Thinking são ideais para inovação, prototipação e validação rápida. A equipe que trabalhei na última empresa utilizava, mas não era de tecnologia, usava para operação. Interessante.

---

## 2️⃣0️⃣ Comandos Git para Implementação de Nova Funcionalidade  

Eu não uso muito o git no prompt, fazendo linha de comando, uso mais pelo VSCode na forma gráfica. Mas consigo fazer algumas coisas.

Então, para atualizar a branch `master` local com as últimas alterações do repositório remoto, uso `git checkout master && git pull origin master`.

Para criar uma nova branch baseada na `master`, uso `git checkout -b feature/nova-funcionalidade`.

Após fazer as alterações no código, adiciono ao controle de versão com `git add .`, crio um commit com `git commit -m "Implementação da nova funcionalidade"` e envio a branch para o repositório remoto com `git push origin feature/nova-funcionalidade`.

Para abrir um Pull Request, acesso a plataforma do git na web, seleciono a branch e solicito a revisão antes da integração ao repositório principal.  

Faria dessa forma, mas uso o VsCode na maioria das vezes, pra não dizer sempre. rs
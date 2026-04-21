# Configuração de Pizzas por TIPO (Nova Funcionalidade)

## Resumo

O sistema foi atualizado para suportar **pizzas renderizadas por TIPO com preços base**, em vez do método tradicional de sabores com preços adicionais.

### O que mudou?

**Antes (Sistema Tradicional):**

- Cliente escolhe sabor(es) da pizza
- Cada sabor pode ter um preço adicional (premium)
- Preço final = tamanho + maior preço de sabor + borda

**Agora (Novo Sistema):**

- Cliente escolhe o TIPO de pizza (ex: Mozzarella, Panceta con Catupiry)
- Cada tipo tem seu próprio preço BASE
- O tamanho tem um multiplicador que altera o preço
- Preço final = preço_tipo × multiplicador_tamanho + borda

---

## Estrutura de Dados no Banco

### Campo: `montagem_config` na tabela `produtos`

Para usar o novo sistema de **pizzas por tipo**, configure o campo `montagem_config` com a seguinte estrutura JSON:

```json
{
  "pizza": {
    "tipos": [
      {
        "nome": "Mozzarella",
        "preco": 50000,
        "desc": "Salsa de Tomate, Mozzarella, Tomate y Orégano",
        "img": "https://example.com/mozzarella.jpg"
      },
      {
        "nome": "Panceta con Catupiry",
        "preco": 55000,
        "desc": "Salsa de Tomate, Mozzarella, Panceta, Catupiry",
        "img": "https://example.com/panceta.jpg"
      },
      {
        "nome": "Camarones",
        "preco": 60000,
        "desc": "Salsa de Tomate, Mozzarella, Camarones",
        "img": "https://example.com/camarones.jpg"
      }
    ],
    "tamanhos": [
      {
        "nome": "Personal",
        "fatias": 4,
        "cm": 25,
        "multiplicador": 1
      },
      {
        "nome": "Media",
        "fatias": 6,
        "cm": 35,
        "multiplicador": 1.5
      },
      {
        "nome": "Grande",
        "fatias": 8,
        "cm": 40,
        "multiplicador": 2
      }
    ],
    "bordas": [
      {
        "nome": "Borda Recheada de Queijo",
        "preco": 8000
      },
      {
        "nome": "Borda Recheada de Chocolate",
        "preco": 8000
      }
    ],
    "tem_borda": true,
    "borda_preco": 8000
  }
}
```

---

## Campos Explicados

### `tipos` (Array)

Lista de tipos de pizzas disponíveis.

| Campo   | Tipo   | Obrigatório | Descrição                       |
| ------- | ------ | ----------- | ------------------------------- |
| `nome`  | String | ✅          | Nome do tipo (ex: "Mozzarella") |
| `preco` | Number | ✅          | Preço base em Guaranis          |
| `desc`  | String | ❌          | Descrição curta (ingredientes)  |
| `img`   | String | ❌          | URL da imagem                   |

### `tamanhos` (Array)

Lista de tamanhos disponíveis para as pizzas.

| Campo           | Tipo   | Obrigatório | Descrição                      |
| --------------- | ------ | ----------- | ------------------------------ |
| `nome`          | String | ✅          | Nome do tamanho (ex: "Media")  |
| `fatias`        | Number | ✅          | Quantidade de fatias           |
| `cm`            | Number | ✅          | Diâmetro em cm                 |
| `multiplicador` | Number | ✅          | Multiplicador do preço do tipo |

**Exemplo de cálculo:**

- Tipo: Mozzarella (50.000 Gs)
- Tamanho: Media (multiplicador 1.5)
- **Preço: 50.000 × 1.5 = 75.000 Gs**

### `bordas` (Array)

Opções de borda recheada disponíveis.

| Campo   | Tipo   | Obrigatório | Descrição       |
| ------- | ------ | ----------- | --------------- |
| `nome`  | String | ✅          | Nome da borda   |
| `preco` | Number | ✅          | Preço adicional |

### `tem_borda` (Boolean)

Se `true`, mostra opção de borda. Se `false`, não exibe.

### `borda_preco` (Number)

Preço padrão da borda (usado se não houver lista customizada).

---

## Como Funciona a UX

### Passo 1: Escolher Tipo

Cliente vê uma lista de tipos de pizzas com:

- Imagem (opcional)
- Nome do tipo
- Descrição dos ingredientes
- Preço base

### Passo 2: Escolher Tamanho

Após escolher o tipo, cliente seleciona o tamanho:

- Nome (ex: "Media")
- Quantidade de fatias
- Diâmetro em cm
- Multiplicador é aplicado automaticamente

### Passo 3: Borda (Opcional)

Cliente pode adicionar borda recheada:

- Sem borda
- Borda Recheada (+ preço adicional)

---

## Compatibilidade

✅ **Sistema Tradicional (por Sabor)** ainda funciona!

Se você não configurar `tipos` no JSON, o sistema usará o método tradicional de sabores.

---

## Exemplo Completo

```json
{
  "pizza": {
    "tipos": [
      {
        "nome": "Mozzarella",
        "preco": 50000,
        "desc": "Clássica",
        "img": "url..."
      },
      {
        "nome": "Pepperoni",
        "preco": 52000,
        "desc": "Com pepperoni",
        "img": "url..."
      },
      {
        "nome": "Especial",
        "preco": 60000,
        "desc": "Completa",
        "img": "url..."
      }
    ],
    "tamanhos": [
      { "nome": "Pequena", "fatias": 4, "cm": 25, "multiplicador": 1 },
      { "nome": "Média", "fatias": 6, "cm": 35, "multiplicador": 1.5 },
      { "nome": "Grande", "fatias": 8, "cm": 40, "multiplicador": 2 }
    ],
    "bordas": [
      { "nome": "Sem Borda", "preco": 0 },
      { "nome": "Recheada", "preco": 8000 }
    ]
  }
}
```

---

## Passos para Ativar

1. **No seu banco de dados**, altere o campo `montagem_config` da sua pizza
2. **Estrutura**: Copie a estrutura JSON acima
3. **Customize** com seus tipos, tamanhos e preços
4. **Teste** no app do cliente
5. **O sistema detectará automaticamente** o modo por tipo e renderizará corretamente

---

## Notas Importantes

- ⚠️ Se houver `tipos` no JSON, o sistema usa o novo modo (por tipo)
- ⚠️ Se não houver `tipos`, usa o modo tradicional (por sabor)
- ✅ Ambos os sistemas trabalham em paralelo
- ✅ O resumo da pizza mostra o tipo escolhido, tamanho e borda
- ✅ O carrinho registra todos os dados para referência

---

## Troubleshooting

### Pizza não está renderizando corretamente

1. Verifique se o JSON é válido (use um validador JSON online)
2. Verifique se `tipos` está presente
3. Verifique se cada tipo tem `nome` e `preco`
4. Verifique se cada tamanho tem `nome`, `fatias`, `cm` e `multiplicador`

### Preço não está calculando corretamente

1. Verifique o multiplicador do tamanho
2. Fórmula: `preço_tipo × multiplicador = preço_final`
3. Borda é sempre adicionada: `preço_final + borda_preco`

### Cliente vê "TIPO" de pizza mas deveria ver "SABOR"

- Confirme que `tipos` NÃO está configurado no JSON
- O sistema deve detectar automaticamente e usar sabores

---

## Dúvidas ou Problemas?

Contato para suporte técnico: [seu email aqui]

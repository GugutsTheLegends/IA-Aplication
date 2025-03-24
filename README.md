# README - Chatbot com LangChain e Groq

## Descrição
Este projeto implementa um chatbot utilizando a biblioteca LangChain integrada à API da Groq. Ele permite manter um histórico de conversação por sessão e otimiza o processamento das mensagens através de pipelines configuráveis. O modelo utilizado para respostas é o **Gemma2-9b-It**.

## Requisitos
Antes de executar o código, certifique-se de ter instalado as seguintes dependências:

- Python 3.8+
- Bibliotecas:
  - `langchain`
  - `langchain_groq`
  - `langchain_community`
  - `python-dotenv`

Para instalar as dependências, utilize:
```sh
pip install langchain langchain_groq langchain_community python-dotenv
```

## Configuração
### 1. Definir chave da API
O chatbot utiliza a API do **Groq** para gerar respostas. Crie um arquivo `.env` no diretório do projeto e adicione a seguinte linha:
```sh
GROQ_API_KEY=your_api_key_here
```
Substitua `your_api_key_here` pela sua chave de API da Groq.

### 2. Estrutura do Código
O código está organizado da seguinte forma:

- **Carregamento da API**: Carrega a chave da API do arquivo `.env` e inicializa o modelo Groq.
- **Histórico de conversação**: Gerencia um histórico de mensagens separado por sessão.
- **Interação com o modelo**: Envia mensagens e recebe respostas.
- **Uso de prompt templates**: Cria prompts personalizados para melhorar as interações.
- **Gerenciamento de memória**: Mantém um limite de tokens no histórico para evitar excesso de contexto.

## Como Usar
### 1. Executar uma interação simples
Para testar a resposta do chatbot, execute:
```python
response = with_message_history.invoke(
    [HumanMessage(content="Oi, meu nome é Gustavo e sou um engenheiro de dados.")],
    config={"configurable": {"session_id": "chat1"}}
)
print("Resposta do modelo:", response)
```
Isso inicia uma sessão e obtém uma resposta do modelo.

### 2. Utilizar prompts personalizados
O prompt é definido para estruturar melhor as respostas:
```python
prompt = ChatPromptTemplate.from_messages(
    [
        ("system", "Você é um assistente útil. Responda todas as perguntas com precisão."),
        MessagesPlaceholder(variable_name="messages")
    ]
)
```
Para usar o prompt:
```python
chain.invoke({"messages": [HumanMessage(content="Oi, meu nome é Gustavo")]})
```

### 3. Gerenciar memória do chatbot
Para evitar que a conversa ultrapasse um limite de tokens:
```python
trimmer = trim_messages(
    max_tokens=45,
    strategy="last",
    token_counter=model,
    include_system=True,
    allow_partial=False,
    start_on="human"
)
```
Isso garante que apenas as mensagens mais recentes sejam mantidas.

### 4. Criar um pipeline de execução
Para otimizar a passagem de informações entre os componentes:
```python
chain = (
    RunnablePassthrough.assign(messages=itemgetter("messages") | trimmer)
    | prompt
    | model
)
```

### 5. Realizar uma interação final
```python
response = chain.invoke(
    {
        "messages": [HumanMessage(content="Qual sorvete eu gosto?")],
        "language": "Português"
    }
)
print("Resposta final:", response.content)
```

## Conclusão
Este projeto demonstra como utilizar o **LangChain** e a API **Groq** para criar um chatbot capaz de manter contexto e gerar respostas personalizadas. O uso de templates e gestão de memória melhora a qualidade das respostas e a eficiência do modelo.


conexao_planilha_omie 🔗
Este projeto é uma solução de automação simples e poderosa, desenvolvida em Google Apps Script, para simplificar o cadastro de clientes na OMIE. Ele elimina a necessidade de preencher inúmeros campos manualmente, permitindo que você colete apenas as informações essenciais através de um Formulário Google e as envie de forma automática e segura para o sistema.

✨ Motivação do Projeto
A ideia surgiu para otimizar o fluxo de trabalho, transformando uma tarefa repetitiva e demorada em um processo ágil. Em vez de navegar por telas e preencher dezenas de campos, esta ferramenta foca na eficiência, economizando tempo e minimizando erros na inserção de dados.

🚀 Funcionalidades Principais
Automação Inteligente: Captura dados de um Formulário Google e os envia para a API da OMIE.

Segurança de Credenciais: As chaves da API são armazenadas de forma segura com o PropertiesService, mantendo-as fora do código.

Fluxo de Trabalho Otimizado: Reduz o trabalho manual, permitindo que você se concentre em tarefas mais importantes.

Controle de Erros: O código inclui um tratamento robusto de falhas, com mensagens de erro claras registradas no console.

⚙️ Como Funciona o Fluxo
Um novo cliente é registrado por meio de um Formulário Google.

A submissão do formulário aciona um gatilho (onFormSubmit) no Google Apps Script.

O script extrai os dados, prepara o payload JSON e realiza uma chamada POST para a API da OMIE.

A OMIE cadastra o cliente, e o script notifica o sucesso ou erro no console, oferecendo visibilidade total do processo.

🛠️ Configuração e Instalação
Siga estes passos para configurar e começar a usar o projeto:

1. Crie os Formulários e a Planilha
Crie seu Formulário Google com os campos desejados.

Vincule-o a uma Planilha Google para que as respostas sejam salvas automaticamente.

2. Configure o Google Apps Script
Na Planilha Google, acesse Extensões > Apps Script.

Copie e cole o código completo do projeto no editor.

/*** * Esse código será APENAS para uso próprio e para a minha preguiça de inserir dados dos clientes na OMIE.
*/

const props = PropertiesService.getScriptProperties();

const OMIE_APP_KEY = props.getProperty('OMIE_APP_KEY');
const OMIE_APP_SECRET = props.getProperty('OMIE_APP_SECRET');

function onFormSubmit(){
    const form = FormApp.getActiveForm();
    const formResponse = form.getResponses()[form.getResponses().length - 1].getItemResponses();
    console.log(formResponse[0].getResponse())

    const clienteData = {
      call: "IncluirCliente", 
      app_key: OMIE_APP_KEY,     
      app_secret: OMIE_APP_SECRET, 
      param: [{
        "codigo_cliente_integracao": String(formResponse[3].getResponse()).replace(/\D/g, ''),
        "nome_fantasia": formResponse[0].getResponse(),
        "razao_social": formResponse[0].getResponse(),
        "contato": formResponse[0].getResponse(),
        "email": formResponse[1].getResponse(),
        "cep": String(formResponse[3].getResponse()).replace(/\D/g, ''), // Limpa o CEP também por segurança
        "telefone1_ddd": String(formResponse[2].getResponse()).slice(0, 2), 
        "telefone1_numero": String(formResponse[2].getResponse()).slice(2), 
        "pessoa_fisica": "S", 
      }]
    };

    const result = enviarClienteParaOmie(clienteData);
    if (!result.success) {
      console.log(`ERRO: Falha ao processar cliente ${formResponse[0].getResponse()}. Detalhes: ${result.message}`);
    }
}

function enviarClienteParaOmie(clienteData) {
  const url = "[https://app.omie.com.br/api/v1/geral/clientes/](https://app.omie.com.br/api/v1/geral/clientes/)";

  const payloadParaEnvio = JSON.stringify(clienteData);

  console.log("==============================================================");
  console.log(`Preparando para enviar cliente: ${clienteData.param[0].razao_social}`);
  console.log("JSON Payload que será enviado:");
  console.log("==============================================================");

  const options = {
    "method": "post",
    "contentType": "application/json",
    "payload": payloadParaEnvio, // Agora a variável existe
    "muteHttpExceptions": true
  };

  try {
    const response = UrlFetchApp.fetch(url, options);
    const responseCode = response.getResponseCode();
    const responseBody = response.getContentText();

    // Uma resposta bem sucedida da Omie geralmente retorna um JSON com 'codigo_status'
    if (responseCode === 200 || responseCode === 201) {
      const responseJson = JSON.parse(responseBody);
      
      if (responseJson.faultstring) {
         return { success: false, message: `Erro Omie: ${responseJson.faultstring}` };
      }
      
      // Sucesso
      console.log(`Cliente ${clienteData.param[0].razao_social} processado. Resposta: ${responseBody}`);
      return { success: true, message: "Cliente processado com sucesso." };

    } else {
      console.log(`ERRO HTTP: Cliente ${clienteData.param[0].razao_social}. Código: ${responseCode}, Resposta: ${responseBody}`);
      return { success: false, message: `Erro HTTP: ${responseCode} - ${responseBody}` };
    }
  } catch (e) {
    console.log(`EXCEÇÃO: Cliente ${clienteData.param[0].razao_social}. Exceção ao chamar a API: ${e.toString()}`);
    return { success: false, message: `Exceção: ${e.toString()}` };
  }
}

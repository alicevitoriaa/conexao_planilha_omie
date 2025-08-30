# 📊 Conexão Planilha → OMIE  

Automatização simples para envio de dados de clientes do **Google Forms/Sheets** diretamente para o **Omie ERP**, evitando preenchimento manual de informações que não são utilizadas no dia a dia.  

Este projeto nasceu da necessidade de **economizar tempo** no cadastro de clientes na empresa em que trabalho. Como apenas **5 campos** do cadastro da OMIE são realmente utilizados, criei esse script para integrar os dados de forma automática.  

---

## 🚀 Funcionalidades  

- Captura respostas enviadas por um **Google Form**.  
- Extrai apenas os campos necessários (nome, e-mail, telefone, CEP e código de integração).  
- Monta o payload no formato aceito pela **API da OMIE**.  
- Envia automaticamente o cliente via **UrlFetchApp**.  
- Retorna no log se o envio foi **bem-sucedido** ou se ocorreu algum erro.  

---

## 🛠️ Tecnologias  

- **Google Apps Script**  
- **API Omie ERP**  

---

## 📂 Estrutura  

- `onFormSubmit()` → Executado sempre que um formulário é enviado.  
- `enviarClienteParaOmie(clienteData)` → Função responsável por enviar os dados para a API da OMIE.  

---

## ⚙️ Pré-requisitos  

1. Criar um **Google Form** com os seguintes campos mínimos:  
   - Nome Fantasia / Razão Social  
   - E-mail  
   - Telefone  
   - CEP / Código de integração  

2. Configurar as chaves da API da Omie:  
   - `OMIE_APP_KEY`  
   - `OMIE_APP_SECRET`  

   > As chaves devem ser salvas em **Propriedades do Script** no projeto do Google Apps Script.  

---

## ▶️ Como usar  

1. Abra o **Google Apps Script** vinculado ao seu Formulário.  
2. Cole o código deste repositório no editor.  
3. Configure suas variáveis de ambiente (`OMIE_APP_KEY` e `OMIE_APP_SECRET`) em:  
   - `Editor > Configurações do Projeto > Propriedades do Script`.  
4. Autorize o script para acessar o Form e realizar requisições externas.  
5. Envie uma resposta de teste no Form e confira os logs:  
   - `Executar > Ver registros`.  

---

## 📝 Observações  

> ⚠️ **Uso pessoal:** Este projeto foi desenvolvido **para uso próprio**, com o objetivo de reduzir o tempo de digitação manual de clientes na OMIE.  
> Não cobre todos os campos e possibilidades oferecidos pela API.  

---

## 📌 Melhorias futuras  

- [ ] Criar tratamento para diferentes tipos de pessoa (Física/Jurídica).  
- [ ] Mapear mais campos do formulário para a OMIE.  
- [ ] Adicionar testes automatizados com dados mockados.  

---

## 📜 Licença  

Este projeto está sob a licença **MIT** – fique à vontade para adaptar e evoluir para suas próprias necessidades.  

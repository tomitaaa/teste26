# Plano de Testes - AnotAI Gestão Financeira

Data de elaboração: 03/11/2026  
Projeto-base: [rafaelzottesso/naes2026](https://github.com/rafaelzottesso/naes2026)  
Ferramenta de gerenciamento: Testiny  
Projeto no Testiny: `AnotAI - Gestão Financeira (ANOT)`

## 1. Objetivo

Verificar se o AnotAI atende corretamente aos principais fluxos de gestão financeira, incluindo cadastro de pessoas, criação de lançamentos, geração e quitação de parcelas, classificação de situações e proteção dos dados de cada usuário.

## 2. Objeto de teste

Aplicação Web AnotAI, desenvolvida no projeto da disciplina NAES. O sistema permite registrar receitas e despesas, pessoas, categorias, centros de lançamento e formas de pagamento, gerar parcelas e acompanhar indicadores financeiros.

## 3. Escopo

Será testado:

- cadastro de pessoa com CPF ou CNPJ;
- validação da quantidade de dígitos e duplicidade de documento;
- criação de lançamento à vista e parcelado;
- divisão do valor e cálculo do restante na última parcela;
- validação do intervalo entre parcelas;
- validação do valor líquido do lançamento;
- registro de pagamento de parcela;
- classificação de parcelas como Paga, Vencida ou Em aberto;
- isolamento dos dados entre usuários.

Não será testado nesta etapa:

- desempenho, carga e estresse;
- testes de invasão e auditoria de segurança;
- acessibilidade completa;
- compatibilidade com todos os navegadores e dispositivos;
- integrações de inteligência artificial;
- implantação em nuvem e recuperação de desastre.

## 4. Estratégia de teste

Serão realizados testes funcionais de caixa-preta, executados manualmente em navegador Web. Os casos foram derivados das regras observáveis do sistema e serão gerenciados no Testiny. Antes de cada execução serão preparados usuário e dados de apoio. O resultado obtido será comparado ao resultado esperado e registrado como `Passed` ou `Failed`, sem alterar o esperado para acomodar falhas.

## 5. Técnicas de teste

- Particionamento de equivalência: documentos, valores monetários, quantidade de parcelas e estados de pagamento válidos e inválidos.
- Análise do valor limite: CPF com 11 dígitos, CNPJ com 14 dígitos, CEP com 8 dígitos, mínimo de 1 parcela, valor mínimo de R$ 0,01 e intervalo mínimo de 1 dia.
- Testes de transição de estado: parcela Em aberto, Vencida e Paga.
- Testes baseados em casos de uso: cadastro de pessoa, lançamento, parcelamento e pagamento.
- Testes negativos: documento duplicado, parcelamento sem intervalo e valor líquido menor ou igual a zero.

## 6. Ambiente e recursos

- Aplicação: código-base `rafaelzottesso/naes2026`, branch `master`.
- Tecnologia: Python e Django 5.2.14, com banco configurado para o ambiente de teste.
- Computador com acesso à Internet.
- Google Chrome atualizado.
- Ambiente local ou de homologação do AnotAI.
- Testiny para planejamento e posterior registro da execução.
- Dados fictícios, sem informações pessoais ou financeiras reais.

## 7. Critérios de aprovação

Um caso será aprovado quando todos os resultados observados corresponderem ao resultado esperado e não houver gravação indevida de dados. Será reprovado quando qualquer comportamento divergir do esperado.

Na futura execução, o plano será considerado aprovado quando:

- 100% dos casos forem executados e registrados;
- não houver defeito crítico ou alto bloqueando os fluxos essenciais;
- os casos de cadastro, lançamento, parcelamento, pagamento e isolamento de usuário forem aprovados;
- divergências forem documentadas para correção e reexecução.

## 8. Cronograma

1. Análise do sistema e das regras de negócio.
2. Elaboração do Plano de Testes.
3. Especificação e cadastro dos casos no Testiny.
4. Preparação do ambiente e dos dados.
5. Execução manual dos casos.
6. Registro, análise dos resultados e reexecução das correções.

## 9. Riscos

- indisponibilidade do AnotAI ou do Testiny;
- ambiente sem dependências, banco ou dados iniciais configurados;
- mudança das regras durante a atividade;
- diferenças entre ambiente local e homologação;
- dados de teste insuficientes ou inconsistentes;
- falha de conexão com a Internet;
- ausência de credenciais de teste para validar isolamento entre usuários.

Mitigação: registrar a versão testada, usar dados controlados, preparar duas contas de teste e documentar impedimentos antes da execução.

## 10. Casos de teste

### CT01 - Cadastrar pessoa com CPF válido

**Pré-condição:** usuário autenticado no AnotAI e tela de Pessoas disponível.

**Procedimento:** acessar Pessoas, selecionar Novo cadastro, informar nome, CPF com 11 dígitos, CEP com 8 dígitos, endereço e cidade válidos e salvar.

**Resultado esperado:** o cadastro é aceito, a pessoa é associada ao usuário e aparece na listagem com os dados informados.

### CT02 - Rejeitar CPF ou CNPJ com quantidade inválida de dígitos

**Pré-condição:** usuário autenticado e formulário de cadastro de pessoa aberto.

**Procedimento:** preencher os campos obrigatórios e informar no Documento um valor diferente de 11 dígitos para CPF e de 14 dígitos para CNPJ; salvar.

**Resultado esperado:** o sistema não grava o cadastro e apresenta a mensagem para informar CPF com 11 dígitos ou CNPJ com 14 dígitos.

### CT03 - Impedir documento duplicado para o mesmo usuário

**Pré-condição:** usuário autenticado e uma pessoa já cadastrada com determinado CPF ou CNPJ.

**Procedimento:** tentar cadastrar outra pessoa usando o mesmo documento já associado à conta atual.

**Resultado esperado:** o sistema mantém apenas o cadastro original e informa que já existe uma pessoa com esse CPF ou CNPJ.

### CT04 - Cadastrar lançamento à vista

**Pré-condição:** usuário autenticado, com categoria, pessoa e forma de pagamento ativas cadastradas.

**Procedimento:** criar uma despesa de R$ 100,00 com 1 parcela, data válida, categoria, pessoa e forma de pagamento; salvar.

**Resultado esperado:** o lançamento é criado e possui exatamente uma parcela de R$ 100,00, com o mesmo vencimento e forma de pagamento.

### CT05 - Dividir lançamento em três parcelas

**Pré-condição:** usuário autenticado, com categoria, pessoa e forma de pagamento ativas cadastradas.

**Procedimento:** criar lançamento de R$ 100,00 em 3 parcelas, usando intervalo de 30 dias entre os vencimentos.

**Resultado esperado:** são geradas três parcelas de R$ 33,33, R$ 33,33 e R$ 33,34; a soma é R$ 100,00 e os vencimentos respeitam intervalos de 30 dias.

### CT06 - Rejeitar parcelamento sem intervalo

**Pré-condição:** usuário autenticado e formulário de novo lançamento aberto.

**Procedimento:** preencher um lançamento válido, informar 4 parcelas e deixar o intervalo entre parcelas vazio; salvar.

**Resultado esperado:** o lançamento não é gravado e o sistema solicita o intervalo em dias para parcelamentos com mais de uma parcela.

### CT07 - Rejeitar valor líquido menor ou igual a zero

**Pré-condição:** usuário autenticado e formulário de novo lançamento aberto.

**Procedimento:** informar valor de R$ 10,00 e desconto de R$ 50,00, mantendo os demais campos obrigatórios válidos; salvar.

**Resultado esperado:** o sistema não grava o lançamento e informa que o valor líquido precisa ser maior que zero.

### CT08 - Exigir valor e data de pagamento em conjunto

**Pré-condição:** usuário autenticado e uma parcela em aberto disponível para edição.

**Procedimento:** editar a parcela, preencher somente o valor pago e deixar a data de pagamento vazia; salvar.

**Resultado esperado:** a baixa não é registrada e o sistema orienta informar valor pago e data de pagamento juntos, ou deixar ambos em branco.

### CT09 - Classificar parcelas por situação

**Pré-condição:** usuário autenticado e parcelas com diferentes datas e estados de pagamento cadastradas.

**Procedimento:** consultar uma parcela quitada, uma não paga com vencimento anterior à data atual e uma não paga com vencimento atual ou futuro.

**Resultado esperado:** as parcelas são classificadas respectivamente como Paga, Vencida e Em aberto, com totais coerentes nos indicadores.

### CT10 - Isolar dados financeiros por usuário

**Pré-condição:** existem duas contas de usuário e dados financeiros cadastrados somente na primeira conta.

**Procedimento:** autenticar com a segunda conta e acessar listas e endereços de detalhes de pessoas, lançamentos e parcelas da primeira conta.

**Resultado esperado:** os dados da primeira conta não aparecem nas listas e seus detalhes não são acessíveis pela segunda conta.

## Situação da atividade

O projeto, a pasta, os dez casos e o Plano de Testes foram cadastrados no Testiny. A execução não foi iniciada, em conformidade com a orientação de que o mesmo projeto será utilizado posteriormente para registrar os resultados.

# Documentação do FinApp - Aplicativo Financeiro com IA e Comandos de Voz

## Visão Geral

O FinApp é um aplicativo financeiro completo que permite aos usuários gerenciar suas finanças pessoais através de uma interface intuitiva, com suporte a comandos de voz e integração com inteligência artificial. O aplicativo está disponível para uso na web, Android e iOS.

## Funcionalidades Principais

1. **Dashboard Financeiro**
   - Visualização de saldo, receitas e despesas
   - Gráficos interativos de fluxo financeiro
   - Resumo de gastos por categoria

2. **Gerenciamento de Transações**
   - Registro de receitas e despesas
   - Categorização automática
   - Histórico de transações

3. **Comandos de Voz**
   - Adição de transações por voz
   - Consultas por voz (ex: "Qual meu saldo atual?")
   - Processamento de linguagem natural com IA

4. **Relatórios e Análises**
   - Relatórios detalhados por período
   - Análise de tendências de gastos
   - Exportação de dados

5. **Metas Financeiras**
   - Definição de objetivos financeiros
   - Acompanhamento de progresso
   - Notificações de marcos atingidos

6. **Integração com IA**
   - Insights personalizados
   - Previsões financeiras
   - Recomendações para economia

## Arquitetura do Sistema

O FinApp foi desenvolvido com uma arquitetura moderna e escalável:

### Frontend
- **Tecnologias**: React.js, Material-UI, Chart.js
- **Recursos**: Interface responsiva, visualizações de dados, formulários interativos

### Backend
- **Tecnologias**: Node.js, Express, MongoDB
- **Recursos**: API RESTful, autenticação JWT, integração com Hugging Face

### Mobile
- **Tecnologias**: React Native
- **Recursos**: Aplicativo nativo para Android e iOS, reconhecimento de voz

### Integração com IA
- **Tecnologias**: Hugging Face LLM
- **Recursos**: Processamento de linguagem natural, análise de dados financeiros

## URLs do Projeto

- **Landing Page**: [https://tclmgaxs.manus.space](https://tclmgaxs.manus.space)
- **Aplicação Web**: [https://eqjuevyw.manus.space](https://eqjuevyw.manus.space)

## Estrutura de Diretórios

```
FinApp/
├── docs/                  # Documentação do projeto
├── landing/               # Landing page
├── frontend/              # Aplicação web (React.js)
│   ├── src/               # Código-fonte
│   │   ├── components/    # Componentes React
│   │   ├── pages/         # Páginas da aplicação
│   │   └── ...
├── backend/               # Backend (Node.js/Express)
│   ├── src/               # Código-fonte
│   │   ├── controllers/   # Controladores da API
│   │   ├── models/        # Modelos de dados
│   │   ├── routes/        # Rotas da API
│   │   └── ...
└── mobile/                # Aplicativo móvel (React Native)
    ├── android/           # Configurações específicas para Android
    ├── ios/               # Configurações específicas para iOS
    └── src/               # Código-fonte compartilhado
```

## Guia de Instalação

### Requisitos

- Node.js 14+
- MongoDB
- React Native CLI (para desenvolvimento móvel)
- Android Studio (para build Android)
- Xcode (para build iOS, apenas em macOS)

### Instalação do Frontend

```bash
cd FinApp/frontend
npm install
npm start
```

### Instalação do Backend

```bash
cd FinApp/backend
npm install
npm start
```

### Instalação do Aplicativo Móvel

```bash
cd FinApp/mobile/FinAppMobile
npm install
```

Para Android:
```bash
npx react-native run-android
```

Para iOS:
```bash
npx react-native run-ios
```

## Empacotamento para Produção

### Web

```bash
cd FinApp/frontend
npm run build
```

### Android

```bash
cd FinApp/mobile
./build-android.sh
```

### iOS

```bash
cd FinApp/mobile
./build-ios.sh
```

## Integração com Hugging Face

O FinApp utiliza modelos de linguagem do Hugging Face para processar comandos de voz e gerar insights financeiros. A integração é feita através do serviço `huggingFaceService.js` no backend.

### Exemplos de Comandos de Voz

- "Adicionar despesa de 50 reais com alimentação ontem"
- "Registrar receita de 1000 reais de salário hoje"
- "Qual é o meu saldo atual?"
- "Quanto gastei com transporte este mês?"

## Segurança

- Autenticação baseada em JWT
- Criptografia de senhas com bcrypt
- Validação de dados em todas as requisições
- Proteção contra ataques comuns (XSS, CSRF, etc.)

## Testes

O projeto inclui scripts de teste para verificar a integridade dos componentes:

```bash
cd FinApp
./test.sh
```

## Implantação

O projeto inclui scripts de implantação para publicar o aplicativo:

```bash
cd FinApp
./deploy.sh
```

## Próximos Passos e Melhorias Futuras

1. Implementação de notificações push
2. Integração com instituições financeiras
3. Suporte a múltiplas moedas
4. Versão offline com sincronização
5. Recursos avançados de planejamento financeiro

## Suporte e Contato

Para suporte técnico ou dúvidas sobre o aplicativo, entre em contato através do formulário na landing page.

---

© 2025 FinApp - Todos os direitos reservados

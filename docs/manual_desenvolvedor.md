# Manual do Desenvolvedor - FinApp

Este manual fornece informações técnicas detalhadas para desenvolvedores que desejam contribuir ou estender o aplicativo FinApp.

## Ambiente de Desenvolvimento

### Requisitos de Software
- Node.js (v14.0.0 ou superior)
- npm (v6.0.0 ou superior)
- MongoDB (v4.4 ou superior)
- Git

### Configuração Inicial

1. Clone o repositório:
```bash
git clone https://github.com/seu-usuario/finapp.git
cd finapp
```

2. Instale as dependências:
```bash
# Instalar dependências do projeto principal
npm install

# Instalar dependências do frontend
cd frontend
npm install
cd ..

# Instalar dependências do backend
cd backend
npm install
cd ..

# Instalar dependências do aplicativo móvel
cd mobile/FinAppMobile
npm install
cd ../..
```

3. Configure as variáveis de ambiente:
   - Crie um arquivo `.env` na pasta `backend` baseado no modelo `.env.example`
   - Obtenha uma chave de API gratuita do Hugging Face e adicione-a ao arquivo `.env`

4. Inicie o banco de dados MongoDB:
```bash
mongod --dbpath=/caminho/para/dados
```

5. Inicie o servidor de desenvolvimento:
```bash
# Iniciar o backend
cd backend
npm run dev

# Em outro terminal, inicie o frontend
cd frontend
npm start
```

## Arquitetura do Código

### Frontend (React.js)

O frontend segue uma arquitetura baseada em componentes com gerenciamento de estado centralizado usando Redux.

#### Estrutura de Diretórios
```
frontend/
├── public/              # Arquivos estáticos
├── src/
│   ├── components/      # Componentes reutilizáveis
│   ├── pages/           # Páginas da aplicação
│   ├── redux/           # Gerenciamento de estado
│   │   ├── actions/     # Ações Redux
│   │   ├── reducers/    # Redutores Redux
│   │   └── store.js     # Configuração da store
│   ├── services/        # Serviços de API
│   ├── utils/           # Funções utilitárias
│   ├── App.js           # Componente principal
│   └── index.js         # Ponto de entrada
└── package.json         # Dependências e scripts
```

#### Fluxo de Dados
1. Componentes disparam ações Redux
2. Ações fazem chamadas à API através dos serviços
3. Redutores atualizam o estado com base nas respostas
4. Componentes são renderizados novamente com os novos dados

### Backend (Node.js/Express)

O backend segue uma arquitetura MVC (Model-View-Controller) com rotas RESTful.

#### Estrutura de Diretórios
```
backend/
├── src/
│   ├── controllers/     # Controladores da API
│   ├── models/          # Modelos de dados (Mongoose)
│   ├── routes/          # Definições de rotas
│   ├── middleware/      # Middleware personalizado
│   ├── services/        # Serviços (incluindo Hugging Face)
│   ├── config/          # Configurações
│   └── index.js         # Ponto de entrada
└── package.json         # Dependências e scripts
```

#### Fluxo de Requisições
1. As requisições chegam às rotas definidas
2. Middleware de autenticação valida o token JWT
3. Controladores processam a requisição
4. Modelos interagem com o banco de dados
5. Resposta é enviada de volta ao cliente

### Aplicativo Móvel (React Native)

O aplicativo móvel compartilha grande parte da lógica de negócios com o frontend web, adaptada para o ambiente móvel.

#### Estrutura de Diretórios
```
mobile/FinAppMobile/
├── android/            # Código específico para Android
├── ios/                # Código específico para iOS
├── src/
│   ├── components/     # Componentes reutilizáveis
│   ├── screens/        # Telas do aplicativo
│   ├── navigation/     # Configuração de navegação
│   ├── services/       # Serviços de API
│   └── utils/          # Funções utilitárias
└── App.js              # Componente principal
```

## Integração com Hugging Face

### Configuração

O serviço `huggingFaceService.js` gerencia a comunicação com a API do Hugging Face:

```javascript
// backend/src/services/huggingFaceService.js
const { HfInference } = require('@huggingface/inference');

class HuggingFaceService {
  constructor() {
    this.hf = new HfInference(process.env.HUGGINGFACE_API_KEY);
    this.model = 'gpt2'; // Modelo padrão, pode ser alterado
  }

  async processVoiceCommand(command) {
    try {
      // Processar o comando usando NLP
      const result = await this.hf.textGeneration({
        model: this.model,
        inputs: command,
        parameters: {
          max_length: 100,
          temperature: 0.7,
        },
      });
      
      // Extrair informações relevantes do comando
      return this.parseCommandResult(result, command);
    } catch (error) {
      console.error('Erro ao processar comando de voz:', error);
      throw error;
    }
  }

  parseCommandResult(result, originalCommand) {
    // Lógica para extrair informações do comando
    // ...
  }
}

module.exports = new HuggingFaceService();
```

### Uso no Controlador

```javascript
// backend/src/controllers/aiController.js
const huggingFaceService = require('../services/huggingFaceService');

exports.processVoiceCommand = async (req, res) => {
  try {
    const { command } = req.body;
    
    if (!command) {
      return res.status(400).json({ error: 'Comando de voz não fornecido' });
    }
    
    const result = await huggingFaceService.processVoiceCommand(command);
    
    // Criar transação baseada no resultado
    // ...
    
    return res.status(200).json({ 
      success: true, 
      message: 'Comando processado com sucesso',
      transaction: result
    });
  } catch (error) {
    console.error('Erro ao processar comando de voz:', error);
    return res.status(500).json({ error: 'Erro ao processar comando de voz' });
  }
};
```

## Reconhecimento de Voz no Frontend

### Web (usando Web Speech API)

```javascript
// frontend/src/components/VoiceCommandListener.js
import React, { useState, useEffect } from 'react';

const VoiceCommandListener = () => {
  const [isListening, setIsListening] = useState(false);
  const [transcript, setTranscript] = useState('');
  
  useEffect(() => {
    // Configurar reconhecimento de voz
    const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
    if (!SpeechRecognition) {
      console.error('Reconhecimento de voz não suportado neste navegador');
      return;
    }
    
    const recognition = new SpeechRecognition();
    recognition.lang = 'pt-BR';
    recognition.continuous = false;
    recognition.interimResults = false;
    
    recognition.onresult = (event) => {
      const transcript = event.results[0][0].transcript;
      setTranscript(transcript);
      processCommand(transcript);
    };
    
    recognition.onerror = (event) => {
      console.error('Erro no reconhecimento de voz:', event.error);
      setIsListening(false);
    };
    
    recognition.onend = () => {
      setIsListening(false);
    };
    
    // Iniciar/parar reconhecimento quando isListening mudar
    if (isListening) {
      recognition.start();
    } else {
      recognition.stop();
    }
    
    return () => {
      recognition.stop();
    };
  }, [isListening]);
  
  const processCommand = async (command) => {
    try {
      // Enviar comando para o backend
      const response = await fetch('/api/ai/voice-command', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${localStorage.getItem('token')}`
        },
        body: JSON.stringify({ command })
      });
      
      const data = await response.json();
      
      if (data.success) {
        // Processar resposta bem-sucedida
      } else {
        // Lidar com erro
      }
    } catch (error) {
      console.error('Erro ao processar comando:', error);
    }
  };
  
  const toggleListening = () => {
    setIsListening(!isListening);
  };
  
  return (
    <div>
      <button onClick={toggleListening}>
        {isListening ? 'Parar' : 'Iniciar Reconhecimento'}
      </button>
      {transcript && <p>Você disse: {transcript}</p>}
    </div>
  );
};

export default VoiceCommandListener;
```

### Mobile (usando React Native Voice)

```javascript
// mobile/FinAppMobile/src/components/VoiceCommandProcessor.js
import React, { useState, useEffect } from 'react';
import { View, Button, Text } from 'react-native';
import Voice from 'react-native-voice';

const VoiceCommandProcessor = () => {
  const [isListening, setIsListening] = useState(false);
  const [transcript, setTranscript] = useState('');
  
  useEffect(() => {
    // Configurar handlers de eventos
    Voice.onSpeechStart = onSpeechStart;
    Voice.onSpeechEnd = onSpeechEnd;
    Voice.onSpeechResults = onSpeechResults;
    Voice.onSpeechError = onSpeechError;
    
    return () => {
      // Limpar listeners ao desmontar
      Voice.destroy().then(Voice.removeAllListeners);
    };
  }, []);
  
  const onSpeechStart = () => {
    console.log('Reconhecimento iniciado');
  };
  
  const onSpeechEnd = () => {
    setIsListening(false);
  };
  
  const onSpeechResults = (event) => {
    const transcriptText = event.value[0];
    setTranscript(transcriptText);
    processCommand(transcriptText);
  };
  
  const onSpeechError = (error) => {
    console.error('Erro de reconhecimento de voz:', error);
    setIsListening(false);
  };
  
  const toggleListening = async () => {
    try {
      if (isListening) {
        await Voice.stop();
        setIsListening(false);
      } else {
        await Voice.start('pt-BR');
        setIsListening(true);
      }
    } catch (error) {
      console.error('Erro ao controlar reconhecimento de voz:', error);
    }
  };
  
  const processCommand = async (command) => {
    // Lógica similar à versão web
  };
  
  return (
    <View>
      <Button
        title={isListening ? 'Parar' : 'Iniciar Reconhecimento'}
        onPress={toggleListening}
      />
      {transcript ? <Text>Você disse: {transcript}</Text> : null}
    </View>
  );
};

export default VoiceCommandProcessor;
```

## Fluxo de Trabalho para Desenvolvimento

1. **Planejamento**
   - Crie uma issue no sistema de rastreamento
   - Descreva a funcionalidade ou correção
   - Atribua a um desenvolvedor

2. **Desenvolvimento**
   - Crie um branch a partir de `main`: `git checkout -b feature/nome-da-feature`
   - Implemente as mudanças
   - Escreva testes para a nova funcionalidade

3. **Teste**
   - Execute os testes locais: `npm test`
   - Verifique a cobertura de código
   - Teste manualmente a funcionalidade

4. **Revisão**
   - Envie um Pull Request para `main`
   - Solicite revisão de outro desenvolvedor
   - Faça as alterações necessárias com base no feedback

5. **Implantação**
   - Mescle o PR após aprovação
   - Execute o script de implantação: `./deploy.sh`
   - Verifique se a implantação foi bem-sucedida

## Solução de Problemas Comuns

### Problemas de Reconhecimento de Voz
- **Navegador não suporta Web Speech API**: Use uma biblioteca alternativa como annyang.js
- **Reconhecimento impreciso**: Ajuste o idioma e treine o modelo com mais exemplos

### Problemas de Integração com Hugging Face
- **Erro de autenticação**: Verifique se a chave API está correta no arquivo .env
- **Modelo não disponível**: Verifique se o modelo especificado existe e está acessível

### Problemas de Build Mobile
- **Erros no Android Studio**: Atualize o Gradle e as dependências do Android
- **Erros no Xcode**: Verifique a versão do CocoaPods e execute `pod install` novamente

## Contribuição

1. Siga o estilo de código estabelecido
2. Documente novas funcionalidades
3. Mantenha a cobertura de testes acima de 80%
4. Atualize a documentação quando necessário

---

© 2025 FinApp - Equipe de Desenvolvimento

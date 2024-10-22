# LiveKit Assistant

First, create a virtual environment, update pip, and install the required packages:

```
$ python3 -m venv .venv
$ source .venv/bin/activate
$ pip install -U pip
$ pip install -r requirements.txt
```

You need to set up the following environment variables:

```
LIVEKIT_URL=...
LIVEKIT_API_KEY=...
LIVEKIT_API_SECRET=...
DEEPGRAM_API_KEY=...
OPENAI_API_KEY=...
```

Then, run the assistant:

```
$ python3 assistant.py download-files
$ python3 assistant.py start
```

Finally, you can load the [hosted playground](https://agents-playground.livekit.io/) and connect it.

O código que você forneceu é parte da classe `VoicePipelineAgent`, que implementa um agente de pipeline de voz (VAD + STT + LLM + TTS) para um sistema de assistente de voz. Ele lida com a interação entre o usuário e o assistente, incluindo a detecção de fala, transcrição, processamento de linguagem natural e síntese de fala.


1. **Inicialização:**


   - A classe `VoicePipelineAgent` é inicializada com vários parâmetros, incluindo instâncias de VAD, STT, LLM e TTS, além de opções de configuração.
   - O código configura um loop de eventos (`self._loop`) e um plotter para fins de depuração (`self._plotter`).
   - Ele também inicializa vários atributos para gerenciar o estado do agente, como o contexto de bate-papo (`self._chat_ctx`), o contexto de função (`self._fnc_ctx`), o histórico de fala do usuário (`self._transcribed_text`), a fila de fala (`self._speech_q`) e outros.


2. **Início do Agente:**


   - O método `start()` inicia o agente, conectando-o a uma sala (`room`) e a um participante específico (`participant`).
   - Ele configura ouvintes para eventos relacionados ao participante, como conexão e desconexão.
   - O método `_link_participant()` é chamado para vincular o agente ao participante, configurando ouvintes para eventos de fala do usuário.


3. **Gerenciamento de Fala do Usuário:**


   - O código configura ouvintes para eventos de fala do usuário, como início da fala (`_on_start_of_speech`), atualização do VAD (`_on_vad_updated`), fim da fala (`_on_end_of_speech`), transcrição intermediária (`_on_interim_transcript`) e transcrição final (`_on_final_transcript`).
   - Quando o usuário começa a falar, o agente registra o evento e inicia a transcrição da fala.
   - A transcrição intermediária é armazenada em `self._transcribed_interim_text` e a transcrição final em `self._transcribed_text`.
   - O código também verifica se o usuário está interrompendo a fala do agente e, se sim, interrompe a fala do agente.


4. **Geração de Resposta do Agente:**


   - O método `_synthesize_agent_reply()` é chamado para gerar a resposta do agente.
   - Ele cria um novo objeto `SpeechHandle` para representar a resposta do agente e adiciona a pergunta do usuário ao contexto de bate-papo.
   - O método `_opts.before_llm_cb()` é chamado para permitir que o usuário personalize a resposta do agente antes de enviar para o LLM.
   - O LLM é chamado com o contexto de bate-papo e o contexto de função para gerar a resposta.
   - O método `_synthesize_agent_speech()` é chamado para sintetizar a resposta do agente em fala.


5. **Reprodução de Fala:**


   - O método `_play_speech()` é chamado para reproduzir a fala do agente.
   - Ele adiciona o objeto `SpeechHandle` à fila de fala (`self._speech_q`) e aguarda até que a fila esteja pronta para ser reproduzida.
   - O método `_agent_output.play()` é chamado para reproduzir a fala do agente.
   - O código também verifica se a resposta do agente está usando ferramentas e, se sim, executa as funções e adiciona os resultados ao contexto de bate-papo.


6. **Gerenciamento de Estado:**


   - O método `_update_state()` é chamado para atualizar o estado do agente, como "inicializando", "ouvindo", "pensando" ou "falando".
   - O código também configura um temporizador para atualizar o estado do agente periodicamente.


7. **Gerenciamento de Interrupções:**


   - O método `_interrupt_if_possible()` é chamado para verificar se a fala do agente deve ser interrompida.
   - Ele verifica se o usuário está falando e se a fala do agente permite interrupções.
   - Se as condições forem satisfeitas, a fala do agente é interrompida.


8. **Validação de Resposta:**

   - O método `_validate_reply_if_possible()` é chamado para verificar se a resposta do agente deve ser validada.
   - Ele verifica se a fala do agente está sendo reproduzida e se a fala do agente permite interrupções.
   - Se as condições forem satisfeitas, a resposta do agente é validada e adicionada à fila de fala.


9. **Gerenciamento de Funções:**

   - O código também lida com a execução de funções, se a resposta do agente estiver usando ferramentas.
   - Ele executa as funções e adiciona os resultados ao contexto de bate-papo.


10. **Gerenciamento de Transcrição:**

   - O código também lida com a transcrição da fala do agente.
   - Ele configura opções de transcrição para o agente, como se a transcrição deve ser enviada para o cliente e a velocidade da transcrição.
   - O código também usa um tokenizador de frases para dividir a fala em frases e um tokenizador de palavras para dividir a fala em palavras.


Este código é uma implementação complexa de um agente de pipeline de voz, que lida com vários aspectos da interação entre o usuário e o assistente de voz. Ele usa várias bibliotecas e frameworks, como LiveKit, VAD, STT, LLM e TTS, para fornecer uma experiência de usuário completa.

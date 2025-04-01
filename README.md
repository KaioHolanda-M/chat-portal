<Assistente>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chat com [Assistente de Governança]</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            margin: 5;
            padding: 5;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        .chat-container {
            background-color: #fff;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
            width: 400px;
            max-width: 90%;
            height: 500px;
            display: flex;
            flex-direction: column;
            overflow: hidden; /* Garante que o conteúdo interno não vaze */
        }

        .chat-header {
            background-color: #007bff;
            color: #463f3f;
            padding: 15px;
            text-align: center;
            font-size: 1.2em;
        }

        .chat-messages {
            flex-grow: 1;
            padding: 15px;
            overflow-y: auto; /* Barra de rolagem vertical quando necessário */
            display: flex;
            flex-direction: column;
        }

        .message {
            background-color: #a09393;
            border-radius: 5px;
            padding: 8px 12px;
            margin-bottom: 8px;
            word-break: break-word; /* Quebra palavras longas para evitar overflow */
            max-width: 80%; /* Limita a largura da mensagem */
        }

        .message.sent {
            background-color: #5a67b4; /* Cor mais clara para mensagens enviadas */
            align-self: flex-end; /* Alinha à direita */
        }

        .chat-input-area {
            padding: 15px;
            display: flex;
            align-items: center;
            border-top: 1px solid #9f98ff;
        }

        .chat-input {
            flex-grow: 1;
            padding: 8px;
            border: 1px solid #d06e6e;
            border-radius: 5px;
            margin-right: 10px;
            font-size: 1em;
        }

        .send-button {
            background-color: #007bff;
            color: #e69c9c;
            border: none;
            border-radius: 5px;
            padding: 8px 16px;
            cursor: pointer;
            font-size: 1em;
        }

        .send-button:hover {
            background-color: #0056b3;
        }
    </style>
</head>
<body>

    <div class="chat-container">
        <div class="chat-header">
            Chat com [Assistente de Governança]
        </div>

        <div class="chat-messages" id="chatMessages">
            <!-- As mensagens serão inseridas aqui -->
        </div>

        <div class="chat-input-area">
            <input type="text" id="chatInput" class="chat-input" placeholder="Digite sua mensagem...">
            <button class="send-button" id="sendButton">Enviar</button>
        </div>
    </div>

    <script>
        // Substitua pela URL real do seu webhook n8n
        const n8nWebhookUrl = 'https://gegov.app.n8n.cloud/webhook/09da125d-6ba4-47cc-8366-86b8b97274fc';

        const chatMessages = document.getElementById('chatMessages');
        const chatInput = document.getElementById('chatInput');
        const sendButton = document.getElementById('sendButton');

        function addMessage(text, isSent = false) {
            const message = document.createElement('div');
            message.classList.add('message');
            if (isSent) {
                message.classList.add('sent');
            }
            message.textContent = text;
            chatMessages.appendChild(message);

            // Rolar para a parte inferior
            chatMessages.scrollTop = chatMessages.scrollHeight;
        }

        sendButton.addEventListener('click', () => {
            const messageText = chatInput.value.trim();
            if (messageText !== '') {
                addMessage(messageText, true); // Mensagem enviada

                // Enviar para o n8n
                fetch(n8nWebhookUrl, {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({ text: messageText })
                })
                .then(response => response.json())
                .then(data => {
                    // Supondo que o n8n retorne um campo 'response'
                    addMessage(data.response);
                })
                .catch(error => {
                    console.error('Erro ao enviar para o n8n:', error);
                    addMessage("Erro ao obter resposta do agente.");
                });

                chatInput.value = '';
            }
        });

        chatInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                sendButton.click();
            }
        });
    </script>

</body>
</html>

# Simulação Educacional de Malware em Python (Ransomware & Keylogger)

> **Aviso Legal e Ético:** Este projeto foi desenvolvido exclusivamente para **fins educacionais**, em ambiente 100% controlado e isolado, como parte de estudos em Segurança da Informação. O objetivo é compreender na prática como ameaças digitais operam para aprimorar mecanismos de detecção, defesa e prevenção. **Nunca utilize estes scripts em sistemas ou redes sem autorização explícita.**

---

## Sobre o Projeto

Este repositório documenta experimentos práticos em Python que simulam o comportamento básico de dois tipos comuns de malware: **Ransomware** e **Keylogger**. Ao reproduzir esses mecanismos de forma segura, o projeto explora desde a manipulação de arquivos e captura de eventos de teclado até a exfiltração simulada de dados e as contramedidas defensivas correspondentes.

### Objetivos de Aprendizagem
- Compreender o ciclo de funcionamento de um ransomware (geração de chave, criptografia simétrica, nota de resgate e descriptografia).
- Entender como um keylogger intercepta eventos de teclado e pode operar em segundo plano ou enviar registros remotamente.
- Identificar brechas técnicas e humanas exploradas nesse tipo de ataque.
- Documentar estratégias de defesa em profundidade, monitoramento e resposta a incidentes.

---

## Tecnologias e Bibliotecas Utilizadas

- **Python 3.x**
- **[`cryptography` (`Fernet`)](https://cryptography.io/):** Implementação de criptografia simétrica autenticada para cifrar e decifrar arquivos de teste.
- **[`pynput`](https://pynput.readthedocs.io/):** Monitoramento e captura de eventos de entrada do teclado (`keyboard.Listener`).
- **`smtplib` & `email.mime`:** Construção e envio automatizado de mensagens via protocolo SMTP.
- **`threading` (`Timer`):** Agendamento periódico para envio de logs em intervalos definidos.
- **`os`:** Navegação recursiva em diretórios (`os.walk`) para localizar arquivos-alvo no diretório de testes.

---

## Estrutura do Repositório

```text
├── docs/
│   └── defesa_e_mitigacao.md   # Análise detalhada sobre prevenção, detecção e defesa
├── images/                     # Capturas de tela dos testes em ambiente controlado (opcional)
├── test_ransoware/             # Diretório isolado contendo arquivos fictícios para teste
│   └── dado_confidenciais.txt  # Arquivo de exemplo utilizado na simulação de criptografia
├── ransoware.py                # Gera chave Fernet, cifra os arquivos de teste e cria a nota de resgate
├── descriptografar.py          # Carrega a chave gerada e restaura os arquivos cifrados
├── LEIA ISSO.txt               # Exemplo de mensagem de resgate gerada durante a simulação
├── keylogger.pyw               # Captura teclas e grava localmente em log.txt (modo sem console)
├── keylogger_email.pyw         # Captura teclas em memória e envia periodicamente por e-mail
├── log.txt                     # Exemplo de saída gerada durante os testes locais
└── README.md                   # Documentação principal do projeto
```

---

## Funcionamento dos Módulos

### 1. Simulação de Ransomware (`ransoware.py` e `descriptografar.py`)
- **Geração e Armazenamento de Chave:** Utiliza `Fernet.generate_key()` para criar uma chave simétrica salva localmente em `chave.key`.
- **Varredura Controlada:** Percorre apenas o diretório `test_ransoware`, ignorando o próprio script e arquivos `.key`.
- **Criptografia e Resgate:** Lê os bytes originais dos arquivos de teste, sobrescreve com o conteúdo cifrado e gera o arquivo `LEIA ISSO.txt` simulando a instrução de resgate.
- **Recuperação:** O script `descriptografar.py` lê a chave gerada e reverte o processo, restaurando o conteúdo original dos arquivos.

### 2. Simulação de Keylogger (`keylogger.pyw` e `keylogger_email.pyw`)
- **Captura de Teclas (`keylogger.pyw`):** Escuta eventos de pressionamento de teclas via `pynput`, filtrando teclas modificadoras (como `Shift`, `Ctrl` e `Alt`) e formatando teclas especiais (`Space`, `Enter`, `Tab`, `Esc`) no arquivo `log.txt`. A extensão `.pyw` permite a execução no Windows sem abrir janela de terminal.
- **Exfiltração Simulada (`keylogger_email.pyw`):** Armazena as teclas pressionadas em memória e utiliza `threading.Timer` para disparar envios automáticos a cada 60 segundos via servidor SMTP (`smtplib`).

---

## Como Executar em Ambiente Seguro

1. **Prepare um ambiente isolado:** Utilize uma Máquina Virtual (VM) ou sandbox dedicada para testes.
2. **Clone o repositório e instale as dependências:**
   ```bash
   pip install cryptography pynput
   ```
3. **Teste o Ransomware:**
   - Garanta que existam apenas arquivos fictícios dentro da pasta `test_ransoware/`.
   - Execute `python ransoware.py` para cifrar os arquivos de teste e gerar `chave.key` e `LEIA ISSO.txt`.
   - Execute `python descriptografar.py` para validar a restauração dos dados.
4. **Teste o Keylogger:**
   - Para teste local, execute `keylogger.pyw` e digite textos fictícios para verificar a gravação em `log.txt`.
   - Para teste com envio de e-mail, utilize exclusivamente uma conta de testes descartável e **nunca** suba credenciais reais ou chaves privadas para o repositório público.
   - Encerre o processo pelo Gerenciador de Tarefas / Monitor de Processos ao finalizar o experimento.

---

## Defesa e Mitigação

As estratégias detalhadas de proteção contra ransomware e keyloggers — incluindo detecção comportamental, controle de saída de rede, política de backups e conscientização — estão documentadas em:

 **[`docs/defesa_e_mitigacao.md`](docs/defesa_e_mitigacao.md)**
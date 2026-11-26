# Reflexão sobre Defesa, Detecção e Mitigação

Compreender como um **Ransomware** e um **Keylogger** operam na prática evidencia que ataques bem-sucedidos raramente dependem apenas de falhas técnicas complexas — na maioria das vezes, exploram permissões excessivas, ausência de monitoramento comportamental e engenharia social.

Abaixo estão documentadas as principais camadas de proteção aplicáveis ao mundo real para prevenir, detectar e mitigar os comportamentos simulados neste projeto.

---

## 1. Antivírus, EDR e Análise Comportamental

Soluções modernas de **Antivírus (AV)** e **EDR (Endpoint Detection and Response)** vão além da simples assinatura de arquivos (hashes), monitorando o comportamento dos processos em tempo real:

- **Contra Ransomware:**
  - Detecção de leitura e sobrescrita rápida de múltiplos arquivos em sequência (`os.walk` + escrita em lote).
  - Monitoramento de chamadas a bibliotecas criptográficas acompanhadas de criação de arquivos de nota de resgate (como `LEIA ISSO.txt`).
  - Proteção de pastas controladas (*Controlled Folder Access*), bloqueando processos não autorizados de modificar diretórios sensíveis do usuário.
- **Contra Keyloggers:**
  - Detecção de *hooks* globais de teclado (como os registrados pelo `pynput` nas APIs do sistema operacional).
  - Alerta sobre processos em segundo plano sem interface gráfica (como scripts `.pyw` ou executáveis ocultos) tentando capturar eventos de entrada enquanto o usuário interage com navegadores ou gerenciadores de senhas.

---

## 2. Firewall e Monitoramento de Rede (Egress Filtering)

Um keylogger com exfiltração por e-mail (`keylogger_email.pyw`) depende de conexões de saída para transmitir os dados capturados:

- **Bloqueio de Saída Não Autorizada:** Regras de firewall devem restringir quais processos e portas podem iniciar conexões externas. Estações de trabalho comuns raramente precisam abrir conexões diretas na porta `587` ou `465` (SMTP) para servidores externos.
- **Inspeção e Detecção de Anomalias:** Ferramentas de monitoramento de rede (IDS/IPS e proxies) identificam padrões periódicos de envio de dados (*beaconing*, como disparos regulares a cada 60 segundos) e conexões com destinos incomuns.

---

## 3. Sandboxing e Isolamento de Ambientes

- **Execução em Sandbox:** Arquivos desconhecidos, anexos de e-mail e scripts de terceiros devem ser analisados em ambientes isolados (máquinas virtuais descartáveis ou sandboxes automatizadas) antes de qualquer execução em sistemas de produção.
- **Princípio do Menor Privilégio:** Usuários não devem operar no dia a dia com contas de administrador. Limitar privilégios reduz o alcance de um ransomware no sistema de arquivos e dificulta a instalação de mecanismos de persistência no sistema operacional.
- **Segmentação de Rede:** Isolar estações de trabalho, servidores críticos e dispositivos impede que um ransomware se propague lateralmente pela rede local.

---

## 4. Política de Backups e Continuidade

Para cenários de ransomware, a resiliência dos dados é a medida mais eficaz de recuperação sem ceder à extorsão:

- **Regra 3-2-1:** Manter pelo menos **3** cópias dos dados, em **2** mídias diferentes, com **1** cópia armazenada fora do local (*off-site*) ou desconectada (*air-gapped*).
- **Backups Imutáveis:** Utilizar repositórios de backup com proteção contra alteração ou exclusão (*WORM — Write Once, Read Many*), impedindo que o próprio ransomware cifre ou apague os backups acessíveis pela rede.
- **Testes Periódicos de Restauração:** Validar regularmente a integridade dos backups para garantir que a recuperação funcione dentro do tempo esperado.

---

## 5. Conscientização do Usuário e Fator Humano

A engenharia social continua sendo o principal vetor inicial para a entrega de malwares:

- **Prevenção contra Phishing:** Treinar usuários para desconfiar de anexos inesperados, links encurtados, falsas atualizações de software e mensagens com senso de urgência artificial.
- **Autenticação Multifator (MFA):** Mesmo que um keylogger capture uma senha digitada (como simulado no `log.txt`), o uso de MFA — especialmente baseado em aplicativos autenticadores ou chaves físicas (FIDO2) — impede o acesso indevido à conta.
- **Atualizações Constantes (Patch Management):** Manter o sistema operacional, o interpretador Python, bibliotecas e softwares de terceiros atualizados fecha vulnerabilidades conhecidas que poderiam ser usadas para escalonamento de privilégios ou execução remota de código.

---

## Resumo: Vetor Simulado vs. Contramedida

| Comportamento Simulado | Risco Associado | Principal Contramedida |
| :--- | :--- | :--- |
| Criptografia em lote de arquivos (`ransoware.py`) | Perda de disponibilidade e sequestro de dados | EDR comportamental, proteção de pastas e backups imutáveis/offline |
| Captura de teclas em segundo plano (`keylogger.pyw`) | Vazamento de credenciais e dados sensíveis | EDR (detecção de *hooks* de teclado), menor privilégio e MFA |
| Envio periódico via SMTP (`keylogger_email.pyw`) | Exfiltração contínua de informações | Firewall com filtro de saída (*egress*) e monitoramento de rede |
| Execução pelo usuário final | Comprometimento inicial da máquina | Conscientização contra *phishing*, sandboxing e controle de aplicações |
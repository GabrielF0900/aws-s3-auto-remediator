# 🚀 AWS S3 Auto Remediator - A Jornada de Um Guerreiro da Nuvem

Uma história épica de como contornar restrições, vencer desafios de segurança e automatizar a remediação de buckets S3 vulneráveis usando Lambda, EventBridge e muita criatividade.

---

## 🏗️ Arquitetura da Solução

![Arquitetura do Projeto](assets/ArquiteturaDrawio.jpeg)

A solução implementa um fluxo automático de detecção e remediação:
- **EventBridge + CloudTrail**: Monitora a criação de buckets S3
- **Lambda Executora**: Valida e bloqueia acesso público automaticamente
- **S3**: Serviço alvo da remediação

---

## 📖 A Jornada (Ou: Como Eu Aprendi a Amar as Restrições da Nuvem)

### Ato I: O Começo - Navegando pela Console AWS

![Página Principal da Console AWS](assets/01-pagina-principal.jpeg)

Tudo começou aqui... na interface imensa e intimidadora da AWS. O desafio era claro: criar uma solução automática para remediar buckets S3 inseguros. Mas havia um problema: **A Escola da Nuvem não permite criar IAM Roles livremente**. 

Primeira lição de humildade: às vezes o caminho direto é bloqueado, e você precisa ser criativo. 🧠

---

### Ato II: A Arma Secreta - LabRole

![LabRole - Nossa Arma](assets/02-LabRole.jpeg)

Eles nos deram uma arma chamada **LabRole** - uma IAM Role pré-criada com permissões limitadas mas estratégicas. Seria essa a solução? Spoiler: ela seria apenas o começo.

---

### Ato III: Criando o Executador

![Função Lambda Sendo Criada](assets/03-Criacao-da-Lambda-Executora.jpeg)

Partimos para a criação da **função Lambda executora** - o coração do sistema. Essa seria a responsável por identificar buckets vulneráveis e "curá-los".

---

### Ato IV: A Tentativa (Que Quase Funcionou)

![Atribuindo LabRole](assets/04-atribuindo-LabRole-Na-Funcao.jpeg)

Com esperança no peito, atribuímos a LabRole à nossa função. Pensávamos: "Pronto, isso deve resolver tudo!"

...spoiler: não resolveu. Mas estávamos no caminho certo. 🛤️

![Lambda Criada com Sucesso](assets/05-Funcao-Criada.jpeg)

Pelo menos a função foi criada com sucesso! Um passo de cada vez.

---

### Ato V: O Algoritmo - A Lógica que Salva Buckets

![A Lógica Principal](assets/06-Logica-principal.jpeg)

Aqui vem o código que faz a mágica acontecer! Com ajuda da IA, desenvolvemos um algoritmo que:
1. ✅ Identifica o bucket criado
2. ✅ Extrai suas informações
3. ✅ Verifica se está publicamente acessível
4. ✅ Se estiver vulnerável → **BLOQUEIA O ACESSO PÚBLICO**

Simples assim? Não. Mas genial? Absolutamente! 🧙‍♂️

![Lógica Deployada](assets/07-logica-deployada.jpeg)

Deploy realizado com sucesso na função executora!

---

### Ato VI: EventBridge - Onde Os Desafios de Verdade Começam

![EventBridge - A Jornada Começa](assets/08-EventBridger.jpeg)

Agora chegava a parte ousada: configurar o **EventBridge** para monitorar logs do CloudTrail e disparar nossa Lambda quando um bucket fosse criado. 

A AWS estava pronta para nós. A questão era: estávamos prontos para a AWS? 😅

---

### Ato VII: Primeira Configuração (Ou: Descobrindo que a Interface Nova é Confusa)

![Criando o EventBridge](assets/09-Configuracao-EventBridger.jpeg)

Começamos a configuração, mas a interface visual moderna da AWS nos fez questionar nossas escolhas na vida...

#### Plot Twist: Desativando o "Visual Builder"

![Visual Builder Ativado](assets/10-Visual-ativado.jpeg)

Aquele bonito botão de ativado? Desativamos ele. 

![Visual Builder Desativado](assets/11-Visual-Desativado.jpeg)

Porque...

![A Interface Antiga (Melhor Opção)](assets/12-Configuracao-EventBridger.jpeg)

...a interface antiga é MUITO melhor. Às vezes retroceder é avançar. 🔙➡️🔜

---

### Ato VIII: Configurando o Gatilho (Parte I)

![Iniciando a Configuração](assets/13-Mais-Configuracao.jpeg)

Aqui começava o jogo de verdade. Configurávamos o EventBridge para monitorar criações de buckets.

---

### Ato IX: A Lógica JSON - Coração da Automação

![Lógica em JSON do EventBridge](assets/15-Logica-EventBridger.jpeg)

Em JSON puro, definimos que o EventBridge deveria:
- Monitorar **chamadas de API do CloudTrail**
- No serviço **S3**
- Na ação específica: **CreateBucket**

Quando essa tríade de eventos acontecesse, um disparo seria enviado para nossa Lambda.

---

### Ato X: Definindo o Destino

![Apontando para a Lambda](assets/16-Destino.jpeg)

O destino era claro: nossa função Lambda executora. Tudo parecia perfeito...

---

### Ato XI: A Bomba Explode 💣

![Erro Critical](assets/17-Erro-EventBridger.jpeg)

**BOOM!** A AWS tentou criar uma **IAM Role nova** automaticamente para o EventBridge. Mas lembram das restrições? Elas voltaram para nos assombrar. 👻

O EventBridge, por natureza, tenta ser independente e criar suas próprias permissões. Mesmo fornecendo uma LabRole, ele insistia em criar a sua própria (coisa chata, a AWS, não é?).

---

### Ato XII: A Redenção - EventBridge Dentro da Lambda

![Criando Gatilho na Lambda](assets/18-Gatilho-Lambda.jpeg)

Depois de pesquisa intensiva, descobrimos a solução genial: **usar EventBridge como gatilho DENTRO da Lambda**. 

Não criamos o recurso do lado de fora. Criamos o gatilho diretamente na função, contornando as restrições. Isso é criatividade! 🧠✨

---

### Ato XIII: Configuração do Gatilho (A Parte Importante)

![Definindo o Gatilho Principal](assets/19-Configuracao-Gatilho.jpeg)

Dentro da Lambda, configuramos:
- **Serviço**: S3 (Simple Storage Service)
- **Tipo de Evento**: CloudTrail API Calls (Chamadas de API via CloudTrail)
- **Detalhe**: CreateBucket

Ele monitoraria tudo "debaixo dos panos" da Lambda. Genial! 🎯

---

### Ato XIV: A Lógica do Gatilho

![Lógica Gerada](assets/20-logica-gatilho.jpeg)

O gatilho foi gerado com a lógica perfeita. Estava pronto para disparar.

![Gatilho Criado com Sucesso](assets/21-gatilho-criado.jpeg)

Sucesso! O gatilho estava vivo e pronto para detectar buckets.

---

### Ato XV: Criando o Bucket de Teste (Aquele que Vai Falhar de Propósito)

![Página Principal do S3](assets/21-paginal-principal-s3.jpeg)

Agora vinha a hora da verdade: criar um bucket **propositalmente inseguro** para testar nossa solução.

---

### Ato XVI: Configuração do Bucket

![Configurando o Bucket](assets/22-criando-bucket-config.jpeg)

Nome único no mundo todo? Check! ✅

---

### Ato XVII: O Pecado Intencional - Tornando Público

![Proteção Ativada (Exemplo Padrão)](assets/23-protecao-ativada-exemplo.jpeg)

Aqui está o padrão: **bloqueio de acesso público ativado** (seguro).

Mas nós?

![Desativando Proteção](assets/24-protecao-desativada-exemplo.jpeg)

Desativamos sem misericórdia. O bucket agora estava **completamente público**. Era hora de testar se nossa solução era tão boa quanto parecia. 🎭

---

### Ato XVIII: Bucket Criado - Vulnerável e Desprotegido

![Bucket Criado e Inseguro](assets/25-bucket-criado.jpeg)

Bucket criado. Inseguro. Pronto para ser remediado.

---

### Ato XIX: Verificando a Segurança (Ou: Por que Ainda Está Público??)

![Ainda Desprotegido?](assets/26-ainda-estava-desprotegido.jpeg)

Hmm... ele ainda estava público nas permissões. Investigação necessária!

Verificamos:
- **CloudWatch Logs**: Nada 🙅
- **CloudTrail Logs**: BINGO! 🎯

O CloudTrail mostrou:
1. Log de criação do bucket
2. **Log de tentativa de bloqueio** 

A Lambda TINHA tentado fazer o bloqueio, mas por algum motivo não funcionou na primeira vez. Nem sempre a primeira tentativa é mágica.

---

### Ato XX: Teste Manual - Invocando a Lambda Manualmente

![Adicionando Teste](assets/27-adicionando-teste.jpeg)

Tempo de ação direta! Criamos um teste manual com os parâmetros do bucket.

---

### Ato XXI: O Milagre Acontece ✨

![Proteção Restaurada](assets/28-Protecao-restaurada.jpeg)

**ATIVAR** - Essa mudança de status conta toda a história:
- **Antes**: Público (vulnerável) 🔴
- **Depois**: Privado (protegido) 🟢

A Lambda pegou os parâmetros → EventBridge detectou → Função executora ativou → **Bloqueio bem-sucedido!**

O sistema funcionou! Em segundos, o bucket passou de inseguro para seguro. 🛡️

---

### Ato XXII: Confirmando o Sucesso com Novo Teste

![Novo Teste](assets/29-novo-teste.jpeg)

Criamos um segundo bucket tão inseguro quanto o primeiro...

![Confirmação Final](assets/30-confirmado.jpeg)

...e o fluxo automático funcionou novamente! **Remediação automática confirmada!** 🎉

---

## 🎯 O que Aprendemos

| Desafio | Solução |
|---------|---------|
| Sem permissão para criar IAM Roles | Usamos LabRole pré-criada + Gatilho Lambda |
| EventBridge criava IAM Role automaticamente | Movemos EventBridge para dentro da Lambda |
| Interface visual confusa | Usamos o editor JSON da interface antiga |
| Bloqueio não funcionava na primeira vez | Testamos manualmente e confirmamos a correção |

---

## 🏆 Resultado Final

✅ Função Lambda que identifica e bloqueia buckets públicos  
✅ Gatilho EventBridge monitorando criação de buckets  
✅ Sistema automático de remediação funcionando  
✅ Contornamento criativo de restrições de permissão  
✅ Buckets inseguros sendo protegidos em segundos  

---

## 💡 Tech Stack

- **AWS Lambda**: Função executora da lógica
- **AWS EventBridge**: Gatilho automático para criação de buckets
- **AWS CloudTrail**: Monitoramento de chamadas de API
- **AWS S3**: Serviço alvo da remediação
- **AWS IAM**: Controle de acesso (com restrições criativas)
- **Python/JavaScript**: Lógica de remediação

---

## 📚 Conclusão

Essa jornada provou que:
1. **Restrições não são barreiras**, são oportunidades para inovação
2. **Pesquisa é seu melhor amigo** quando você bate em uma parede
3. **Testes manuais revelam verdades** que os logs ocultam
4. **A automação funciona**... mas às vezes precisa de um empurrãozinho

Buckets inseguros agora estão vivendo seus últimos dias. Bem-vindo ao futuro da segurança automática. 🚀

---

**Desafio completado.** *Missão cumprida.*

```
 _______________
< Fim da história >
 _______________
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

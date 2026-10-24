<p align="center">
<img width="600" alt="Image" src="https://github.com/user-attachments/assets/d8735861-b016-4a8b-bb0c-be83c0335750" />
</p>


<header>
 <h1 align="center">☁️ AWS — Criar um site no Amazon S3
</header>

<h1 align="center"> 📌 Sobre o laboratório </h1>

Este laboratório apresenta a utilização da **AWS Command Line Interface (AWS CLI)** para criar e configurar recursos na AWS, com o objetivo de hospedar um site estático no **Amazon Simple Storage Service (Amazon S3)**.

Durante a atividade, foram praticados comandos para criar um bucket S3, configurar um usuário do IAM com permissões de acesso, realizar o upload dos arquivos do site e habilitar a hospedagem de conteúdo estático.

Também foi criado um script Bash para automatizar a atualização do site após alterações nos arquivos locais.

---

## 🎯 Objetivos

Ao concluir este laboratório, os principais objetivos foram:

- ☁️ Criar um bucket no Amazon S3 utilizando a AWS CLI;
- 🔐 Criar um usuário do IAM e configurar suas permissões;
- 💻 Executar comandos da AWS CLI em uma instância Amazon EC2;
- 🌐 Hospedar um site estático em um bucket S3;
- 📂 Realizar upload de arquivos e diretórios para o Amazon S3;
- ⚙️ Criar um script Bash para automatizar a atualização do site;
- 🔄 Explorar o comando `aws s3 sync` para otimizar a sincronização de arquivos.

---

## ☁️ Amazon Simple Storage Service (Amazon S3)

O **Amazon S3** é um serviço de armazenamento de objetos que permite armazenar e disponibilizar arquivos na AWS.

Neste laboratório, o S3 foi utilizado para hospedar um site estático de uma cafeteria e padaria, disponibilizando seus arquivos por meio de um endpoint de hospedagem de site.

Os principais serviços e recursos utilizados foram:

- 📦 **Amazon S3:** armazenamento dos arquivos e hospedagem do site;
- 🔐 **AWS IAM:** criação de usuário e configuração de permissões;
- 💻 **Amazon EC2:** ambiente para executar os comandos da AWS CLI;
- 🛠️ **AWS CLI:** criação de recursos, configuração e transferência de arquivos;
- 🔄 **Bash:** criação de um script para automatizar a atualização do site.

**Duração aproximada:** 45 minutos.

---

## 🛠️ Configuração utilizada

| Recurso | Configuração |
|---|---|
| Serviço principal | Amazon S3 |
| Ambiente de execução | Amazon EC2 |
| Sistema operacional | Amazon Linux |
| Interface de gerenciamento | AWS CLI |
| Região utilizada | `us-west-2` |
| Bucket S3 | Nome exclusivo definido durante o laboratório |
| Usuário IAM | `awsS3user` |
| Arquivo principal do site | `index.html` |
| Diretórios do site | `css` e `imagens` |
| Script de atualização | `update-website.sh` |
| Hospedagem | Site estático no Amazon S3 |

---

## 💻 Tarefa 1 — Conectar à instância EC2 usando Session Manager

A primeira etapa consistiu em acessar a instância Amazon EC2 por meio do **AWS Systems Manager Session Manager**, utilizando uma sessão no navegador.

Após estabelecer a conexão, foi utilizado o comando abaixo para assumir o usuário `ec2-user` e acessar seu diretório inicial:

```bash
sudo su -l ec2-user
pwd
```

Essa etapa preparou o ambiente para a execução dos comandos da AWS CLI.

**Principais resultados:**

- ✅ Acesso à instância pelo navegador;
- ✅ Utilização do usuário `ec2-user`;
- ✅ Preparação do terminal para as atividades.

---

## ⚙️ Tarefa 2 — Configurar a AWS CLI

A AWS CLI foi configurada para utilizar as credenciais temporárias fornecidas pelo ambiente do laboratório.

```bash
aws configure
```

Foram informados os seguintes parâmetros:

| Parâmetro | Valor |
|---|---|
| Access Key ID | Credencial fornecida pelo laboratório |
| Secret Access Key | Credencial fornecida pelo laboratório |
| Região padrão | `us-west-2` |
| Formato de saída | `json` |

Essa configuração permitiu executar operações nos serviços AWS diretamente pelo terminal.

> **Observação de segurança:** não publique chaves de acesso, senhas ou credenciais reais em repositórios GitHub.

---

## 📦 Tarefa 3 — Criar um bucket S3 usando a AWS CLI

Nesta etapa, foi criado um bucket no Amazon S3 utilizando o comando `aws s3api create-bucket`.

```bash
aws s3api create-bucket \
  --bucket alessandro-edn-875 \
  --region us-west-2 \
  --create-bucket-configuration LocationConstraint=us-west-2
```

O nome do bucket deve ser exclusivo, conforme as regras de nomenclatura do Amazon S3.

Após a execução, a resposta em JSON foi utilizada para verificar a criação do recurso.

**Principais resultados:**

- ✅ Criação do bucket pelo terminal;
- ✅ Definição da região AWS;
- ✅ Utilização de comandos da família `s3api`.

---

## 🔐 Tarefa 4 — Criar um usuário IAM com acesso ao S3

Nesta etapa, foi criado o usuário `awsS3user` utilizando a AWS CLI.

### Criar o usuário

```bash
aws iam create-user --user-name awsS3user
```

### Criar o perfil de login

O material do laboratório utiliza o seguinte comando de exemplo:

```bash
aws iam create-login-profile \
  --user-name awsS3user \
  --password '<SENHA_DE_TESTE>'
```

A senha acima deve ser substituída por uma senha de laboratório adequada, respeitando a política de senhas configurada.

Em seguida, foram consultadas as políticas gerenciadas que contêm `S3` no nome:

```bash
aws iam list-policies \
  --query "Policies[?contains(PolicyName,'S3')]"
```

Após identificar a política apropriada, foi anexada ao usuário uma política que concede acesso total ao Amazon S3:

```bash
aws iam attach-user-policy \
  --policy-arn arn:aws:iam::aws:policy/<NOME_DA_POLITICA> \
  --user-name awsS3user
```

O objetivo foi compreender como as permissões do IAM controlam o acesso aos serviços e recursos da AWS.

---

## 🌐 Tarefa 5 — Configurar as permissões do bucket S3

Para permitir a disponibilização pública do site estático, foram ajustadas as configurações de acesso público e de propriedade dos objetos do bucket.

As configurações realizadas no console incluíram:

- 🔓 Ajuste de **Bloquear acesso público**;
- 📋 Habilitação de ACLs;
- ⚙️ Salvamento das alterações nas permissões do bucket.

Essas configurações fizeram parte do procedimento específico do laboratório para disponibilizar os arquivos do site publicamente.

> **Atenção:** desabilitar o bloqueio de acesso público e habilitar ACLs pode expor objetos à internet. Em ambientes reais, avalie cuidadosamente os requisitos de segurança e utilize somente as permissões necessárias.

---

## 📂 Tarefa 6 — Extrair os arquivos do site

Os arquivos do site foram extraídos no terminal da instância EC2.

```bash
cd ~/sysops-activity-files
tar xvzf static-website-v2.tar.gz
cd static-website
ls
```

A listagem permitiu verificar a presença do arquivo `index.html` e dos diretórios `css` e `imagens`.

Esses arquivos compõem o conteúdo estático que seria enviado ao bucket S3.

**Principais resultados:**

- ✅ Extração dos arquivos do site;
- ✅ Verificação da estrutura de diretórios;
- ✅ Preparação do conteúdo para publicação.

---

## 🚀 Tarefa 7 — Publicar o site no Amazon S3

Nesta etapa, foi habilitada a hospedagem de site estático e realizado o upload dos arquivos para o bucket.

### Habilitar a hospedagem de site estático

```bash
aws s3 website s3://alessandro-edn-875/ \
  --index-document index.html
```

### Enviar os arquivos para o bucket

```bash
aws s3 cp \
  /home/ec2-user/sysops-activity-files/static-website/ \
  s3://alessandro-edn-875/ \
  --recursive \
  --acl public-read
```

O parâmetro `--recursive` permite copiar os arquivos e subdiretórios do diretório de origem.

### Verificar os arquivos enviados

```bash
aws s3 ls alessandro-edn-875
```

Após o upload, foi verificada a configuração de hospedagem de site estático nas propriedades do bucket. O endpoint disponibilizado pelo S3 foi utilizado para abrir o site no navegador.

**Principais resultados:**

- ✅ Habilitação da hospedagem estática;
- ✅ Upload dos arquivos HTML, CSS e imagens;
- ✅ Verificação dos objetos armazenados;
- ✅ Acesso ao site por meio do endpoint do Amazon S3.

---

## 🔄 Tarefa 8 — Criar um script para atualizar o site

Para tornar a publicação repetível, foi criado o script Bash `update-website.sh`, responsável por enviar novamente os arquivos locais ao bucket S3.

### Criar o arquivo

```bash
cd ~
touch update-website.sh
vi update-website.sh
```

### Conteúdo do script

Substitua `alessandro-edn-875` pelo nome real do bucket utilizado no laboratório.

```bash
#!/bin/bash

aws s3 cp \
  /home/ec2-user/sysops-activity-files/static-website/ \
  s3://alessandro-edn-875/ \
  --recursive \
  --acl public-read
```

### Tornar o script executável

```bash
chmod +x update-website.sh
```

### Executar a atualização

```bash
./update-website.sh
```

Após modificar o arquivo `index.html`, o script foi executado novamente e a página do site foi atualizada no navegador para verificar as alterações.

**Principais resultados:**

- ✅ Criação de um script Bash;
- ✅ Automatização do upload dos arquivos;
- ✅ Atualização do site após alterações locais;
- ✅ Reutilização de comandos da AWS CLI.

---

## 🧠 Desafio opcional — Otimizar a sincronização com `aws s3 sync`

Como etapa adicional, o laboratório propõe substituir o comando `aws s3 cp` por `aws s3 sync` para sincronizar o diretório local com o bucket.

Exemplo:

```bash
aws s3 sync \
  /home/ec2-user/sysops-activity-files/static-website/ \
  s3://alessandro-edn-875/ \
  --acl public-read
```

O comando `aws s3 sync` compara os arquivos da origem e do destino e transfere os arquivos que precisam ser atualizados, evitando reenviar desnecessariamente arquivos que já estão sincronizados.

Essa abordagem pode tornar as atualizações mais eficientes quando apenas parte do site foi modificada.

---

## 📚 Principais aprendizados

Durante o laboratório, foram praticados conceitos importantes de armazenamento, permissões e implantação de sites na AWS:

- ☁️ Criação e configuração de buckets no Amazon S3;
- 🔐 Gerenciamento de usuários e políticas do IAM;
- 💻 Utilização da AWS CLI em uma instância EC2;
- 📂 Transferência recursiva de arquivos para o S3;
- 🌐 Hospedagem de sites estáticos;
- 🛠️ Automação de tarefas com scripts Bash;
- 🔄 Diferenças entre os comandos `aws s3 cp` e `aws s3 sync`;
- 🔒 Importância de avaliar permissões e exposição pública de arquivos.

---

## 🧠 O que ficou de aprendizado

O principal aprendizado deste laboratório foi compreender como a **AWS CLI permite automatizar a criação de recursos, o gerenciamento de permissões e a publicação de conteúdo no Amazon S3**.

A atividade demonstrou o processo de implantação de um site estático, desde a preparação dos arquivos locais até sua disponibilização por meio do endpoint do bucket.

A criação do script Bash também mostrou como automatizar atualizações recorrentes, enquanto o estudo do `aws s3 sync` apresentou uma alternativa para otimizar a transferência de arquivos modificados.

---

## 📸 Evidências do laboratório

### 📦 Criação do bucket S3

Registro da criação do bucket utilizando a AWS CLI.

<p align="center">
  <img width="1904" height="892" alt="Image" src="https://github.com/user-attachments/assets/f0938f13-3bea-42ee-9a57-726dfb9df05b" />
  <img width="1920" height="887" alt="Image" src="https://github.com/user-attachments/assets/fc79a940-6d17-4916-bffc-21abc1f5a1d9" />
</p>

### 🔐 Usuário e permissões IAM

Registro da criação do usuário e da configuração da política de acesso ao S3.

<p align="center">
  <img width="1903" height="883" alt="Image" src="https://github.com/user-attachments/assets/12ac3bc7-422c-4500-b8f1-34b4188a35cc" />
  <img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/c9207393-388a-4d56-847f-b39e55d9f4cd" />
  <img width="1897" height="885" alt="Image" src="https://github.com/user-attachments/assets/21fff78c-564c-4d05-a4fc-685e94b4e3da" />
  <img width="1901" height="884" alt="Image" src="https://github.com/user-attachments/assets/bc50fad2-24e7-4b50-88ca-c29450d6a8b9" />
</p>

### 🌐 Site estático publicado

Registro do site da cafeteria e padaria acessível pelo navegador.

<p align="center">
  <img width="1894" height="887" alt="Image" src="https://github.com/user-attachments/assets/e6728a20-3cf0-407c-8482-4ba0aaa81f77" />
</p>

### 🔄 Script de atualização

Registro do script `update-website.sh` e da execução da atualização do site.

<p align="center">
  <img width="1920" height="881" alt="Image" src="https://github.com/user-attachments/assets/b5a64524-41cb-4e14-ae16-1563d7d17e75" />
  <img width="1920" height="985" alt="Image" src="https://github.com/user-attachments/assets/2f92382e-2559-456d-bae4-0d17fb18a555" />
</p>

---

## 🧰 Tecnologias e serviços

![AWS](https://img.shields.io/badge/AWS-Cloud-orange?style=for-the-badge\&logo=amazonaws)

![Amazon S3](https://img.shields.io/badge/Amazon-S3-orange?style=for-the-badge\&logo=amazons3)

![Amazon EC2](https://img.shields.io/badge/Amazon-EC2-orange?style=for-the-badge\&logo=amazonec2)

![IAM](https://img.shields.io/badge/AWS-IAM-orange?style=for-the-badge\&logo=amazonaws)

![AWS CLI](https://img.shields.io/badge/AWS-CLI-orange?style=for-the-badge\&logo=amazonaws)

![Linux](https://img.shields.io/badge/Amazon-Linux-orange?style=for-the-badge\&logo=linux)

![Bash](https://img.shields.io/badge/Bash-Scripting-black?style=for-the-badge\&logo=gnubash)

**Serviços e conceitos estudados:**

`Amazon S3` • `Amazon EC2` • `AWS IAM` • `AWS CLI` • `Amazon Linux` • `Bash` • `Static Website Hosting` • `Bucket Policies` • `IAM Policies` • `S3 cp` • `S3 sync` • `Session Manager`

---

## 📖 Referência

**AWS Training and Certification**

Laboratório: **Criar um site no S3**.

Material utilizado para a realização das atividades práticas de criação, configuração e hospedagem de um site estático no Amazon S3.

---

## 🚀 Próximos passos

🔹 Continuar os laboratórios práticos de **AWS Cloud**;

🔹 Aprofundar os estudos em Amazon S3, IAM e AWS CLI;

🔹 Explorar estratégias de sincronização e automação de arquivos;

🔹 Estudar alternativas seguras para publicação de sites estáticos;

🔹 Aplicar os conhecimentos adquiridos em novos laboratórios e projetos práticos.

---

<p align="center">
  ☁️ <strong>Aprendizado contínuo em Cloud Computing</strong> ☁️
</p>

<p align="center">
  <sub>Laboratório realizado para fins educacionais.</sub>
</p>




<p align="center">
  <sub>© 2026 Alessandro Batista Prudente — Todos os direitos reservados.</sub>
</p>

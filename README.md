# Desafio de Estágio em DevOps - VExpenses

Este projeto faz parte do desafio para a vaga de estágio em DevOps, cujo objetivo é criar uma infraestrutura na AWS utilizando Terraform, implementando segurança, automação e melhorias no código.

## Tecnologias Usadas

![Terraform](https://img.shields.io/badge/Terraform-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Debian](https://img.shields.io/badge/Debian-A81D33?style=for-the-badge&logo=debian&logoColor=white)
![Nginx](https://img.shields.io/badge/Nginx-009639?style=for-the-badge&logo=nginx&logoColor=white)
![SSH](https://img.shields.io/badge/SSH-000000?style=for-the-badge&logo=ssh&logoColor=white)
![VPC](https://img.shields.io/badge/AWS--VPC-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)

## Ferramentas e Serviços

![EC2](https://img.shields.io/badge/AWS--EC2-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Security Groups](https://img.shields.io/badge/Security%20Groups-FF4C29?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Subnets](https://img.shields.io/badge/Subnets-006600?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Internet Gateway](https://img.shields.io/badge/Internet%20Gateway-FF8C00?style=for-the-badge&logo=amazon-aws&logoColor=white)

## 1. Descrição Técnica do Código Original (Tarefa 1)

O código original utiliza o Terraform para provisionar uma infraestrutura básica na AWS. Os recursos criados são:

1. **Provedor AWS**:
   - O código define a AWS como provedora de serviços e especifica a região `us-east-1` para a criação dos recursos.

2. **Variáveis**:
   - O código utiliza variáveis para parametrizar o nome do projeto e do candidato, facilitando a reutilização do código.
   - Variáveis:
     - `projeto`: Nome do projeto.
     - `candidato`: Nome do candidato.

3. **Criação de Chave SSH**:
   - `tls_private_key`: Gera uma chave privada usando o algoritmo RSA com 2048 bits.
   - `aws_key_pair`: Cria um par de chaves para ser usado para acessar a instância EC2.

4. **Rede e Sub-rede (VPC e Subnet)**:
   - `aws_vpc`: Cria uma VPC (Virtual Private Cloud) com o bloco de IP `10.0.0.0/16`, que serve como rede principal para os recursos.
   - `aws_subnet`: Cria uma sub-rede dentro da VPC, com o bloco de IP `10.0.1.0/24`, localizada na zona de disponibilidade `us-east-1a`.

5. **Internet Gateway e Tabela de Rotas**:
   - `aws_internet_gateway`: Proporciona acesso à Internet para os recursos dentro da VPC.
   - `aws_route_table`: Cria uma tabela de rotas associada à VPC, com uma rota padrão para o Internet Gateway.
   - `aws_route_table_association`: Associa a tabela de rotas à sub-rede.

6. **Grupo de Segurança**:
   - `aws_security_group`: Define um grupo de segurança que permite:
     - Tráfego SSH (porta 22) de qualquer origem (IPv4 e IPv6).
     - Todo o tráfego de saída.
   
7. **Imagem AMI e Instância EC2**:
   - `data "aws_ami"`: Seleciona a AMI mais recente do Debian 12 para ser usada pela instância EC2.
   - `aws_instance`: Cria uma instância EC2 utilizando a AMI do Debian, configurando o disco com 20 GB e associando um endereço IP público.

8. **Saídas**:
   - `private_key`: Exibe a chave privada para acesso SSH.
   - `ec2_public_ip`: Mostra o endereço IP público da instância EC2.

## 2. Descrição Técnica das Modificações (Tarefa 2)

As modificações realizadas no código original visam melhorar a segurança, funcionalidade e usabilidade. As mudanças incluem:

1. **Variável para IP Permitido no SSH**:
   - **Alteração:** Adicionei a variável `ssh_allowed_ip` para definir o IP autorizado para acesso SSH.
   - **Motivação:** Aumentar a segurança permitindo acesso SSH apenas de um IP específico.
   - **Impacto:** O código agora pode restringir o acesso SSH para um endereço IP ou intervalo de IPs específico.

2. **Correção e Melhoria do Grupo de Segurança**:
   - **Alteração:** Atualizei o grupo de segurança para permitir acesso HTTP (porta 80) e alterei a descrição para remover caracteres não ASCII.
   - **Motivação:** Permitir que o Nginx seja acessado via HTTP e garantir que a configuração do grupo de segurança seja válida.
   - **Impacto:** Melhor acessibilidade do servidor e conformidade com os requisitos de segurança da AWS.

3. **Uso do ID do Grupo de Segurança**:
   - **Alteração:** Substituí o campo `security_groups` por `vpc_security_group_ids` para usar o ID do grupo de segurança.
   - **Motivação:** Garantir que o grupo de segurança seja corretamente associado à instância EC2.
   - **Impacto:** Resolveu o erro onde o Terraform não encontrava o grupo de segurança pelo nome.

4. **Alteração da AMI para Debian 11**:
   - **Alteração:** Atualizei o código para usar uma AMI pública do Debian 11 em vez do Debian 12 do AWS Marketplace.
   - **Motivação:** Evitar a necessidade de aceitar termos do AWS Marketplace, tornando o código mais acessível.
   - **Impacto:** Permite a criação da instância EC2 sem precisar de passos manuais adicionais.

5. **Automação da Instalação do Nginx**:
   - **Alteração:** Modifiquei o `user_data` para incluir a instalação e configuração automática do Nginx.
   - **Motivação:** Atender ao critério do desafio de automatizar a instalação de um servidor web.
   - **Impacto:** A instância já estará pronta para servir conteúdo web assim que for iniciada.


## 3. Como Executar o Código

### 3.1. Pré-requisitos
- **Terraform instalado:** Siga as instruções em [https://www.terraform.io/downloads](https://www.terraform.io/downloads).
- **AWS CLI configurado:** Configure suas credenciais com `aws configure` e tenha permissões suficientes para criar recursos na AWS.

### 3.2. Passos para Execução
1. **Clone o repositório e entre na pasta do projeto:**
   ```bash
   git clone <URL_DO_REPOSITORIO>
   cd terraform-devops-desafio
   ```

2. **Inicialize o Terraform:**
  ```bash
  terraform init
  ```

3. **Valide o arquivo de configuração:**
  ```bash
  terraform validate
  ```
4. **Crie o plano de execução:**
  ```bash
  terraform plan
  ```
5. **Aplique o plano:**
  ```bash
  terraform apply
  ```
- **Confirme a execução digitando `"yes"` quando solicitado.**

### 3.3. Acesso à Instância EC2
1. **Salve a chave privada gerada em um arquivo chamado `ec2_key.pem` e ajuste as permissões:**
```bash
echo "SUA_CHAVE_PRIVADA" > ec2_key.pem
chmod 400 ec2_key.pem
```
2. **Conecte-se à instância:**
```bash
ssh -i ec2_key.pem ubuntu@<EC2_PUBLIC_IP>
```
### 3.4. Destruição dos Recursos
**Para evitar custos, destrua os recursos criados após os testes:**
```bash
terraform destroy
```
- **Digite `"yes"` para confirmar a destruição dos recursos.**

### 4. Considerações Finais
**O código foi atualizado para atender aos requisitos de segurança e melhores práticas, com configurações dinâmicas, uso de AMI pública e automação para configuração do servidor Nginx. O projeto demonstra uma infraestrutura básica na AWS com ênfase em segurança e automação.**


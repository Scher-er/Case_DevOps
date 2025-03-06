# Guia de Inicialização e Aplicação da Configuração Terraform

## Pré-requisitos

Antes de iniciar, certifique-se de que você possui os seguintes pré-requisitos instalados e configurados:

1. **Terraform**: Instale a versão mais recente do Terraform. Você pode baixar o Terraform [aqui](https://www.terraform.io/downloads.html).
2. **AWS CLI**: Instale a AWS CLI e configure suas credenciais. Você pode seguir as instruções [aqui](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html).
3. **Conta AWS**: Certifique-se de ter uma conta AWS com permissões suficientes para criar e gerenciar recursos.

## Passos para Inicializar e Aplicar a Configuração Terraform

### 1. Configurar as Credenciais AWS

Configure suas credenciais AWS usando o comando abaixo:

```sh
aws configure
```

Você será solicitado a inserir seu `AWS Access Key ID`, `AWS Secret Access Key`, `Default region name` e `Default output format`.

### 2. Inicializar o Terraform

Navegue até o diretório onde seu arquivo main_mod2.tf está localizado e inicialize o Terraform:

```sh
cd /c:/Users/55129/OneDrive - FUNDACAO VALEPARAIBANA DE ENSINO/Programação/Vexpenses
terraform init
```

Este comando inicializa o diretório de trabalho contendo a configuração do Terraform. Ele baixa os plugins necessários para interagir com os provedores de nuvem especificados no arquivo de configuração.

### 3. Validar a Configuração

Valide a configuração do Terraform para garantir que não há erros de sintaxe ou configuração:

```sh
terraform validate
```

### 4. Visualizar o Plano de Execução

Gere e visualize o plano de execução para ver quais ações o Terraform realizará:

```sh
terraform plan
```

### 5. Aplicar a Configuração

Aplicar a configuração do Terraform para criar os recursos na AWS:

```sh
terraform apply
```

Você será solicitado a confirmar a aplicação digitando `yes`.

### 6. Verificar os Recursos Criados

Após a aplicação bem-sucedida, você pode verificar os recursos criados na AWS Management Console. Além disso, você pode visualizar as saídas definidas no arquivo main_mod2.tf:

```sh
terraform output
```

### 7. Limpar os Recursos Criados (Opcional)

Se você deseja remover todos os recursos criados pelo Terraform, execute o comando abaixo:

```sh
terraform destroy
```

Você será solicitado a confirmar a destruição digitando `yes`.

## Adendo: Caso o Nginx Não Seja Instalado Automaticamente

Se o Nginx não for instalado automaticamente pela instância EC2, você pode acessar a instância via SSH e instalar manualmente. Siga os passos abaixo:

1. **Obtenha o endereço IP público da instância EC2**:

   ```sh
   terraform output ec2_public_ip
   ```

2. **Acesse a instância via SSH**:

   ```sh
   ssh -i /caminho/para/chave_privada.pem ec2-user@<EC2_PUBLIC_IP>
   ```

3. **Instale o Nginx manualmente**:

   ```sh
   sudo apt update -y
   sudo apt install -y nginx
   sudo systemctl enable nginx
   sudo systemctl start nginx
   ```

4. **Verifique se o Nginx está em execução**:

   ```sh
   sudo systemctl status nginx
   ```

Seguindo esses passos, você deve ser capaz de instalar e configurar o Nginx manualmente na instância EC2.
-------
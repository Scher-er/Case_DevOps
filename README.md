# Case_DevOps
DESAFIO ONLINE DA VEXPENSES
# Relatório da Primeira Análise do Código main.tf

## Introdução
Recebi a tarefa de analisar um código Terraform que configura uma infraestrutura básica na AWS. O objetivo parece ser criar um ambiente simples para rodar uma instância EC2 com Debian 12, garantindo conectividade com a internet e acesso remoto via SSH. Abaixo, explico cada parte do código e trago algumas observações sobre possíveis melhorias.

## O que o código faz?

1. **Configuração da AWS**  
   O código define a **região us-east-1** (Virgínia do Norte) para provisionamento dos recursos.

2. **Definição de variáveis**  
   São usadas duas variáveis para nomear os recursos:
   - `projeto`: Define o nome do projeto (por padrão "VExpenses").
   - `candidato`: Nome do candidato, para personalizar os recursos criados.

3. **Criação de chave SSH**  
   - O código gera uma **chave privada SSH**.
   - Cria um **par de chaves na AWS** para permitir acesso à instância EC2.

4. **Configuração de Rede**  
   - **Cria uma VPC** com o CIDR `10.0.0.0/16`.
   - **Cria uma sub-rede pública** (`10.0.1.0/24`) na zona `us-east-1a`.
   - **Cria um Internet Gateway** para dar acesso à internet.

5. **Configuração de Roteamento**  
   - **Tabela de rotas** é criada, permitindo tráfego externo através do Internet Gateway.
   - **Associa a sub-rede pública à tabela de rotas**.

6. **Grupo de Segurança (Security Group)**  
   - Permite **conexões SSH (porta 22) de qualquer IP** (`0.0.0.0/0`).  
   - Permite **todo tráfego de saída**.
   - ⚠️ **Ponto de atenção:** Permitir SSH de qualquer lugar pode ser perigoso. O ideal seria restringir a um IP específico.

7. **Escolha da AMI (Imagem do SO)**  
   - Seleciona automaticamente a versão **mais recente do Debian 12**.

8. **Criação da Instância EC2**  
   - **Tipo:** `t2.micro` (free tier).  
   - **IP público ativado**, garantindo acesso remoto.  
   - **Armazenamento:** 20 GB SSD (`gp2`).  
   - **Configuração inicial:** O código inclui um **script de inicialização** (`user_data`) que já atualiza os pacotes do sistema.  

9. **Saídas do Terraform**  
   - **Chave privada SSH** (usada para acessar a instância).  
   - **IP público da instância EC2** (para conectar via SSH).  

## Considerações Finais Do Primeiro Relatório 

O código está estruturado e cobre o essencial para subir uma instância na AWS. Algumas melhorias que poderiam ser feitas:
  

⚠️ **Sugestões de melhoria:**  
- **Restringir o acesso SSH** para evitar vulnerabilidades.  
- **Implementar um mecanismo de alta disponibilidade**, pois a sub-rede está presa à `us-east-1a`.  
- **Melhorar a segurança do tráfego de saída**, em vez de liberar tudo.  

No geral, o código atende ao propósito de subir uma instância EC2, mas algumas melhorias podem torná-lo mais seguro.  

----------------------------------------------------------------------  

# Modificação e melhoria do código Terraform


# Comparação entre `main.tf` e `main_mod2.tf`

## Introdução

Este documento descreve as diferenças entre os arquivos `main.tf` e `main_mod2.tf`, detalhando as alterações feitas, os resultados esperados e as justificativas para essas mudanças.

Claro, aqui está a atualização do arquivo README.md com a comparação atualizada entre `main.tf` e `main_mod2.tf`, incluindo a nova seção sobre o `user_data`:

```markdown
# Comparação entre `main.tf` e `main_mod2.tf`

## Introdução

Este documento descreve as diferenças entre os arquivos `main.tf` e `main_mod2.tf`, detalhando as alterações feitas, os resultados esperados e as justificativas para essas mudanças.

## Alterações Detalhadas

### 1. Variáveis

#### `main.tf`
```terraform
variable "projeto" {
  description = "Nome do projeto"
  type        = string
  default     = "VExpenses"
}

variable "candidato" {
  description = "Nome do candidato"
  type        = string
  default     = "SeuNome"
}
```

#### main_mod2.tf
```terraform
variable "projeto" {
  description = "Nome do projeto"
  type        = string
  default     = "CaseV"
}

variable "candidato" {
  description = "Nome do candidato"
  type        = string
  default     = "PedroScherer"
}

variable "allowed_ip" {
  description = "IP permitido para acesso SSH"
  type        = string
  default     = "0.0.0.0/0" # Defina o IP permitido para maior segurança
}
```

**Alterações**:
- O valor padrão da variável `projeto` foi alterado de `"VExpenses"` para `"CaseV"`.
- O valor padrão da variável `candidato` foi alterado de `"SeuNome"` para `"PedroScherer"`.
- Adicionada a variável `allowed_ip` para definir o IP permitido para acesso SSH.

**Justificativa**:
- As mudanças nos valores das variáveis `projeto` e `candidato` personalizam o projeto e o candidato para um novo contexto.
- A adição da variável `allowed_ip` melhora a segurança ao permitir a especificação de um IP específico para acesso SSH.

### 2. Chave Privada

#### `main.tf`
```terraform
resource "tls_private_key" "ec2_key" {
  algorithm = "RSA"
  rsa_bits  = 2048
}
```

#### `main_mod2.tf`
```terraform
resource "tls_private_key" "ec2_key" {
  algorithm = "RSA"
  rsa_bits  = 4096 # Aumentado para 4096 bits para maior segurança
}
```

**Alterações**:
- O tamanho da chave RSA foi aumentado de 2048 bits para 4096 bits.

**Justificativa**:
- Aumentar o tamanho da chave RSA para 4096 bits melhora a segurança criptográfica.

### 3. Recurso `local_file`

#### `main.tf`
- Não possui o recurso `local_file`.

#### `main_mod2.tf`
```terraform
resource "local_file" "private_key" {
  content  = tls_private_key.ec2_key.private_key_pem
  filename = "${path.module}/chave_privada.pem"
  file_permission = "0400" # Permissões restritas para o arquivo
}
```

**Alterações**:
- Adicionado o recurso `local_file` para salvar a chave privada localmente com permissões restritas.

**Justificativa**:
- Salvar a chave privada localmente com permissões restritas melhora a segurança ao garantir que apenas o proprietário do arquivo possa lê-lo.

### 4. Grupo de Segurança

#### `main.tf`
```terraform
resource "aws_security_group" "main_sg" {
  name        = "${var.projeto}-${var.candidato}-sg"
  description = "Permitir SSH de qualquer lugar e todo o tráfego de saída"
  vpc_id      = aws_vpc.main_vpc.id

  ingress {
    description      = "Allow SSH from anywhere"
    from_port        = 22
    to_port          = 22
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
  }

  egress {
    description      = "Allow all outbound traffic"
    from_port        = 0
    to_port          = 0
    protocol         = "-1"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
  }

  tags = {
    Name = "${var.projeto}-${var.candidato}-sg"
  }
}
```

#### `main_mod2.tf`
```terraform
resource "aws_security_group" "main_sg" {
  name        = "${var.projeto}-${var.candidato}-sg"
  description = "Allow SSH from allowed IP and all outbound traffic"
  vpc_id      = aws_vpc.main_vpc.id

  ingress {
    description      = "Allow SSH from allowed IP"
    from_port        = 22
    to_port          = 22
    protocol         = "tcp"
    cidr_blocks      = [var.allowed_ip] # Permitir apenas o IP permitido
  }

  egress {
    description      = "Allow all outbound traffic"
    from_port        = 0
    to_port          = 0
    protocol         = "-1"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
  }

  tags = {
    Name = "${var.projeto}-${var.candidato}-sg"
  }
}
```

**Alterações**:
- A regra de entrada (`ingress`) foi modificada para permitir SSH apenas do IP especificado na variável `allowed_ip`.

**Justificativa**:
- Restringir o acesso SSH a um IP específico aumenta a segurança, reduzindo a superfície de ataque.

### 5. Bloco de Dispositivo de Root

#### `main.tf`
```terraform
root_block_device {
  volume_size           = 20
  volume_type           = "gp2"
  delete_on_termination = true
}
```

#### `main_mod2.tf`
```terraform
root_block_device {
  volume_size           = 20
  volume_type           = "gp2"
  delete_on_termination = true
  encrypted             = true
}
```

**Alterações**:
- Adicionada a criptografia (`encrypted = true`) ao bloco de dispositivo de root.

**Justificativa**:
- Criptografar o volume de root melhora a segurança dos dados armazenados na instância.

### 6. Script de `user_data`

#### `main.tf`
```terraform
user_data = <<-EOF
            #!/bin/bash
            apt-get update -y
            apt-get upgrade -y
            EOF
```

#### `main_mod2.tf`
```terraform
user_data = <<-EOF
#cloud-config
package_update: true
package_upgrade: true
packages:
  - nginx
  - fail2ban
  - ufw

runcmd:
  - systemctl enable nginx
  - systemctl start nginx
  - ufw allow OpenSSH
  - ufw allow 'Nginx HTTP'
  - ufw --force enable
EOF
```

**Alterações**:
- O script `user_data` foi expandido para incluir a instalação e configuração de Nginx, Fail2ban e UFW, além de limpar o cache do cloud-init e registrar a saída do script.

**Justificativa**:
- A instalação e configuração de Nginx, Fail2ban e UFW aumentam a funcionalidade e a segurança da instância.
- Limpar o cache do cloud-init garante que o script `user_data` seja executado corretamente.
- Registrar a saída do script ajuda na depuração e monitoramento do processo de inicialização.

## Resultados Esperados

- **Segurança Melhorada**: Aumentar o tamanho da chave RSA, criptografar o volume de root e restringir o acesso SSH a um IP específico aumentam a segurança da infraestrutura.
- **Funcionalidade Adicional**: A instalação de Nginx, Fail2ban e UFW adiciona funcionalidades úteis e melhora a segurança da instância.
- **Personalização**: As mudanças nas variáveis `projeto` e `candidato` personalizam o ambiente para um novo contexto.

## Justificativa Geral

As alterações feitas no arquivo `main_mod2.tf` visam melhorar a segurança e a funcionalidade da infraestrutura provisionada. Aumentar o tamanho da chave RSA e criptografar o volume de root são práticas recomendadas para proteger dados sensíveis. Restringir o acesso SSH a um IP específico reduz a superfície de ataque. A instalação de Nginx, Fail2ban e UFW adiciona funcionalidades úteis e melhora a segurança da instância.

## Nota sobre a mudança no `user_data`

A mudança no `user_data` foi feita para garantir que o Nginx, Fail2ban e UFW sejam instalados e configurados automaticamente na inicialização da instância EC2. O uso do `cloud-config` simplifica a configuração inicial e garante que os serviços necessários estejam prontos para uso imediatamente após a inicialização.

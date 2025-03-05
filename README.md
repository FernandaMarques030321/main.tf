# Documentação do Desafio Terraform

## 1. Análise do Código Original

O arquivo `main.tf` define uma infraestrutura básica na AWS utilizando Terraform. A infraestrutura criada inclui:

- **VPC (Virtual Private Cloud)**: Rede privada na AWS.
- **Subnet**: Sub-rede dentro da VPC.
- **Internet Gateway**: Permite que a VPC tenha conexão com a internet.
- **Tabela de Rotas**: Redireciona tráfego externo para o Internet Gateway.
- **Grupo de Segurança (Security Group)**: Define regras de firewall.
- **Par de Chaves (Key Pair)**: Gera uma chave SSH para acesso à instância EC2.
- **Instância EC2**: Servidor baseado em Debian 12.

## 2. Melhorias Implementadas

### **2.1. Melhorias de Segurança**

- **Aumento da segurança do acesso SSH**:
  - O código original permitia conexão SSH de qualquer IP (`0.0.0.0/0`).
  - Agora, o acesso está restrito a um IP específico (`SEU_IP/32`).
  - Isso evita acessos indevidos e ataques de força bruta.

- **Aumento do tamanho da chave RSA**:
  - A chave SSH foi aumentada de **2048 bits** para **4096 bits**, tornando-a mais resistente a ataques criptográficos.

- **Fechamento de portas desnecessárias**:
  - Apenas portas necessárias estão abertas: **22 (SSH)** e **80 (HTTP - Nginx)**.

### **2.2. Automalização do Nginx**

- A instância EC2 agora **instala automaticamente o Nginx**.
- O serviço inicia automaticamente e é configurado para reiniciar após falhas.
- O script de inicialização (`user_data`) foi atualizado:
  ```bash
  #!/bin/bash
  apt-get update -y
  apt-get upgrade -y
  apt-get install -y nginx
  systemctl enable nginx
  systemctl start nginx
  ```

### **2.3. Outras Melhorias**

- Melhor organização do código e inclusão de comentários explicativos.
- Adicionada **tagging** mais organizada para facilitar a identificação dos recursos AWS.

## 3. Instruções de Uso

### **3.1. Pré-requisitos**

Antes de rodar o Terraform, você precisa:
- Ter uma conta AWS configurada.
- Instalar o Terraform em sua máquina.
- Configurar as credenciais AWS via AWS CLI (`aws configure`).

### **3.2. Inicialização e Execução**

1. **Clone o repositório**:
   ```bash
   git clone <seu-repositorio>
   cd <seu-repositorio>
   ```

2. **Inicialize o Terraform**:
   ```bash
   terraform init
   ```

3. **Visualize as mudanças antes de aplicar**:
   ```bash
   terraform plan
   ```

4. **Aplique as configurações**:
   ```bash
   terraform apply
   ```

5. **Obtenha o IP da instância**:
   ```bash
   terraform output ec2_public_ip
   ```

6. **Acesse o servidor via navegador**:
   Abra `http://<IP-PUBLICO>` no navegador para verificar se o Nginx está rodando.

### **3.3. Destruir a Infraestrutura**

Para remover os recursos criados:
```bash
terraform destroy
```

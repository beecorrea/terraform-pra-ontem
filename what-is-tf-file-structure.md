---
layout: post.njk 
title: "Terraform, pra ontem."
date: 2023-01-04
tags: ['post', 'terraform']
---

[[toc]]
***

## Introdução
De forma simples, Terraform é uma ferramenta de IaC comumente usada para abstrair o provisionamento (criação) de recursos de infraestrutura, a partir de arquivos de configuração declarativos (também chamada apenas de configuração).

## Conteúdos de um arquivo Terraform
No geral, esse é o formato de um arquivo terraform. Ele inclui algumas meta-configurações do Terraform no bloco `terraform`, um módulo para provisionar máquinas virtuais e um recurso para provisionar bancos de dados.

```terraform
terraform {
  backend "remote" {
    organization = "empresa_do_meu_chefe_e_nao_minha"

    workspaces {
      name = "app_da_empresa_e_nao_meu"
    }
  }

  required_providers {
    milkloud_provider = {
      version = ">= 1.0.0"
      source = "clouddemilhoes/milcloud"
    }
  }
}

resource "milcloud_database" "banco_de_dev_com_dado_de_prod" {

}

module "modulo_da_empresa_criar_vm" {
  source = ""
  version = ""
}

```

### Bloco de configuração
É responsável por configurar o comportamento do Terraform, como as dependências da configuração e onde o Terraform armazenará o estado resultante do provisionamento. Nesse exemplo, o estado será salvo na Terraform Cloud, no workspace `app_da_empresa_e_nao_meu` dentro da organização `empresa_do_meu_chefe_e_nao_minha`, devido ao uso do backend `remote`.


```terraform
terraform {
  backend "remote" {
    organization = "empresa_do_meu_chefe_e_nao_minha"

    workspaces {
      name = "app_da_empresa_e_nao_meu"
    }
  }

  required_providers {
    milkloud_provider = {
      version = ">= 1.0.0"
      source = "clouddemilhoes/milcloud"
    }
  }
}
```

### Bloco de recurso
O bloco de recurso é, no final das contas, o mais importante da configuração Terraform. Ele é constituído de 4 partes:

1. A palavra chave `resource`, para indicar que um recurso será criado.
2. O tipo do recurso, nesse caso `"milcloud_database"`.
3. Um identificador **opcional** para apelidar o recurso no **estado** criado, visível apenas para o Terraform.
4. Um bloco com os parâmetros da configuração, que podem ser encontrados na documentação do `provider`.

```terraform
resource "milcloud_database" "banco_de_dev_com_dados_de_prod" {
  instance_name = "o_banco"
  size = "40G"
  allow_deletion = false
}
```

### Bloco de módulo
Os módulos de Terraform são como funções ou classes em linguagens de programação modernas. Eles permitem abstrair diversos recursos e provisioná-los de uma vez só ou com padrões e boas-práticas pré-configurados.

Parecido com o bloco de recurso, ele é composto pelas seguintes partes:

1. A palavra chave `module`, para indicar que um módulo será utilizado.
2. Um identificador **opcional** para apelidar o módulo no **estado** criado, visível apenas para o Terraform.
3. Um bloco com os parâmetros da configuração, que podem ser encontrados na documentação do `provider`.

Dois destes parâmetros são usados para o setup do módulo:
- `source`: obrigatório. indica onde código do módulo está, pode ser um repositório Git externo, uma pasta dentro do projeto...
- `version`: opcional. indica qual versão do módulo utilizar.

```terraform
module "modulo_da_empresa_criar_vm" {
  source = "clouddemilhoes/milcloud/vm"
  version = ">= v1.2.3"
}
```

## Como provisionar a infraestrutura?
Após criar um arquivo de configuração, com extensão `.tf` (por exemplo, `main.tf`), descrevendo o estado desejado da sua infraestrutura, e executa-se os comandos para provisioná-la. Os comandos mais importantes são:

`terraform plan`
Exibe quais alterações irão ocorrer quando você provisionar os recursos.

`terraform apply`
Além de mostrar as alterações que vão ocorrer, efetivamente provisiona os recursos desejados.

`terraform destroy`
Destrói **permanentemente** os recursos provisionados anteriormente.
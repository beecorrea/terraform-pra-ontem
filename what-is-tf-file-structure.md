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

## Como provisionar a infraestrutura?
Após criar um arquivo de configuração, com extensão `.tf` (por exemplo, `main.tf`), descrevendo o estado desejado da sua infraestrutura, e executa-se os comandos para provisioná-la. Os comandos mais importantes são:

`terraform plan`
Exibe quais alterações irão ocorrer quando você provisionar os recursos.

`terraform apply`
Além de mostrar as alterações que vão ocorrer, efetivamente provisiona os recursos desejados.

`terraform destroy`
Destrói **permanentemente** os recursos provisionados anteriormente.
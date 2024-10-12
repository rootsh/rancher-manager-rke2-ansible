# Instalação do cluster para o Rancher Manager

## RKE2

Para instalação do Rancher Manager com Kubernetes em RKE2 necessitamos de 3 VMs onde 1 será para usada como master.

## Pré-requisitos

### Vault

É usado para guardar e ler o token onde os nós vão ser usados para se comunicar com o master e também usando para pegar a senha do admin do Rancher Manager.

Precisamos da varável de ambiente que sao:
- VALT_ADDR
- VAULT_TOKEN

### DNS

Todos os nomes das VMs precisam estar cadastradas no DNS

### SSH

Necessitamos do acesso as VMs com acesso via SSH, no caso usamos com o usuário root

### Oracle Linux 9

Essa implementção foi baseada usando Oracle Linux 9

### Python HVAC

Precisamos dessa biblioteca para que conseguimos pegar informações no Vault

### Variáveis do Ansible

As variáveis padrões estão no arquivo [RKE2/ansible/roles/default/defaults/main.yml](RKE2/ansible/roles/default/defaults/main.yml)

## Deployment

### Ansible

Para rodar o Ansible Playbook usando o arquivo [RKE2/ansible/deployment.yml](RKE2/ansible/deployment.yml) que é chamada para role o arquivo [RKE2/ansible/inventory.yaml](RKE2/ansible/inventory.yaml) onde está contidas os endereços das VMs IP e Hostname, esse arquivo pode ser duplicado e consigurado para cluster que criarmos.

### Deploy

Para o deploy precisamos que a máquina tenha o Ansible instalado

```shell
cd RKE2/ansible
export VAUT_ADDR=http://192.168.86.185
export VAULT_SKIP_VERIFY=true
vault login -method=userpass username=alex.laner
export VAUT_TOKEN=$(cat ~/.vault-token)
ansible-playbook -i inventory.yaml -u root deployment.yml -e vault_addr=$VAULT_ADDR -e vault_token=$VAULT_TOKEN
```

Como a configuração do cluster do RKE2 necessita primeiro executamos o node (VM) que vai ser o master, depois disso ele vai ficar com o prompt esperando para validar que está tudo OK com o node master.
Alguns comandos e procedimentos úteis para agilizar as atividades do laboratório.
*Obs.: Considerando a utilização do Linux*

## VirtualBox

### VM setup

Após importar o arquivo da máquina virtual, caso queira fazer em casa, o jeito mais fácil de acesso ssh para a VM é via uma interface de rede exclusiva do hospedeiro (host-only), como citado no roteiro. Entretanto caso deseje conectar à internet a partir da VM, sugere-se criar também uma interface NAT, dessa forma nas configurações de rede da máquina virtual, habilite dois adaptadores, um *host-only* e outro *NAT*.
Se não existir opção para *host-only*, é necessário criar uma nas preferências do VirtualBox (*Arquivo>Preferências>Rede*). Configure um IP se desejar, podendo também habilitar o DHCP para a VM obter um IP automático.
**Utilizando 2 adaptadores:** Na máquina virtual edite `/etc/network/interfaces` e adicione uma entrada para o adaptador extra, e.g.
> auto eth1
> iface eth1 inet dhcp

### Pastas compartilhadas

É possível configurar pastas compartilhadas entre a máquina local e a virtual, e assim compartilhar arquivos entre ambas de uma maneira mais simples.
Nas configurações da VM (>Pastas compartilhadas), pode-se configurar pastas temporárias ou permanentes, estas pastas são caminhos para um diretório da máquina local que serão montados na máquina virtual.
Após configurar no VirtualBox (caso montagem automática), dentro da VM deve executar o seguinte comando:
```bash
sudo usermod -aG vboxsf mininet
```
A pasta compartilhada estará montada na máquina virtual em `/media/sf_<nome_escolhido>` após fazer o login novamente.

Também é possível montar um diretório da máquina virtual na máquina local utilizando o pacote `sshfs`, que será explicado mais adiante.

### CLI (alternativo)

###### Pasta Compartilhada
Para adicionar uma pasta compartilhada permanente e montar automaticamente:
```bash
VBoxManage sharedfolder add "nome da VM" --name "nome escolhido" --hostpath "/path/local --automount"
```
###### Inicializar
Caso não deseje utilizar a interface do VirtualBox, pode-se iniciar a máquina virtual pela linha de comando da máquina local:
```bash
vboxmanage startvm "nome da VM" --type headless
```
###### Salvar estado
Parar a máquina e salvar o estado atual no disco.
```bash
vboxmanage controlvm "nome da VM" savestate
```
###### Desligar
E desligar:
```bash
vboxmanage controlvm "nome da VM" acpipowerbutton
```

### Dentro da máquina virtual
Supondo o adaptador de rede host-only padrão *vboxnet0* com IP 192.168.56.1/24.
Caso não utilize o DHCP, para definir os parâmetros da interface como feito em aula:
```bash
sudo ifconfig eth0 192.168.56.2 netmask 255.255.255.0
```
Para ser permanente deve-se editar o arquivo `/etc/network/interfaces` e adicionar a configuração desejada para o adaptador, e.g.
> auto eth0
> iface eth0 inet static
> address 192.168.56.2
> netmask 255.255.255.0

## Acesso SSH

Com o IP configurado utlizamos o seguinte comando para acessar o shell da máquina virtual com IP 192.168.56.2:
```bash
ssh -Y mininet@192.168.56.2
```
Mas podemos facilitar o acesso ssh para a máquina virtual criando o arquivo `.ssh/config` no home da máquina local.
> Host mininet
> User mininet
> Hostname 192.168.56.2

E assim acessar com:
```bash
ssh -Y mininet
```
Pode-se também adicionar uma entrada em `/etc/hosts` definindo um nome para o IP da máquina virtual, e.g.
> 192.168.56.2  mininet

E substituir a utilização do IP pelo nome definido, exemplos:
```bash
ping mininet
ssh -Y mininet@mininet
```
###### Acesso sem senha
Outra facilidade é fazer o acesso sem a necessidade de digitar a senha todo login. Para isso deve-se seguir o seguinte procedimento na máquina local:
- Gerar chave de autenticação (caso não já tenha)
    ```bash
    ssh-keygen
    ```
    *Obs.:* Usar opções padrão para local do arquivo e senha vazia (só pressionar *enter*).
    
- Copiar chave pública para máquina virtual
    ```bash
    ssh-copy-id -i ~/.ssh/id_rsa.pub mininet@<IP> 
    ```

## Montar diretório da máquina virtual

Como mencionado antes é possível montar na máquina local um diretório da máquina virtual para facilitar o acesso e cópia de arquivos, mas para isso é necessário o pacote `sshfs`.
* Instale o pacote na sua máquina local. No caso do Ubuntu:
```bash
sudo apt install sshfs
```
* Para executar:
```bash
sshfs [user@]hostname:[directory] mountpoint
```
Com o *mountpoint* sendo um diretório possuído pelo usuário na máquina local, dessa forma, para montar o home da máquina virtual do mininet, no diretório local *mininet* no home:
```bash
sshfs mininet@<IP>:/home/mininet ~/mininet
```

Para desmontar o sistema de arquivos montado na máquina local:
```bash
fusermount -u ~/mininet
```

```python
def myNetwork():
  print 'sasd'
```

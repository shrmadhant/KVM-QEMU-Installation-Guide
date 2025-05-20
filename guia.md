# Guia de Instalação e configuração de máquinas virtuais com QEMU-KVM no Arch Linux


### **Preparando o sistema**

Primeiramente, vamos garantir que o sistema esteja atualizado. Rode:

	`sudo pacman -Syu`

Isso vai atualizar **todo o sistema** e sincronizar os pacotes com a base atual dos mirrors.

Agora iremos instalar o **stack completo de virtualização com suporte a KVM**, que inclui:

- `qemu`
    
- `libvirt`
    
- `virt-manager`
    
- `dnsmasq`, `bridge-utils` (dependendo da rede)
    
- e ativar os módulos `kvm` no kernel.

Instale o conjunto correto:

	`sudo pacman -S qemu-full virt-manager virt-viewer dnsmasq vde2 bridge-utils libvirt edk2-ovmf`

Se quiser um pacote mais leve, pode usar `qemu-desktop` no lugar de `qemu-full`.

### **Pós-instalação do KVM**

Verifique se seu processador suporta virtualização e se os módulos foram carregados:

	`egrep -c '(vmx|svm)' /proc/cpuinfo`

Se o resultado for `0`, a virtualização está desativada na BIOS/UEFI.







Verifique novamente:

	`egrep -c '(vmx|svm)' /proc/cpuinfo`

Se retornar algo diferente de `0`, podemos prosseguir. Carregue os módulos (Intel ou AMD):

Intel:
	`sudo modprobe kvm_intel`

AMD: 
	`sudo modprobe kvm_amd`

Ative e inicie o libvirtd:

	`sudo systemctl enable --now libvirtd`

	`sudo usermod -aG libvirt $(whoami)`

### **Configurando o aplicativo**

Crie um diretório para suas VMs, por exemplo.

	`mkdir -p ~/VMs/Windows`

Aqui eu estou criando na raiz do sistema, mas também poderia criar em lugares diferentes:

	`mkdir -p ~/home/VMs/Windows`

	`mkdir -p ~/home/seu_usuário/Documentos/VMs/Windows`

### **Instalação do sistema**



### **Ignorar os requisitos via registro  
Na tela de instalação do Windows onde aparece o erro:

Pressione `Shift + F10` para abrir o prompt.

Digite `regedit` e pressione Enter.

Navegue até:

	`HKEY_LOCAL_MACHINE\SYSTEM\Setup`

Crie uma nova chave chamada `LabConfig`

Dentro de `LabConfig`, crie os seguintes valores DWORD (32-bit):

    - `BypassTPMCheck` = `1`

    - `BypassSecureBootCheck` = `1`

    - `BypassRAMCheck` = `1`

    - `BypassCPUCheck` = `1`

Feche tudo e continue a instalação.

**Se quiser seguir com instalação básica offline:**

Desabilite a rede nas configurações.

Pressione `Shift + F10`

Digite:

	`OOBE\BYPASSNRO`


### **Ativando a rede `default`:**

Caso ainda não esteja ativa ou retorne erro:

	`sudo virsh net-start default`

**Garanta que ela sempre inicie automaticamente com o sistema:**

	`sudo virsh net-autostart default`

**Verifique se a rede está ativa com:**

	`virsh net-list --all`

Você deve ver `default` com status `active` e `autostart` como `yes`.

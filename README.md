<!-- =======================================================
  * Template Name: Antares-OS - v2.0
  * Author: José Valdemir de Melo
  ======================================================== -->  

<img src="assets/img/antares.png" class="img-fluid" alt="">

<img src="https://komarev.com/ghpvc/?username=valdemir26&color=yellow" alt="Profile views" /> [![NPM](https://img.shields.io/npm/l/react)](https://github.com/valdemir26/Customize/blob/main/LICENSE) [![iso](https://img.shields.io/badge/iso-images-cyan)](https://drive.google.com/file/d/10QLqtox-Px-aFXzscw4JQiMbipou1fMm/view?usp=drive_link)

[![Typing SVG](https://readme-typing-svg.herokuapp.com/?color=fff&size=35&center=true&vCenter=true&width=1000&lines=Como+customizar+o+seu+GNU/Linux;rsync;zenity;chroot;squashfs-tools;genisoimage)](https://git.io/typing-svg)

<p align="center">
   <img src="http://img.shields.io/static/v1?label=STATUS&message=EM%20DESENVOLVIMENTO&color=RED&style=for-the-badge" #vitrinedev/>
</p>

[![Ashutosh's github activity graph](https://github-readme-activity-graph.vercel.app/graph?username=valdemir26&bg_color=0d1117&color=fff&line=0563bb&point=272829&area=true&hide_border=true)](https://github.com/ashutosh00710/github-readme-activity-graph)

# <p align="center">_Como funciona_</p>
Escolhi utilizar o processo manual de customização do GNU/Linux, utilizando o rsync, zenity, squashfs-tools, chroot e genisoimage, é simples, você usa a própria distribuição instalada como base, sem aplicativos extras, que às vezes é incompatível dependendo da distribuição.
# <p align="center"> _Requisito_
Computador (Desktop ou Notebook) com Linux instalado e com suporte para o "squashfs" no kernel, desde 2006 o linux já possui suporte para o Squashfs no kernel, porém sugerimos utilizar a versão mais atual disponível.

Para melhor entendimento do conteúdo aqui descrito, você deve usar a ISO que está no link do tópico, para ser usada como requisito do conteúdo.
      
### Tópicos 

- [Live CD](#Live-CD)
- [Diretório principal](#Criar-diretório)
- [Copiar ISO](#Copiar-ISO)
- [Extarir a ISO](#Extarir-ISO)
- [Executando chroot](#Executando-chroot)
- [Adicionando repositório](#Adicionando-repositório)
- [Calamares](#Calamares)
- [Arquivos temporários](#Arquivos-temporários)
- [Desmontar OS filesystems](#Desmontar-filesystems)
- [Excluir arquivos desnecessários](#Excluir-arquivos)
- [Criar o filesystem.manifest](#Squashfs)
- [Criar o MD5sum](#MD5sum)
- [Criando a Imagem ISO](#Imagem-ISO)
- [Excluir diretório de customização](#Excluir-diretório)
- [ISO do Antares OS](https://drive.google.com/file/d/10QLqtox-Px-aFXzscw4JQiMbipou1fMm/view?usp=drive_link)
- [Desenvolvedor](#Desenvolvedor)
 
# Live CD
Pré-requisitos
_Agora, vamos criar um diretório para manipular os arquivos que utilizaremos durante todo processo._

 ### Pré-requesitos

_Para criar o ambiente para customização, necessitamos de:_
```bash
sudo apt install rsync zenity squashfs-tools genisoimage xorriso
```
Para virtualização
```bash
sudo apt install gnome-boxes
```
# Criar diretório 
Criar o diretório e os subdiretórios
```bash
mkdir Distro && cd Distro
mkdir antares
mkdir chroot
mkdir mnt
mkdir squashfs
```
Ativando o módulo do Kernel
```bash
sudo modprobe squashfs
```
# Copiar ISO
Copia a ISO para o ambiente de customização
```bash
ISO=$(zenity --file-selection --separator=" " --multiple --title "Escolha a ISO para copiar");
printf "%s\n" ${ISO}
[ -z "$ISO" ] && { zenity --error --title "Copiar" --text "O Processo foi cancelado pelo usuário" 2>/dev/null;exit;}
cp $ISO $HOME/Distro/
```
# Extarir ISO
Monta a ISO na pasta mnt
```bash
sudo mount -o loop *.iso mnt
```
Copiar os arquivos e sincroniza as pastas
```bash
rsync --exclude=/live/filessystem.squashfs -a mnt/ antares
```
Montar o sistema de arquivos na pasta squashfs
```bash
sudo mount -t squashfs -o loop mnt/live/filesystem.squashfs squashfs
```
Copiar os arquivos da pasta squashfs para a pasta chroot
```bash
sudo cp -a squashfs/* chroot/
```
Permissão na pasta onde vai ser criado os novos arquivos
```bash
sudo chmod -R 755 antares && sudo chmod -R 755 antares/
```
# Executando chroot
Configuração de ambiente para uso do chroot
Copia o arquivo /etc/resolv.conf para chroot/etc/
```bash
sudo cp /etc/resolv.conf chroot/etc/
```
Copia o arquivo /etc/hosts para chroot/etc/
```bash
sudo cp /etc/hosts chroot/etc/
```
Monta o /dev em chroot/dev
```bash
sudo mount --bind /dev chroot/dev
```
Monta o /proc em chroot/proc
```bash
sudo mount --bind /proc chroot/proc
```
Monta o /sys em chroot/sys
```bash
sudo mount --bind /sys chroot/sys
```
Agora vamos fazer chroot na pasta de customização
```bash
sudo chroot chroot
```
# Adicionando repositório
Aqui eu vou mostrar duas opçoẽs de edição do sources.list
- [ ]  Editor nano
```bash
sudo nano /etc/apt/sources.list
```
- [x] Com o comando cat
```bash
cat > /etc/apt/sources.list << 'EOF'
### Debian 12 bookworm
deb http://deb.debian.org/debian bookworm main non-free-firmware contrib non-free
deb-src http://deb.debian.org/debian bookworm main non-free-firmware contrib non-free

deb http://deb.debian.org/debian bookworm-updates main non-free-firmware contrib non-free
deb-src http://deb.debian.org/debian bookworm-updates main non-free-firmware contrib non-free

deb http://security.debian.org/debian-security/ bookworm-security main non-free-firmware contrib non-free
deb-src http://security.debian.org/debian-security/ bookworm-security main non-free-firmware contrib non-free

deb http://deb.debian.org/debian bookworm-backports main non-free-firmware
deb-src http://deb.debian.org/debian bookworm-backports main non-free-firmware
deb http://deb.debian.org/debian bookworm-proposed-updates main non-free-firmware contrib non-free
EOF
```
# Atualizar o sistema
Atualizar o sistema
```bash
apt update && apt dist-upgrade
```
Pacotes instalados por padrão no sistema:
```bash
apt-transport-https linux-image-amd64 live-boot live-config dbus-x11 dkms curl wget gnome-shell gnome-software \
gnome-session gnome-terminal nautilus network-manager-gnome mutter gdm3 xinit gnome-control-center xdg-user-dirs-gtk
```
Instalar pacotes extras
Exemplo de instalação manual de programas extras, configuração e remoção
```bash
cd home 
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb 
dpkg -i *.deb 
apt -f install 
rm -r *.deb
cd
cd Distro
```
# Calamares
Instalar o calamares
```bash
apt install calamares calamares-settings-debian
```
Instalar os drivers firmware-linux-nonfree
```bash
apt install amd64-microcode atmel-firmware bluez-firmware dahdi-firmware-nonfree firmware-amd-graphics firmware-ast firmware-ath9k-htc firmware-atheros firmware-bnx2 firmware-bnx2x firmware-brcm80211 firmware-cavium firmware-intel-sound firmware-ipw2x00 firmware-ivtv firmware-iwlwifi firmware-libertas firmware-linux firmware-linux-free firmware-linux-nonfree firmware-misc-nonfree firmware-myricom firmware-netronome firmware-netxen firmware-nvidia-tesla-gsp firmware-qcom-soc firmware-qlogic firmware-realtek firmware-realtek-rtl8723cs-bt firmware-samsung firmware-siano firmware-sof-signed firmware-ti-connectivity firmware-zd1211 intel-microcode hdmi2usb-fx2-firmware mesa-utils mesa-utils-bin:amd64 mesa-va-drivers:amd64 mesa-vdpau-drivers:amd64 mesa-vulkan-drivers:amd64
```
# Arquivos temporários
Removendo arquivos de configuração
Removendo arquivos temporários e finalizando o Chroot
_Os arquivos temporários e o cache do APT, deve ser apagados para diminuir a ISO imagem a ser gerada. Neste ponto nós desmontaremos os filesystems e finalizar o chroot, pois apenas as tarefas de customização são necessárias neste ambiente, as terefas de criação da ISO devem ser feitas fora do chroot. Para remover os arquivos temporários e os outros arquivos que não serão mais necessários._
Limpar o cache do APT
```bash
apt autoclean
```
Removendo os arquivos temporários
```bash
rm -rf /tmp/* ~/.bash.history
rm /etc/resolv.conf
rm /etc/hosts
```
# Finalizar chroot
```bash
edit
```
# Desmontar filesystems
Desmontar os filesystems de customização não nescessários e finalizar o chroot
```bash
sudo umount -lf chroot/dev
sudo umount -lf chroot/proc
sudo umount -lf chroot/sys
sudo umount -lf squashfs
sudo umount -lf mnt
```
# Excluir arquivos
```bash
sudo rm mnt && sudo rm squashfs
sudo rm chroot/vmlinuz && sudo rm chroot/vmlinuz.old
sudo rm chroot/initrd.img && sudo rm chroot/initrd.img.old
```
# Squashfs
Regerando os arquivos, o filesystem.manifest e filesystem.squashfs
```bash
chmod +w antares/live/filesystem.manifest
sudo chroot chroot dpkg-query -f '${binary:Package} ${Version}\n' -W > antares/live/filesystem.manifest
sudo cp antares/live/filesystem.manifest antares/live/filesystem.manifest
sudo rm antares/live/filesystem.squashfs
sudo mksquashfs chroot antares/live/filesystem.squashfs -comp xz
```
# MD5sum
Criar o MD5sum
```bash
cd antares
sudo rm md5sum.txt
find -type f -print0 | xargs -0 md5sum | grep -v isolinux/boot.cat | tee md5sum.txt
cd
cd Distro
```
# Imagem ISO
Criando a imagem ISO com genisoimage
```bash
genisoimage \
-D -r -V “Antares-OS” -cache-inodes -J -l -b isolinux/isolinux.bin -c isolinux/boot.cat \
-no-emul-boot -boot-load-size 4 -boot-info-table -o Antares-OS-amd64-$(date +%d-%m-%Y).iso antares/
```
Criando a imagem ISO com xorriso
```bash
xorriso \
-as mkisofs -iso-level 3 -c isolinux/boot.cat -b isolinux/isolinux.bin -no-emul-boot \
-boot-load-size 4 -boot-info-table -o Antares-OS-amd64-$(date +%d-%m-%Y).iso antares/
```
Excluir o diretório e subdiretório não vazio
_Para excluir o diretório e os subdiretórios que não estejam vazios, é necessário desmontar o ponto de montagem, use a opção -r (recursiva). Para ser claro, isso remove os diretórios e todos os arquivos e subdiretórios contidos neles:_

# Excluir diretório
Excluir diretório de customização
```bash
sudo rm -r Distro
```
# Desenvolvedor
  <footer id="footer">
    <div class="container">      
      <h5 class="font-italic">
      <div>Remasterização de imagens ISOs customizada do GNU/Linux</div>
      </h5>
      <div class="social-links">
        <a href="https://github.com/valdemir26/Customize#readme" class="telegram"><i class="bx bxl-github"></i></a>
        <a href="https://t.me/jvmelo26linux" class="telegram"><i class="bx bxl-telegram"></i></a>
        <a href="https://twitter.com/jvmelo26?s=09" class="twitter"><i class="bx bxl-twitter"></i></a>
        <a href="https://www.facebook.com/josevaldemir.melo" class="facebook"><i class="bx bxl-facebook"></i></a>
        <a href="https://www.instagram.com/josevaldemir.melo/" class="instagram"><i class="bx bxl-instagram"></i></a>   
      </div>
      <div class="copyright">
        &copy; Copyright <strong><span>Antares OS</span></strong>. 2018 2023 All Rights Reserved
      </div>
      <div class="credits">
      Sobre mim <a href="https://www.facebook.com/josevaldemir.melo">José Valdemir de Melo</a>
      </div>
    </div>
  </footer>


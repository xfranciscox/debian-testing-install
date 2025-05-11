# debian-testing-install
A how-to-debian installation guide from the testing branch. [ES - EN]

> [ES]
**Resumen de la Guía de Instalación Personalizada de Debian Testing**

Esta guía describe los pasos para crear una unidad USB de instalación personalizada de Debian testing con el entorno de escritorio XFCE4, la última versión estable de Mozilla Firefox desde el repositorio oficial de Mozilla y el gestor de inicio LightDM con su herramienta de configuración.

1.  **Descargar la Imagen ISO Mínima de Debian Testing:** Obtén la imagen ISO "netinst" para tu arquitectura desde la página oficial de descargas de Debian.
```link
https://cdimage.debian.org/cdimage/daily-builds/daily/arch-latest/amd64/iso-cd/
```
2.  **Crear el USB de Arranque:** Utiliza la herramienta dd en Linux para crear la unidad USB booteable. Reemplaza /dev/sdX con el identificador correcto de tu unidad USB:
```bash
sudo dd if=debian-testing-TU_ARQUITECTURA-netinst.iso of=/dev/sdX bs=4M status=progress
sync
```
¡Precaución! Asegúrate de identificar correctamente tu unidad USB para evitar la pérdida de datos. Puedes usar herramientas como `lsblk` para ayudarte.

3.  **Iniciar la Instalación de Debian:** Arranca tu computadora desde la unidad USB y sigue los pasos de la instalación del sistema base. Configura el idioma, la ubicación, el teclado, el nombre de la máquina, la contraseña de root y la cuenta de usuario.

4.  **Red y Particionado de Discos:** Configura tu conexión de red y particiona tu disco duro según tus necesidades.

5.  **Selección de Software:** Durante el paso de "Selección de software", **desmarca** "Entorno de escritorio Debian" y cualquier otro entorno de escritorio, así como servidores u otras utilidades. Mantén seleccionada la opción de "Utilidades estándar del sistema".

6.  **Configurar los Repositorios Base:** Después de la instalación base y el reinicio, inicia sesión como root y edita el archivo `/etc/apt/sources.list`. Asegúrate de que incluya las siguientes líneas (usando `testing`):

    ```
    deb http://deb.debian.org/debian/ testing main contrib non-free
    deb-src http://deb.debian.org/debian/ testing main contrib non-free

    deb http://security.debian.org/debian-security testing-security main contrib non-free
    deb-src http://security.debian.org/debian-security testing-security main contrib non-free
    ```

7.  **Actualizar la Lista de Paquetes:** Ejecuta `sudo apt update` para actualizar las listas de paquetes.

8.  **Instalar XFCE4 y Herramientas Gráficas:** Instala el entorno de escritorio XFCE4 y algunas herramientas gráficas útiles:
    ```bash
    sudo apt install xfce4 xfce4-goodies synaptic gnome-disk-utility network-manager-gnome
    ```

9.  **Instalar LightDM y su Herramienta de Configuración:** Instala el gestor de inicio LightDM y su configuración GTK+:
    ```bash
    sudo apt install lightdm lightdm-gtk-greeter-settings
    ```
    Selecciona `lightdm` cuando se te pregunte por el gestor de inicio.

10. **Instalar la Última Versión Estable de Firefox desde Mozilla:**

    Crea un directorio para almacenar las claves del repositorio APT si no existe:
```bash
sudo install -d -m 0755 /etc/apt/keyrings
```

Importa la clave de firma del repositorio APT de Mozilla:
```bash
wget -q https://packages.mozilla.org/apt/repo-signing-key.gpg -O- | sudo tee /etc/apt/keyrings/packages.mozilla.org.asc > /dev/null
```
Si no tienes `wget` instalado, puedes instalarlo con:
```bash
sudo apt-get install wget
```
La huella digital debería ser `35BAA0B33E9EB396F59CA838C0BA5CE6DC6315A3`. Puedes verificarla con el siguiente comando:
```bash
gpg -n -q --import --import-options import-show /etc/apt/keyrings/packages.mozilla.org.asc | awk '/pub/{getline; gsub(/^ +| +$/,""); if($0 == "35BAA0B33E9EB396F59CA838C0BA5CE6DC6315A3") print "\nLa huella digital de la clave coincide ("$0").\n"; else print "\nVerificación fallida: la huella digital ("$0") no coincide con la esperada.\n"}'
```
A continuación, añade el repositorio APT de Mozilla a tu lista de fuentes:
```bash
echo "deb [signed-by=/etc/apt/keyrings/packages.mozilla.org.asc] https://packages.mozilla.org/apt mozilla main" | sudo tee -a /etc/apt/sources.list.d/mozilla.list > /dev/null
```
Configura APT para dar prioridad a los paquetes del repositorio de Mozilla:
```bash
echo '
Package: *
Pin: origin packages.mozilla.org
Pin-Priority: 1000
' | sudo tee /etc/apt/preferences.d/mozilla
```
Actualiza tu lista de paquetes e instala firefox (o uno de firefox-esr, -beta, -nightly, -devedition):
```bash
sudo apt-get update && sudo apt-get install firefox
```

11. **Configurar el Sistema:** Reinicia tu sistema. Ahora deberías tener el entorno de escritorio XFCE4 con LightDM. Utiliza `lightdm-gtk-greeter-settings` para personalizar la pantalla de inicio de sesión.

**Consideraciones Importantes:**

* Debian testing podría ser menos estable que las versiones estables.
* Agregar repositorios de terceros (como el PPA de Mozilla) puede introducir inestabilidad y requiere una gestión cuidadosa de las actualizaciones.
* Los pasos de instalación de Mozilla Firefox utilizan un repositorio específico de una versión de Ubuntu y **no** se pueden reemplazar con "testing".
* Ten precaución al gestionar paquetes de diferentes fuentes.

Esta guía resumida debería ayudarte a crear tu USB de instalación personalizado de Debian testing. Recuerda proceder con precaución, especialmente con el repositorio de terceros.

<hr>

> [EN]
**Summary of the Debian Testing Custom Installation Guide (English)**

This guide outlines the steps to create a custom Debian testing installation USB drive with the XFCE4 desktop environment, the latest stable version of Mozilla Firefox from Mozilla's official repository, and the LightDM display manager with its configuration tool.

1.  **Download the Minimal Debian Testing ISO:** Obtain the "netinst" ISO image for your architecture from the official Debian downloads page.
```link
https://cdimage.debian.org/cdimage/daily-builds/daily/arch-latest/amd64/iso-cd/
```
2.  **Create the Bootable USB:** Use a tool like `dd` (on Linux) to write the ISO image to your USB drive. **Be careful to identify the correct USB device.**
```bash

sudo dd if=debian-testing-TU_ARQUITECTURA-netinst.iso of=/dev/sdX bs=4M status=progress
sync
```

3.  **Start the Debian Installation:** Boot your computer from the USB drive and proceed with the base system installation. Configure language, location, keyboard, hostname, root password, and user account.

4.  **Network and Disk Partitioning:** Configure your network connection and partition your hard drive as needed.

5.  **Software Selection:** During the "Software selection" step, **deselect** "Debian desktop environment" and any other desktop environments, as well as servers or other utilities. Keep "Standard system utilities" selected.

6.  **Configure Base Repositories:** After the base installation and reboot, log in as root and edit the `/etc/apt/sources.list` file. Ensure it includes the following lines (using `testing`):

    ```
    deb http://deb.debian.org/debian/ testing main contrib non-free
    deb-src http://deb.debian.org/debian/ testing main contrib non-free

    deb http://security.debian.org/debian-security testing-security main contrib non-free
    deb-src http://security.debian.org/debian-security testing-security main contrib non-free
    ```

7.  **Update Package List:** Run `sudo apt update` to refresh the package lists.

8.  **Install XFCE4 and Graphical Tools:** Install the XFCE4 desktop environment and some useful graphical tools:
    ```bash
    sudo apt install xfce4 xfce4-goodies synaptic gnome-disk-utility network-manager-gnome
    ```

9.  **Install LightDM and Configuration Tool:** Install the LightDM display manager and its GTK+ greeter settings:
    ```bash
    sudo apt install lightdm lightdm-gtk-greeter-settings
    ```
    Select `lightdm` when prompted for the display manager.

10. **Install Latest Stable Firefox from Mozilla:**

Create a directory to store APT repository keys if it doesn't exist:
```bash
sudo install -d -m 0755 /etc/apt/keyrings
```

Import the Mozilla APT repository signing key:
```bash
wget -q https://packages.mozilla.org/apt/repo-signing-key.gpg -O- | sudo tee /etc/apt/keyrings/packages.mozilla.org.asc > /dev/null
```
If you do not have `wget` installed, you can install it with:
```bash
sudo apt-get install wget
```
The fingerprint should be `35BAA0B33E9EB396F59CA838C0BA5CE6DC6315A3`. You may check it with the following command:
```bash
gpg -n -q --import --import-options import-show /etc/apt/keyrings/packages.mozilla.org.asc | awk '/pub/{getline; gsub(/^ +| +$/,""); if($0 == "35BAA0B33E9EB396F59CA838C0BA5CE6DC6315A3") print "\nThe key fingerprint matches ("$0").\n"; else print "\nVerification failed: the fingerprint ("$0") does not match the expected one.\n"}'
```
Next, add the Mozilla APT repository to your sources list:
```bash
echo "deb [signed-by=/etc/apt/keyrings/packages.mozilla.org.asc] https://packages.mozilla.org/apt mozilla main" | sudo tee -a /etc/apt/sources.list.d/mozilla.list > /dev/null
```
Configure APT to prioritize packages from the Mozilla repository:
```bash
echo '
Package: *
Pin: origin packages.mozilla.org
Pin-Priority: 1000
' | sudo tee /etc/apt/preferences.d/mozilla
```
Update your package list, and install firefox (or one of firefox-esr, -beta, -nightly, -devedition):
```bash
sudo apt-get update && sudo apt-get install firefox
```

11. **Configure System:** Reboot your system. You should now have the XFCE4 desktop environment with LightDM. Use `lightdm-gtk-greeter-settings` to customize the login screen.

**Important Considerations:**

* Debian testing might be less stable than stable releases.
* Adding third-party repositories (like Mozilla's PPA) can introduce instability and requires careful management of updates.
* The Mozilla Firefox installation steps use a specific Ubuntu release repository and **cannot** be replaced with "testing".
* Be cautious when managing packages from different sources.

This summarized guide should help you create your custom Debian testing USB installation. Remember to proceed with caution, especially with the third-party repository.

# Install Donkeycar on Linux

![donkey](https://docs.donkeycar.com/assets/logos/linux_logo.png)

> Nota : testado em Ubuntu 18.04 LTS  e Ubuntu 20.04 LTS



PC Host (NOTEBOOK)

- Abra um novo terminal.
- Instale [o miniconda Python 3.7 64 bits](https://conda.io/miniconda.html) .

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash ./Miniconda3-latest-Linux-x86_64.sh
```

Após a instalação feche o terminal e abra outro para carregar o ambiente base.

- Crie um diretório para trabalhar nos projetos.

```bash
mkdir projects
cd projects
```

- Clone o repositório mais recente do donkeycar do Github.

```bash
git clone https://github.com/autorope/donkeycar
cd donkeycar
git checkout master
```

- Se esta não for sua primeira instalação, atualize o Conda e remova os antigos pacotes.

```bash
conda update -n base -c defaults conda
conda env remove -n donkey
```

- Crie o ambiente Python anaconda (Este procedimento é demorado e pode travar, Siga o método do proximo passo !)

```bash
conda env create -f install/envs/ubuntu.yml
conda activate donkey
pip install -e .[pc]
```

Observamos que a instalação via conda pode ser muito lenta. Se a instalação parecer que está travando, você poderá instalar com `mamba`da seguinte forma :

```bash
conda install mamba -n base -c conda-forge
mamba env create -f install/envs/ubuntu.yml
conda activate donkey
pip install -e .[pc]
```



<H3> A seção abaixo deve ser seguida apenas por usuários com GPU  

==========================================================================

Verifique se os drivers da GPU NVidia estão atualizados. O Conda cuidará da instalação das bibliotecas cuda e cuddn corretas para a versão do tensorflow que você está usando.

```bash
conda install tensorflow-gpu==2.2.0
```

- Instale o compilador tpu Coral edge opcional

Se você tiver uma tpu de borda Google Coral, talvez queira compilar modelos. Você precisará instalar o executável edgetpu_compiler. Siga [suas instruções](https://coral.withgoogle.com/docs/edgetpu/compiler/) .

- Opcionalmente, configure o PyTorch para usar GPU - apenas para placas gráficas NVidia

Se você tiver uma placa NVidia, você deve atualizar para os drivers mais recentes e [instalar o Cuda SDK](https://www.tensorflow.org/install/gpu#windows_setup) .

```bash
conda install cudatoolkit=<CUDA Version> -c pytorch
```

Você deve substituir `<CUDA Version>`pela sua versão CUDA. Qualquer versão acima de 10.0 deve funcionar. Você pode descobrir sua versão do CUDA executando `nvcc --version`ou `nvidia-smi`. (se esses comandos não funcionarem, significa que você ainda não os instalou. Siga as instruções fornecidas por esse erro para instalá-los.) Se a versão fornecida por esses dois comandos não corresponder, vá com a versão fornecida por `nvidia-smi`.

==========================================================================

- Crie seu diretório de trabalho local:

```bash
donkey createcar --path ~/mycar
```

> Nota: Depois de fechar o Prompt do Anaconda, ao abri-lo novamente, você precisará digitar `conda activate donkey`para reativar os mapeamentos para bibliotecas Python específicas do donkey.



Raspberry [Embarcado]

## Passo 1: Preparar Cartão SD com imagem Linux Raspbian

- Para isso utilizar software raspberry pi imager que pode ser encontrado no seguinte link:
  https://www.raspberrypi.com/software/

  Deverá ser utilizado a versão Buster armhf lite.

## Passo 2: Conectar em uma rede WiFi:

 Utilize a ferramenta rapi config para acessar a guia de configurações 

```bash
sudo rapi-config
```

## Passo 3: Conectar no donkey via SSH

```bash
ssh pi@<IP>
```

o ip poderá ser encontrado com o comando hostname -I

## Passo 4: Atualiazar plataforma

```bash
sudo apt-get update --allow-releaseinfo-change
sudo apt-get upgrade
```

## Passo 5: Configuração do raspberry

```bash
sudo raspi-config
```

- alterar a senha padrão para pi
- alterar nome de host
- habilitar `Interfacing Options`-`I2C`
- habilitar `Interfacing Options`-`Camera`
- selecione `Advanced Options`- `Expand Filesystem`para que você possa usar todo o armazenamento do cartão SD

Escolha `<Finish>`e aperte enter.

> Nota: Reinicie depois de alterar essas configurações. Deve acontecer se você selecionar `yes`.

## Passo 6: Instalar Dependências

```bash
sudo apt-get install build-essential python3 python3-dev python3-pip python3-virtualenv python3-numpy python3-picamera python3-pandas python3-rpi.gpio i2c-tools avahi-utils joystick libopenjp2-7-dev libtiff5-dev gfortran libatlas-base-dev libopenblas-dev libhdf5-serial-dev libgeos-dev git ntp
```

## Passo 7: (opcional) instalar dependências do OpenCV

Se você está indo para uma instalação mínima, você pode passar sem eles. Mas pode ser útil ter o OpenCV.

```bash
sudo apt-get install libilmbase-dev libopenexr-dev libgstreamer1.0-dev libjasper-dev libwebp-dev libatlas-base-dev libavcodec-dev libavformat-dev libswscale-dev libqtgui4 libqt4-test
```

## Etapa 8: configurar o ambiente virtual

Isso precisa ser feito apenas uma vez:

```bash
python3 -m virtualenv -p python3 env --system-site-packages
echo "source env/bin/activate" >> ~/.bashrc
source ~/.bashrc
```

Com esses comandos você irá modificar o seu `.bashrc`dessa forma habilitará automaticamente este ambiente toda vez que você fizer login. Para retornar ao python do sistema, você pode digitar `deactivate`.

## Etapa 9: Instale o código Python do Donkeycar

- Crie diretório para trabalhar nos projetos.

```bash
mkdir projects
cd projects
```

- Clone o repositório mais recente do donkey no Github.

```bash
git clone https://github.com/autorope/donkeycar
cd donkeycar
git checkout master
pip install -e .[pi]
pip install numpy --upgrade
#Instale a versão mais atualizada do donkey :
cd ~/projects/
pip install -e donkeycar
# Install TensorFlow
sudo apt-get update
sudo apt-get upgrade
# remove old versions, if not placed in a virtual environment (let pip search for them)
sudo pip uninstall tensorflow
sudo pip3 uninstall tensorflow
# Install the dependencies (if not already onboard)
sudo apt-get install gfortran libhdf5-dev libc-ares-dev libeigen3-dev libatlas-base-dev libopenblas-dev libblas-dev openmpi-bin libopenmpi-dev liblapack-dev cython
sudo pip3 install keras_applications==1.0.8 --no-deps
sudo pip3 install keras_preprocessing==1.1.0 --no-deps
sudo pip3 install -U --user six wheel mock
sudo -H pip3 install pybind11
sudo -H pip3 install h5py==2.10.0
# upgrade setuptools 40.8.0 -> 52.0.0
sudo -H pip3 install --upgrade setuptools
# download the wheel
wget https://github.com/Qengineering/Tensorflow-Raspberry-Pi/raw/master/tensorflow-2.1.0-cp37-cp37m-linux_armv7l.whl
# install TensorFlow
sudo -H pip3 install tensorflow-2.1.0-cp37-cp37m-linux_armv7l.whl
# and complete the installation by rebooting
sudo reboot
```

Você pode validar sua instalação do tensorflow com

```bash
python -c "import tensorflow"
```

Referência tensorflow instalação

https://qengineering.eu/install-tensorflow-2.1.0-on-raspberry-pi-4.html

## Passo 10: Instale o OpenCV

Se você optou por instalar as dependências do OpenCV anteriormente, você pode instalar as ligações do Python OpenCV agora com o comando:

```bash
sudo apt install python3-opencv
```

Se isso falhou, você pode tentar pip:

```bash
pip install opencv-python
```

Em seguida, teste para ver se a importação foi bem-sucedida.

```bash
python -c "import cv2"
```

E se não houver erros, você tem o OpenCV instalado!

## Passo 11: Arquivos de controle

Instale o pacote do donkeycar e crie a pasta com os arquivos de configuração do veículo.

```bash
pip install donkeycar
donkey createcar ~/mycar
```

Se estiver utilizando um PCA9685 i2c poderá verificar com o seguinte comando: 

```bash
sudo i2cdetect -y 1
#Caso não esteja instalado o pacote i2c, instale-o e tente novamente.
sudo apt install -y i2c-tools
```

Isso deve mostrar uma grade de endereços como:

```text
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- -- --
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
40: 40 -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
70: 70 -- -- -- -- -- -- --
```

Neste caso, o 40 aparece como o endereço da nossa placa PCA9685. Se isso não aparecer, verifique a fiação da placa. Em um pi, certifique-se de que o I2C esteja ativado no menu de `sudo raspi-config`(observe, ele sugere a reinicialização).

Se você atribuiu um endereço não padrão à sua placa, ajuste o endereço na `myconfig.py`variável under `PCA9685_I2C_ADDR`. Se sua placa estiver em outro barramento, você poderá especificar isso com o `PCA9685_I2C_BUSNUM`.

## Passo 12: Calibração

O objetivo de calibrar seu carro é fazê-lo dirigir de forma consistente.

## Como ajustar as configurações do seu carro

> Você precisará ssh em seu Pi para fazer a calibração.

Todas as configurações padrão do carro estão no arquivo `config.py`. Você pode substituir as configurações padrão editando o `myconfig.py`script no diretório do carro. Isso foi gerado quando você executou o `donkey createcar --path ~/mycar`comando. Você pode editar este arquivo em seu carro executando:

```bash
nano ~/mycar/myconfig.py
```

## Calibração de direção

> **Certifique-se de que seu carro está fora do chão para evitar uma situação de fuga.**

1. Ligue seu carro.
2. Encontre o cabo do servo em seu carro e veja qual canal está conectado à placa PCA. Deve ser 1 ou 0.
3. Corre`donkey calibrate --channel <your_steering_channel> --bus=1`
4. Primeiro **, encontre o valor que gira os pneus até o** extremo esquerdo. Ao calibrar a direção você quer **escolher o valor que apenas gira as rodas ao máximo** ; as rodas devem girar completamente, mas **o servo \*NÃO\* deve fazer barulho** . Experimente o valor `360`e você deverá ver as rodas do seu carro se moverem levemente. Se não tentar `400`ou `300`. Em seguida, insira valores +/- 10 do seu valor inicial para encontrar a configuração de PWM que faz seu carro virar totalmente para a esquerda, novamente certificando-se de que o motor não está fazendo um som de ganido. Lembre-se deste valor.
5. Em seguida **, encontre o valor que gira os pneus até o** extremo direito. Insira valores +/- 10 do seu valor inicial para encontrar a configuração de PWM que faz seu carro virar totalmente para a direita, novamente certificando-se de que o motor não está fazendo um som de ganido. Lembre-se deste valor.

**Edite o `myconfig.py`script em seu carro** e insira esses valores como `STEERING_LEFT_PWM`e `STEERING_RIGHT_PWM`respectivamente.

- `STEERING_LEFT_PWM` = PWM para curva completa à esquerda
- `STEERING_RIGHT_PWM`= valor PWM para curva completa à direita



Para utilizar uma camera USB 

```bash
#Instalar dependencias
sudo apt-get install git curl libsdl2-mixer-2.0-0 libsdl2-image-2.0-0 libsdl2-2.0-0
python3 -m pip install -U pygame --user
#Após instalação reiniciar raspberry 
```



===========================================================================================

Treino 

- apagar images e catalogs
- rodar o treino (manage.py drive) *sem desconectar
	- 20000/20000 frames é o ideal
- cria uma cópia do data
- cria um arquivo zipado do data (cópia)
- transfere a pasta zipada para pc
- entra na pasta do mycar (pc)
- extrair pasta zipada dentro do mycar (pc)
- passar comando (terminal do pc (comando no discord))
	- momento de treino
	- treino é demorado, ele mostra quando finaliza
- transferir mypilot.h5 para raspberry (dentro do models do rasp)
- executar python manage.py drive --model ~/mycar/models/mypilot.h5 (rasp)	

*NUNCA apagar manifest.json*
*Quando criar cópia, criar com o número da versão*

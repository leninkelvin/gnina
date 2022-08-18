![GitHub all releases](https://img.shields.io/github/downloads/leninkelvin/gnina/total?style=for-the-badge&logo=appveyor)
![GitHub forks](https://img.shields.io/github/forks/leninkelvin/gnina?style=for-the-badge&logo=appveyor)
![GitHub Repo stars](https://img.shields.io/github/stars/leninkelvin/gnina?style=for-the-badge&logo=appveyor)

# gnina
### GNINA para todos
**Nota: parte de este material se ha traducido y adaptado del repositorio [original](https://github.com/gnina/gnina)**

gnina (pronunciado NEE-na) es un programa de docking molecular con soporte de scoring así como la optimización de ligandos usando [redes neurales convolucionales](https://es.wikipedia.org/wiki/Red_neuronal_convolucional). Es un fork de [smina](https://github.com/mwojcikowski/smina), el cual es a su vez, un fork de [AutoDock Vina](https://github.com/ccsb-scripps/AutoDock-Vina).

Las referencias a los artículos de gnina estan al final.

### Instalación
**Se requiere un GPU de NVIDIA**
**Aunque gnina puede correr en CPU, los verdaderos beneficios solo se apreciaran con GPUs. Y, se requieren al menos 4 GBs de RAM en el GPU. Hay opciones avanzadas que no son accesibles a menos de 15 GBs de RAM en el GPU**

Los autores originales recomiendan que se gnina se compiles desde el código fuente. Para esta guía usaré [docker](https://docs.docker.com/get-started/), específicamente el [docker](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html) de NVIDIA. Para más información, visiten [gnina](https://github.com/gnina/gnina).

### Ubuntu 20.04
Asegurense de tener estas librerias al día.

```
apt-get install build-essential cmake git wget libboost-all-dev libeigen3-dev libgoogle-glog-dev libprotobuf-dev protobuf-compiler libhdf5-dev libatlas-base-dev python3-dev librdkit-dev python3-numpy python3-pip python3-pytest
```

[Sigan las instrucciones de NVIDIA para instalar CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/#axzz4TWipdwX1) se requiere la versión >= 10.0. 
Aca hay un video en [mi canal de youtube](https://youtu.be/Ip3M3Moc_-8) hay un video de como instalar los drivers de NVIDIA y CUDA.
**Asegurense que `nvcc` este en el PATH.**

### Windows 11

Instale el subsistema de Ubuntu mediante la tiena de Microsoft y sigan las intrucciones de arriba para Ubuntu.

Docker
======

La imagen para docker esta [aquí](https://hub.docker.com/u/gnina).

Para usar el docker de NVIDIA necesita usar este comando para activar el repositorio correcto y obtener la llave GPG:

```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
      && curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
      && curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list | \
            sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
            sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```
Actualiza tus paquetes
```
sudo apt-get update
```
e instala el docker
```
sudo apt-get install -y nvidia-docker2
```
No olviden reiniciar el daemon
```
sudo systemctl restart docker
```
y darle los permisos adecuados a docker
```
sudo usermod -a -G docker $USER
newgrp docker
```
Si todo funcionó, debería ver un texto como el que sigue pero con su GPU.

```
Fri Jul 29 02:19:44 2022
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 470.129.06   Driver Version: 470.129.06   CUDA Version: 11.4     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  Quadro K600         Off  | 00000000:01:00.0 Off |                  N/A |
| 25%   45C    P8    N/A /  N/A |     52MiB /   981MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
+-----------------------------------------------------------------------------+
```

Instalen la imagen de docker
```
docker pull gnina/gnina
```
Con este repositorio se incluyen dos archivos dentro de un folder llamado files: rec.pdb y lig.pdb. Bajenlos, entren a ese folder en la terminal y ejecuten el comando 
```
nvidia-docker run -it -v $(pwd)/:$(pwd)/ docker.io/gnina/gnina
```
Este comando te va a dejar en un folder que contiene

```
cmake-3.18.6-Linux-x86_64  cmake-3.18.6-Linux-x86_64.tar.gz  gnina  openbabel
```
pero te dejara acceder al folder donde ejecutaste el comando. Si lo ejecutas en /home/usuario/Documents (donde usuario se cambia por tu nombre de usuario) tendras acceso a ese folder. Ejecuta 
```
cd /home/admin/Documents/files/
```
**No olvides cambiar admin por tu nombre de usuario**

Para ejecutar el docking
```
gnina -r rec.pdb -l lig.pdb --autobox_ligand lig.pdb -o docked.sdf --seed 0
```
Los resultados apareceran en segundos. Con el comando
```
obrms --firstonly lig.pdb  docked.sdf
```
podrán extraer los resultados y visualizarlos a su gusto. 

**Listo, si llegaron hasta aquí ya empezaron a dominar este proceso. Consulten la página original y extiendan sus habilidades.**

Citation
========
If you find gnina useful, please cite our paper(s):  

**GNINA 1.0: Molecular docking with deep learning** (Primary application citation)  
A McNutt, P Francoeur, R Aggarwal, T Masuda, R Meli, M Ragoza, J Sunseri, DR Koes. *J. Cheminformatics*, 2021  
[link](https://jcheminf.biomedcentral.com/articles/10.1186/s13321-021-00522-2) [PubMed](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC8191141/) [ChemRxiv](https://chemrxiv.org/articles/preprint/GNINA_1_0_Molecular_Docking_with_Deep_Learning/13578140)

**Protein–Ligand Scoring with Convolutional Neural Networks**  (Primary methods citation)  
M Ragoza, J Hochuli, E Idrobo, J Sunseri, DR Koes. *J. Chem. Inf. Model*, 2017  
[link](http://pubs.acs.org/doi/full/10.1021/acs.jcim.6b00740) [PubMed](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5479431/) [arXiv](https://arxiv.org/abs/1612.02751)  

**Ligand pose optimization with atomic grid-based convolutional neural networks**  
M Ragoza, L Turner, DR Koes. *Machine Learning for Molecules and Materials NIPS 2017 Workshop*, 2017  
[arXiv](https://arxiv.org/abs/1710.07400)  

**Visualizing convolutional neural network protein-ligand scoring**  
J Hochuli, A Helbling, T Skaist, M Ragoza, DR Koes.  *Journal of Molecular Graphics and Modelling*, 2018  
[link](https://www.sciencedirect.com/science/article/pii/S1093326318301670) [PubMed](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6343664/) [arXiv](https://arxiv.org/abs/1803.02398)

**Convolutional neural network scoring and minimization in the D3R 2017 community challenge**  
J Sunseri, JE King, PG Francoeur, DR Koes.  *Journal of computer-aided molecular design*, 2018  
[link](https://link.springer.com/article/10.1007/s10822-018-0133-y) [PubMed](https://www.ncbi.nlm.nih.gov/pubmed/29992528)

**Three-Dimensional Convolutional Neural Networks and a Cross-Docked Data Set for Structure-Based Drug Design**  
PG Francoeur, T Masuda, J Sunseri, A Jia, RB Iovanisci, I Snyder, DR Koes. *J. Chem. Inf. Model*, 2020  
[link](https://pubs.acs.org/doi/abs/10.1021/acs.jcim.0c00411) [PubMed](https://pubmed.ncbi.nlm.nih.gov/32865404/) [Chemrxiv](https://chemrxiv.org/articles/preprint/3D_Convolutional_Neural_Networks_and_a_CrossDocked_Dataset_for_Structure-Based_Drug_Design/11833323/1)

**Virtual Screening with Gnina 1.0**
J Sunseri, DR Koes D. *Molecules*, 2021
[link](https://www.mdpi.com/1420-3049/26/23/7369) [Preprints](https://www.preprints.org/manuscript/202111.0329/v1)


# AP02 – Implementação de Sistemas em Containers RunC, LXC, Docker e Podman

## Objetivo
Implementar sistemas em containers utilizando diferentes tecnologias: **RunC**, **LXC**, **Docker** e **Podman**, além de versionar o projeto utilizando **Git** e hospedar no **Codeberg**.

---

## Materiais utilizados
- **Sistema Operacional**: 
- **Hardware**: 
- **Softwares**: RunC, LXC, Docker, Podman, Git

---


## Parte 0 – Git / Codeberg
1. Criar repositório no [Codeberg](https://codeberg.org/)
2. Vai em criar um repositório novo e segue o passo a passo parecido com oq tem abaixo
3. vai ser parecido com esse:
   ```bash
   touch README.md
   git init
   git switch -c main
   git add README.md
   git commit -m "first commit"
   git remote add origin https://codeberg.org/pedromujica1/novo.git
   git push -u origin main
   ```
Depois faz o git clone no seu wsl
   ```bash
   #vai em 
   git clone https://codeberg.org/pedromujica1/novo.git
   ```

Tu tem que faz um commit a cada tecnologia usada pq ele pede no relatorio, para fazer o commit segue esses comandos:

```bash
   git add .
   git commit -m "seu_commit-mensagem"
   git config --global user.name "Fulano de Tal"
   git config --global user.email fulanodetal@exemplo.br
   git push
   ```

## Parte I – RunC
O **RunC** é o runtime padrão para containers no formato OCI, utilizado internamente por ferramentas como Docker e Podman.

### Passos realizados
1. Instalar o RunC (`sudo apt install runc`)
2. Criar diretórios e gerar o `config.json`:
   ```bash
   mkdir /runC
   cd runC
   mkdir rootfs
   runc spec
   ```
3. Exportar a imagem Busybox (Linux leve) para o `rootfs` contendo sistema de arquivos minímo:
   ```bash
   docker export $(docker create busybox) | tar -C rootfs -xvf -
   ```
4. Explorar o `config.json` (namespaces e capabilities)
   ```bash
   cat config.json
   ```
5. Executar o container:
   ```bash
   sudo runc run cg1
   ```

---

## Parte II – LXC
O **LXC** é uma tecnologia de virtualização a nível de sistema operacional que permite criar e gerenciar múltiplos containers.

### Passos realizados
1. Instalar o LXC (`sudo apt install lxc`)
2. Criar containers BusyBox e Debian:
   ```bash
   sudo lxc-create -n c1 -t busybox
   sudo lxc-create -n c2 -t debian
   ```
   Caso a imagem não esteja disponível:
   ```bash
   sudo lxc-create -n c2 -t download -- --dist alpine --release 3.19 --arch amd64
   ```
3. Executar containers:
   ```bash
   sudo lxc-start -n c1 && sudo lxc-attach -n c1
   sudo lxc-start -n c2 && sudo lxc-attach -n c2
   ```
4. Gerenciar containers via **virt-manager** e **libvirt**:
   ```bash
   sudo apt install libvirt-daemon-system virt-manager virtinst libvirt-clients libvirt-daemon-driver-lxc
   sudo systemctl enable --now libvirtd
   ```

---

## Parte III – Docker
O **Docker** é a plataforma mais popular para empacotar e executar aplicações em containers.

### Instalação
Ao invés de seguir os comandos diretamente aqui, utilize a **documentação oficial** do Docker:  
🔗 [Instalar Docker no Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

---

### Publicando imagem no DockerHub
1. Aqui a gente segue um tutorial e cria uma imagem com Servidor Javascript.
2. Vamos seguir o guia "A simple application for the getting started guide in Docker's documentation" disponível na [documentação oficial](https://docs.docker.com/get-started/workshop/02_our_app/) do Docker.
3. Segue o tutorial do link que dá bom
4. Resumindo, faz o git clone do repositório lá
5. Depois cria o `Dockerfile`
6. Depois faz o build
   ```bash
   docker build -t getting-started .
   ```
7. Roda o cotainer com o comando abaixo
   ```bash
   docker run -d -p 127.0.0.1:3000:3000 getting-started
   ```
8. Push para o DockerHub, publica a imagem no Dockerhub, tu precisa da conta do DockerHub mas essa parte é opicional:
   ```bash
   docker login
   docker push <usuario_github>/getting-started:latest
   ```

---

### Docker Rootless
O modo rootless é uma forma de segurnaça para quando hackearem seu container, o hacker não tenha privilegios Root. Dessa forma, para executar no modo rootless, siga as instruções da **documentação oficial**:  
🔗 [Rootless Docker](https://docs.docker.com/engine/security/rootless/)
```bash
   sudo apt-get install -y dbus-user-session
```
```bash
   sudo apt-get install -y uidmap
```
```bash
sudo apt-get install -y systemd-container
```
```bash
sudo systemctl disable --now docker.service docker.socket
sudo rm /var/run/docker.sock
```
```bash
dockerd-rootless-setuptool.sh #tem que instalar como um usario non-root as a non-root user 
```
Roda esse comando antes de testar um container Rootlesse
```bash
export PATH=/usr/bin:$PATH
export DOCKER_HOST=unix:///run/user/1000/docker.sock
```
Ai vc testa com algum container aleatorio, aqui tem um exemplo rodando com o alpine
```bash
docker run --rm alpine sh -c "echo Olá, mundo! do Docker"
```

---

## Parte IV – Podman
O **Podman** é uma alternativa ao Docker, compatível com seus comandos, mas sem daemon.

### Instalação
```bash
sudo apt install podman
```

### Exemplo de uso

Aqui roda uma imagem busybox 
```bash
podman run --rm busybox sh -c "echo Olá, mundo! do Podman"
```

Lembra de colocar o relatorio em PDF no codeberg no final

---

---

## Conclusão
A prática permitiu explorar múltiplas tecnologias de containers e entender o uso de ferramentas de versionamento de código.  
O projeto completo está disponível em:  
🔗 [Repositório no Codeberg](https://codeberg.org/pedromujica1/tds-container/src/branch/master)

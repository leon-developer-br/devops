*** Instalação

docker run -d -p 9000:9000 -n portainer -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer

- o volume é necessário para que o portainer saiba o que está acontecendo com o docker localmente.
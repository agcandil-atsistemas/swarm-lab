# Entornos con problemas de conexión a internet
Para entornos con problemas de conexión se proporcionará los OVA de las instancias de virtualbox con un registry y con las imágenes necesarias para realizar todos los pasos de swarm-lab

### Para levantar el registry:

``` [bash]
mkdir /home/vagrant/registry
docker run -d \
  -p 5000:5000 \
  --restart=always \
  --name registry \
  -v /home/vagrant/registry:/var/lib/registry \
  registry:2
```

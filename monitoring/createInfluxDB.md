# Crear la base de datos de InfluxDB
Es necesario crear la base de datos manualmente en influxdb para que cadvisor empieze a persistir las métricas


## create new user
$ curl -G http://192.168.77.21:8086/query --data-urlencode "q=CREATE USER root WITH PASSWORD 'root' WITH ALL PRIVILEGES"

## create database for cadvisor
$ curl -G http://192.168.77.21:8086/query -u root:root --data-urlencode "q=CREATE DATABASE cadvisor"

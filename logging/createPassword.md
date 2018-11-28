Para generación de la configuración de passwords en graylog se usan los comandos detallados en la documentación oficial.

# GRAYLOG
pwgen -N 1 -s 96
echo -n yourpassword | shasum -a 256

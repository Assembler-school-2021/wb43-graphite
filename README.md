# wb43-graphite

Instalamos todo siguiendo las instrucciones del workbook: https://mimir.blackdogs.io/books/workbooks/page/wb-43---graphite

## Crear graficos en grafana
Para crear los gráficos vamos a la config global y add data source y añadimos graphite
 ![image](https://user-images.githubusercontent.com/65896169/127328397-273cbef1-7c9a-4264-9776-7f315a51497c.png)

Creamos un nuevo dashboard y un nuevo gráfico para el uso de cpu:
 ![image](https://user-images.githubusercontent.com/65896169/127328415-2ca75965-59e8-44c0-8da6-496a009ec6b5.png)

Otro para la carga media:
 ![image](https://user-images.githubusercontent.com/65896169/127328427-d54f6f46-f106-4553-825d-1c0652b4d83a.png)


Otros para el uso de memoria:
 ![image](https://user-images.githubusercontent.com/65896169/127328466-1afa85ce-c62d-424c-bfb2-505e79bc86e7.png)
 ![image](https://user-images.githubusercontent.com/65896169/127328492-8da79b79-1743-41d8-af11-71843b2775fb.png)



El uso del disco:
 ![image](https://user-images.githubusercontent.com/65896169/127328524-e91b2949-bd93-4f9d-8d0c-7b5950eb4ea7.png)
 ![image](https://user-images.githubusercontent.com/65896169/127328538-cb9018eb-7241-4ee1-af49-a15e6a198f97.png)



Y el uso de la red:

 ![image](https://user-images.githubusercontent.com/65896169/127328562-3186ddca-c29f-4f77-8a80-03531ab4cb38.png)
 ![image](https://user-images.githubusercontent.com/65896169/127328582-fd818b4b-8f8c-4354-80f7-a61e6fc16124.png)



Y tenemos un dashboard así:
 ![image](https://user-images.githubusercontent.com/65896169/127328605-29f1c5c1-2dc9-4bcc-a907-e7ce3f95faca.png)



Metricas servicios especificos (mysql)
-
> Crear todos una metrica de mysql. Vosotros solos:

Hay que levantar la máquina de wordpress
```
sensu-install -P sensu-plugins-mysql
sensuctl check create metrics-mysql --command '/opt/sensu-plugins-ruby/embedded/bin/metrics-mysql-raw.rb -h 127.0.0.1 -P 3306 -u sensu -p unpasswordmuyseguro --scheme `grep name\: /etc/sensu/agent.yml | cut -d\" -f 2`.mysql ' --interval 60 --subscriptions mysql --handlers graphite --output-metric-format graphite_plaintext
```
 ![image](https://user-images.githubusercontent.com/65896169/127328926-973fab23-0736-418a-962c-d3b0ed243741.png)

## Ejercicios
> Pregunta 1 : saca las metricas adecuadas de nginx y dibuja un gráfico

Creamos el comando:
```
sensuctl check create metrics-ngix --command '/opt/sensu-plugins-ruby/embedded/bin/metrics-nginx.rb -u http://localhost/stub_status --scheme `grep name\: /etc/sensu/agent.yml | cut -d\" -f 2`.nginx ' --interval 60 --subscriptions nginx --handlers graphite --output-metric-format graphite_plaintext
```
 ![image](https://user-images.githubusercontent.com/65896169/127328945-6050bd05-90e4-45c6-8d4d-05632ecf9cff.png)

> Pregunta 2 : saca las metricas adecuadas de php y dibuja un gráfico

Actualizamos el comando que ya teníamos:

`sensuctl check  set-output-metric-format php-status graphite_plaintext`

Y editamos el check desde el panel, modificando el comando y el los handlers:
 ![image](https://user-images.githubusercontent.com/65896169/127328970-d2ee9c3f-fa59-4c1a-bec1-cf36f250572c.png)

Habilitamos las slow request en el pool y configuramos el gráfico:
 ![image](https://user-images.githubusercontent.com/65896169/127328988-cd7a375f-ca68-431e-85c6-edac9b752b1d.png)


> Pregunta 3 : saca las metricas adecuadas de elasticsearch y dibuja un gráfico

Creamos el comando:
```
sensuctl check create elastic-metrics--command '/opt/sensu-plugins-ruby/embedded/bin/metrics-es-node-graphite.rb  -h 95.216.200.57 -u elastic -P elastic -p 9200 -e --cert /etc/filebeat/elasticsearch-ca.pem --scheme `grep name\: /etc/sensu/agent.yml | cut -d\" -f 2`.elastic' --interval 60 --subscriptions elastic --handlers graphite --output-metric-format graphite_plaintext
```
 ![image](https://user-images.githubusercontent.com/65896169/127329090-08df6e41-afea-4fbd-919d-fec104f0284c.png)

> Pregunta 4 : saca las metricas adecuadas de redis y dibuja un gráfico

Creamos el comando:
```
sensuctl check create redis-metrics --command '/opt/sensu-plugins-ruby/embedded/bin/metrics-redis-graphite.rb --scheme `grep name\: /etc/sensu/agent.yml | cut -d\" -f 2`.redis' --interval 60 --subscriptions redis --handlers graphite --output-metric-format graphite_plaintext
```

Hacemos uno de la memoria
![image](https://user-images.githubusercontent.com/65896169/127329149-fa7919f6-91b7-441b-b393-7d20d94f74bc.png)

Y otro de los clientes:
![image](https://user-images.githubusercontent.com/65896169/127329169-f9fa5f01-1609-4cbe-9092-b703d6ed57f5.png)

Y otra de los comandos
![image](https://user-images.githubusercontent.com/65896169/127329192-a2ac6712-643a-4e47-b3fc-100350707702.png)


> Pregunta 5 : haz un dashboard general para el servicio de wordpress con indicativos adecuados.

Nos quedaría un dashboard así:
![image](https://user-images.githubusercontent.com/65896169/127329231-e8396a27-28e5-4709-88d8-81bd41d3d54c.png)



> Pregunta 6
> Levanta un entorno con docker que contenga un wordpress con su contenedor web y su contenedor de base de datos.
> Configura los checks de sensu, saca metricas y dijuba graficos adecuados.

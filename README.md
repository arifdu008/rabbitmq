# Here we will install rabbitmq as message broker for ducouple application 


At first we clone a sample publisher and consumer app written in Golang
```bash
git clone https://github.com/arifdu008/rabbitmq.git
```
![imagename](/image/1.JPG)

Change directory
```bash
cd rabbitmq/publisher/
```
Create a custom bridge neywork named rabbits so that we can communicate by container name

Note : Default bridge network cannot communicate by name
```bash
docker network create rabbits
```
![imagename](/image/2.JPG)

Delete all container 
```bash
docker rm -f $(docker ps -a -q)
```
delete all images
```bash
docker rmi -f $(docker images -aq)
```
test any service running on port 80
```bash
netstat -anpe | grep "80" | grep "LISTEN"
```
Create rabbitmq container using below command on port 8000 with image rabbitmq:3-management
```bash
docker run -d --rm --net rabbits -p 8000:15672 --name rabbit rabbitmq:3-management
```

![imagename](/image/7.JPG)

Check container is running
```bash
docker ps
```
![imagename](/image/8.JPG)

Check rabbitmq is  running on port 8000 with no queue

Default user and password is guest
![imagename](/image/9.JPG)

Build publisher container
```bash
docker build . -t rabpub:v1
```
Run publisher container
```bash
docker run -it --rm --net rabbits -e RABBIT_HOST=rabbit -e RABBIT_PORT=5672 -e RABBIT_USERNAME=guest -e RABBIT_PASSWORD=guest -p 80:80 rabpub:v1
```
![imagename](/image/10.JPG)

Send message
```bash
curl -X POST http://localhost/publish/message1
```
Got message in publisher conatainer

![imagename](/image/11.JPG)


total 1 message in queue
![imagename](/image/12.JPG)

Change to consumer direstory
```bash
cd /root/rabbitmq/consumer/
```
Build consumer image
```bash
docker build . -t rabcon:v1
```
Run consumer container
```bash
docker run -it --rm --net rabbits -e RABBIT_HOST=rabbit -e RABBIT_PORT=5672 -e RABBIT_USERNAME=guest -e RABBIT_PASSWORD=guest rabcon:v1
```
As soon as we run consumer container message receive in consumer screen and rabbit queue is clear

![imagename](/image/13.JPG)

![imagename](/image/14.JPG)

Total 3 message send and instant deliver to consumer no queue in rabbitmq

![imagename](/image/15.JPG)



# Zalando_AWS_Deployment

scripts to deploy apps/services to AWS(zalando account currently) with STUPS.

## AWS Account login
```bash
zaws login __account-name__ PowerUser
```

## Deployment
```bash
senza create aws/senza.yaml 1 ApplicationId=zzhang-experiment-app AvailabilityZone=eu-west-1a DockerSource={} DockerTag={}
```
The docker source/tag could be found in hub.docker.com. 
For example, to deploy a GPU-supported jupyter server.
```bash
senza create aws/senza.yaml 1 ApplicationId=zzhang-experiment-app AvailabilityZone=eu-west-1a DockerSource=tensorflow/tensorflow DockerTag=latest-gpu
```

## SSH to the EC2 instance.
Get the instance IP address:
```bash
ip=$(senza  --region eu-west-1 inst zzhang-experiment-gpu 1 | awk '/RUNNING/ {print $5}')
```
Request the permission through odd-bastion server.
```bash
piu request-access -O odd-eu-west-1.echo.zalan.do $ip "Tensorflow experiment"
```
SSH to the instance.
```bash
ssh -tA odd-eu-west-1.echo.zalan.do ssh -o StrictHostKeyChecking=no ${ip}
```

## Open SSH channel to access jupyter notebook or tensorboard in browser.
Since the instance is behind a private subnet work for security reason, we could open a SSH channel 
to access the web service. we need foxyproxy extension in Chrome.
* Firstly request permission to access odd-bastion server.
```bash
piu zzhang@odd-eu-west-1.echo.zalan.do "SSH Connection"
```
* Then open the channel to forward all the traffic to 8157 port.
```bash
ssh -ND 8157 zzhang@odd-eu-west-1.echo.zalan.do
```  
* Finally, open the web service in browser.
For example:
```html
Jupyter notebook: http://ip-172-31-142-97.eu-west-1.compute.internal:8888/
```
```html
Tensorboard: http://ip-172-31-142-97.eu-west-1.compute.internal:6006/
```
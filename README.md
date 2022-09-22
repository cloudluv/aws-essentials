# 명령어

**Amazon Linux 2를 사용합니다**

## Mounting EBS volume

```bash
lsblk
sudo mkfs -t xfs /dev/xvdf
sudo file -s /dev/xvdf
sudo mkdir /data
sudo mount /dev/xvdf /data
sudo umount /data
```

## Install Corretto, Git and Maven

```bash
sudo yum install java-11-amazon-corretto -y
sudo yum install git -y
wget https://dlcdn.apache.org/maven/maven-3/3.8.6/binaries/apache-maven-3.8.6-bin.tar.gz -P /tmp
sudo tar xf /tmp/apache-maven-*.tar.gz -C /opt
sudo ln -s /opt/apache-maven-3.8.6/ /opt/maven
export PATH=/opt/maven/bin:${PATH}
```

## Build Spring Boot and launch it

```bash
git clone https://github.com/cloudluv/essentials.git
git checkout v0.1
mvn clean install
java -jar target/essentials-0.0.1-SNAPSHOT.jar > /tmp/essentials.log 2>&1 &
```

## Launch Spring Boot in the background

```bash
#!/bin/bash
nohup java -jar /home/ec2-user/essentials/target/essentials-0.0.1-SNAPSHOT.jar &
```

## Install Nodejs and run Artillery

```bash
curl -sL https://rpm.nodesource.com/setup_16.x | sudo bash -
sudo yum install -y nodejs
npm install -g artillery
artillery quick --count 20 --num 500 http://test-asg-1-782568508.ap-northeast-2.elb.amazonaws.com/health
```

## Issue certicates for AWS VPN client

```bash
git clone https://github.com/OpenVPN/easy-rsa.git
cd easy-rsa/easyrsa3
./easyrsa init-pki
./easyrsa build-ca nopass
./easyrsa build-server-full server nopass
./easyrsa build-client-full client1 nopass
```

## S3 bucket policy

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCloudFrontServicePrincipalReadOnly",
      "Effect": "Allow",
      "Principal": {
        "Service": "cloudfront.amazonaws.com"
      },
      "Action": "s3:GetObject",
      "Resource": "<S3 ARN>",
      "Condition": {
        "StringEquals": {
          "AWS:SourceArn": "<Cloudfront ARN>"
        }
      }
    }
  ]
}
```

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicAccess",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "<S3 ARN>"
    }
  ]
}
```

## EFS 연결

**EC2 보안그룹 NFS 허용 & EFS Network NFS 허용**

```bash
sudo su -
mkdir /efs
cd /
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport <EFS DNS 이름>:/ efs
```

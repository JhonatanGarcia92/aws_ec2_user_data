#!/bin/bash

INSTANCE_ID=$(curl http://169.254.169.254/latest/meta-data/instance-id)
VOLUME_ID="vol-0076ee6cc71da51b7"
DEVICE="/dev/sdf"

# Executa o comando retornando o oitavo parametro (array)
IN_USE=$(aws --region us-east-1 --output text ec2 describe-volumes --volume-id $VOLUME_ID | awk '{ print $8}')
IN_USE=$(echo $IN_USE | awk '{ print $1 }')

# Executa para verificar se o disco tem particao criada
PARTICAO=$(sudo file -s /dev/xvdf | awk '{ print $2}')

# Testa se IN_USE tem o valor in-use
if [ $IN_USE != "in-use" ]
then
  aws --region us-east-1 --output text ec2 attach-volume --instance-id $INSTANCE_ID --volume-id $VOLUME_ID --device $DEVICE
  sleep 15
  PARTICAO=$(sudo file -s /dev/xvdf | awk '{ print $2}')

else
  ATTACHMENT_INFO=$(aws --region us-east-1 --output text ec2 describe-volumes --volume-id $VOLUME_ID | egrep ATTACHMENT | awk '{ print $5}')
if [ $ATTACHMENT_INFO == $INSTANCE_ID ]
  then
    mount /dev/xvdf /mnt
  fi
fi

if [ $PARTICAO == "data" ]
  then
    mkfs -t xfs /dev/xvdf
    mount /dev/xvdf /mnt
    echo "particao criada"
else
    mount /dev/xvdf /mnt
fi

yum install httpd php -y
aws s3 cp s3://seubucket/index.php /var/www/html/
systemctl start httpd

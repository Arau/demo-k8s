#!/bin/bash

# Set up of R53 zone to use for k8s operation purposes.
# Please set up a domain you own and a zone name that doesn't collide with an existing one.


# Load variables to have name consistency across the bootstrap process
source ../etc/vars.conf

#Variables defined at vars
# DNS_ZONE_NAME

# Check that the zone we are creating doesn't exist, hence we don't collide domains

zone_exists=$(aws route53 list-hosted-zones-by-name | grep -c  "$DNS_ZONE_NAME")

if [ $zone_exists -eq 1 ]; then
    echo "The DNS zone we are about to create or modify already exists. Finishing the execution for security"
    exit 1
fi

aws route53 create-hosted-zone --name $DNS_ZONE_NAME --caller-reference <(date "+%N")

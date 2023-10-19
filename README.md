See 
* https://medium.com/tensult/guide-to-setup-kubernetes-in-aws-eks-using-terraform-and-deploy-sample-applications-ee8c45e425ca
* https://github.com/kubernetes-sigs/aws-load-balancer-controller/blob/main/docs/guide/ingress/annotations.md#target-type
* https://gitlab.com/nicosingh/medium-deploy-eks-cluster-using-terraform-sample-app/-/tree/master/helm?ref_type=heads

# Install 

* copy the template to your own version and set your values
> cp config/values.yaml config/values-development.yaml

* Install 
> helm upgrade -i  sample-app  ./   -f config/values-development.yaml   --debug --dry-run 

> helm upgrade -i  sample-app  ./   -f config/values-development.yaml
* List 
> helm list


* Then check Route53 -> Hosted Zones for new DNS entry 
* Check EC2 -> Loadbalancers -> <YOUR_LB_NAME>
* Check ing 

```

kctl get ing
NAME                       CLASS   HOSTS                              ADDRESS                                                 PORTS   AGE
sample-app-ingress-rules   alb     sample.<YOUR_DOMAIN>   XXX.us-east-1.elb.amazonaws.com   80      7m13s
```

Check DNS

> dig sample.<YOUR_DOMAIN>

> dig  XXX.us-east-1.elb.amazonaws.com

* Wait ~ 5 min until LB is deployed, before we wil get 404 and other errors 
```
curl -ILk  http://sample.<YOUR_DOMAIN>
HTTP/1.1 301 Moved Permanently
Server: awselb/2.0
Date: Thu, 19 Oct 2023 05:18:43 GMT
Content-Type: text/html
Content-Length: 134
Connection: keep-alive
Location: https://sample.<YOUR_DOMAIN>:443/

HTTP/2 200
date: Thu, 19 Oct 2023 05:18:44 GMT
content-type: text/html
server: nginx/1.25.2
expires: Thu, 19 Oct 2023 05:18:43 GMT
cache-control: no-cache
```

Open in Browser 
> open http://sample.<YOUR_DOMAIN>


# Annotations

public facing
```
  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
    alb.ingress.kubernetes.io/group.name: {{ .Values.ingress.lbGroup }}
    alb.ingress.kubernetes.io/load-balancer-name: {{ .Values.ingress.lbGroup }}
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTP":80},{"HTTPS":443}]'
    alb.ingress.kubernetes.io/ssl-redirect: "443"
    
    #see in aws console -> securitiy groups -> Security group name > clsutername-alb (acaternberg-tf-02-alb)  an grep the securitygroup-id 
    alb.ingress.kubernetes.io/security-groups: {{ .Values.ingress.securityGroup }}
    
    external-dns.alpha.kubernetes.io/hostname: {{ .Values.ingress.host }}
    external-dns.alpha.kubernetes.io/alias: "true"
```

Internal ALP (private subnets)
```
  annotations:
    alb.ingress.kubernetes.io/scheme: internal
    alb.ingress.kubernetes.io/target-type: ip
    alb.ingress.kubernetes.io/group.name: {{ .Values.ingress.lbGroup }}
    alb.ingress.kubernetes.io/load-balancer-name: {{ .Values.ingress.lbGroup }}
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTP":80},{"HTTPS":443}]'
    alb.ingress.kubernetes.io/ssl-redirect: "443"
    
    #Just enable alb.ingress.kubernetes.io/subnets: if private subnets and a internal/private ALB is  used
    #Check your  VPC to get the two private subnets 
    alb.ingress.kubernetes.io/subnets: subnet-1234,subnet-5678
    
    #see in aws console -> securitiy groups -> Security group name > clsutername-alb (acaternberg-tf-02-alb)  an grep the securitygroup-id 
    alb.ingress.kubernetes.io/security-groups: {{ .Values.ingress.securityGroup }}
    
    external-dns.alpha.kubernetes.io/hostname: {{ .Values.ingress.host }}
    external-dns.alpha.kubernetes.io/alias: "true"
```

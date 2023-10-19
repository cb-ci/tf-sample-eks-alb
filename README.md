See 
* https://medium.com/tensult/guide-to-setup-kubernetes-in-aws-eks-using-terraform-and-deploy-sample-applications-ee8c45e425ca
* https://github.com/kubernetes-sigs/aws-load-balancer-controller/blob/main/docs/guide/ingress/annotations.md#target-type
* https://gitlab.com/nicosingh/medium-deploy-eks-cluster-using-terraform-sample-app/-/tree/master/helm?ref_type=heads


 
> helm upgrade -i  sample-app  ./   -f config/values-development.yaml   --debug --dry-run 

> helm upgrade -i  sample-app  ./   -f config/values-development.yaml

> helm list


* Then check Route53 -> Hosted Zones for new DNS entry 
* Check EC2 -> Loadbalancers -> caternberg-alb
* Check ing 

```

kctl get ing
NAME                       CLASS   HOSTS                              ADDRESS                                                 PORTS   AGE
sample-app-ingress-rules   alb     sample.acaternberg.pscbdemos.com   XXX.us-east-1.elb.amazonaws.com   80      7m13s
```

Check DNS

> dig sample.acaternberg.pscbdemos.com 

> dig  XXX.us-east-1.elb.amazonaws.com

* Wait ~ 5 min until LB is deployed, before we wil get 404 and other errors 
```
curl -ILk  http://sample.acaternberg.pscbdemos.com
HTTP/1.1 301 Moved Permanently
Server: awselb/2.0
Date: Thu, 19 Oct 2023 05:18:43 GMT
Content-Type: text/html
Content-Length: 134
Connection: keep-alive
Location: https://sample.acaternberg.pscbdemos.com:443/

HTTP/2 200
date: Thu, 19 Oct 2023 05:18:44 GMT
content-type: text/html
server: nginx/1.25.2
expires: Thu, 19 Oct 2023 05:18:43 GMT
cache-control: no-cache
```

Open in Browser 
> open http://sample.acaternberg.pscbdemos.com 

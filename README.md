See 
* https://medium.com/tensult/guide-to-setup-kubernetes-in-aws-eks-using-terraform-and-deploy-sample-applications-ee8c45e425ca
* https://github.com/kubernetes-sigs/aws-load-balancer-controller/blob/main/docs/guide/ingress/annotations.md#target-type
* https://gitlab.com/nicosingh/medium-deploy-eks-cluster-using-terraform-sample-app/-/tree/master/helm?ref_type=heads

 
> > helm upgrade -i  sample-app  ./   -f config/values-development.yaml   --debug --dry-run 

```

kctl get ing
NAME                       CLASS   HOSTS                              ADDRESS                                                 PORTS   AGE
sample-app-ingress-rules   alb     sample.acaternberg.pscbdemos.com   XXX.us-east-1.elb.amazonaws.com   80      7m13s
```

> open http://sample.acaternberg.pscbdemos.com 

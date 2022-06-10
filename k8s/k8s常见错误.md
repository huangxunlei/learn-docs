

##  1、查看日志

```bash
[root@k8s-master ~]``# kubectl logs nginx-dbddb74b8-j9rdf
Error from server (Forbidden): Forbidden (user=system:anonymous, verb=get, resource=nodes, subresource=proxy) ( pods``/log` `nginx-dbddb74b8-j9rdf)
解决办法：将system:anonymous绑定到cluster-admin角色中 起个cluster-system:anonymous名字
[root@k8s-master ~]``# kubectl create clusterrolebinding cluster-system:anonymous --clusterrole=cluster-admin --user=system:anonymous
clusterrolebinding.rbac.authorization.k8s.io``/cluster-system``:anonymous created
```
# kubernetes-VPN
This is a VPN solution for purposed connection remote clusters each other.<br>
I [deployed](https://kubernetes.github.io/ingress-nginx/deploy/) nginx ingress controller on Azure Kubernetes Service for this example.Before moving forward if you would like to use ingress for exposing your cluster you should setup [TCP-UDP exposing](https://kubernetes.github.io/ingress-nginx/user-guide/exposing-tcp-udp-services/) for ingress.<br>
<br>
In lazy way:<br>
``kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.41.2/deploy/static/provider/cloud/deploy.yaml``<br>
``EDITOR=nano kubectl edit deployment -n ingress-nginx   ingress-nginx-controller``<br>
Add these commands after - /nginx-ingress-controller for both tcp and udp exposing from ingress<br>
 ``- --tcp-services-configmap=ingress-nginx/tcp-services``<br>
 ``- --udp-services-configmap=ingress-nginx/udp-services``<br>
 
After that with server.yaml you can deploy your open vpn server on your kubernetes cluster.<br>

``kubectl apply -f server.yaml``<br>
``kubectl apply -f ingress-server.yaml``<br>

Now after waiting 1-2 minutes you must be able to reach access server ui on https://(loadbalancer OR ingress-ip):943/admin.<br>
  
Default username password is admin:password on openvpn.You can create user(in his example without authentication so you should check the allow autologin checkbox.) from ui.Logout and login from user page(https://(loadbalancer OR ingress-ip):943).So you can download and use your .ovpn certificate.<br>

For using on desktop you must download and install [openvpn-gui](https://openvpn.net/client-connect-vpn-for-windows/) and import your certificate it.After that you can reach your pod network from your desktop.<br>

For reaching a VPN server from kubernetes pod network:<br>

Copy inside of your .ovpn(client.ovpn) file and paste it to client.yaml's ovpn config map.
Apply client.yaml.You should replace 2. container as your own container that will be use VPN.
``kubectl apply -f client.yaml``
> :warning: **This is not battle tested yet!Think twice before use it in production.**

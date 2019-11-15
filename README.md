# istio-issue

Step to reproduce issue ->

1. Label default namespace with **istio-injection: enabled**
2. Enable Mixer Policy Check ( disablePolicyChecks=false )
3. kubectl apply -f application.yaml
4. kubectl apply -f gw-vs.yaml
5. Verify application return status code of 200( curl -I -H 'Host:httpbin.example.com' http://$INGRESS_HOST:80/status/200)
6. Inject your public IP into handlers/handler-1.yaml
7. kubectl apply -f handlers/handler-1.yaml -n istio-system
8. kubectl apply -f instance.yaml -n istio-system
9. kubectl apply -f rule-handler-1.yaml -n istio-system
10. Verify application return status code of 403, Forbidden( curl -I -H 'Host:httpbin.example.com' http://$INGRESS_HOST:80/status/200 ) 
11. Start to observe Mixer Log( kubectl logs -f $MixerPod -c mixer -n istio-system )
12. kubectl apply -f rule-all.yaml -n istio-system
13. Apply all handlers file( find . -name "handler-*" -exec kubectl apply -n istio-system -f {} \; )
14. You may see the following error:

```
error	mcp	Error receiving MCP resource: rpc error: code = ResourceExhausted desc = grpc: received message larger than max (7399865 vs. 4194304)
```
# OKD Prometheus 인증 및 TLS 연결 방법 정리
https://access.redhat.com/documentation/en-us/openshift_container_platform/4.1/html/authentication/configuring-certificates

https://docs.openshift.com/container-platform/3.11/install_config/prometheus_cluster_monitoring.html


https://access.redhat.com/documentation/ko-kr/openshift_container_platform/4.7/pdf/authentication_and_authorization/openshift_container_platform-4.7-authentication_and_authorization-ko-kr.pdf

https://www.techbeatly.com/openshift-4-query-prometheus-api-for-firing-alert/


# OKD Prometheus Bearer Token 구하는 방법
1. `oc sa get-token ${ServiceAccountName} -n ${Prometheus Deploy Namespace}`
> Prometheus Server가 사용하는 Service Account 정보를 입력 

2.`curl -XGET https://${prometheusServiceName}.${prometheusNamespace}:${port}/-/healthy -H "Authorization: Bearer $TOKEN`

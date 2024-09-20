# Preparation

1. Attach `CloudWatchAgentServerPolicy` into IAM role of Worker Node

# Deploy Amazon CloudWatch Agent & Fluentdas Daemonsets

This command will:

- Creates the Namespace amazon-cloudwatch.

```
kubectl create ns amazon-cloudwatch
```

- Creates all the necessary security objects for both DaemonSet:

  - SecurityAccount

  - ClusterRole

  - ClusterRoleBinding

- Deploys Cloudwatch-Agent (responsible for sending the metrics to CloudWatch)
  as a DaemonSet.

- Deploys fluentd (responsible for sending the logs to Cloudwatch) as a
  DaemonSet.

- Deploys ConfigMap configurations for both DaemonSets.

```
# Template
curl -s https://raw.githubusercontent.com/aws-samples/amazon-cloudwatch-container-insights/latest/k8s-deployment-manifest-templates/deployment-mode/daemonset/container-insights-monitoring/quickstart/cwagent-fluentd-quickstart.yaml | sed "s/{{cluster_name}}/<REPLACE_CLUSTER_NAME>/;s/{{region_name}}/<REPLACE-AWS_REGION>/" | kubectl apply -f -

# Replaced Cluster Name and Region
curl -s https://raw.githubusercontent.com/aws-samples/amazon-cloudwatch-container-insights/latest/k8s-deployment-manifest-templates/deployment-mode/daemonset/container-insights-monitoring/quickstart/cwagent-fluentd-quickstart.yaml | sed "s/{{cluster_name}}/vietaws5/;s/{{region_name}}/ap-southeast-1/" | kubectl apply -f -

# Verify

# List Daemonsets
kubectl -n amazon-cloudwatch get daemonsets
```

# Deploy App Demo

```
kubectl apply -f 01-app.yaml
```

# Generate Load

```
kubectl run test-load1 --rm --tty -i --restart='Never' --image devth/alpine-bench --command -- /go/bin/main -n 500000 -c 1000 http://home-svc.default.svc.cluster.local/
```

# Create CloudWatch Dashboard

1. Create Dashboard "EKS-Demo"

## Widget 1 - Node Performance

```
# Query log insights
STATS avg(node_cpu_utilization) as avg_node_cpu_utilization by NodeName
| SORT avg_node_cpu_utilization DESC

# Type: Bar
# Log Group: /aws/containerinsights/vietaws1/performance
```

## Widget 2 - Container Restart

```
# Query
STATS avg(number_of_container_restarts) as avg_number_of_container_restarts by PodName
| SORT avg_number_of_container_restarts DESC

# Type: Table
# Log Group: /aws/containerinsights/vietaws1/performance
```

## Widget 3 - Node Failure

```
# Query
stats avg(cluster_failed_node_count) as CountOfNodeFailures
| filter Type="Cluster"
| sort @timestamp desc

# Type: Table
# Log Group: /aws/containerinsights/vietaws1/performance
```

## Widget 4 - CPU by Container

```
# Query
stats pct(container_cpu_usage_total, 50) as CPUPercMedian by kubernetes.container_name
| filter Type="Container"

# Type: Bar
# Log Group: /aws/containerinsights/vietaws1/performance
```

## Widget 5 - Pod Requested vs Running

```
# Query
fields @timestamp, @message
| sort @timestamp desc
| filter Type="Pod"
| stats min(pod_number_of_containers) as requested, min(pod_number_of_running_containers) as running, ceil(avg(pod_number_of_containers-pod_number_of_running_containers)) as pods_missing by kubernetes.pod_name
| sort pods_missing desc

# Type: Bar
# Log Group: /aws/containerinsights/vietaws1/performance
```

## Widget 6 - Application log errors by Container

```
# Query
stats count() as countoferrors by kubernetes.container_name
| filter stream="stderr"
| sort countoferrors desc

# Type: Bar
# Log Group: /aws/containerinsights/vietaws1/application
```

📗 Reference:
https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-view-metrics.html

# Clean

```
# Template
curl https://raw.githubusercontent.com/aws-samples/amazon-cloudwatch-container-insights/latest/k8s-deployment-manifest-templates/deployment-mode/daemonset/container-insights-monitoring/quickstart/cwagent-fluentd-quickstart.yaml | sed "s/{{cluster_name}}/cluster-name/;s/{{region_name}}/cluster-region/" | kubectl delete -f -

# Replace Cluster Name & Region Name
curl https://raw.githubusercontent.com/aws-samples/amazon-cloudwatch-container-insights/latest/k8s-deployment-manifest-templates/deployment-mode/daemonset/container-insights-monitoring/quickstart/cwagent-fluentd-quickstart.yaml | sed "s/{{cluster_name}}/vietaws1/;s/{{region_name}}/ap-southeast-1/" | kubectl delete -f -
```

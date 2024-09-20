# Create Fargate Profiles using YAML file

eksctl create fargateprofile -f 01-fp.yaml --profile eks

# List Fargate profiles

eksctl get fargateprofile --cluster vietaws1 --profile eks

# Delete fargate profile

eksctl delete fargateprofile --cluster vietaws1 --name home-fp --profile eks

eksctl delete fargateprofile --cluster vietaws1 --name app2-fp --profile eks

eksctl delete fargateprofile --cluster vietaws1 --name fargate-demo --profile
eks

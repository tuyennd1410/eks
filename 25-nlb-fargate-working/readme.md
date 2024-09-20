# 1 create fargate profile

eksctl create fargateprofile -f fargate-profile/home-fp.yaml --profile eks

# 2 Update home app to ip (NOT instance)

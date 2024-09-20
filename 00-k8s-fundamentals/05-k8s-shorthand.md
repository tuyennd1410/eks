✅ Finding your bash configure

- ~/.bash - default macos
- ~/.zshrc - macos with zsh (my default)

```
vi ~/.zshrc
```

✅ Paste following shorthand to top of the file

```
#: <<'END_COMMENT'
alias k='kubectl '
alias kg='kubectl get '
alias kd='kubectl delete '
alias kr='kubectl run --image-pull-policy Always --image '
alias ka='kubectl apply -f '
alias kgn='kubectl get nodes'
alias kgp='kubectl get pods'
alias kgc='kubectl config get-contexts'
alias kc='kubectl config use-context '
alias kdc='kubectl config delete-context '
alias kgs='kubectl get svc'
alias kgd='kubectl get deploy'
master_role=arn:aws:iam::1234567890:role/eks-master-role
alias getekskeys='aws sts assume-role --role-arn $master_role --role-session-name vieteks --duration-seconds $((8*60*60)) --profile  eks'
echo "Amazon EKS's status: Success!"
#END_COMMENT
```
